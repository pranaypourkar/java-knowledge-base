# Creational Pattern

## Description

Creational patterns in software engineering deal with the process of object creation in a flexible and reusable manner. They aim to provide mechanisms to instantiate objects in various situations while promoting loose coupling between the creator and the created objects. Creational patterns often involve hiding the object creation logic and making the system more independent of how its objects are created, composed, and represented



## Types of Creational Pattern

### **Singleton Pattern**

#### Description

The Singleton Pattern ensures a <mark style="background-color:yellow;">class</mark> has only one instance and provides a global point of access to that instance. It involves creating a class with a method that returns the same instance of the class every time it's called, thus restricting the instantiation of the class to a single object. It's like having a single source of truth for a specific concept within your application.

Imagine a scenario where we only need one instance of a class throughout the application. For example, a system might have a single `Logger` object to handle all logging activities, or a configuration manager to hold all application settings. The Singleton Pattern ensures this by:

1. **Restricting object creation:** The constructor of the Singleton class is typically private or protected, preventing direct instantiation from outside the class.
2. **Providing a static method:** A public static method, often named `getInstance()`, is used to access the single instance. If the instance doesn't exist, it's created on the first call and returned. Subsequent calls reuse the same instance.

#### **Benefits of Singleton Pattern**

* **Global access point:** Provides a centralized and consistent way to access the single instance.
* **Resource management:** Ensures only one instance exists, which can be helpful for managing resources like file handles or database connections.
* **Enforces single instance:** Guarantees that there's only one instance of the class, preventing conflicts or inconsistencies.

#### **Drawbacks of Singleton Pattern**

* **Tight coupling:** Code that relies on the Singleton class becomes tightly coupled to it, making testing and refactoring more challenging.
* **Global state:** The Singleton instance holds global state, which can be difficult to manage and reason about in complex applications.
* **Limited flexibility:** It might be difficult to create multiple instances for testing or special scenarios.

#### Example

```java
public class Logger {
    // Private static instance variable
    private static Logger instance;
    
    // Private constructor to prevent instantiation
    private Logger() {
        // Initialization logic
    }
    
    // Public static method to get the singleton instance
    public static Logger getInstance() {
        // Lazy initialization: Create the instance if it doesn't exist
        if (instance == null) {
            instance = new Logger();
        }
        return instance;
    }
    
    // Public method to log messages
    public void log(String message) {
        System.out.println("Logging: " + message);
        // Additional logging logic
    }
}
```



{% hint style="info" %}
In a Spring Boot application, the Singleton pattern is utilized extensively due to the nature of Spring's bean management. By default, Spring manages beans as singletons within the container, meaning that only one instance of a bean is created per container context.

#### Example: UserService in a Spring Boot Application

Suppose we have a UserService class responsible for managing user-related operations, such as fetching user data from a database, performing user authentication, and so on. We want to ensure that there's only one instance of UserService throughout the application, so we annotate it with `@Service` (or `@Component`) to let Spring manage it as a singleton:



```java
import org.springframework.stereotype.Service;

@Service
public class UserService {

    public void fetchUserData() {
        // Code to fetch user data from the database
        System.out.println("Fetching user data...");
    }

    public void authenticateUser() {
        // Code to authenticate user
        System.out.println("Authenticating user...");
    }
}
```



By default, Spring will create a single instance of `UserService` and manage it as a singleton bean within the application context. Let's use the `UserService` in a controller. We inject the `UserService` dependency using constructor injection. When the Spring Boot application starts up, Spring creates a single instance of `UserService` and manages it as a singleton bean within the application context. Whenever the `UserController` is instantiated, it receives the same instance of `UserService`. This ensures that all requests to `/userdata` endpoint is handled by the same instance of `UserService`, maintaining consistency and preventing unnecessary object creation.



```java
package org.example.api;

import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RequiredArgsConstructor
@RestController
public class UserController {

    private final UserService userService;

    @GetMapping("/userdata")
    public String getUserData() {
        userService.fetchUserData();
        return "User data fetched successfully!";
    }
}
```
{% endhint %}



### **Factory Method Pattern**

#### Description

The Factory Method Pattern is a creational design pattern that provides an interface for creating objects in a superclass but allows subclasses to alter the type of objects that will be created. It defines an interface for creating an object, but subclasses are responsible for instantiating the appropriate concrete class. This pattern promotes loose coupling between the creator and the created objects, allowing the creator to defer object creation to its subclasses.

