# Contracts

Laravel's Contracts are a set of interfaces that define the core services provided by the framework. 
For example, a Illuminate\Contracts\Queue\Queue contract defines the methods needed for queueing jobs, while the Illuminate\Contracts\Mail\Mailer contract defines the methods needed for sending e-mail.

Contracts Vs. Facades

Laravel's facades and helper functions provide a simple way of utilizing Laravel's services without needing to type-hint and resolve contracts out of the service container. In most cases, each facade has an equivalent contract.

Contracts allow you to define explicit dependencies for your classes. Some developers prefer to explicitly define their dependencies in this way and therefore prefer to use contracts, while other developers enjoy the convenience of facades.

