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


## Lesson 6 - Artisan Signatures


## Lesson 7 - Directory Changes


## Lesson 8 - Documentation and LTS


## Lesson 9 - Middleware Parameters


## Lesson 10 - Named Route Groups


## Lesson 11 - Login Throttling


## Lesson 12 - Push Events to the Client


## Lesson 13 - ACL in Laravel: Part 1


## Lesson 14 - ACL in Laravel: Part 2 (Policy Objects)


## Lesson 15 - ACL in Laravel: Part 3 (Behind the Scenes)


## Lesson 16 - ACL in Laravel: Roles and Permissions

