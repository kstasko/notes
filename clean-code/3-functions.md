# chapter 3: Functions
---  
> _What is it that makes a function...easy to read and understand? How can we make a function communicate its intent? What attributes can we give our functions that will allow a casual reader to intuit the kind of program they live inside?_

## Small!
> The first rule of functions is that they should be small. The second rule of functions is that _they should be smaller than that_.

Functions should not be 100 lines long. Functions should hardly EVER be 20 lines long.

### Blocks and Indenting
Blocks within `if` statement, `else` statements, `while` statements and so on should be one line long.
- this keeps the enclosing function small, but it also adds documentary value because the fucntion called within the block can have a nice descriptive name.

Functions should not be large enough to hold nested structures.
- the indent level of a function should not be greater than one or two.

## Do One Thing
> FUNCTIONS SHOULD DO ONE THING. THEY SHOULD DO IT WELL. THEY SHOULD DO IT ONLY.

If a function does only the steps that are one level below the stated name of the function, then the function is doing one thing.

Another way to know that a function is doing more than "one thing" is if you can extract another function from it with a name thatis not merely a restatement of its implementation.
- Functions that do one thing cannot reasonably be divivided into sections.

## One level of Astraction per Function
In order to make sure our functions are doing "one thing", we need to make sure that the statements within our function are all at the same level of abstraction.
- What does this mean?

> It is easy to see how Listing 3-1 viiolates this rule. There are concepts in there that are at a very high level of abstraction, such as `getHtml()`; others that are at an intermediate level of abstraction, such as : `String pagePathName = PathParser.render(pagePath)`; and still others that are remarkably low level, such as : `.append("\n")`

## Reading Code from Top to Bottom: _The Stepdown Rule_
> _We want the code to read like a top-down narrative. We want every function to be followed by those at the next level of abstraction so that we can read the program, descending one level of abstraction at a time as we read down the list of functions.
- We want to be able to read the program as though it were a set of TO paragraphs, each of which is describing the current level of abstraction and referencing subsequent TO paragraphs at the next level down

## Switch Statements
It's hard to make a small `switch` statement...that does on thing. By their nature, `switch` statements always do _N_ things. We cannot always avoid `switch` statements, but we _can_ make sure that each `switch` statment is buried ina  low-level class and is never repeated. We do this with polymorphism.
- General rule for `switch` statements is that they can be tolerated if they appear only once, are used to create polymorphic objects, and are hidden behind an inheritance relationship so that the ret of the system cannot see them.

```java
public Money calculatePay(Employee e)
throws InvalidEmployeeType {
    switch (e.type) {
        case COMMISSIONED:
            return calculateCommissionedPay(e);
        case HOURLY:
            return calculateHourlyPay(e);
        case SALARIED:
            return calculateSalariedPay(e);
        default:
            throw new InvalidEmployeeType(e.type);
    }
}
```

becomes....

```java
public abstract class employee {
    public abstract boolean isPayday();
    public abstract Money calculatePay();
    public abstract void delivery(Money pay);
}
--------------------
public interface EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}
--------------------
public class EmployeeFactoryImpl implements EmployeeFactory {
    public Employee makeEmployee(EmployeeRedorc r) throws InvalidEmployeeType {
        switch (r.type) {
            case COMMISSIONED:
                return new CommissionedEmployee(r) ;
            case HOURLY:
                return new HourlyEmployee(r) ;
            case SALARIED:
                return new Salaried Employee(r) ;
            default:
                throw new InvalidEmployeeTye(r.type) ;
        }
    }
}
```

## Use Descriptive Names
> _"You know you are working on clean code when each routine turns out to be pretty much what you expected._" Half the battle to achieving that principle is choosing good names for small functions that do one thing. The smaller and more focused a function is, th easier it is to choose a descriptive name.

