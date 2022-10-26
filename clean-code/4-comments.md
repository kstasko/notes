# Comments
---  
> The proper use of comments is to compensate for our failure to express ourself in code. 
- comments are always failures.
- We must have them because we cannot always figure out how to express ourselves without them, but their use is not a cause for celebration.

When you find yourself in a position you need to write a comment, think it through and see whether there is not some way to turn the tables and express yourself in code.

> Everytime you express yourself in code, you should pat yourself on the back. Every time you write a comment, you should grimace and feel the failure of your ability of expression. 

The older a comment is, and the farther away it is from the code it describes, the more likely it is to be just plain wrong. 

## Comments Do Not Make Up for Bad Code
Clear and expressive code with few comments is far superior to cluttered and complex code with lots of comments. 
Rather than spend your time writing comments that explain the mess you have made, sped it cleaning the mess.

```java
// Check to see if the employee is eligible for full benefits
if ((employee.flags & HOURLY_FLAG) &&
    (employee.age > 65))
```
versus

```java
if (employeee.isEligibleForFullBenefits())
```

## Good Comments
### Legal Comments
Copyright and authorship statements are necessary and reasonable things to put into a comment at the start of each source file.
- these should NOT be contracts or legal tomes
- where possible, refer to a standard license or other external docs rather than putting all the terms and conditions into the comment

### Informative Comments
It is sometimes useful to provide basic information with a comment...but it is better to [let your code] convey the information where possible

### Explanation of Intent
Sometimes a comment goes beyond just useful information about the implementation and provides the intent behind a decision. 

### Clarification
> Sometimes it is just helpful to translate the meaning of some obscure argument or return value into something that's readable. In general it is better to find a way to make that argument or return value clear in its own right; but when its part of the standard library, or in code that you cannot alter, then a helpful clarifying comment can be useful.

```java
public void testCompareTo() throws Exception
{
    WikiPagePath a = PathParser.parse("PageA");
    WikiPagePath ab = PathParser.parse("PageA.PageB");
    WikiPagePath b = PathParser.parse("PageB");
    WikiPagePath aa = PathParser.parse("PageA.PageA");
    WikiPagePath bb = PathParser.parse("PageB.PageB");
    WikiPagePath ba = PathParser.parse("PageB.PageA");

    assertTrue(a.compareTo(a) == 0);        // a == a
    assertTrue(a.compareTo(b) != 0);        // a != a
    assertTrue(ab.compareTo(ab) == 0);      // ab == ab
    assertTrue(a.compareTo(b) == -1);       // a < b
    assertTrue(aa.compareTo(ab) == -1);     // aa < ab
    assertTrue(ba.compareTo(bb) == -1);     // ba < bb

            ....page 57
}
```

> Go through this example and see how diffficult it is to verify that the comment matches the code. There is a substantial risk that a clarifying comment is incorrect.

### Warning of Consequences

```java
public static SimpleDateFormat makeStandardHttpDateFormat()
{
    //SimpleDateFormat is not thread safe,
    //so we need to create each instance independently.
    SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
    df.setTimeZone(TimeZone.getTimeZone("GMT"));
    return df
}
```
The comment, as given here, is perfectly reasonable. It will prevent some overly eager programmer from using a static initializer in the name of efficiency.

### TODO comments
These are acceptable but you do not want your code to be littered with `TODO`s. So scan through them regularly and eliminate the ones you can.

---  
## Bad Comments
> Most comments fall into this category. Usually they are crutches or excuses for poor code or justifications for insufficient decisions, amounting to little more than the programmer talking to himself.

### Mumbling
If you decide to write a comment, then spend the time necessary to make sure it is the best comment you can write.

### Redundant Comments
Let your code talk and read better rather than have comments that repeat what the code is saying.

### Mandated Comments
> It is just plain silly to have a rule that says that every function must have a javadoc, or every variable must have a comment. Comments like this just clutter up the code, propagate lies and lend to general confusion and disorganization.

### Journal Comments
Sometimes people add a comment to the stat of a module every time they edit it. These comments accumulate as a kind of journal of every change that has ever been made. Don't do this

> Replace the temptation to create noice with the determination to clean your code. You'll find it makes you a better and happier programmer.

### Do not Use a Comment When You Can Use a Function or a Variable
```java
//does the module from the global list <mod> depend on the
// subsystem we are part of?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))
```
could be rephrased without the comment as...
```java
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSystems = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
```

### Position Markers
`// Actions ////////////////////////////`  
There are rare times when it makes sense to do this.

> Think of it this way. A banner is startling and obvious if you do not see banners very often. So use them very sparingly and only when the benefit is significant. If you overuse banners, they will fall into the background noise and be ignored.

### Closing Brace Comments
If you find yourself wanting to mark your closing braces, try to shorten your functions instead.

### Commented-Out Code
There was a time back in the sixties, when commenting-out code might have been useful. But we have good source code control systems for a very long time now. THose systems will remember the code for us.

### HTML in Source Code Comments is an abomination

### Nonlocal Information
If you must write a comment, then make sure it describes the code it appears near. Do not offer systemwide information in the context of a local comment. 

### Too Much Information
Do not put interesting historical discussions or irrelevant descriptions of details into your comments. 

### The Connection between a comment and the code it describes should be obvious
