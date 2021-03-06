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
```html
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
* Create a view with a `textarea` and `button` that allows a user to add a comment. Be sure to include a `{{ csrf_field() }}`, then `POST` it to `/posts/{id}/comment`.
* Should use the `store` method on the `CommentController`
```php
public function store(Post $post) {
	Comment::create([
		'body' => request('body'),
		'post_id' => $post->id
	]);
	return back();
}
```
* Alternatively, you could create a method on the `Post` model that would handle adding a comment, i.e. `addComment()`.
```php
public function addComment() {
	Comment::create([
		'body' => request('body'),
		'post_id' => $this->id
	]);
}
```
* Then the `store()` method would be simplified:
```php
public function store(Post $post) {
	$post->addComment(request('body'));
	return back();
}
```

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
* Can get specific queries using various methods available from Eloquent.
```php
$archives = Post::selectRaw('year(created_at) year, monthname(created_at) month, count(*) published')
	->groupBy('year', 'month')
	->orderBy('min(created_at) desc')
	->get()
	->toArray();
```
* Carbon is useful when dealing with dates. For example, archives example could modify the Post model so it can handle a query scope.
```php
use Carbon\Carbon;

public function scopeFilter($query, $filters) {
	// Use Carbon to turn "May" into month number 5
	if ($month = $filters['month']) {
		$query->whereMonth('created_at', Carbon::parse($month)->month);
	}

	if ($year = $filters['year']) {
		$query->whereYear('created_at', $year);
	}
}
```
* In the `PostsController`, it can be filtered now using this syntax. If the Month and Year are present as URL variables, they'll be filtered accordingly.
```php
public function index() {
	$posts = Post::latest()
		->filter(request(['month', 'year']))
		->get();
}
```

## Lesson 21 - View Composers
* Since the Archives section in the sidebar is present in every page, this would require running the query to get the `$archives` from lesson 20. View composers is the best way to accomplish this.
* Move the archives query code to the model.
```php
public static function archives() {
	return static::selectRaw('year(created_at) year, monthname(created_at) month, count(*) published')
	->groupBy('year', 'month')
	->orderBy('min(created_at) desc')
	->get()
	->toArray();
}
```
* Register a view composer in the `app/Providers/AppServiceProvider.php`.
```php
public function boot() {
	view()->composer('layouts.sidebar', function($view) {
		$view->with('archives', \App\Post::archives());
	});
}
```

## Lesson 22 - Testing 101
* Laravel has `phpunit` installed by default. Can run individual tests by using command `phpunit tests/Feature/ExampleTest.php`.
```php
public function testBasicTest() {
	// load homepage and check for text "My Blog"
	$this->get('/')->assertSee('My Blog');
}
```
* Look into Laravel Desk for more testing, including Javascript.
* Good way to think about testing is: Given, When, Then.
```php
use App\Post;

public function archiveTest() {
	// Given I have two records in the database that are posts
	// and each one is posted a month apart.
	$first = factory(Post::class)->create();
	$second = factory(Post::class)->create([
		'created_at' => \Carbon\Carbon::now()->subMonth()
	]);

	// When I fetch the archives
	$posts = Post::archives();

	// Then the response should be in the proper format
	$this->assertCount(2, $posts);
	$this->assertEquals([
		"year" => $first->created_at->format('Y'),
		"month" => $first->created_at->format('F'),
		"published" => 1
	],
	[
		"year" => $second->created_at->format('Y'),
		"month" => $second->created_at->format('F'),
		"published" => 1
	]);
}
```
* Use environment variables to specify a different database for testing purposes. Can be set in `phpunit.xml`: `<env name="DB_DATABASE" value="blog_testing"/>`. Database will need migrations ran on it before it'll work.
* Use `DatabaseTransactions` so it'll roll back and database entries made during testing. `use DatabaseTransactions;`

## Lesson 23 - DI, Auto-Resolution, and Repositories
* Laravel allows you to create repositories that are used in dependency injections and auto-resolution. This is made possible through Laravel's service container. For example, `app/Repositories/Posts.php`:
```php
<?php

