## Singleton (127 - 136)
### creational pattern
---  

#### Intent
Ensure a lass only has one instance, and proide a global point of access to it

> How do we ensure that a class has only one instance and that the instance is easily accesible?...
> ...make the class itself responsible for keeping track of its sole instance (by intercepting requests to create new objects), and it 
> can provide a way to access the instance.

#### **Applicability**
Use the `singleton` pattern when:
- there must be exactly one instance of a class, and it must be accesible to clients from a well-known access point
- when the sole instance should be extensible by subclassing, and clients should be able to use an extended instance without modifying their code.

#### **Consequences**
- controlled acces to sole instance
- reduced name space`
- permits refinement of operations and representation
- permits a variable number of instances
- more flexible than class operations

### **Implementation**
**Ensuring a unique instance**
> The singleton pattern makes the sole instance a normal instance of a clas, but that class is written so that only one instance can ever be created.
> A common way to do this is to hide the operation that creates the instance behind a class operation (static member function) that gauruntees only once instance is created.

`Notice the constructor is protected. A client that tries to instantiate Singleton direcly will get an error at compile-time.`

It is not enough to define the singleton as a global or static object and then rely on automatic intialization
- cannot guarantee that only one instance of a static object will ever be declared
- might not have enough information to instantiate every singleton at static initialization time. It might require values that are computed later in the program's execution
- C++ does not define the order in which constructors for global objects are called across translation units. This means taht no dependencies can exist between singletons; if any do, then errors are inevitable

**Subclassing the Singleton class**
- take the implmentation of `Instance` out of the parent class and put it in the subclass
- registry of singletons