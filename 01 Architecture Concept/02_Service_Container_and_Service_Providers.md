Server container and Service provider are the backbone of the Laravel framework and do all heavy jobs when your application runs.

## Service Container

> The Laravel service container is a powerful tool for managing class dependencies and performing dependency injection.

In the simplest way to understand service container, we can assume that it is a box that holds various components’ bindings, and they are served as needed throughout the application.

## Service provider

Service providers are the central place of all Laravel application bootstrapping. Your own application, as well as all of Laravel’s core services are bootstrapped via service providers.

[ Bootstrapping is the process Laravel takes to combine the necessary bits in the framework together to be able to process and handle the functionality thrown at the system. ]

So whenever you want to inject a service into other services, you can add it into constructor or method, and it’s injected automatically from service container by the service provider.

```php
class MyDemoClass
{
   public function __construct(AwesomeService $awesome_service)
   {
        $awesome_service->doAwesomeThing();
   }
}
```

As you can see, the MyDemoClass needs an instance of AwesomeService to instantiate itself. So basically, it has a dependency that needs to be injected. Laravel does this automatically by looking into the service container and injecting the appropriate dependency.

Look at the contents of file config/app.php. You’ll find an array which used to declare all service providers, These service providers will be loaded during the bootstrapping of the Laravel application.

```php
'providers' => [
    /*
    * Laravel Framework Service Providers...
    */
   Illuminate\Auth\AuthServiceProvider::class,
   Illuminate\Broadcasting\BroadcastServiceProvider::class,
   Illuminate\Bus\BusServiceProvider::class,
   Illuminate\Cache\CacheServiceProvider::class,
   Illuminate\Foundation\Providers\ConsoleSupportServiceProvider::class,
   Illuminate\Cookie\CookieServiceProvider::class,
   Illuminate\Database\DatabaseServiceProvider::class,
   Illuminate\Encryption\EncryptionServiceProvider::class,
   Illuminate\Filesystem\FilesystemServiceProvider::class,
   Illuminate\Foundation\Providers\FoundationServiceProvider::class,
   Illuminate\Hashing\HashServiceProvider::class,
   Illuminate\Mail\MailServiceProvider::class,
   Illuminate\Notifications\NotificationServiceProvider::class,
   Illuminate\Pagination\PaginationServiceProvider::class,
   Illuminate\Pipeline\PipelineServiceProvider::class,
   Illuminate\Queue\QueueServiceProvider::class,
   Illuminate\Redis\RedisServiceProvider::class,
   Illuminate\Auth\Passwords\PasswordResetServiceProvider::class,
   Illuminate\Session\SessionServiceProvider::class,
   Illuminate\Translation\TranslationServiceProvider::class,
   Illuminate\Validation\ValidationServiceProvider::class,
   Illuminate\View\ViewServiceProvider::class,
	/*
    * Application Service Providers...
    */
   App\Providers\AppServiceProvider::class,
   App\Providers\AuthServiceProvider::class,
   App\Providers\EventServiceProvider::class,
   App\Providers\RouteServiceProvider::class,
	/**
    * Custom Provider
    */
],
```

#### Create service provider
In Laravel official document, you can create your own service provider by running following cli

> php artisan make:provider AwesomeServiceProvider

All service providers extend the Illuminate\Support\ServiceProvider class. Most service providers contain a register and a boot method. Within the register method, you should only bind things into the service container. You should never attempt to register any event listeners, routes, or any other piece of functionality within the register method.

```php
<?php
namespace App\Providers;
use Illuminate\Support\ServiceProvider;
class AwesomeServiceProvider extends ServiceProvider
{
    public function boot()
    {
     //
    }
    public function register()
    {
     //
    }
}
```
The register method is the place where you declare all your service container bindings.
On the other hand, the boot method is the place where you can use already registered services via the register method to do awesome things, it means this method is called after all other service providers have been registered.

> There is no need to bind classes into the container if they do not depend on any interfaces. The container does not need to be instructed on how to build these objects, since it can automatically resolve these objects using reflection.

So you don’t need to create your own service provider if you don’t have an interface. Laravel will use reflection. That’s what happens in above example with MyDemoClass and AwesomeService.