## Function Arguments
> The ideal number of arguments for a function is zero (nildic). Next comes one (monodic), followed closely by two (dyadic). Three arguments(triadic should be avoided where possible. More than three (polyadic) requires special justification and then should not be used anyways.

### Common Monadic Forms
There are two very common reasons to pass a single argument into a function
- you may be asking a question about that argument
- you may be operating on that argument, transforming it into something else and _returning it_

> These two uses are what readers expect when they see a fucntion. You should choose names that make the distinction clear, and always use the two forms ina  consistent context.

Somewhat less common, but still very useful form for a single argument function, is an _event_
- there is an `input` argument but no `output`
- the overall program is meant to interpre the function call as an event and use the arugment to alter the state of the system

#### Avoid any monadic functions that don't follow these forms.

### Flag Arguments
Just dont......

### Dyadic Functions
> A function with two arguments is harder to understand than a monadic function. For example, `writeField(name)` is easier to understand than `writeField(output-Stream, name)`. Though the meaning of both is clear, the first glides past the eye, easily depositing its meaning. The second requires a short pause until we learn to ignore the first parameter. And _that_, of course, eventually results in problems becuase we should never ignore any part of code. The parts we ignore are where the bugs will hide.

Dyands are not evil, and you will certainly have to write them. However, you should be aware that they come at a cost and should take advantage of what mechanisms may be available to you to convert them into monads.

### Triads
Functions that take three arguments are significatnly harder to understand than dyands. The issues of ordering, pausing and ignoring are more than doubled.

### Argument Objects
When a function seems to need more than two or three arguments, it is likely that some of those arguments ought to be wrapped into a class of their own.

```java
    Circle makeCircle(double x, double y, double radius);
    //Versus
    Circle makeCircle(Point center, double radius)
``
Reducing the number of arguments by creating objects out of them may seem like cheating, but it is not. WHen groups of variables are passed together the way x and y are in the example above, they are likely part of a concept tha deserves a name of its own.

### Verbs and Keywords
Choosing good names for a function can go a long way toward explaining the intent of the function and the order and intent of the argument.
- Monads, the function  and argument should form a nice verb/noun pair (e.g. `write(name)`)

## Have No Side Effects
> Side effects are lies. Your function promises to do one thing, but it also does other _hidden_ things. 

## Output Arguments
In general output argumnts should be avoided. If your function must change the state of something, have it change the state of its owning object.

## Command Query Separation
Functions should either do something or answer something, but not both. Either your function should change the state of an object, or it should return some information about that object. Doing both often leads to confusion.

```java
    public boolean set(String attribute, String value);
    // This function sets the value of a named attribute and returns true if it is successful and false if no such attribute exists.
    //This leads to odd statements like....
    if (set("username"), "unclebob"))....
```
The author intended `set` to be a verb, but in the context of the `if` statement it _feels_ like an adjective. 
So the statement reads as "If the `username` attribute was previously set to `unclebob`" and not "set the `username` attribute to `unclebob` and if that worked then..."

## Prefer Exceptions to Returning Error Codes
Returning error codes from command functions is a subtle violation of command query separation. It promotes commands being used as expressions in the predicates of `if` statements...

`if (deletePage(page) == E_OK)`  
This does not suffer from ver/adjective confusion but does lead to deeply nested structures.
- When you return an error code, you create the problem that the caller must deal with the error immediately

```java
if (deletePage(page) == E_OK ) {
    if (registry.deleteReference(page.name) == E_OK) {
        if (configKey.deleteKey(page.name.makeKey()) == E_OK){
        logger.log("page deleted");
    } else {
        logger.log("configKey not deleted");
    }
} else {
    logger.log("deleteReference from registry failed");
} else {
    logger.log("delete failed");
    return E_ERROR
}
```
becomes....

```java
    try {
        deletePage(page);
        registry.deleteReference(page.name);
        configKeys.deleteKey(page.name.makeKey());
    }
    catch (Exception e) {
        logger.log(e.getMessage());
    }
```
### Extract Try/Catch Blocks
`try/catch` blocks are ugly in their own right. They confuse the structure of the code and mix error processing with normal processing. So it is better to extract the bodies of the `try` and `catch` blocks out into functions of their own.

### Error Handling is One Thing
Fucntions should do one thing. Error handling is one thing. 
- this implies that if the keyword `try` exists in a function, it should be the very first word in the function and that there should be nothing after the `catch/finally` blocks.

### The `Error.java` Dependency Magnet
> Programmers do not want to add new errors because then they have to rebuild and redeplooy everything. So they reuse old error codes instead of adding new ones. When you use exceptions rather than error codes, then the new exceptions are _derivativves_ of the exception class. They can be added without forcing any recompilation or redeployment.

## Don't Repeat Yourself
Duplication may be the root of all evil in software. Many principals and practices have been created for the purpose of controlling or eliminating it. 

## Structure Programming
If you keep your functions small, then the occasional multiple `return`, `break` or `continue` statement does no harm and can sometimes even be more expressive than the single-entry, single-exit rule. 
- `goto` only makes sense in large functions, so it should be avoided.

## How Do You Write Functions Like This?
Rough Draft. Iterate.

