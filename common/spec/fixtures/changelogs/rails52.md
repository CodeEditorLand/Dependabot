## Rails 5.2.1.1 (November 27, 2018)

- No changes.

## Rails 5.2.1 (August 07, 2018)

- PostgreSQL: Support new relkind for partitioned tables.

    Fixes #33008.

    _Yannick Schutz_

- Rollback parent transaction when children fails to update.

    _Guillaume Malette_

- Fix default value for MySQL time types with specified precision.

    _Nikolay Kondratyev_

- Fix `touch` option to behave consistently with `Persistence#touch` method.

    _Ryuta Kamizono_

- Fix `save` in `after_create_commit` won't invoke extra `after_create_commit`.

    Fixes #32831.

    _Ryuta Kamizono_

- Fix logic on disabling commit callbacks so they are not called unexpectedly
  when errors occur.

    _Brian Durand_

- Fix parent record should not get saved with duplicate children records.

    Fixes #32940.

    _Santosh Wadghule_

- Fix that association's after_touch is not called with counter cache.

    Fixes #31559.

    _Ryuta Kamizono_

- `becomes` should clear the mutation tracker which is created in
  `after_initialize`.

    Fixes #32867.

    _Ryuta Kamizono_

- Allow a belonging to parent object to be created from a new record.

    _Jolyon Pawlyn_

- Fix that building record with assigning multiple has_one associations wrongly
  persists through record.

    Fixes #32511.

    _Sam DeCesare_

- Fix relation merging when one of the relations is going to skip the query
  cache.

    _James Williams_

## Rails 5.2.0 (April 09, 2018)

- MySQL: Support mysql2 0.5.x.

    _Aaron Stone_

- Apply time column precision on assignment.

    PR #20317 changed the behavior of datetime columns so that when they have a
    specified precision then on assignment the value is rounded to that
    precision. This behavior is now applied to time columns as well.

    Fixes #30301.

    _Andrew White_

- Normalize time column values for SQLite database.

    For legacy reasons, time columns in SQLite are stored as full datetimes
    because until #24542 the quoting for time columns didn't remove the date
    component. To ensure that values are consistent we now normalize the date
    component to 2001-01-01 on reading and writing.

    _Andrew White_

- Ensure that the date component is removed when quoting times.

    PR #24542 altered the quoting for time columns so that the date component
    was removed however it only removed it when it was 2001-01-01. Now the date
    component is removed irrespective of what the date is.

    _Andrew White_

- Fix `dependent: :destroy` issue for has_one/belongs_to relationship where the
  parent class was getting deleted when the child was not.

    Fixes #32022.

    _Fernando Gorodscy_

- Whitelist `NULLS FIRST` and `NULLS LAST` in order clauses too.

    _Xavier Noria_

- Fix that after commit callbacks on update does not triggered when optimistic
  locking is enabled.

    _Ryuta Kamizono_

- Fix `#columns_for_distinct` of MySQL and PostgreSQL to make
  `ActiveRecord::FinderMethods#limited_ids_for` use correct primary key values
  even if `ORDER BY` columns include other table's primary key.

    Fixes #28364.

    _Takumi Kagiyama_

- Make `reflection.klass` raise if `polymorphic?` not to be misused.

    Fixes #31876.

    _Ryuta Kamizono_

- PostgreSQL: Allow pg-1.0 gem to be used with Active Record.

    _Lars Kanis_

- Deprecate `expand_hash_conditions_for_aggregates` without replacement. Using a
  `Relation` for performing queries is the prefered API.

    _Ryuta Kamizono_

- Fix not expanded problem when passing an Array object as argument to the where
  method using `composed_of` column.

    ```
    david_balance = customers(:david).balance
    Customer.where(balance: [david_balance]).to_sql

    # Before: WHERE `customers`.`balance` = NULL
    # After : WHERE `customers`.`balance` = 50
    ```

    Fixes #31723.

    _Yutaro Kanagawa_

- Fix `count(:all)` with eager loading and having an order other than the
  driving table.

    Fixes #31783.

    _Ryuta Kamizono_

- Clear the transaction state when an Active Record object is duped.

    Fixes #31670.

    _Yuriy Ustushenko_

- Support for PostgreSQL foreign tables.

    _fatkodima_

- Fix relation merger issue with `left_outer_joins`.

    _Mehmet Emin İNAÇ_

- Don't allow destroyed object mutation after `save` or `save!` is called.

    _Ryuta Kamizono_

- Take into account association conditions when deleting through records.

    Fixes #18424.

    _Piotr Jakubowski_

