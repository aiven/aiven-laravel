# Aiven Commands for Laravel

**✨ Add some Aiven magic to your Laravel project ✨**

This Laravel package provides some `aiven` commands for `artisan` to help with managing your development databases and producing the correct configuration to use with them. This version supports both MySQL and PostgreSQL.

Use the commands to:
* List the Aiven services in your project
* Power your databases on and off from `artisan`, so you don't leave the meter running when you're not working
* Get database config you can paste straight into your `.env` file or environment.

## Getting started

Install the package with [Composer](https://getcomposer.org):

```
composer require aiven/aiven-laravel
```

You will need an Aiven account - [sign up for a free trial](https://console.aiven.io/signup?utm_source=github&utm_medium=aiven-laravel) if you don't have one already. Go ahead and create the database(s) you'll be using in your project through the web interface, or investigate the [Aiven CLI](https://developer.aiven.io/docs/tools/cli).

Get an [auth token](https://developer.aiven.io/docs/platform/howto/create_authentication_token) for your Aiven account, and set it as `AIVEN_API_TOKEN` in your environment.

It's recommended to also set `AIVEN_DEFAULT_PROJECT` as the project in your Aiven account that you'll be using services from (but you can also supply `--project [projectname]` for all the commands instead if you like)

## Usage

Get a list of the Aiven services (databases) available:

```
php artisan aiven:list 
```

Get the environment variables to export or paste into `.env` for a particular service:

```
php artisan aiven:getconfig --service my-postgres-db
```

> When you use the `DATABASE_URL` in Laravel, you must remove the existing `DB_HOST`, `DB_PORT`, etc configuration so that it does not conflict.

Check the status of the service:

```
php artisan aiven:state --service my-postgres-db
```

Power the service on or off:
```
php artisan aiven:powerup --service my-postgres-db
php artisan aiven:powerdown --service my-postgres-db
```

It's useful to power things down when you're not using them so that you aren't being charged (even if you're still paying in trial credits!)

## Datastore-specific setup

### MySQL and PostgreSQL®

PHP is on good terms with relational databases, and Laravel makes this very straight forward.

1. Remove all the configuration entries from `.env` starting with `DB_`

2. Paste the output of the `aiven:getconfig` command. For both MySQL and PostgreSQL, this is a `DATABASE_URL`. For PostgreSQL, we need to tell Laravel we want to use Postgres (MySQL is the default) so the command also outputs `DB_CONNECTION=pgsql` and this should also be included.

### Redis™*

Redis™ needs some extra dependencies (see [Laravel Redis docs](https://laravel.com/docs/8.x/redis)), and then you can set the `REDIS_URL` to the value returned by `aiven:getconfig`.

### OpenSearch® with Laravel Scout

This uses some Elasticsearch libraries because the OpenSearch project is a fork so they are reasonably compatible.

* Use [Laravel Scout](https://laravel.com/docs/8.x/scout)

* Add [Explorer](https://jeroen-g.github.io/Explorer/)

* Pin your PHP elasticsearch library dependencies, I have this in `composer.json`:

```
        "elasticsearch/elasticsearch": ">=7.9 <7.14",
```

The `aiven:getconfig` command will give you just the connection string for an OpenSearch service; put this in the `config/explorer.php` so that it looks something like this:

```
    'connection' => "https://avnadmin:s3cr3t@servicename.aivencloud.com:port",
```

The Scout documentation has a good overview of how to make your models searchable, and the Explorer project adds commands to create the index.

## Troubleshooting

### MySQL primary key errors

Laravel's initial migrations assumes that your MySQL database doesn't enforce primary keys (see [related bug](https://github.com/laravel/framework/issues/33238) for more info) - disable the `mysql.sql_require_primary_key` setting on Aiven if you run into this.

### Connection problems

If you add the config to your environment and still can't connect, check that you don't have other environment variables with names starting `DB_` ... values like `DB_HOST` should be removed when you use the `DATABASE_URL` field to connect.

## Get in touch

Questions? Problems? Open an issue and let us know.

## Trademarks
MySQL, OpenSearch, PostgreSQL and Laravel are trademarks and property of their respective owners. All product and service names used in this website are for identification purposes only and do not imply endorsement.

/* Redis is a trademark of Redis Ltd. Any rights therein are reserved to Redis Ltd. Any use by Aiven Oy is for referential purposes only and does not indicate any sponsorship, endorsement or affiliation between Redis and Aiven Oy.

