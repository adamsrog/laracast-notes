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

# Lesson 4 - Blade Layout Files
* Specify a section in a layout using `@yield('content')`
* Extend the layout in a view:
```
@extends('layout.app')

@section('content')

@endsection
```
* Another example of a section is one for the title of the page.
```
<title>@yield('title')</title>
```

# Lesson 5 - Sending Data to Your Views
* Passing data through a route example:
```php
Route::get('/', function() {
    $name = 'Roger';
    $age = 32;
    $tasks = ['task 1', 'task 2', 'task 3'];
    return view('welcome', compact('name', 'age', 'tasks'));
});
```
* Using the data in a view example:
```php
<ul>
    @foreach ($tasks as $task)
    <li>{{ $task }}</li>
    @endforeach
</ul>
```
* Can get stuff from query strings too. For example, `localhost:8000/?title=test` would pass this into the route.
```php
Route::get('/', function() {
    $title = request('title');
    return view('welcome', ['title' => $title])
})
```
* Mustache syntax `{{ $foo }}` escapes characters to protect, i.e. you can't pass in `<script>alert('WHOA');</script>`, it'll use escape codes to actually display that rather than actually injecting the script code.
* Blade can unescape by using `{!! $foo !!}`.
* Laravel has magic methods for passing data into the views from the route using `->withXXX()`:
```php
Route::get('/', function() {
    $title = request('title');
    $foo = 'foo';
    return view('welcome')->withTitle($title)->withFoo($foo)->withBar('bar');
})
```

# Lesson 6 - Controllers 101
* Controllers are the "middle-man" between a route and view. Keeps the route file cleaner, especially for big projects.
* `php artisan make:controller AdminsController` to create a new controller.
* In the resulting controller file, you create new methods that will be called from a route:
```php
public function index() {
    $admins = Admin::all();
    return view('admins', compact('admins'));
}
public function show($id) {
    $admin = Admin::find($id);
    return view('admin.show', compact('admin'));
}
```
* Example of calling a controller for a route:
```php
Route::get('/admins', 'AdminsController@index');
Route::get('/admins/{$id}', 'AdminsController@show');
```