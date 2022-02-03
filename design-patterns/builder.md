## Builder (97 - 106)
### creational pattern

---  

## *Intent*
Separate the construction of a complex object from its representation so that the same construction process can create different representations

## *Applicability*
Use the `Builder pattern` when:
- the algorithm for creating a complex object should be independent of the parts that make up the object and how they are assembled  

- the construction process must allow diferent representations for the object that is contructed

## *Participants*
**`Builders`** 
- specifies an abstract interface for creating parts of a Product object  

**`ConcreteBuilder`**
- constructs and assebles parts of the product by implementing the `Builder` interface
- defines and keeps track of the representation it creates
- provides an interface for retrieving the product

**`Director`**
- constructs an object using the `Builder` interface

**`Product`**
- represents the complex object under construction. ConcreteBuilder builds the product's internal representation and defines the process by which it is assembled
- includes classes that define the constituent parts, including interfaces for assembling the parts into the final result

## *Collaborations*
- the client creates the `Director` object and configures it with the desired `Builder` object
- `Director` notifies the builder whenever a part of the product should be built
- `Builder` handles requests from the director and adds parts to the product
- the client retrieves the product from the builder

## *Consequences*
- It lets you vary a product's internal representation
- It isolates code for construction and representation
- It gives you finer control over the construction process

> Unlike creational patterns that construct products in one shot,
> the `Builder pattern` constructs the product step by step under the `Director`'s control 

## *Implementation*
Typically there is an abstract `Builder` class that defines an operation for each component that a director may ask it to create. The operations do nothing by default. 

A `ConcreteBuilder` class overrides operations for components it is interested in creating.

### *Assembly and Construction*
- `Builder` class must be general enough to allow the construction of products for all kinds of concrete builders

> A key design issue concerns the model for the construction and assembly process. 
> A model where the results of construction requests are simply appended to the product is usually sufficient. 

### *Why no abstract class for products?*
The products produced by the `ConcreteBuilders` differ so greatly in their representation that there is little to gain from giving different products a common parent class.

### *Empty methods as default in Builder*
letting clients override only the operations they are interested in

---

> `Abstract Factory` is similar to `Builder` in that it too may construct complex objects. 
> The primary difference is that the `Builder` pattern focuses on constructing a complex object step by step.
> `Abstract Factory's` emphasis is on families of product objects (either simple or complex). 
> `Builder` returns the product as a final step, but as the `Abstract Family` pattern is concerned, the product gets returned immediately.
---  



## *Questions*
Is the constructor class an example of the `Builder pattern`?
Is there too simple of an object to use this on? Is two representations enough to justify this? e.g. coin?
