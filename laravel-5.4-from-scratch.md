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
});
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
	@foreach ($tasks as $task)
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
	return view('welcome', compact('admins'));
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
* Eloquent is Laravel's active record implementation.
* `php artisan make:model Admin` to create a model.
* `php artisan tinker` allows you to tinker/debug/troubleshoot from the shell.
	* `App\Admin::all();` to get all Admin records.
	* `App\Admin::where('id', '>', 1)->get();` to get Admins with an `id` greater than 1.
	* `App\Admin::pluck('username')->first();` grab the first username.
* Example passing database query into a route using a model:
```php
Route::get('/admins', function() {
	$admins = App\Admin::all();
	return view('welcome', compact('admins'));
});
```
* Can add additional behavior to a model by creating functions in the model file:
```php
public function isComplete() {
	return static::where('completed', 1)->get();
}
public function incomplete() {
	return static::where('completed', 0)->get();
}
```
* Useful to reference the namespaced model so it doesn't have to have `App\` prepended to it.
```php
use App\Admin;
//...
$admins = Admin::all();
```
* When creating a model, use `-m` to create a migration for it as well. `php artisan make:model Admin -m`. Can use `-c` to create a controller for it, too.

## Lesson 8 - Controllers
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
* Example of using in a route:
```php
Route::get('/admins', 'AdminsController@index');
Route::get('/admins/{$id}', 'AdminsController@show');
```

## Lesson 9 - Route Model Binding
* Instead of specifying an `id` in the show method, you can use a model. This is route-model binding.
* Route code is changed to:
```php
Route::get('/admins/{$admin}', 'AdminsController@show');
```
* Controller code is simplified to:
```php
public function show(Admin $admin) {
	return view('admin.show', compact('admin'));
}
```

## Lesson 10 - Layouts and Structure
* Use layouts to avoid having to write repetitive markup for every view.
* `@yield ('content')` in a layout will allow you to inject anything into that section.
* `@extends ('layout')` in a view will use the layout then wrap the content in `@section ('content')` and `@endsection`.
```php
@section ('content')
<h1> content! </h1>
@endsection
```
* Keep things tidy by extracting specific elements into partials files and then including them: `@include ('partials.nav')`

## Lesson 11 - Form Request Data and CSRF
* Basic structure of a resource controller:
```
Show all posts
GET /posts

Show create post form
GET /posts/create

Store a post
POST /posts

Show edit post form
GET /posts/{id}/edit

Show a single post
GET /posts/{id}

Submit and update to a post
PATCH /posts/{id}

Delete a post
DELETE /posts/{id}
```
* `php artisan make:controller PostsController -r` will create a "resourceful" controller with boilerplate for the various methods.
* Laravel protects against Cross-site Request Forgery out of the box. Automatically generates a token for every request. Obfuscates a bunch of security concerns that would otherwise have to be addressed if it weren't handled by the framework.
* `store()` a post example:
```php
Post::create({
	'title' => request('title'),
	'body'  => request('body')
});

//... or ...

Post::create(request(['title', 'body']));
redirect('/');
```
* The model must have the `fillable` field to configured.
```php
class Post extends Model {
	protected $fillable = ['title', 'body'];
}
```
* `guarded` is the inverse of `fillable`. It'll act as a filter that prevents anything specified from being written, while allowing everything else through. Setting it an empty array would allow everything through.
```php
	protected $guarded = ['user_id'];
```


## Lesson 12 - Form Validation 101
* HTML5 provides built in validation to the browser. Older browsers don't support this.
* Laravel can validate it server-side.
```php
public function store() {
	$this->validate(request(), [
		'title' => 'required|max:20',
		'body'  => 'required'
	]);
}
```
* It will return an array containing the error messages in `$errors`. Array will be empty if there are no errors.
```html
@if (count($errors))
<div class="alert alert-danger">
	<ul>
		@foreach ($errors->all() as $error)
			<li>{{ $error }}</li>
		@endforeach
	</ul>
</div>
@endif
```
* Best to include the error markup as an include and just `@include` it in every view that has validation.

## Lesson 13 - Rendering Posts
* Creating a view for a post allows you to iterate through an array of them and render them.
```php
@foreach ($posts as $post)
	@include (posts.post)
@endforeach
```
* The `posts.post` view uses the $post variables.

## Lesson 14 - Laravel Mix and the Front-end
* Asset pipeline allowing for "mixing" of CSS, JS, SCSS, LESS, etc. 
* Configure this by editing the `webpack.mix.js`
* Run it by using `npm run dev` (or whatever environment you require, defined in `package.json`).
* Enable a watcher by using `npm watch dev`

## Lesson 15 - Eloquent Relationships and Comments
* A comment should belong to a post, so in the Comment migration, add an entry that specifies the post's `id`: `$table->integer('post_id')`.
* Create a method on the Post model that can grab the comments.
```php
public function comments() {
	return $this->hasMany(Comment::class);
}
```
* Create the inverse on the Comment model
```php
public function post() {
	return $this->belongsTo(Post::class);
}
```

## Lesson 16 - Add Comments


## Lesson 17 - Rapid Authentication and Configuration
* `php artisan make:auth` generates the authentication scaffolding. Creates some routes, templates and layout for authentication.
* Middleware runs for every request, i.e. CheckForMaintenanceMode. Authentication check is middleware.

## Lesson 18 - Associating With Users
* `make:auth` command is something that'll almost always be ran at the beginning of a project.
* Passwords should never be stored in clear text (obviously). Run it through something like `bcrypt()` if you're manually entering them via `tinker` or in Sequel Pro. i.e. `bcrypt('secret')`.
* Update the migrations to contain a field that associates it with a specific `user_id`. 
* Update the model to allow grabbing of the user it is associated with:
```php
public function user() {
	return $this->belongsTo(User::class);
}
```

## Lesson 19 - Associating With Users: Part 2
* Creating auth from scratch, generate `SessionsController` and `RegistrationController`. Creating a session would mean a new login.
* 

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