- Fix nested `has_many :through` associations on unpersisted parent instances.

    For example, if you have

        class Post < ActiveRecord::Base
          belongs_to :author
          has_many :books, through: :author
          has_many :subscriptions, through: :books
        end

        class Author < ActiveRecord::Base
          has_one :post
          has_many :books
          has_many :subscriptions, through: :books
        end

        class Book < ActiveRecord::Base
          belongs_to :author
          has_many :subscriptions
        end

        class Subscription < ActiveRecord::Base
          belongs_to :book
        end

    Before:

    If `post` is not persisted, then `post.subscriptions` will be empty.

    After:

    If `post` is not persisted, then `post.subscriptions` can be set and used
    just like it would if `post` were persisted.

    Fixes #16313.

    _Zoltan Kiss_

- Fixed inconsistency with `first(n)` when used with `limit()`. The `first(n)`
  finder now respects the `limit()`, making it consistent with
  `relation.to_a.first(n)`, and also with the behavior of `last(n)`.

    Fixes #23979.

    _Brian Christian_

- Use `count(:all)` in `HasManyAssociation#count_records` to prevent invalid SQL
  queries for association counting.

    _Klas Eskilson_

- Fix to invoke callbacks when using `update_attribute`.

    _Mike Busch_

- Fix `count(:all)` to correctly work `distinct` with custom SELECT list.

    _Ryuta Kamizono_

- Using subselect for `delete_all` with `limit` or `offset`.

    _Ryuta Kamizono_

- Undefine attribute methods on descendants when resetting column information.

    _Chris Salzberg_

- Log database query callers.

    Add `verbose_query_logs` configuration option to display the caller of
    database queries in the log to facilitate N+1 query resolution and other
    debugging.

    Enabled in development only for new and upgraded applications. Not
    recommended for use in the production environment since it relies on Ruby's
    `Kernel#caller_locations` which is fairly slow.

    _Olivier Lacan_

- Fix conflicts `counter_cache` with `touch: true` by optimistic locking.

    ```
    # create_table :posts do |t|
    #   t.integer :comments_count, default: 0
    #   t.integer :lock_version
    #   t.timestamps
    # end
    class Post < ApplicationRecord
    end

    # create_table :comments do |t|
    #   t.belongs_to :post
    # end
    class Comment < ApplicationRecord
      belongs_to :post, touch: true, counter_cache: true
    end
    ```

    Before:

    ```
    post = Post.create!
    # => begin transaction
         INSERT INTO "posts" ("created_at", "updated_at", "lock_version")
         VALUES ("2017-12-11 21:27:11.387397", "2017-12-11 21:27:11.387397", 0)
         commit transaction

    comment = Comment.create!(post: post)
    # => begin transaction
         INSERT INTO "comments" ("post_id") VALUES (1)

         UPDATE "posts" SET "comments_count" = COALESCE("comments_count", 0) + 1,
         "lock_version" = COALESCE("lock_version", 0) + 1 WHERE "posts"."id" = 1

         UPDATE "posts" SET "updated_at" = '2017-12-11 21:27:11.398330',
         "lock_version" = 1 WHERE "posts"."id" = 1 AND "posts"."lock_version" = 0
         rollback transaction
    # => ActiveRecord::StaleObjectError: Attempted to touch a stale object: Post.

    Comment.take.destroy!
    # => begin transaction
         DELETE FROM "comments" WHERE "comments"."id" = 1

         UPDATE "posts" SET "comments_count" = COALESCE("comments_count", 0) - 1,
         "lock_version" = COALESCE("lock_version", 0) + 1 WHERE "posts"."id" = 1

         UPDATE "posts" SET "updated_at" = '2017-12-11 21:42:47.785901',
         "lock_version" = 1 WHERE "posts"."id" = 1 AND "posts"."lock_version" = 0
         rollback transaction
    # => ActiveRecord::StaleObjectError: Attempted to touch a stale object: Post.
    ```

    After:

    ```
    post = Post.create!
    # => begin transaction
         INSERT INTO "posts" ("created_at", "updated_at", "lock_version")
         VALUES ("2017-12-11 21:27:11.387397", "2017-12-11 21:27:11.387397", 0)
         commit transaction

    comment = Comment.create!(post: post)
    # => begin transaction
         INSERT INTO "comments" ("post_id") VALUES (1)

         UPDATE "posts" SET "comments_count" = COALESCE("comments_count", 0) + 1,
         "lock_version" = COALESCE("lock_version", 0) + 1,
         "updated_at" = '2017-12-11 21:37:09.802642' WHERE "posts"."id" = 1
         commit transaction

    comment.destroy!
    # => begin transaction
         DELETE FROM "comments" WHERE "comments"."id" = 1

         UPDATE "posts" SET "comments_count" = COALESCE("comments_count", 0) - 1,
         "lock_version" = COALESCE("lock_version", 0) + 1,
         "updated_at" = '2017-12-11 21:39:02.685520' WHERE "posts"."id" = 1
         commit transaction
    ```

    Fixes #31199.

    _bogdanvlviv_