namespace App\Repositories;
use App\Post;

class Posts {
	public function all() {
		return Post::all();
	}
}
```
* Inject them into a controller like so:
```php
use App\Repositories\Posts;

public function index(Posts $posts) {

}
```

## Lesson 24 - The Service Container
* All registered dependencies will be resolved in the service container.
```php
// routes/web.php
App::bind('App\Billing\Stripe', function() {
	return new \App\Billing\Stripe(config(services.stripe.api_key));
});

$stripe = App::make('App\Billing\Stripe');

// app/Billing/Stripe.php
namespace App\Billing;

class Stripe {
	public function __construct($key) {
		$this->key = $key;
	}
}
```
* Can also register singleton using `App::singleton('App\Billing\Stripe')`.

## Lesson 25 - Service Providers Explained
* The Stripe service code put in the `route/web.php` last lesson is better suited for the Providers folder located at `app/Providers`.
```php
// app/Providers/AppServiceProvider.php
use App\Billing\Stripe;
public function register() {
	$this->app->singleton(Stripe::class, function() {
		return new Stripe(config(services.stripe.api_key));
	});
}
```
* Some packages may instruct you to add a line to the `app/config/app.php` file. This will tell Laravel to register the service.
```php
App\Package\PackageServiceProvider::class
```
* `php artisan make:provider` to create a new provider.

## Lesson 26 - Sending Email
* Mail drivers are configured in the `app/config/mail.php`. Supports many mail delivery services out of the box.
* Get started by generating a new `Mail` class using `php artisan make:mail Welcome`. Any public variable put in this file will be available to the view.
```php
// app/Emails/Welcome.php
use App\User;
public $user;
public function __construct(User $user) {
	$this->user = $user;
}
public function build() {
	return $this->view('emails.welcome');
}
```
* The new email class uses a view to generate the content of the email.
```php
// app/resources/views/email/welcome.blade.php
<h1>Welcome to our site, {{ $user->name }}!</h1>
```
* Use the new `Mail` class in a controller like so:
```php
use App\Mail\Welcome;
...
\Mail::to($user)->send(new Welcome);

```

## Lesson 27 - Markdown Mail
* `php artisan make:mail WelcomeAgain --markdown="emails.welcome-again"`
* Adding the `markdown` flag generates a new view and also uses `markdown()` instead of `view()`.
* Allows for styled emails using a default theme split into different components.
* Can customize the theme by executing `php artisan vendor:publish --tag=myapp-mail`. This will copy the views to `app/resources/views/vendor/mail`. Edit these files to manipulate the styling, or anything else.
* Tell Laravel to use these by editing the `app/config/mail.php`. Change `theme` from `default` to `myapp`.

## Lesson 28 - Form Requests and Form Objects
* Create a request using this command: `php artisan make:request RegistrationRequest`.
* Requests allow you to move logic out of the `Controller` and into it's own file that can be reused.
```php
use App\Http\Requests\RegistrationRequest;

public function store(RegistrationRequest $request) {
	// ...
}
```
* Request file contains `authorize()` and `rules()`.
```php
public function authorize() {
	return true; //anyone can make the request because it is a registration request!
}

