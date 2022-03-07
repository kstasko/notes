# Domain-Driven Design Quickly
### by Abel Avram & Floyd Marinescu
---  

## Preface: Why DDD Quickly  
> When I noticed how Eric used the domain model to discuss solutions to some of the various technical challenges the group discussed,
> and how much emphasis he placed on the business domain instead of technology-specific hype,
> I knew right away that this vision is one that the community sorely needed.

> This book boes **not introduce any new concepts**; it attempts to concisely summarize the essence of what DDD is,
> drawing mostly on Eric Evans' original book on the subject, as well other resources since published.

---  
## Introduction 
Software has to be practical and useful; otherwise we would not invest so much time and resources into its creation...
- a useful package of software cannot be decoupled from that sphere of ( the domain it is supposed to help manage )
  - the software is deeply entangled with it
  
> “Domain-Driven Design: Tackling Complexity in the Heart of Software”, published by Addison- Wesley, ISBN: 0-321-12521-5.

---  
## What is Domain-Driven Design
In order to create good software, you have to know what that software is all about.
- You cannot create a banking software system unless you have a good understanding of what banking is all about, one must understand the *domain* of banking. 
- The banking system is very well understood [ by bankers ], by their specialists. They know all the details, all the catches, all the possible issues, all the rules.

When we begin a software project, we should focus on the `domain` it is operating in
- entire purpose of the software is to enhance a specific `domain`
- best way to make the software fit 'harmoniously' with the domain is to make it a reflection of the domain
- software has to model the domain

> We learn a lot about a domain while talking with the domain experts. But this raw knowledge is not going to be easily transformed into software constructs,
> unless we build an `abstraction` of it, a blueprint in our minds.

What is this abstraction?
- It is a model of the domain
- A Domain model is not a particular diagram; it is the idea that the diagram is inteded to convey.
- Not just the knowledge in a domain expert's head; it is a rigorously organized and selective abstraction of that knowledge.
