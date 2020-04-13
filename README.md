
# MagicLink for Laravels App

Through the `MagicLink` class we can create a secure link that later
being visited will perform certain actions, which will allow us
offer secure content and even log in to the application.

[![Latest Version on Packagist](https://img.shields.io/packagist/v/cesargb/laravel-magiclink.svg?style=flat-square)](https://packagist.org/packages/cesargb/laravel-magiclink)
[![Build Status](https://travis-ci.org/cesargb/laravel-magiclink.svg?branch=2.x)](https://travis-ci.org/cesargb/laravel-magiclink)
[![StyleCI](https://github.styleci.io/repos/98337902/shield)](https://github.styleci.io/repos/98337902)
[![Total Downloads](https://img.shields.io/packagist/dt/cesargb/laravel-magiclink.svg?style=flat-square)](https://packagist.org/packages/cesargb/laravel-magiclink)

## Installation

You can install this package via composer using:

```bash
composer require cesargb/laravel-magiclink
```

You can publish migration with command:

```bash
php artisan vendor:publish --provider="MagicLink\MagicLinkServiceProvider" --tag=migrations
```

After the migration has been published you can create the table by running the
migrations:

```bash
php artisan migrate
```

Note: If you have the version 1 installed,
[read this](https://github.com/cesargb/laravel-magiclink/blob/v1/README.md).

## Use case

With this example you can create a link to auto login on your application with
the desired user:

```php
use MagicLink\Actions\LoginAction;
use MagicLink\MagicLink;

$urlToAutoLogin =  MagicLink::create(new LoginAction($user))->url
```

## Create a MagicLink

The `MagicLink` class has the `create` method to generate a class that through
the `url` property we will obtain the link that we will send to our visitor.

This method requires the action to be performed.

## Actions

Each MagicLink is associated with an action, which is what will be performed
once the link is visited.

* [Login Action](#login-action)
* [Download file Action](#download-file-action)
* [View Action](#view-action)
* [Http Reponse Action](#http-response-action)

### Login Action

Through the `LoginAction` action, you can log in to the application using the generated link by `MagicLink`.

Your constructor supports the user who will login. Optionally we can specify
the [HTTP response](https://laravel.com/docs/master/responses) using the
`$httpResponse` argument and specify the `$guard`.

Examples:

```php
use MagicLink\Actions\LoginAction;
use MagicLink\MagicLink;

// Sample 1; Login and redirect to dash board
$urlToDashBoard = MagicLink::create(
    new LoginAction(User::first(), redirec('/dashboard'))
)->url;

// Sample 2; Login and view forms to password reset and use guard web
$urlShowView = MagicLink::create(
    new LoginAction(
        User::first(),
        view('password.reset', ['email' => 'user@example.tld'])
    )
)->url;

// Sample 3; Login in other guard and redirect default
$urlShowView = MagicLink::create(
    new LoginAction(
        User::first(),
        null,
        'otherguard'
    )
)->url;
```

### Download file Action

This action, `DownloadFileAction`, permit create a link to download a private file.

The constructor require the file path.

Example:

```php
use MagicLink\Actions\DownloadFileAction;
use MagicLink\MagicLink;

// Url to download the file storage_app('private_document.pdf')
$url = MagicLink::create(new DownloadFileAction('private_document.pdf'))->url;
```

### View Action

With the action `ViewAction`, you can provide access to the view. You can use
in his constructor the same arguments than method `view` of Laravel.

Example:

```php
use MagicLink\Actions\ViewAction;
use MagicLink\MagicLink;

// Url to view a internal.blade.php
$url = MagicLink::create(new ViewAction('internal', [
    'data' => 'Your private custom content',
]))->url;
```


### Http Response Action

Through the `ResponseAction` action we can access private content without need
login. Its constructor accepts as argument the
[HTTP response](https://laravel.com/docs/master/responses)
which will be the response of the request.

Examples:

```php
use MagicLink\Actions\ResponseAction;
use MagicLink\MagicLink;

$urlToCustomFunction = MagicLink::create(
    new ResponseAction(function () {
        Auth::login(User::first());

        return redirect('/change_password');
    })
)->url;
```

## MagicLink link lifetime

By default a link will be available for 24 hours after your creation. We can
modify the life time in minutes of the link by the `$lifetime` option
available in the `create` method. This argument accepts the value `null` so
that it does not expire in time.

```php
$lifetime = 60; // 60 minutes

$magiclink = MagicLink::create(new ResponseAction(), $lifetime);

$urlToSend = $magiclink->url;
```

We also have another option `$numMaxVisits`, with which we can define the
number of times the link can be visited, `null` by default indicates that there
are no visit limits.

```php
$lifetime = null; // not expired in the time
$numMaxVisits = 1; // Only can visit one time

$magiclink = MagicLink::create(new ResponseAction(), $lifetime, $numMaxVisits);

$urlToSend = $magiclink->url;
```

## Events

MagicLink fires two events:

* `MagicLink\Events\MagicLinkWasCreated`
* `MagicLink\Events\MagicLinkWasVisited`

## Testing

Run the tests with:

``` bash
composer test
```

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security

If you discover any security-related issues, please email cesargb@gmail.com
instead of using the issue tracker.

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
