## What is Software Development?
- The process of designing, writing, testing, and maintaining the source code that powers applications and systems
### Key Responsibilities
- Writing clean, maintainable, and efficient code
	- Following style guides and team conventions
- Designing and implementing software architecture
	- Choosing appropriate patterns, libraries, and structures
- Debugging and troubleshooting
	- Root cause analysis of defects, not just patching symptoms
- Participating in code reviews
	- Reviewing pull requests for correctness, style, and maintainability
- Collaborating with QA, Product, and DevOps
	- Translating requirements into working, testable software
### Essential Skills
- Proficiency in at least one programming language
- Understanding of data structures & algorithms
- Version control (Git)
- Debugging and problem-solving
- Working knowledge of databases and APIs
- Written and verbal communication/documentation

## Programming Paradigms
*Different approaches to structuring and reasoning about code*
##### Object-Oriented Programming (OOP)
- Organizes code around objects that bundle data and behavior together
##### Functional Programming
- Treats computation as the evaluation of pure functions
- Avoids shared state and mutable data
##### Procedural Programming
- Organizes code as a sequence of instructions/procedures that operate on data
##### Imperative vs Declarative
- Imperative -> describes *how* to reach a result, step-by-step (e.g. for-loops)
- Declarative -> describes *what* result is wanted, not how (e.g. SQL, HTML, regex)

## OOP Principles
*The four pillars of Object-Oriented Programming*
##### Encapsulation
- Bundles data and the methods that operate on it into a single unit (class)
- Restricts direct access to internal state (private fields, public methods)
##### Abstraction
- Hides complex implementation details behind a simple interface
##### Inheritance
- A class can inherit properties and behavior from a parent class
##### Polymorphism
- Objects of different classes can be used interchangeably through a common interface
- Method Overloading -> same method name, different parameters (compile-time)
- Method Overriding -> subclass redefines a parent method (runtime)
- Full breakdown of SOLID principles and design patterns -> see [[OOP and SOLID Principles]]

## Data Structures & Algorithms
*Foundation for writing efficient code - a staple of technical interviews*
- Choice of data structure affects the time/space complexity of every operation built on top of it
- Full reference -> see [[Data Structures and Algorithms]]

## Agile & Scrum
*Most common framework for iterative software delivery*
##### Roles
- Product Owner -> owns the backlog and priorities
- Scrum Master -> facilitates the process and removes blockers
- Development Team -> builds the product increment
##### Ceremonies
- Sprint Planning
- Daily Standup
- Sprint Review
- Sprint Retrospective
- Backlog Refinement/Grooming
##### Artifacts
- Product Backlog
- Sprint Backlog
- Increment
- See [[QA Foundations]] `SDLC` for Waterfall and V-Model comparisons

## Version Control
*Tracking and coordinating changes to code over time*
- Full command reference -> see [[Git Commands]]
##### Common Branching Strategies
- Feature Branching -> one branch per feature, merged back via PR
- Git Flow -> long-lived `develop`/`main` branches plus feature/release/hotfix branches
- Trunk-Based Development -> short-lived branches merged frequently into `main`

## Databases
*Persisting and retrieving application data*
##### Relational (SQL)
- Structured, table-based, enforces a schema
- Full syntax reference -> see [[SQL]]
##### Non-Relational (NoSQL)
- Document (MongoDB), Key-Value (Redis), Column-family (Cassandra), Graph (Neo4j)
- Favors flexible schema and horizontal scalability over strict consistency
##### ACID Properties
- Atomicity -> a transaction fully completes or fully fails
- Consistency -> a transaction brings the database from one valid state to another
- Isolation -> concurrent transactions don't interfere with each other
- Durability -> committed data survives a crash
##### CAP Theorem
- In a distributed system, only 2 of 3 can be guaranteed at once:
	- Consistency -> every read gets the latest write
	- Availability -> every request gets a response
	- Partition Tolerance -> the system keeps working despite network failures
- Deeper dive on distributed data trade-offs -> see [[System Design Basics]]

## APIs & Web Fundamentals
##### HTTP Basics
- Client-server, request/response model
- Stateless -> each request contains all the information needed to process it
##### REST Principles
- Resource-based URIs (`/users/123`, not `/getUser?id=123`)
- Standard HTTP methods represent actions (GET, POST, PUT, PATCH, DELETE)
- Stateless and cacheable responses
- HTTP methods and status code reference -> see [[Test Types]] `API Testing`

## Software Architecture
*How systems are structured at a high level*
##### Client-Server Model
- Clients request services/resources; servers provide them
##### Monolith vs Microservices
- Monolith -> single deployable unit, simpler to start, harder to scale independently
- Microservices -> independently deployable services, easier to scale, adds operational complexity
##### MVC Pattern
- Model -> data and business logic
- View -> presentation layer
- Controller -> handles input and coordinates Model/View
- Full system design reference -> see [[System Design Basics]]

## Security Fundamentals
##### Authentication vs Authorization
- Authentication -> confirms who you are (login)
- Authorization -> confirms what you're allowed to do (permissions)
##### Common Vulnerabilities (OWASP Top 10 highlights)
- Injection (SQL, command)
- Cross-Site Scripting (XSS)
- Broken Authentication
- Sensitive Data Exposure
- Broken Access Control
##### Input Validation
- Never trust client-side input - validate and sanitize on the server

## Testing (Developer Perspective)
##### Unit Testing
- Testing individual functions/methods in isolation, typically written by developers
##### Test-Driven Development (TDD)
- Red -> write a failing test
- Green -> write the minimum code to make it pass
- Refactor -> clean up the code while keeping tests green
- Full testing methodology and levels -> see [[QA Foundations]] and [[Test Types]]
