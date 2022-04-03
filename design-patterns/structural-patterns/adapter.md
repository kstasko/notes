## Adaoter (139 - 150)
### creational pattern

---  

## *Intent*
Convert the interface of a class into another interface clients expect. Adapter lets clases work together that could not otherwise because of incompatible interfaces.

## Motivation
Sometimes a toolkit clas that is designed for reuse is not reusable only because its interface does not match the domain-specific interface an applciation requires.

> How can existing and unrelated classes work in an application that expects classes with a different and incompatible interface? We could change the clas so that it conforms to the interface, but that is not an option unless we have the toolkit's source code. Even if we did, it would not make sense to change the class; the toolkit should not have to adopt domain-specific interfaces just to make one application work.

Instead, we could define the class so that it *adapts* the interface to the class....
- by inheriting the class's interface and app's implementation
- by composing the app's instance with a app and implementing the class in terms of the app's interface

## Applicability
Use the `Adapter` pattern when:
- you want to use an exisitng class, and its interface does not match the one you need  
- you want to create a reusable class that cooperates with unrelated or unforeseen classes, that is, classes that do no necessarily have compatible interfaces.  
- *(object adapter only)* you need to use several existing subclasses, but it is impractical to adapt their interface by subclassing every one. An object adapter can adapt the interface of its parent

## Participants
- `Target` - defines the domain-specific interface that `Client` uses
- `Client` - collaborates with objects conforming to the `Target` interface
- `Adaptee` - defines an existing interface that needs adapting
- `Adapter` - adapts the interface of `Adaptee` to the `Target` interface

## Collaborations
- `Clients` call operations on an `Adapter` instance. In turn, the `Adapter` calls `Adaptee` operations that carry out the request

## Consequences
