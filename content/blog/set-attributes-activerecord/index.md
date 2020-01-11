---
title: Different Ways to Set Attributes in ActiveRecord (Rails 6)
date: "2020-01-11T13:41:00.169Z"
description: A updated version of the excellent blogpost by David Verhasselt.
---

In 2014 [David Verhasselt](https://twitter.com/DavidVerhasselt) wrote an [excellent blogpost](https://davidverhasselt.com/set-attributes-in-activerecord/) on the various methods `ActiveRecord` provides us to update records, how they differ, and unique their side-effects.

I've used it many times over the years as a quick reference when reading codebases, and determining how exactly I want to update a particular attribute.

In this post, I'd like to bring his blogpost up-to-date for Rails 6.

## Cheat Sheet

| Method              | Default Accessor | Saved to DB | Validations | Callbacks | Touches `updated_at` | Readonly check |
| ------------------  |:----------------:| -----------:| -----------:| ---------:| --------------------:| --------------:|
| `attribue=`         |                  |             |             |           |                      |                |
| `write_attribute`   |                  |             |             |           |                      |                |
| `update_attribute`  |                  |             |             |           |                      |                |
| `update_attributes` |                  |             |             |           |                      |                |
| `attributes`=       |                  |             |             |           |                      |                |
| `update`            |                  |             |             |           |                      |                |
| `update_column`     |                  |             |             |           |                      |                |
| `update_columns`    |                  |             |             |           |                      |                |
| `User::update`      |                  |             |             |           |                      |                |
| `User::update_all`  |                  |             |             |           |                      |                |
| `User::insert_all`  |                  |             |             |           |                      |                |
| `User::upsert_all`  |                  |             |             |           |                      |                |

