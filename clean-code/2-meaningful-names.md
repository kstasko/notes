# chapter 2: Meaningful Names
---  

## Use Intention-Revealing Names
### names should reveal intent
The name of a variable, function, or class, should answer all the big questions. It should tell you:
- why it exists;
- what it does;
- how it is used

#### example of bad variable name
```java
int d; //elapsed time in days
```
_If a name requires a comment, then the name does not reveal its intent. The name `d` reveals nothing._

#### example of variable names with intent
```java
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays
```

## Avoid Disinformation
> _Do not refer to a grouping of accounts as an `accountList` unless it is actually a `List.` It's probably better not to encode the container type into the name even. The word list means something specific to programmers. If the container holding the accounts is not actually a `List`, it may lead to false conclusions. So `accountGroup` or `bunchOfAccounts` or just plain `accounts` would be better.

Spelling similar concepts similarly is _information_. Using inconsistent spellings is _disinformation_.

## Make Meaningful Distinctions
Programmers create problems for themselves when they write code solely to satisfy a compiler or interpreter
- It is not sufficient to add number serires or noise words
- If names must be different, then they should also mean something different.

> In the absence of specific convetnions, the variable `moneyAmount` is indistinguishable from `money`, `customerInfo` is indistinguishable from `customer`, `accountData` is indistinguishable from `account`, and `theMessage` is indistinguishable from `message`. *Distinguish names in such a way that the reader knows what the differences offer.

## Use Pronounceable Names
If you cannot pronounce it, you cannot discuss it without sounding like an idiot. *Programming is a social acivity.*

## Use Searchable Names
Single-letter names and numeric constants have a particular problem in that they are not easy to locate across a body of text.
- longer names trump shorter names, and any searchable name trumps a constant in code

> My personal preference is that single-letter names can ONLY be used as local variables inside short methods. _The length of a name should correspond to the size of its scope._

## Avoid Encoding
We have enough encodings to deal with without adding more to our burden. Encoding type or scope information into names simply adds an extra burden of deciphering.

### Hungarian Notation
Nothing of note, maybe look into difference between `integer handle`, `long pointer` and `void pointer`

### Member Prefixes
You do not need to prefix member variables with `m_` anymore. Your classes and functions should be small enough that you do not nee them. And you should be using an editing environment that highlights or colorizes embers to make them distinct. 

## Avoid Mental Mapping
Readers should not have to mentally transalte your names into other names they already know.
- This problem generally arises from a choice to use neither problem domain term nor solution domain terms.
- There can be no worse reason for using the name `c` than because `a` and `b` were already taken

*Clarity is King*

## Class Names
Classes and objects should have noun or noun phrase names like `Customer`, `WikiPage`, `Accounts`, and `AddressParser`. Avoid words like `Manager`, `Processor`, `Data`, or `Info` in the name of a class. A class name should not be a verb.

## Method Names
Methods should have verb or verb phrase names like `postPayment`, `deletePage`, or `save`.

Accessors, mutators and predicates should be named for their value and prefixed with `get`, `set`, and `is` according to [javabean standard](http://java.sun.com/products/javabeans/docs/spec.html)

When constructuors are overloaded, use static factory methods with names that describe the arguments

## Don't be Cute
Say what you mean, mean what you say.

## Pick One Word per Concept
A consistent lexicon is a great boon to the programmers who must use your code

## Don't Pun
Avoid using the same word for two purposes.
- using the same term for two different ideas is essentially a pun

> However one might decide to use the word `add` for "consistency" when they are not in fact adding in the same sense. Let us say we ahve many classes where `add` will create a new value by adding or concatenating two existing values. Now let us say we are writing a new class that has a method that puts its single parameter into a collection. Should we call this method `add`? It might seem consistent because we have so many other `add` methods, but in this case, the semantics are different, so we should use a name like `insert` or `append` instead. to call the new method would be a pun.

## Use Solution Domain names
Remember that the people who read your code will be programmers. So use computer science terms, algorithm names, pattern names, math terms, etc.

## Use Problem Domain names
When there is no "programmer-eese" for what you are doing, use the name of the problem domain. 

> Separating solution and problem domain concepts is part of the job of a good programmer and designer. The code that has more to do with problem domain concepts should have names drawn from the problem domain.

## Don't Add Gratuitous Context
In an imaginary app called "Gas Station Deluxe" it is a bad idea to prefix every class with GSD