# Solid_Principle

# What is the principle of SOLID?

SOLID is a popular set of design principles that are used in object-oriented software development. SOLID is an acronym that stands for five key design principles: single responsibility principle, open-closed principle, Liskov substitution principle, interface segregation principle, and dependency inversion principle.

# What Problem these S.O.L.I.D principles solved?
    1.  Re-reading the code multiple times to get to the part you need to change
    2.  Hard to understand what the method does. jumping multiple methods of the same name
    3.  spending a lot more time to fix a minor bug.
    4. working with the legacy code.
Basically, we spend a lot more time reading than writing code.

# The SOLID principles of Object Oriented Design include these five principles:

    • SRP - Single Responsibility Principle.
    • OCP - Open/Closed Principle.
    • LSP - Liskov Substitution Principle.
    • ISP - Interface Segregation Principle.
    • DIP - Dependency Inversion Principle.

# 1. Single Responsibility principle.
As it’s clear from its name single responsible. A class should only be responsible for one thing that means a class could change for only one reason.

### Taking an example of the (simple) class called Page.

```bash
class Page {
  protected $title;

  public getTitle($title) {
    return $this->title;
  }

  public function formatJson() {
    return json_encode($this->getTitle());
  }
}
```
This class knows about a title property and allows this title property to be retrieved by a get() method. We can also use a method in this class called formatJson() to return the page as a JSON string. This might seem like a good idea as the class is responsible for its own formatting.

What happens, however, if we want to change the output of the JSON string, or to add another type of output to the class? We would need to alter the class to either add another method or change an existing method to suit. This is fine for a class as simple as this, but if it contained more properties then the formatting would be more complex to change.

A better approach to this is to modify the Page class so that is only knows about the data is handles. We then create a secondary class called JsonPageFormatter that is used to format the Page objects into JSON.

```bash
class Page {
  protected $title;

  public getPage($title){
    return $this->title;
  }
}

class JsonPageFormatter {
    public function format(Page $page) {
        return json_encode($page->getTitle());
    }
}
```

Doing this means that if we wanted to create an XML format we could just add a class called XmlPageFormatter and write some simple code to output XML. We now have only one reason to change the Page class.
# 2. Open Closed Principle

An entity should be open for extensions but closed for modification.
It states that for good practise you should be able to add new features without modifying the existing code.

### As an example, take the following three classes. 

```bash
class Programmer
{
    public function code()
    {
        return 'coding';
    }
}

class Tester
{
    public function test()
    {
        return 'testing';
    }
}

class ProjectManagement
{
    public function process($member)
    {
        if ($member instanceof Programmer) {
            $member->code();
        } elseif ($member instanceof Tester) {
            $member->test();
        };

        throw new Exception('Invalid input member');
    }
}
```

Here if we add another new class named developer. Then we have to modify the process method of project management class again. But, the Open Closed Principle does not support this.

```bash
interface Workable
{
    public function work();
}

class Programmer implements Workable
{
    public function work()
    {
        return 'coding';
    }
}

class Tester implements Workable
{
    public function work()
    {
        return 'testing';
    }
}

class Designer implements Workable
{
    public function work()
    {
        return 'frontend web designer';
    }
}

class ProjectManagement
{
    public function process(Workable $member)
    {
        return $member->work();
    }
}
```

But, if we code in this approach. Then if we add a new class. We don't have to modify anything. We just create new class. And implement workable interface.

# 3. Liskov Substitution Principle.

The Liskov Substitution Principle in practical software development. The principle defines that objects of a superclass shall be replaceable with objects of its subclasses without breaking the application. That requires the objects of your subclasses to behave in the same way as the objects of your superclass.

### The following code defines a Rectangle class that we can use to create and calculate the area of a rectangle.

```bash
class Rectangle {
  public function setWidth($w) { 
      $this->width = $w;
  }

  public function setHeight($h) {
      $this->height = $h;
  }

  public function getArea() {
      return $this->height * $this->width;
  }
}
```

Using that we can extend this into a Square class. Because a square a little different from a rectangle we need to override some of the code in order to allow a Square to exist correctly.

```bash
class Square extends Rectangle {
  public function setWidth($w) {
    $this->width = $w;
    $this->height = $w;
  }

  public function setHeight($h) {
    $this->height = $h;
    $this->width = $h;
  }
}
```

