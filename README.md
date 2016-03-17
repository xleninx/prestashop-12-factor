# Twelve-Factor Prestashop

Prestashop, the [Twelve-Factor](http://12factor.net/) way: fully managed using Composer and configured using environment variables. This has been heavily inspired by [Wordpress 12 Factor](https://github.com/dzuelke/wordpress-12factor)

## General Concepts and Considerations

The Prestashop installation is fully contained in a `store` subfolder upon Heroku deployment. A `settings.inc.php` resides in the root of the project, and uses several different environment variables to control behavior.

The configuration file is kept as generic as possible; on Heroku, add-ons [JawsDB](https://elements.heroku.com/addons/jawsdb) (for MySQL), [Bucketeer](https://elements.heroku.com/addons/bucketeer) (for S3 storage), and [SendGrid](https://elements.heroku.com/addons/sendgrid) (for E-Mails) are used.

The assumption is that this installation runs behind a load balancer whose `X-Forwarded-Proto` header value can be trusted; it is used to determine whether the request protocol is HTTPS or not.

## Quick Deploy

If you have a [Heroku](http://heroku.com) account, you may simply use the following button to deploy this application:

[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy)

After the deploy, in [Heroku's Dashboard](https://dasboard.heroku.com) under "Settings" for your deployed application, **remove the `PRESTASHOP_ADMIN_*` environment variables**.

## Manual Deploy

### Clone

Clone this repo:

```
$ git clone https://github.com/absalomedia/prestashop-12-factor
$ cd prestashop-12-factor
```

If you like, you can locally install dependencies with [Composer](https://getcomposer.org):

```
$ composer install
```

### Create Application and Add-Ons

Create a new app and add add-ons for MySQL, S3 and E-Mail:

```
$ heroku create
$ heroku addons:create jawsdb
$ heroku addons:create bucketeer
$ heroku addons:create sendgrid
```

### Deploy

```
$ git push heroku master
```

### Finalize Installation

This will create tables and set up an admin user:

```
$ heroku run 'composer prestashop-db-core --name='Prestashop on Heroku' --domain=example.herokuapp.com --db_server=localhost --db_name=prestashop --db_user=admin --db_password=admin --email=admin@example.com '
```

### Visit ecommerce store

Navigate to the application's URL, or open your browser the lazy way:

```
$ heroku open
```

## Updating Prestashop and Plugins

To update all dependencies:

```
$ composer update
```


## Environment Variables

`settings.inc.php` will use the following environment variables (if multiple are listed, in order of precedence):

### Database Connection

`DATABASE_URL` or `JAWSDB_URL` or `CLEARDB_DATABASE_URL` (format `mysql://user:pass@host:port/dbname`) for database connections.

### AWS/S3

* `AWS_ACCESS_KEY_ID` or `BUCKETEER_AWS_ACCESS_KEY_ID` for the AWS Access Key ID;
* `AWS_SECRET_ACCESS_KEY` `BUCKETEER_AWS_SECRET_ACCESS_KEY` for the AWS Secret Access Key;
* `S3_BUCKET` or `BUCKETEER_BUCKET_NAME` for the name of the S3 bucket;
* `S3_REGION` for a non-default S3 region name.

### SendGrid

`SENDGRID_USERNAME` and `SENDGRID_PASSWORD` for SendGrind credentials.
