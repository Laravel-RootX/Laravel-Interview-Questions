# Laravel Interview Questions


### Architecture Concept:

#### 01. Request Lifecycle:
When you understand how your development tools function, you feel more comfortable and confident using them.

1. First Things

> The entry point for all requests to a Laravel application is the public/index.php file.
- All requests are directed to this file by your web server (Apache / Nginx) configuration.
- The index.php file doesn't contain much code. Rather, it is a starting point for loading the rest of the framework.
> The index.php file loads the Composer generated autoloader definition, and then retrieves an instance of the Laravel application from bootstrap/app.php script. 
- The first action taken by Laravel itself is to create an instance of the application / service container.

2. HTTP / Console Kernels

> Next, the incoming request is sent to either the HTTP kernel or the console kernel, depending on the type of request that is entering the application.
- These two kernels serve as the central location that all requests flow through.
- HTTP kernel, which is located in app/Http/Kernel.php.
- The HTTP kernel extends the Illuminate\Foundation\Http\Kernel class, which defines an array of bootstrappers that will be run before the request is executed. 
- These bootstrappers configure error handling, configure logging, detect the application environment, and perform other tasks that need to be done before the request is actually handled.

3. Service Providers

> One of the most important Kernel bootstrapping actions is loading the service providers for your application. All of the service providers for the application are configured in the config/app.php configuration file's providers array.
-  First, the register method will be called on all providers, then, once all providers have been registered, the boot method will be called.
- Service providers are responsible for bootstrapping all of the framework's various components, such as the database, queue, validation, and routing components.

- Bootstrapping is the process Laravel takes to combine the necessary bits in the framework together to be able to process and handle the functionality thrown at the system.

4. Dispatch Request

> Once the application has been bootstrapped and all service providers have been registered, the Request will be handed off to the router for dispatching.
- The router will dispatch the request to a route or controller, as well as run any route specific middleware.

![Life Cycle of Request](https://user-images.githubusercontent.com/23103980/54848970-f3fe8200-4d0c-11e9-9a94-93ece9717422.PNG)