This seems fine, but ultimately a square is not a rectangle and so we have added code to force this situation to work.

One solution to the rectangle vs square situation is to create an interface called Quadrilateral and implement this in separate Rectangle and Square classes. In this situation we are allowing the classes to be responsible for their own data, but enforcing the need for certain method footprints being available.

```bash
interface Quadrilateral {
  public function setHeight($h);

  public function setWidth($w);

  public function getArea();
}

class Rectangle implements Quadrilateral;
 
class Square implements Quadrilateral;
```
The bottom line here is that if you find you are overriding a lot of code then maybe your architecture is wrong and you should think about the Liskov Substitution principle.

# 4. Interface Segregation Principle

In the field of software engineering, the interface-segregation principle states that no client should be forced to depend on methods it does not use. ISP splits interfaces that are very large into smaller and more specific ones so that clients will only have to know about the methods that are of interest to them.

### Let's take an example of a Worker interface. This defines several different methods that can be applied to a worker at a typical development agency.

```bash
interface Worker {

  public function takeBreak();

  public function writeCode();

  public function callToClient();

  public function attendMeetings();

  public function getPaid();
}
```

The problem is that because this interface is too generic we are forced to create methods in classes that implement this interface just to suit the interface.

For example, if we create a Manager class then we are forced to implement a writeCode() method because that's what the interface requires. Because managers generally don't code we can't actually do anything in this method so we just return false.

```bash
class Manager implements Worker {
  public function writeCode() {
    return false;
  }
}

```
Also, if we have a Developer class that implements Worker then we are forced to implement a callToClient() method because that's what the interface requires.

```bash
class Developer implements Worker {
  public function callToClient() {
    echo "I'll ask my manager.";
  }
}
```
Having a fat and bloated interface means having to implement methods that do nothing.

The correct solution to this is to split our interfaces into separate parts, each of which deals with specific functionality. Here, we split out a Coder and ClientFacer interface from our generic Worker interface.

```bash
interface Worker {
  public function takeBreak();
  public function getPaid();
}
 
interface Coder {
  public function code();
}
 
interface ClientFacer {
  public function callToClient();
  public function attendMeetings();
}
```

With this in place we can implement our sub-classes without having to write code that we don't need. So our Developer and Manager classes would look like this.

```bash
class Developer implements Worker, Coder {
}
 
class Manager implements Worker, ClientFacer {
}
```
Having lots of specific interfaces means that we don't have to write code just to support an interface.

# 5. Dependency Inversion Principle

Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.

### Taking an example of a PageLoader class that uses a MySqlConnection class to load pages from a database we might create the classes so that the connection class is passed to the constructor of the PageLoader class.

```bash
class MySqlConnection {
    public function connect() {}
}

class PageLoader {
    private $dbConnection;
    public function __construct(MySqlConnection $dbConnection) {
        $this->dbConnection = $dbConnection;
    }
}
```
This structure means that we are essentially stuck with using MySQL for our database layer. What happens if we want to swap this out for a different database adaptor?

The solution here is to create an interface called DbConnectionInterface and then implement this interface in the MySqlConnection class. Then, instead of relying on a MySqlConnection object being passed to the PageLoader class, we instead rely on any class that implements the DbConnectionInterface interface.

```bash
interface DbConnectionInterface {
    public function connect();
} 

class MySqlConnection implements DbConnectionInterface {
    public function connect() {}
}

class PageLoader {
    private $dbConnection;
    public function __construct(DbConnectionInterface $dbConnection) {
        $this->dbConnection = $dbConnection;
    }
}
```

With this in place we can now create a MemcacheConnection class and as long as it implements the DbConnectionInterface then we can use it in the PageLoader class to load pages.

This approach also forces us to write code in such a way that prevents specific implementation details in classes that don't care about it. Because we have passed in a MySqlConnection class to our PageLoader class we shouldn't then write SQL queries in the PageLoader class. This means that when we pass in a MemcacheConnection object it will behave in the same way as any other type of connection class.

When thinking about interfaces instead of classes it forces us to move that specific domain code out of our PageLoader class and into the MySqlConnection class.
