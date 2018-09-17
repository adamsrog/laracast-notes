# Laravel 5.4 from Scratch
https://laracasts.com/series/laravel-from-scratch-2017

## Lesson 1 - Laravel Installation and Composer
* Composer is used as the dependency manager for PHP. (https://getcomposer.org)
* PHP package repository available at https://packagist.org
* Be sure to update `PATH` in your shell to make composer accessbile globally.
* `laravel new project` to get started on a new project. Will install all the dependencies.
* `laravel new project --dev` would do the same, but with the development branch.

## Lesson 2 - Basic Routing and Views
* `php artisan serve` would boot up a basic development server to run your app in the browser.
* Define a route that tells the your app to load the welcome view when a user goes to `/`.
```
Route::get('/', function() {
	return view('welcome');
})
```
* Laravel uses the Blade templating engine. Much more convenient that templating with plain PHP.

## Lesson 3 - Laravel Valet is Your Best Friend
* Laravel Valet is an easy way to setup a local dev server. Specifically for Mac OS X.
* Not going to use this due to a preference for Laravel Homestead.