---
title: Defer database constraints with Rails + PostgreSQL
date: "2020-01-01T11:00:00.169Z"
description: A short intro and example of a deferrable PostgreSQL constraint in a Rails application.
---

Database constraints are great. They define the rules the data in our DB must abide by, upholding the integrity of the data it contains.
You're probably familiar with constraints like `NOT NULL`, `UNIQUE`, `PRIMARY/FOREIGN KEY` etc. but are there cases where we would rather ignore a particular constraint?

I recently ran into this issue when updating the item positions in a list.

Say you have a `list` that `has_many` `list_items`. You would likely have a multicolumn index on the `list_items` table on `list_id` and `position` to ensure that not list has more than one list item in the same position.

This makes sense and is a good constraint to apply but what happens when we try to update the position of one or more of the existing list items?

Take the following list for example:

```
1. albatross
2. eagle
3. birdy
```

Updating the `birdy` record from position `3` to `2` will violate our contraint raising an exception when we try to make the update as that position is already held by `eagle`.

One workaround is to keep track of what all the new positions will be in our application logic, clear all of the list item positions, and subsequently update with the newly calculated positions.

While this approach works, it complicates our application code and involves unnecessary writes to the DB.

Alternatively we can make the uniquness constraint on `list_id` and `position` deferrable while updating the list item positions:

```ruby
...
  def up
    remove_index :list_items, [:list_id, :position]
    execute <<~SQL
      ALTER TABLE list_items
        ADD CONSTRAINT list_items_position UNIQUE (list_id, position)
        DEFERRABLE INITIALLY IMMEDIATE;
      SQL
  end

  def down
    execute <<~SQL
      ALTER TABLE list_items
        DROP CONSTRAINT list_items_position;
    SQL
    add_index :list_items, [:list_id, :position], unique: true
  end
...
```

Now in `ListItemsController#update`:

```ruby
...
  ActiveRecord::Base.transaction do
    ActiveRecord::Base.connection.execute('SET CONSTRAINTS list_items_position DEFERRED')

    list_item_ids.each_with_index do |id, index|
      list_item = ListItem.find(id)
      # We use update_attribute to avoid triggering application level constraints.
      list_item.update_attribute(:position, index)
    end
  end
...
```

We can now reorder our list items, deferring the constraint until the end of the transaction.
