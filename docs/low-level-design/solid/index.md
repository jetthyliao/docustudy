# SOLID 

SOLID is a design principle for object oriented software development. It stands for the following: 

- Single Responsibility Principle
- Open-Closed Principle
- Liskov Substitution Principle
- Interface Segregation Principle
- Dependency Inversion

## Single Responsibility Principle

Single responsibility principle: a class should have one, and only one, reason to change. Each class should have one sole purpose (responsibility) and not be filled with excessive functionality. 

Advantages: 

- More organize
- If errors occur, they're isolated and smaller

## Open-Closed Principle

Open-closed principle: should be able to extend a class's behavior without modifying it. 

- Minimize risk of adding something that'll break code
- Less merge conficts
- Scalable

## Liskov Substitution Principle

Liskov substitution principle: every derived class should be substitutable for its parent class. 

- Minimize useless inheritance (some people do it just to reuse code)
- Integrate new objects faster

## Interface Segregation Principle

Interface segregation principle: make fine grained interfaces that are client-specific. Clients should not be forced to implement interfaces they do not use. 

- Goes with single responsibility (more organized) 
- Not forcing users to implement things they don't need == less code, less problems

## Dependency Inversion Principle

Dependency inversion principle: developers should depend on abstractions, not on concretions. High level modules should not depend upon low level modules. Both should depend on abstractions. Abstractions should not depend on details, details should depend upon abstractions. 

- Decouples code 
