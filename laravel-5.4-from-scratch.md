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
```php
Route::get('/', function() {
	return view('welcome');
})
```
* Laravel uses the Blade templating engine. Much more convenient that templating with plain PHP.

## Lesson 3 - Laravel Valet is Your Best Friend
* Laravel Valet is an easy way to setup a local dev server. Specifically for Mac OS X.
* Not going to use this due to a preference for Laravel Homestead.

## Lesson 4 - Database Setup and Sequel Pro
* Laravel uses MySQL by default, but can integrate with other databases.
* `.env` stores your environment variables (dev, prod, etc). Database variables belong here.
* `php artisan migrate` will setup the database schema from the `database/migrations/` files.
* Sequel Pro works well on OS X if you want a GUI to manage the MySQL database.

## Lesson 5 - Pass Data to Your Views
* Passing data through a route example:
```php
Route::get('/', function() {
	$name = 'Roger';
	$age = 32;
	$tasks ['task 1', 'task 2', 'task 3'];
	return view('welcome', compact('name', 'age', 'tasks'));
});
```
* Using the data in a view example:
```php
<ul>
	@foreach $tasks as $task
	<li>{{ $task }}</li>
	@endforeach
</ul>
```

## Lesson 6 - Working With the Query Builder
* Laravel comes with a User table migration out of the box.
* `php artisan make:` will generate different types of files for Laravel.
* `php artisan make:migration create_admins_table --create=admins` will create a migration that sets up a table `admins`.
```php
Schema::create('admins', function (Blueprint $table) {
	$table->increments('id');
	$table->string('username')->unique();
	$table->string('email')->unique();
	$table->string('password');
	$table->string('fname');
	$table->string('lname');
	$table->timestamps();
});
```
* Run the migration by using `php artisan migrate`
* Reset and re-run all migrations `php artisan migrate:refresh`
* Example of passing database query into a route
```php
Route::get('/', function() {
	$admins = DB::table('admins')->get();
	return view('welcome', compact('tasks'));
});
```
* Can also just return the query itself, and it'll cast it to `json`:
```php
Route::get('/', function() {
	$admins = DB::table('admins')->get();
	return $admins;
});
```

## Lesson 7 - Eloquent 101


## Lesson 8 - Controllers


## Lesson 9 - Route Model Binding


## Lesson 10 - Layouts and Structure


## Lesson 11 - Form Request Data and CSRF


## Lesson 12 - Form Validation 101


## Lesson 13 - Rendering Posts


## Lesson 14 - Laravel Mix and the Front-end


## Lesson 15 - Eloquent Relationships and Comments


## Lesson 16 - Add Comments


## Lesson 17 - Rapid Authentication and Configuration


## Lesson 18 - Associating With Users


## Lesson 19 - Associating With Users: Part 2


## Lesson 20 - Archives


## Lesson 21 - View Composers


## Lesson 22 - Testing 101


## Lesson 23 - DI, Auto-Resolution, and Repositories


## Lesson 24 - The Service Container


## Lesson 25 - Service Providers Explained


## Lesson 26 - Sending Email


## Lesson 27 - Markdown Mail


## Lesson 28 - Form Requests and Form Objects


## Lesson 29 - Session Handling and Flash Messaging


## Lesson 30 - Tags and Pivot Tables


## Lesson 31 - Sorting Posts By Tags


## Lesson 32 - Eventing