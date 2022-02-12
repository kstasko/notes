## Factory Method (107 - 117)
#### *creational pattern*
---  
*also known as `Virtual Constructor`

### *Intent*
define an interface for creating an object, but let subclasses decide which class to instantiate. 
`Factory Method` lets a class defer instantiation to subclasses.

### *Motivation*
Frameworks use abstract classes to define and maintain relationships between objects. A framework is responsible for creeating these objects as well...  
The framework must instantiate classes, but it only knows about abstract classes, which it cannot instantiate.

> The `Factory Method` pattern offers a solution. It encapsulates the knowledge of which abstract class to create and moves the knowledge out of the framework.

### *Applicability*
Use the `Factory Method` when:
- a class cannot anticipate the class of objects it must create
- a class wants it subclasses to specify the object it creates
- classes delegate responsibility to one of several helper subclases, and you want to localize the knowledge of which helper subclass is the delegate.

### *Participants*
- `Product` - defines the interface of objects the factory method creates  

- `Concrete Product` - implements the `Product` interface

- `Creator` - declares the factory method, which returns an object of type `Product`. 
    - `Creator` may also define a default implementation of the `Factory Method` that returns a default `ConcreteProduct` object.

- `ConcreteCreator` - overrides the factory method to return an instance of a `ConcreteProduct`

### *Collaborations*
`Creator` relies on its subclasses to define the factory method so that it returns an instance of the appropriate `ConcreteProduct`

### *Consequences*
- *eliminate the need to bind application-specific classes into your code*
    - code only deals with the `Product` interface, therefore t can work with any user-defined `ConcreteProduct` classes.
- *Provide hooks for subclasses*
    - creating objects inside a class with a `Factory Method` is always mroe flexible than creating an object directly. 
    - `Factory Method` gives subclases a hook for providing an extended version of an object.
- *Connects parallel class hierarchies*
    - `parallel hierarchies` result when a class delegates some of its responsibilities to a separate class. 

**Disadvantage**
- clients might have to subclass the `Creator` class just to create a particular `ConcreteProduct` object. 

### *Implementation*
- *Two major varieties*
    - when the `Creator` class is an abstract class and does not provide an implementation for the `Factory Method` it declares
        - requires subclasses to define an implementation, because there is no reasonable default
    - when the `Creator` class is a concrete class and provides a default implementation for the `Factory Method`
        - create objects in a separate operation so that subclasses can override the way they are created
- *Parameterized Factory Method*
    - lets the `Factory Method` create multiple kinds of products
- *Language-specific variants and issues*
- *Using templates to avoid subclassing*
- *Naming convention*
