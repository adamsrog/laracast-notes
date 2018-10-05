# Laravel 5.7 from Scratch
* Many sections copied from Laravel 5.4 from Scratch
https://laracasts.com/series/laravel-from-scratch-2018

## Lesson 1 - The Laravel Sell
* Many different frameworks out there: Symfony, Cake, Code Ignitor, Zend.
* Laravel is skyrocketing in popularity. Massive community surrounding it.
* Has lots of first-party tooling for deployment and more: Forge, Spark, Nova.

## Lesson 2 - Initial Setup Requirements
* Composer is used as the dependency manager for PHP. (https://getcomposer.org)
* PHP package repository available at https://packagist.org
* Be sure to update `PATH` in your shell to make composer accessbile globally.
* `laravel new project` to get started on a new project. Will install all the dependencies.
* `laravel new project --dev` would do the same, but with the development branch.

## Lesson 3 - Basic Routing
* `php artisan serve` would boot up a basic development server to run your app in the browser.
* Define a route that tells the your app to load the welcome view when a user goes to `/`.
```php
Route::get('/', function() {
	return view('welcome');
});
```