- Add support for PostgreSQL operator classes to `add_index`.

    Example:

        add_index :users, :name, using: :gist, opclass: { name: :gist_trgm_ops }

    _Greg Navis_

- Don't allow scopes to be defined which conflict with instance methods on
  `Relation`.

    Fixes #31120.

    _kinnrot_

- Add new error class `QueryCanceled` which will be raised when canceling
  statement due to user request.

    _Ryuta Kamizono_

- Add `#up_only` to database migrations for code that is only relevant when
  migrating up, e.g. populating a new column.

    _Rich Daley_

- Require raw SQL fragments to be explicitly marked when used in relation query
  methods.

    Before:

    ```
    Article.order("LENGTH(title)")
    ```

    After:

    ```
    Article.order(Arel.sql("LENGTH(title)"))
    ```

    This prevents SQL injection if applications use the [strongly discouraged]
    form `Article.order(params[:my_order])`, under the mistaken belief that only
    column names will be accepted.

    Raw SQL strings will now cause a deprecation warning, which will become an
    UnknownAttributeReference error in Rails 6.0. Applications can opt in to the
    future behavior by setting `allow_unsafe_raw_sql` to `:disabled`.

    Common and judged-safe string values (such as simple column references) are
    unaffected:

    ```
    Article.order("title DESC")
    ```

    _Ben Toews_

- `update_all` will now pass its values to `Type#cast` before passing them to
  `Type#serialize`. This means that `update_all(foo: 'true')` will properly
  persist a boolean.

    _Sean Griffin_

- Add new error class `StatementTimeout` which will be raised when statement
  timeout exceeded.

    _Ryuta Kamizono_

- Fix `bin/rails db:migrate` with specified `VERSION`. `bin/rails db:migrate`
  with empty VERSION behaves as without `VERSION`. Check a format of `VERSION`:
  Allow a migration version number or name of a migration file. Raise error if
  format of `VERSION` is invalid. Raise error if target migration doesn't exist.

    _bogdanvlviv_

- Fixed a bug where column orders for an index weren't written to `db/schema.rb`
  when using the sqlite adapter.

    Fixes #30902.

    _Paul Kuruvilla_

- Remove deprecated method `#sanitize_conditions`.

    _Rafael Mendonça França_

- Remove deprecated method `#scope_chain`.

    _Rafael Mendonça França_

- Remove deprecated configuration `.error_on_ignored_order_or_limit`.

    _Rafael Mendonça França_

- Remove deprecated arguments from `#verify!`.

    _Rafael Mendonça França_

- Remove deprecated argument `name` from `#indexes`.

    _Rafael Mendonça França_

- Remove deprecated method
  `ActiveRecord::Migrator.schema_migrations_table_name`.

    _Rafael Mendonça França_

- Remove deprecated method `supports_primary_key?`.

    _Rafael Mendonça França_

- Remove deprecated method `supports_migrations?`.

    _Rafael Mendonça França_

- Remove deprecated methods `initialize_schema_migrations_table` and
  `initialize_internal_metadata_table`.

    _Rafael Mendonça França_

- Raises when calling `lock!` in a dirty record.

    _Rafael Mendonça França_

- Remove deprecated support to passing a class to `:class_name` on associations.

    _Rafael Mendonça França_

- Remove deprecated argument `default` from `index_name_exists?`.

    _Rafael Mendonça França_

- Remove deprecated support to `quoted_id` when typecasting an Active Record
  object.

    _Rafael Mendonça França_

- Fix `bin/rails db:setup` and `bin/rails db:test:prepare` create wrong
  ar_internal_metadata's data for a test database.

    Before:

    ```
    $ RAILS_ENV=test rails dbconsole
    > SELECT * FROM ar_internal_metadata;
    key|value|created_at|updated_at
    environment|development|2017-09-11 23:14:10.815679|2017-09-11 23:14:10.815679
    ```

    After:

    ```
    $ RAILS_ENV=test rails dbconsole
    > SELECT * FROM ar_internal_metadata;
    key|value|created_at|updated_at
    environment|test|2017-09-11 23:14:10.815679|2017-09-11 23:14:10.815679
    ```

    Fixes #26731.

    _bogdanvlviv_