public function rules() {
	return [
		'name' => 'required',
		'email' => 'required|email',
		'password' => 'required|confirmed'
	];
}
```
* Nothing in the Controller's `store()` method will execute if the Request's `rules()` validation fails.
* Can also add additional methods in the Request, for example, `persist()`
```php
public function persist() {
	$user = User::create(
		$this->only(['name', 'email', 'password'])
	);
	auth()->login($user);
	Mail::to($user)->send(new Welcome($user));
}
```
* This method would then be used from the Controller like so:
```php
public function store(RegistrationRequest $request) {
	$request->persist();
	return redirect()->home();
}
```

## Lesson 29 - Session Handling and Flash Messaging
* Good UX to give the user some feedback about something that may have happened in the background (i.e. registration was successful).
* `session()` allows for persisting data across page loads. `session()->flash()` persists the data only for a single page load.
```php
public function store(RegistrationRequest $request) {
	session()->flash('message', 'Thanks for signing up!');
	return redirect()->home();
}
```
* Show the message in the view. Style it however you'd like. Can use Javascript to fade out the message too.
```html
@if ($flash = session('message'))
	<div class="alert alert-success" role="alert">
		{{ $flash }}
	</div>
@endif
```

## Lesson 30 - Tags and Pivot Tables
* A post should be able to have multiple tags. This can be done by creating a new Model named `tag` and using a pivot table. It's migration would look something like this:
```php
public function up() {
	Schema::create('tags', function(Blueprint $table) {
		$table->increments('id');
		$table->string('name')->unique();
		$table->timestamps();
	});

	Schema::create('post_tag', function(Blueprint) {
		$table->integer('post_id');
		$table->integer('tag_id');
		$table->primary(['post_id', 'tag_id']);
	});
}
```
* In the `Post` model:
```php
public function tags() {
	return $this->belongsToMany(Tag::class);
}
```
* And the inverse in the `Tag` model:
```php
public function posts() {
	return $this->belongsToMany(Post::class);
}
```
* To avoid the `n+1` problem (making tons of database queries), Laravel comes ready to "eager load" out of the box. Example syntax: `App\Post::with('tags')->get();`
* Adding a tag: `$post->tags()->attach($tag);`
* Removing a tag: `$post->tags()->detach($tag);`

## Lesson 31 - Sorting Posts By Tags
* We want our users to be able to specify the tag name in the URL, rather than it's `id`. For example, `site.dev/posts/tags/personal` instead of `site.dev/posts/tags/1`. To accomplish this, create a new method in the `Tag` model to tell Laravel how to handle it.
```php
public function getRouteKeyName() {
	return 'name';
}
```
* Since you'll probably be doing various actions with tags, it makes sense to create a new controller to handle all the actions.
```php
use App\Tag;

class TagsController extends Controller {
	public function index(Tag $tag) {
		$posts = $tag->posts;
		return view('posts.index', compact('posts'));
	}
}
```
* Back from lesson 21, you could list out all the tags in the side bar using the view composers.
* Register a view composer in the `app/Providers/AppServiceProvider.php`.
```php
public function boot() {
	view()->composer('layouts.sidebar', function($view) {
		$archives = \App\Post::archives();
		$tags = \App\Tag::has('posts')->pluck('name');
		$view->with(compact('archives', 'tags'));
	});
}
```

## Lesson 32 - Eventing
* Think of an event as a significant thing in your application that took place: user registered, order was completed, post was created, etc. "When this happens, I need to do A, B and C".
* `php artisan make:event` to create a new event class.
* `php artisan make:listener` to create a new listener class. This is what responds to an event. Can also use `--event` to specify the event. For example `php artisan make:listener SendNotification --event="SomeEvent"`.
* Alternatively you could edit the `app\Providers\EventServiceProvider.php` file and add stuff to `$listen`, then run `php artisan event:generate`
```php
protected $listen = [
	'App\Events\ThreadCreated' => [
		'App\Listeners\NotifySubscribers',
	],
];
```
* Put whatever you want done in the `handle()` method in the `Listener` class.
```php
public function handle(ThreadCreated $event) {
	// do something
}
```
* Can simulate an event triggering using `php artisan tinker`.
	- `event(new App\Events\ThreadCreated(['name' => "Some new thread"]));`
* Sending an event throuhg a queue will prevent it from holding up the process, i.e. sending an email. This can be done by implementing `ShouldQueue`:
	- `class NotifySubscribers implements ShouldQueue`
