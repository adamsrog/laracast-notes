# What's New in Laravel 5.1
https://laracasts.com/series/whats-new-in-laravel-5-1/

## Lesson 1 - Adopting PSR-2
* PSR-2 is a coding style guide: https://www.php-fig.org/psr/psr-2/
* Braces on new lines.
* Spaces instead of tabs.
* Can use a build system in Sublime Text to automate compliance using `php-cs-fixer`.

## Lesson 2 - Injecting Services With Blade
* Use a view composer to inject services with Blade instead of having to specify it in every Router/Controller.
* See Lesson 21 in Laravel 5.4 from Scratch course.

## Lesson 3 - Elixir Improvements
* Laravel Elixir provides a clean, fluent API for defining basic Gulp tasks for your Laravel application. Elixir supports several common CSS and JavaScript pre-processors, and even testing tools.
* https://github.com/laravel/elixir
* Appears to be deprecated/removed.

## Lesson 4 - Better Testing Facilities
* Faker (fake data), Mockery (mock classes), PHPUnit (testing), PHPSpec
* Syntax is much more readable.
```php
$this->visit('/')
  ->type('some query', '#search')
  ->see('Search results for "some query"')
  ->onPage('/search-results');
```

## Lesson 5 - Model Factories
* Has a factory builder straight out of the box. This allows you to generate dummy data to populate your database with.
* Uses `Faker` to generate the dummy data. Has tons of different types of data it can generate.
  - https://github.com/fzaninotto/Faker

## Lesson 6 - Artisan Signatures
* Allows you to perform custom actions through `artisan` in the console.
* `php artisan make:console ShowGreeting`
```php
// app/Console/Commands/ShowGreeting.php
public function handle() {
  $this->info('Hello there, ' . $this->argument('name'));
}
```
* `protected $signature = 'laracasts:greet {name}'`
  - Use `{name?}` to indicate that `name` isn't required.
  - Use `{name=Rog}` to specify a default.
* Use it by executing `php artisan laracasts:greet Rog`

## Lesson 7 - Directory Changes
* No longer have a Commands directory. 
  - Has been renamed to Jobs.
* No longer have a Handlers directory.
  - Has been changed to Listeners.
* `php artisan event:generate` creates all `Event` and a `Listener` based on the `app/Providers/EventServiceProviders.php` file.
* 

## Lesson 8 - Documentation and LTS
* Documentation has been massively improved, search is more functional: https://laravel.com/docs
* Laravel 5.1 is a Long-term Support version (LTS). This means bug fixes for two years, and security updates for three years.

## Lesson 9 - Middleware Parameters
* Crash course episode for https://laracasts.com/series/intermediate-laravel/episodes/8
* Allow parameters to middleware.

## Lesson 10 - Named Route Groups
* Functions for naming routes. Deprecated by the time I'm viewing this (5.7 is current), so not going to give examples of deprecated syntax..

## Lesson 11 - Login Throttling
* Protects against malicious users from brute forcing logins. 
* It will lock a users account if they have too many failed attempts over a specified threshold (default 5 in a minute).

## Lesson 12 - Push Events to the Client
* Laravel has the ability to listen for events and update the DOM without requiring the user to refresh manually.
* Can use various services to broadcast to the client side. Works with `Pusher` out of the box, just need to install the SDK.
  - https://pusher.com/ to create an account and get API key/secret.
* All you need to do is create an event and have it implement `ShouldBroadcast`.

## Lesson 13 - ACL in Laravel: Part 1
* Laravel can implement Access-Control-Lists by using `Gate`.
```php
// AuthServiceProvider.php
public function boot(GateContract $gate) {
  $gate->define('update-post', function($user, $post) {
    return $user->owns($post);
    // or...
    return $user->id == $post->user_id;
  });
}
```
* Can implement it in the controller like so:
```php
use Gate;
...
public function show($id) {
  $post = Post::findOrFail($id)

  if (Gate::denies('update-post', $post)) {
    abort(403, "Nope.");
  }
  // or...
  $this->authorize('update-post', $post);

  return $post->title;
}
```
* Use it in Blade templates like so:
```html
@can('update-post', $post)
<a href="#">Edit post</a>
@endcan
```
* Also a `@cannot` helper too.

## Lesson 14 - ACL in Laravel: Part 2 (Policy Objects)


## Lesson 15 - ACL in Laravel: Part 3 (Behind the Scenes)


## Lesson 16 - ACL in Laravel: Roles and Permissions

