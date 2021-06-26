---
title: When the index won't fit
date: "2021-26-06T13:41:00.169Z"
description: Indexing large columns of text
---

Recently at the ol' day job a strange error cropped up in our production environment. A worker consuming messages from another service threw the following error: (exact names of entities have been altered for reasons)

```
ERROR: index row size 2832 exceeds maximum 2712 for index "index_dog_on_animal_url" HINT: Values larger than 1/3 of a buffer page cannot be indexed. Consider a function index of an MD5 hash of the value, or use full text indexing.
```

The index in question was to enforce uniqueness on the `animal_url` of the `dog` table. The column in question was so big that Postgres could no longer index it as it exceeded 1/3 of the size of a Postgres buffer page.

To remedy this, we simply created a migration for the index to be created on a hash of the `animal_url` in order to drastically reduce the length of the string to be indexed. Alternatively, one could compute this hash themselves in the application, save that to the database, and index it seperately. Doing it directly on the database is cleaner in my opinion and requires only a migration rather than any extra application code. Said code would also have to keep the hashed value up-to-date if the URL ever changed.

The migration in question:

```ruby
# frozen_string_literal: true
class AmendDogAnimalUrlIndex < ActiveRecord::Migration[6.1]
  def up
    remove_index :dogs, :animal_url
    execute create_hashed_index_query
  end

  def down
    remove_index :dogs, name: 'index_dogs_on_animal_url'
    add_index :dogs, :animal_url, unique: true
  end

  private

  def create_hashed_index_query
    <<~SQL
      CREATE UNIQUE INDEX index_dogs_on_animal_url
      ON dogs
      USING btree (digest(animal_url, 'sha512'::text));
    SQL
  end
end
```

As you can see, this will drop the existing index and create a new one where we hash the `animal_url` column with the SHA512 algorithm.

Other alternatives include changing Postgres defaults by increasing the size of its buffer pages. I do not want to venture into the realm of database tuning though when a simpler (and probably safer) solution exists 😊.