Imagine a scenario where you need to create different types of objects based on certain conditions, but you want to keep the client code (code that uses the objects) decoupled from the specific object creation logic. The Factory Method Pattern achieves this by:

1. **Defining a Factory Interface:** This interface declares a method (often named `createProduct()`) that all concrete factory classes must implement. This method returns an instance of a product (the object to be created).
2. **Creating Concrete Factory Classes:** These classes implement the `FactoryInterface` and provide specific implementations for the `createProduct()` method. Each concrete factory creates a specific type of product.
3. **Client Code Uses Factory:** The client code interacts with a factory object (often obtained through a static method or dependency injection) and calls the `createProduct()` method to get the desired product instance. It doesn't need to know the concrete class of the product being created.

#### **Benefits of Factory Method Pattern**

* **Decouples object creation:** Separates object creation logic from the client code, promoting loose coupling.
* **Flexibility in object creation:** Allows for creating different types of objects based on runtime conditions.
* **Open/Closed Principle compliance:** New concrete factories can be added without modifying existing client code.

#### **Drawbacks of Factory Method Pattern**

* **Introduces an extra layer of abstraction:** The factory classes add complexity compared to direct object creation.
* **Might not be necessary for simple scenarios:** If only a few object types exist, simpler approaches might be sufficient.

#### Example 1

```java
// Interface for creating Pizzas
public interface PizzaFactory {
  Pizza createPizza(String type);
}

// Concrete Factory Class for Margherita Pizza
public class MargheritaPizzaFactory implements PizzaFactory {
  @Override
  public Pizza createPizza(String type) {
    if (type.equals("Margherita")) {
      return new MargheritaPizza();
    } else {
      throw new IllegalArgumentException("Invalid pizza type");
    }
  }
}

// Concrete Factory Class for Hawaiian Pizza (similar structure)
public class HawaiianPizzaFactory implements PizzaFactory {
  @Override
  public Pizza createPizza(String type) {
    // ... (implementation to create Hawaiian Pizza)
  }
}

// Client Code (doesn't know concrete Pizza classes)
public class PizzaOrder {
  public Pizza orderPizza(String type, PizzaFactory factory) {
    return factory.createPizza(type);
  }
}

public class Main {
  public static void main(String[] args) {
    PizzaOrder order = new PizzaOrder();
    PizzaFactory factory = new MargheritaPizzaFactory(); // Choose factory
    Pizza pizza = order.orderPizza("Margherita", factory);
    // ... (use the pizza)
  }
}
```

#### Example 2

Create a Shape interface and concrete classes implementing the Shape interface.

<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (76).png" alt="" width="311"><figcaption></figcaption></figure>

```java
// Shape interface
package src.main.java.shape;

public interface Shape {
    public void draw();
}


// Circle class
package src.main.java.shape;

public class Circle implements Shape{
    @Override
    public void draw() {
        System.out.println("Drawing Circle");
    }
}


// Square class
package src.main.java.shape;

public class Square implements Shape{
    @Override
    public void draw() {
        System.out.println("Drawing Square");
    }
}


// Rectangle class
package src.main.java.shape;

public class Rectangle implements Shape{
    @Override
    public void draw() {
        System.out.println("Drawing Rectangle");
    }
}


// ShapeFactory
package src.main.java.shape;

public class ShapeFactory {
    public static Shape getShape(String shapeType) {
        return switch (shapeType) {
            case "CIRCLE" -> new Circle();
            case "SQUARE" -> new Square();
            case "RECTANGLE" -> new Rectangle();
            default -> null;
        };
    }
}


// Main Application class
package src.main.java;

import src.main.java.shape.Shape;
import src.main.java.shape.ShapeFactory;

public class Application {
    public static void main(String[] args) {
        Shape shape1 = ShapeFactory.getShape("SQUARE");
        shape1.draw();

        Shape shape2 = ShapeFactory.getShape("CIRCLE");
        shape2.draw();

        Shape shape3 = ShapeFactory.getShape("RECTANGLE");
        shape3.draw();
    }
}
```





The Factory Method Pattern allows for flexible object creation by delegating the responsibility of creating objects to subclasses, making the system more extensible and maintainable. Each subclass can determine the type of object to create based on its specific requirements.



### **Abstract Factory Pattern**



### **Builder Pattern**



### **Prototype Pattern**