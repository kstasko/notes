## Builder (87 - 97)
### creational pattern

---  

## *Intent*
Provide an interface for creating families of related or dependent objects without specifying their concrete classes.

## *Motivation*
Instantiating look-and-feel-specific classes of wigets throughout [ an ] application makes it hard to change the look and feel later.
There is a concrete subclass for each type type of [ Factory ]. A [AbstractFactory ] also encforces dependencies between the concrete builder classes. 

## *Applicability*
Use the `Abstract Factory` pattern when:
- a system should be independent of how its products are created, composed, and represented.
- a system should be configured with one of multiple families of products.
- a family of related product objects is designed to be used together, and you need to enforce this constraint.
- you want to provide a class library of products, and you want to reveal just their interfaces, not their implementations.

## *Participants*
- `AbstractFactory` 
    - declares an interface for operations that create abstract product objects.
- `ConcreteFactory` 
    - implements the operations to create concrete product objects.
- `AbstractProduct` 
    - declares an interface for a type of product object.
- `ConcreteProduct` 
    - defines a product object to be created by the correspeonding concrete factory.
    - implements the `AbstractProduct` interface.
- `Client`
    - uses only interfaces declared by `AbstractFactory` and `AbstractProduct` classees

## *Consequences*
- normally a single instance of a `ConcreteFactory` class is created at run-time. This concrete factory creates product objects having a particular implementation. TO create different preoduct objects, clients hsould use a different concrete factory.
- `AbstractFactory` defers creation of product objects to its `ConcreteFactory` subclass

## *Consequences*
- It isolates concrete clases - because it encapsulates the responsibility and the process of creating product objects, it isolates clients from implementation classes. 
- It makes exchanging product families easy - The class of a concrete factory appears only once in an application when it is instantiated. It can use different product configurations simply by changing the concrete factory. 
- It promotes consistency among products
- Supporting new kinds of products is difficult - Because the interfaces fixes the set of products that can be created. 

## *Questions*
- they recc using Factories as Singletons....are there other examples that come to mind that one pattern is an example of another?
    - can Factory Methods somehow be an example of an `AbstractFactory`?
- What is the core diffference between `AbstractFactory` and `the `Factory Method`?
