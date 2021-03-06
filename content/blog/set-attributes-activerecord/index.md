---
title: Different Ways to Set Attributes in ActiveRecord (Rails 6)
date: "2020-01-11T13:41:00.169Z"
description: A updated version of the excellent blogpost by David Verhasselt.
---

In 2014, [David Verhasselt](https://twitter.com/DavidVerhasselt) wrote an [excellent blogpost](https://davidverhasselt.com/set-attributes-in-activerecord/) on the various methods `ActiveRecord` provides us to update records, how they differ, and their unique side-effects.

I've referenced it many times over the years when reading codebases, and determining the best way to update a particular attribute.

In this post, I'd like to bring his blogpost up-to-date for Rails 6.

## Cheat Sheet

| Method                | Default Accessor | Saved to DB | Validations | Callbacks | Touches `updated_at` | Readonly check |
| --------------------  |:----------------:| -----------:| -----------:| ---------:| --------------------:| --------------:|
| `attribue(s)=`        | Yes              | No          | -           | -         | -                    | -              |
| `write_attribute`     | No               | No          | -           | -         | -                    | -              |
| `assign_attributes`   | Yes              | No          | -           | -         | -                    | -              |
| `update_attribute`    | Yes              | Yes         | No          | Yes       | Yes                  | Yes            |
| `update_attributes`   | Yes              | Yes         | Yes         | Yes       | Yes                  | Yes            |
| `update`              | Yes              | Yes         | Yes         | Yes       | Yes                  | Yes            |
| `update_column(s)`    | Yes              | Yes         | No          | No        | No                   | Yes            |
| `User::update`        | Yes              | Yes         | Yes         | Yes       | Yes                  | Yes            |
| `User::update_all`    | No               | Yes         | No          | No        | No                   | No             |
| `User::insert(_all)`  | No               | Yes         | No          | No        | No                   | No             |
| `User::upsert(_all)`  | No               | Yes         | No          | No        | No                   | No             |

As of Rails 6, `update_attributes` has been depreciated in favour of `update`.