Now, we go ahead with an example which uses interface, so let create your interface.
```php
<?php
namespace App\Service;
interface AwesomeServiceInterface
{
    public function doAwesomeThing();
}
```
And the service
```php
<?php
namespace App\Service;
class AwesomeService implements AwesomeServiceInterface
{
    public function doAwesomeThing()
    {
        echo ‘do awesome thing !!!’;
    }
}
```
After that, instead of binding a class, we’ll bind an interface.
```php
<?php
namespace App\Providers;
use Illuminate\Support\ServiceProvider;
class AwesomeServiceProvider extends ServiceProvider
{
    public function boot()
    {
        //
    }
    
    public function register()
    {
        $this->app->bind(‘App\Service\AwesomeServiceInterface’, ‘App\Service\AwesomeService’);
    }
}
```
Add your service provider into file config/app.php
```php
‘providers’ => [
    ……
    /**
    * Custom Provider
    */
    App\Providers\AwesomeServiceProvider::class,
],
```
Let create controller to test your service
```php
<?php
namespace App\Http\Controllers;
use App\Service\AwesomeServiceInterface;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
class TestController extends Controller
{
     public function doAwesome(AwesomeServiceInterface $awesome_service)
     {
         $awesome_service->doAwesomeThing();
         return new Response();
     }
}
```
This is working follow of above example

- Using service provider, it’s flexible to change service injected to others. Behaviors of the consumer are fixed.
- And dependency injection decreases coupling between a class and its dependency. So we can implement a class and its dependency at the same time.

For a sample, you inject ProductService which provides function getAllProduct() into ProductController by using the constructor method. You can create MockProductService with hardcoded data to implement ProductController, and the other one can implement ProductService at the same time.

By using the service provider, you can change class injected quickly and easily.


The boot method
If you want to register a view composer within our service provider, this is a perfect place to do that
```php
/**
* Bootstrap services.
*
* @return void
*/
public function boot()
{
    View::composer(
        ‘profile’, ‘App\Http\ViewComposers\ProfileComposer’
    );
}
```

-------------------------------------------------

 সার্ভিস প্রোভাইডারই হচ্ছে লারাভেলের কোর। অর্থাৎ আপনি লারাভেলে অটোমেটিক হয়ে যাওয়া যতকিছু পাচ্ছেন এর সিংহভাগই সার্ভিস প্রোভাইডারের মাধ্যমে হচ্ছে। এর অর্থ সার্ভিস প্রোভাইডার হচ্ছে লারাভেলের একটা সেন্ট্রাল প্লেস যেখানে এ্যাপ্লিকেশনটি বুটস্ট্রাপিং হয়। 

এবার আসি ServiceProvider এর ডিফল্ট মেথড দুটো কি কাজ করে সে বিষয়ে।

Register:
এই মেথডটিই মূলত লারাভেলের প্রান। অর্থাৎ এখানেই সার্ভিস কন্টেইনার বাইন্ডিং, রাউট রেজিস্টারিং, মিডলওয়্যার রেজিস্টার, ইভেন্ট লিসেনার রেজিস্টার হয়। পুরো এ্যাপ্লিকেশনের যত জায়গায় আপনি অটোমেটিক ডিপেন্ডেন্সি রিসলভ হতে দেখেন এই সব কিছুই এখান থেকে হয়।

Boot:
এই মেথডটি Register মেথডগুলো এক্সিকিউট হবার পরে কাজ করে। অর্থাৎ লারাভেল যখন পুরো সিস্টেমের সকল কিছু রিসলভ করে ফেলে তখন এই Boot মেথডগুলো কাজ করে। তাই আপনি এখানে যদি সার্ভিস বাইন্ডিংস এর কাজ করে থাকেন তবে সেটা কাজ করবে না। এখানে আপনি ভিউ কম্পোজার নিয়ে কাজ করতে পারেন, কনফিগ নিয়ে কাজ করতে পারেন, মাইগ্রেশন পাবলিশের কাজ এখানে করতে পারেন। অর্থাৎ সার্ভিসগুলো রেজিস্টার হয়ে যাবার পরে যেকোনো কাজ এখানে করতে পারবেন।

------------------------------------------------


Copyright: 
1. https://medium.com/grevo-techblog/service-provider-in-laravel-3b7267b0576e

