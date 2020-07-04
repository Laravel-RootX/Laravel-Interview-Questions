### Dependency Injection

Let's imagine that you currently work on a "Question and Answers" website, similar to Stack Overflow. You would more than likely create a class, called Question, which would contain a member of type Author. In 'ye olden days, programmers would have created the Author object directly in the Question constructor, like this:
```php
class Author {
    private $firstName;
    private $lastName;
 
    public function __construct($firstName, $lastName) {
        $this->firstName = $firstName;
        $this->lastName = $lastName;
    }
 
    public function getFirstName() {
        return $this->firstName;
    }
 
    public function getLastName() {
        return $this->lastName;
    }
}
 
class Question {
    private $author;
    private $question;
    public function __construct($question, $authorFirstName, $authorLastName) {
        $this->author = new Author($authorFirstName, $authorLastName);
        $this->question = $question;
    }
 
    public function getAuthor() {
        return $this->author;
    }
 
    public function getQuestion() {
        return $this->question;
    }
}
```

While many programmers might call this good code, there are in fact many problems with it:

- The author's information passed to the Question constructor has nothing to do inside Question's scope. The name of an author should be inside the Author class because it has nothing to do with the question, itself.
- The Author class is tightly coupled with the Question class. If we add a new parameter to Author's constructor, we then have to modify every class where we create an Author object - a tedious and long process, especially in large applications.
- Unit testing the Question class creates the unwanted behavior of having to test the Author class as well.

Dependency injection alleviates these issues by inserting the dependencies through the dependent class' constructor ("Constructor Injection"). The result is highly maintainable code, which might look like this:

```php
class Author {
    private $firstName;
    private $lastName;
     
    public function __construct($firstName, $lastName) {
        $this->firstName = $firstName;
        $this->lastName = $lastName;
    }
 
    public function getFirstName() {
        return $this->firstName;
    }
 
    public function getLastName() {
        return $this->lastName;
    }
}
 
class Question {
    private $author;
    private $question;
 
    public function __construct($question, Author $author) {
        $this->author = $author;
        $this->question = $question;
    }
 
    public function getAuthor() {
        return $this->author;
    }
 
    public function getQuestion() {
        return $this->question;
    }
}
```
Advantages of Dependency Injection

- You need to use Dependency Injection for long-term projects.
- The application is also impossible to unit test, degrading the code's quality.

----------------------------------------------------------------------------------

Dependency injection is a technique whereby one object supplies the dependencies of another object.

```php
class Profile {
    public function deactivateProfile(Setting $setting)
    {
        $setting->isActive = false;
    }
}
```
Different Ways of Injecting Objects

There are many ways to inject objects, here are a couple of most known:
- Constructor Injection: it is how you inject an object through the class constructor:
```php
class Profile {
    private $setting;
    public function __construct(Setting $setting)
    {
        $this->setting = $setting;
    }
}
// to instantiate Profile, you have to instantiate Setting first
$setting = new Setting;
$profile = new Profile($setting);
```
This is the most common case, and it is useful to create a loosely coupled components. It is also useful while writing tests for your application.

- Setter Injection: where you inject the object to your class through a setter function:

```php
class Profile {
    private $setting;
    public function setSetting(Setting $setting)
    {
        $this->setting = $setting;
    }
}
// to instantiate Profile, you have the option to inject Setting object
$setting = new Setting;
$profile = new Profile();
$profile->setSetting($setting);
```
This way gives you the option to inject whenever you need to use it inside the Profile class.

Dependency Injection Container is the way to manage injecting and reading objects and third party libraries in your application.

```php
<?php
interface ContainerInterface {
    public function get($id);
    public function has($id);
} 
```

It is a very simple implementation for the ContainerInterface which has two main functions get() and has():

get(): get the object from the container.
has() : check if we have the object in the container


Copyright: 
- https://medium.com/tech-tajawal/dependency-injection-di-container-in-php-a7e5d309ccc6