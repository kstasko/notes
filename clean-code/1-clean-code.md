# chapter 1: clean code 

> By the time [we are done with this book], we're going to know a lot about code. What's more, we'll be able to tell the difference between good code and bad code.
---  
---
## there will be code  
> We will never be rid of code, because code represents the details of the requirements. At some level those details cannot be ignored or abstracted; they have to be specified. And specifying requirements in such detail that a machine can execute them IS PROGRAMMING.

> Remember that code is really the language in which we ultimately express the requirements. We may create languages that are closer to the requirements. We may create tools that help us parse and assemble those requirements into formal structures. But we will never eliminate necessary precision--so there will always be code.
---  
## bad code
> Have _you_ ever been significantly impeded by bad code?...We call it _wading_. We wade through bad code. We slog through a morass of tangled brambles and hidden pitfalls. We struggle to find our way, hoping for some hint, some clue of what is going on; but all we see is more and more senseless code.
---  
## the total cost of owning a mess
> As the mess builds, the productivity of the team continues to decrease, asymptotically approaching zero. As productivity decreases, management does the only thing they can; they add more staff to the project in hopes of increasing productivity. 
---  
## the grand redesign in the sky
> eventually the team rebels. They inform management that they cannot continue to develop in this odious code base. They demand a redesign. Management does not want to expend the resources on a whole new redesign of the project, but they cannot deny that productivity is terrible. Eventually [management] bends to the demands of the developers and authorize the grand redesign in the sky.
---
## attitude
>We are unprofessional...The managers and marketers look to _us_ for the information they need to make promises and commitments; and even when they don't look to us, we should not be shy about telling them what we think.

>The _only_ way to make the deadline--the only way to go fast--is to keep the code as clean as possible at all times.
--- 
## the art of clean code?
> the bad nes is that writing clean code is a lot like painting a picture. Most of us know when a picture is painted well or badly. But being able to recognize good art from bad does not mean that we know how to pain

> Writing clean code requires the disciplined use of a myriad little techniques applied through a painstakingly acquire sense of "cleanlines".
---
## what is clean code?
**Bjarne Stroustrup, inventor of C++**  
>_I like my code to be elegane and efficient. The logic should be straightforward to make it hard for bugs to hide, the dependencies minimal to ease maintenance, error handling complete according to an articulated strategy, and performance close to optimal so as not to tempt people to make the code messy with unprincipled optimizations. Clean code does one thing well._   

**Grady Booch, author of Object Oriented Analysis and Design with Application**
> _Clean code is simple and direct. Clean code reads like well-written prose. Clean code never obscures the designer's intent but rather is full of crisp abstractions and straightforwards lines of control_

**"Big" Dave Thomas, founder of OTI, godfather of the Eclipse strategy**
> _Clean code can be read and enhanced by a developer other than its original author. It has unit and acceptance tests. It has meaningful names. It provides one way rather than many ways for doing one thing. It has minimal dependencies, which are explicitly defined, and provides a clear minimal API. Code should be literate since depending on the language, not all necessarry information can be expressed clearly in code alone._

**Michael Feathers, author of Working Effectively with Legacy Code**
> _