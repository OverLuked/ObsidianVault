## OOP & SOLID Principles
*Deeper look at object-oriented design and the principles that keep it maintainable*

- See [[Dev Foundations]] for the four pillars of OOP - this note covers SOLID and common design patterns

### SOLID Principles

##### Single Responsibility Principle (SRP)
- A class should have only one reason to change
- Example: separate a `UserValidator` from a `UserRepository` instead of one `UserManager` doing both

##### Open/Closed Principle (OCP)
- Software entities should be open for extension, closed for modification
- Achieved via interfaces/abstraction rather than editing existing code for every new case

##### Liskov Substitution Principle (LSP)
- Subtypes must be substitutable for their base types without breaking correctness
- Classic violation: a `Square` extending `Rectangle` that breaks `setWidth`/`setHeight` behavior

##### Interface Segregation Principle (ISP)
- Clients shouldn't be forced to depend on methods they don't use
- Prefer several small, specific interfaces over one large general-purpose interface

##### Dependency Inversion Principle (DIP)
- High-level modules shouldn't depend on low-level modules - both should depend on abstractions
- Enables dependency injection and easier testing (mocking)

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