- Fix longer sequence name detection for serial columns.

    Fixes #28332.

    _Ryuta Kamizono_

- MySQL: Don't lose `auto_increment: true` in the `db/schema.rb`.

    Fixes #30894.

    _Ryuta Kamizono_

- Fix `COUNT(DISTINCT ...)` for `GROUP BY` with `ORDER BY` and `LIMIT`.

    Fixes #30886.

    _Ryuta Kamizono_

- PostgreSQL `tsrange` now preserves subsecond precision.

    PostgreSQL 9.1+ introduced range types, and Rails added support for using
    this datatype in Active Record. However, the serialization of
    `PostgreSQL::OID::Range` was incomplete, because it did not properly cast
    the bounds that make up the range. This led to subseconds being dropped in
    SQL commands:

    Before:

        connection.type_cast(tsrange.serialize(range_value))
        # => "[2010-01-01 13:30:00 UTC,2011-02-02 19:30:00 UTC)"

    Now:

        connection.type_cast(tsrange.serialize(range_value))
        # => "[2010-01-01 13:30:00.670277,2011-02-02 19:30:00.745125)"

    _Thomas Cannon_

- Passing a `Set` to `Relation#where` now behaves the same as passing an array.

    _Sean Griffin_

- Use given algorithm while removing index from database.

    Fixes #24190.

    _Mehmet Emin İNAÇ_

- Update payload names for `sql.active_record` instrumentation to be more
  descriptive.

    Fixes #30586.

    _Jeremy Green_

- Add new error class `LockWaitTimeout` which will be raised when lock wait
  timeout exceeded.

    _Gabriel Courtemanche_

- Remove deprecated `#migration_keys`.

    _Ryuta Kamizono_

- Automatically guess the inverse associations for STI.

    _Yuichiro Kaneko_

- Ensure `sum` honors `distinct` on `has_many :through` associations.

    Fixes #16791.

    _Aaron Wortham_

- Add `binary` fixture helper method.

    _Atsushi Yoshida_

- When using `Relation#or`, extract the common conditions and put them before
  the OR condition.

    _Maxime Handfield Lapointe_

- `Relation#or` now accepts two relations who have different values for
  `references` only, as `references` can be implicitly called by `where`.

    Fixes #29411.

    _Sean Griffin_

- `ApplicationRecord` is no longer generated when generating models. If you need
  to generate it, it can be created with `rails g application_record`.

    _Lisa Ugray_

- Fix `COUNT(DISTINCT ...)` with `ORDER BY` and `LIMIT` to keep the existing
  select list.

    _Ryuta Kamizono_

- When a `has_one` association is destroyed by `dependent: destroy`,
  `destroyed_by_association` will now be set to the reflection, matching the
  behaviour of `has_many` associations.

    _Lisa Ugray_

- Fix `unscoped(where: [columns])` removing the wrong bind values.

    When the `where` is called on a relation after a `or`, unscoping the column
    of that later `where` removed bind values used by the `or` instead.
    (possibly other cases too)

    ```
    Post.where(id: 1).or(Post.where(id: 2)).where(foo: 3).unscope(where: :foo).to_sql
    # Currently:
    #     SELECT "posts".* FROM "posts" WHERE ("posts"."id" = 2 OR "posts"."id" = 3)
    # With fix:
    #     SELECT "posts".* FROM "posts" WHERE ("posts"."id" = 1 OR "posts"."id" = 2)
    ```

    _Maxime Handfield Lapointe_

- Values constructed using multi-parameter assignment will now use the
  post-type-cast value for rendering in single-field form inputs.

    _Sean Griffin_

- `Relation#joins` is no longer affected by the target model's `current_scope`,
  with the exception of `unscoped`.

    Fixes #29338.

    _Sean Griffin_

- Change sqlite3 boolean serialization to use 1 and 0.

    SQLite natively recognizes 1 and 0 as true and false, but does not natively
    recognize 't' and 'f' as was previously serialized.

    This change in serialization requires a migration of stored boolean data for
    SQLite databases, so it's implemented behind a configuration flag whose
    default false value is deprecated.

    _Lisa Ugray_

- Skip query caching when working with batches of records (`find_each`,
  `find_in_batches`, `in_batches`).

    Previously, records would be fetched in batches, but all records would be
    retained in memory until the end of the request or job.

    _Eugene Kenny_

