# Pre-requisites

Before you install Pixelfed, you will need to setup a webserver with the required dependencies.

[[toc]]

## HTTP Web server
The following web servers are officially supported:
- Apache
- nginx

Pixelfed uses HTTPS URIs, so you will need to have HTTPS set up at the perimeter of your network before you proxy requests internally.

## Database

You can choose one of three supported database drivers:
- MySQL (5.7+)
- MariaDB (10.2.7+ -- 10.3.5+ recommended)
- PostgreSQL (10+)

::: warning A note on using PostgreSQL:
PostgreSQL support is not primary -- there may be Postgre-specific bugs within Pixelfed. If you encounter any issues while running Postgre as a database, please file those issues on our [Github tracker](https://github.com/pixelfed/pixelfed/issues).
:::

You will need to create a database and grant permission to an SQL user identified by a password. To do this with MySQL or MariaDB, do the following:

```bash
$ sudo mysql -u root -p
```

You can then create a database and grant privileges to your SQL user. The following SQL commands will create a database named `pixelfed` and allow it to be managed by a user `pixelfed` with password `strong_password`:

```sql{1,2}
create database pixelfed;
grant all privileges on pixelfed.* to 'pixelfed'@'localhost' identified by 'strong_password';
flush privileges;
```

::: tip Changing database drivers:
If you decide to change database drivers later, please run a backup first! You can do this with `php artisan backup:run --only-db`
:::

## PHP

You can check your currently installed version of PHP by running `php -v`. Make sure you are running **PHP >= 7.3**.

You can check your currently loaded extensions by running `php -m`. Modules are usually enabled by editing `/etc/php/php.ini` and uncommenting the appropriate line under the "Dynamic extensions" section. Make sure the following extensions are loaded (extensions generally not loaded by default will be marked with an asterisk):
- `bcmath` *
- `ctype`
- `curl`
- `iconv` *
- `intl` *
- `json`
- `mbstring`
- `openssl`
- `tokenizer`
- `xml`

Additionally, you will need to enable extensions for database drivers.
- For MySQL or MariaDB: enable `pdo_mysql` and `mysqli`
- For PostgreSQL: enable `pdo_pgsql` and `pgsql`

::: danger A note about php-redis vs. predis:
Make sure you do NOT have the `redis` PHP extension installed/enabled! Pixelfed uses the [predis](https://github.com/nrk/predis) library internally, so the presence of any Redis extensions can cause issues.
:::

Finally, make sure to set the desired upload limits for your PHP processes. You will want to check the following:
- `post_max_size` (default 8M, set this around or slightly greater than your desired post size limit)
- `file_uploads` (default On, which it needs to be)
- `upload_max_filesize` (default 2M, set this <= `post_max_size`)
- `max_file_uploads` (default 20, but make sure it is >= your desired attachment limit)
- `max_execution_time` (default 30, consider raising this to 600 or more so that longer tasks aren't interrupted)

## External programs
- [Composer](https://getcomposer.org/) (PHP dependency manager)
- Git (for fetching updates)
- Redis (in-memory caching)
- GD or ImageMagick (image processing)
- [JPEGOptim](https://github.com/tjko/jpegoptim)
- [OptiPNG](http://optipng.sourceforge.net/)
- [PNGQuant](https://pngquant.org/)