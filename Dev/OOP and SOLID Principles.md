## OOP & SOLID Principles
*Deeper look at object-oriented design and the principles that keep it maintainable*

- See [[Dev Foundations]] for the four pillars of OOP - this note covers SOLID and common design patterns

### SOLID Principles

##### Single Responsibility Principle (SRP)
- A class should have only one reason to change
- Example: separate a `UserValidator` from a `UserRepository` instead of one `UserManager` doing both
```java
// Violates SRP - validation and persistence are two reasons to change
class UserManager {
    void validate(User user) { /* ... */ }
    void save(User user) { /* ... */ }
}

// Follows SRP - each class has a single responsibility
class UserValidator {
    void validate(User user) { /* ... */ }
}

class UserRepository {
    void save(User user) { /* ... */ }
}
```

##### Open/Closed Principle (OCP)
- Software entities should be open for extension, closed for modification
- Achieved via interfaces/abstraction rather than editing existing code for every new case
```java
interface DiscountStrategy {
    double apply(double price);
}

class NoDiscount implements DiscountStrategy {
    public double apply(double price) { return price; }
}

class BlackFridayDiscount implements DiscountStrategy {
    public double apply(double price) { return price * 0.7; }
}

// New discount types are added without modifying PriceCalculator
class PriceCalculator {
    double calculate(double price, DiscountStrategy strategy) {
        return strategy.apply(price);
    }
}
```

##### Liskov Substitution Principle (LSP)
- Subtypes must be substitutable for their base types without breaking correctness
- Classic violation: a `Square` extending `Rectangle` that breaks `setWidth`/`setHeight` behavior
```java
class Rectangle {
    protected int width, height;

    void setWidth(int w) { width = w; }
    void setHeight(int h) { height = h; }
    int area() { return width * height; }
}

// Violates LSP - forcing width == height breaks callers that expect
// setWidth/setHeight to be independent, as Rectangle's contract implies
class Square extends Rectangle {
    @Override
    void setWidth(int w) { width = height = w; }

    @Override
    void setHeight(int h) { width = height = h; }
}
```

##### Interface Segregation Principle (ISP)
- Clients shouldn't be forced to depend on methods they don't use
- Prefer several small, specific interfaces over one large general-purpose interface
```java
// Violates ISP - RobotWorker is forced to implement eat()
interface Worker {
    void work();
    void eat();
}

// Follows ISP - split into focused interfaces
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

class HumanWorker implements Workable, Eatable {
    public void work() { /* ... */ }
    public void eat() { /* ... */ }
}

class RobotWorker implements Workable {
    public void work() { /* ... */ }
}
```

##### Dependency Inversion Principle (DIP)
- High-level modules shouldn't depend on low-level modules - both should depend on abstractions
- Enables dependency injection and easier testing (mocking)
```java
// Notification (high-level) depends on the MessageSender abstraction,
// not on a concrete implementation like EmailSender
interface MessageSender {
    void send(String message);
}

class EmailSender implements MessageSender {
    public void send(String message) { /* ... */ }
}

class Notification {
    private final MessageSender sender;

    Notification(MessageSender sender) { // injected dependency
        this.sender = sender;
    }

    void notify(String message) {
        sender.send(message);
    }
}
```

### Common Design Patterns

##### Creational
- Singleton -> ensures a class has only one instance
- Factory Method -> delegates object creation to subclasses
- Builder -> constructs complex objects step by step

##### Structural
- Adapter -> converts one interface into another a client expects
- Decorator -> adds behavior to an object dynamically without altering its class
- Facade -> provides a simplified interface to a complex subsystem

##### Behavioral
- Observer -> objects subscribe to and get notified of another object's state changes
- Strategy -> selects an algorithm's behavior at runtime
- Command -> encapsulates a request as an object

### Composition vs Inheritance
- Inheritance -> "is-a" relationship, tightly coupled to the parent
- Composition -> "has-a" relationship, more flexible - favored in modern design ("favor composition over inheritance")

### Common Interview Questions
- Explain the four pillars of OOP with real examples
- What's the difference between an abstract class and an interface?
- Why favor composition over inheritance?
- Describe a SOLID violation you've seen and how you'd fix it
- Walk through a scenario where you'd apply the Strategy or Observer pattern