- Prevent errors raised by `sql.active_record` notification subscribers from
  being converted into `ActiveRecord::StatementInvalid` exceptions.

    _Dennis Taylor_

- Fix eager loading/preloading association with scope including joins.

    Fixes #28324.

    _Ryuta Kamizono_

- Fix transactions to apply state to child transactions.

    Previously, if you had a nested transaction and the outer transaction was
    rolledback, the record from the inner transaction would still be marked as
    persisted.

    This change fixes that by applying the state of the parent transaction to
    the child transaction when the parent transaction is rolledback. This will
    correctly mark records from the inner transaction as not persisted.

    _Eileen M. Uchitelle_, _Aaron Patterson_

- Deprecate `set_state` method in `TransactionState`.

    Deprecated the `set_state` method in favor of setting the state via specific
    methods. If you need to mark the state of the transaction you can now use
    `rollback!`, `commit!` or `nullify!` instead of `set_state(:rolledback)`,
    `set_state(:committed)`, or `set_state(nil)`.

    _Eileen M. Uchitelle_, _Aaron Patterson_

- Deprecate delegating to `arel` in `Relation`.

    _Ryuta Kamizono_

- Query cache was unavailable when entering the `ActiveRecord::Base.cache` block
  without being connected.

    _Tsukasa Oishi_

- Previously, when building records using a `has_many :through` association, if
  the child records were deleted before the parent was saved, they would still
  be persisted. Now, if child records are deleted before the parent is saved on
  a `has_many :through` association, the child records will not be persisted.

    _Tobias Kraze_

- Merging two relations representing nested joins no longer transforms the joins
  of the merged relation into LEFT OUTER JOIN.

    Example:

    ```
    Author.joins(:posts).merge(Post.joins(:comments))
    # Before the change:
    #=> SELECT ... FROM authors INNER JOIN posts ON ... LEFT OUTER JOIN comments ON...

    # After the change:
    #=> SELECT ... FROM authors INNER JOIN posts ON ... INNER JOIN comments ON...
    ```

    _Maxime Handfield Lapointe_

- `ActiveRecord::Persistence#touch` does not work well when optimistic locking
  enabled and `locking_column`, without default value, is null in the database.

    _bogdanvlviv_

- Fix destroying existing object does not work well when optimistic locking
  enabled and `locking_column` is null in the database.

    _bogdanvlviv_

- Use bulk INSERT to insert fixtures for better performance.

    _Kir Shatrov_

- Prevent creation of bind param if casted value is nil.

    _Ryuta Kamizono_

- Deprecate passing arguments and block at the same time to `count` and `sum` in
  `ActiveRecord::Calculations`.

    _Ryuta Kamizono_

- Loading model schema from database is now thread-safe.

    Fixes #28589.

    _Vikrant Chaudhary_, _David Abdemoulaie_

- Add `ActiveRecord::Base#cache_version` to support recyclable cache keys via
  the new versioned entries in `ActiveSupport::Cache`. This also means that
  `ActiveRecord::Base#cache_key` will now return a stable key that does not
  include a timestamp any more.

    NOTE: This feature is turned off by default, and `#cache_key` will still
    return cache keys with timestamps until you set
    `ActiveRecord::Base.cache_versioning = true`. That's the setting for all new
    apps on Rails 5.2+

    _DHH_

- Respect `SchemaDumper.ignore_tables` in rake tasks for databases structure
  dump.

    _Rusty Geldmacher_, _Guillermo Iguaran_

- Add type caster to `RuntimeReflection#alias_name`.

    Fixes #28959.

    _Jon Moss_

- Deprecate `supports_statement_cache?`.

    _Ryuta Kamizono_

- Raise error `UnknownMigrationVersionError` on the movement of migrations when
  the current migration does not exist.

    _bogdanvlviv_

- Fix `bin/rails db:forward` first migration.

    _bogdanvlviv_

- Support Descending Indexes for MySQL.

    MySQL 8.0.1 and higher supports descending indexes: `DESC` in an index
    definition is no longer ignored. See
    https://dev.mysql.com/doc/refman/8.0/en/descending-indexes.html.

    _Ryuta Kamizono_

- Fix inconsistency with changed attributes when overriding Active Record
  attribute reader.

    _bogdanvlviv_

- When calling the dynamic fixture accessor method with no arguments, it now
  returns all fixtures of this type. Previously this method always returned an
  empty array.

    _Kevin McPhillips_

Please check
[5-1-stable](https://github.com/rails/rails/blob/5-1-stable/activerecord/CHANGELOG.md)
for previous changes.
