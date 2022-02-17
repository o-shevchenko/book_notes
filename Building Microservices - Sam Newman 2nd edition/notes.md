# Building Microservices - Sam Newman 2nd edition

## Chapter 1. What Are Microservices?

Microservices are independently releasable services that are modeled around a business domain.
James Lewis, technical director at Thoughtworks, has been known to say that “a microservice should be as big as my head.”
Chris Richardson, the author of Microservice Patterns (Manning Publications), once said—the goal of microservices is to have “as small an interface as possible.”

I urge people not to worry about size. When you are first starting out, it’s much more important that you focus on two key things. First, how many microservices can you handle? As you have more services, the complexity of your system will increase, and you’ll need to learn new skills (and perhaps adopt new technology) to cope with this. A move to microservices will introduce new sources of complexity, with all the challenges this can bring. It’s for this reason that I am a strong advocate for incremental migration to a microservice architecture. Second, how do you define microservice boundaries to get the most out of them, without everything becoming a horribly coupled mess? These topics are much more important to focus on when you start your journey.

We don’t know what the future holds, so we’d like an architecture that can theoretically help us solve whatever problems we might face down the road. Finding a balance between keeping your options open and bearing the cost of architectures like this can be a real art.

Think of adopting microservices as less like flipping a switch, and more like turning a dial. As you turn up the dial, and you have more microservices, you have increased flexibility. But you likely ramp up the pain points too. This is yet another reason I strongly advocate incremental adoption of microservices. By turning up the dial gradually, you are better able to assess the impact as you go, and to stop if required.

I don’t think you need to adopt lots of new technology when you first start using microservices.

the implementation of a log aggregation system as a prerequisite for adopting a microservice architecture.

You can make these log aggregation tools even more useful by implementing correlation IDs, in which a single ID is used for a related set of service calls—for example, the chain of calls that might be triggered due to user interaction. By logging this ID as part of each log entry, isolating the logs associated with a given flow of calls becomes much easier, which in turn makes troubleshooting much easier.

Microservices may well give you the option for each microservice to be written in a different programming language, to run on a different runtime, or to use a different database—but these are options, not requirements. You have to carefully balance the breadth and complexity of the technology you use against the costs that a diverse array of technology can bring.

So are microservices a way to drive more profits? Perhaps. Are microservices a way to reduce costs? Not so much.

Although it can be difficult to measure the exact impact on latency of operations at the design or coding phase, this is another reason it’s important to undertake any microservice migration in an incremental fashion. Make a small change and then measure the impact.

Adopting an incremental approach to decomposition, so that you are able to assess the impact of changes to your architecture in production, is really important.

Given the importance of defining stable service boundaries, I feel that microservice architectures are often a bad choice for brand-new products or startups.

In general, I feel it’s more appropriate to wait until enough of the domain model has stabilized before looking to define service boundaries.

The process of finding product market fit means that you might end up with a very different product at the end than the one you thought you’d build when you started.

## Chapter 2. How to Model Microservices
The ability to change one microservice in isolation from another is vital

### Information Hiding
Improved development time
By allowing modules to be developed independently, we can allow for more work to be done in parallel and reduce the impact of adding more developers to a project.

### Comprehensibility
Each module can be looked at in isolation and understood in isolation. This in turn makes it easier to understand what the system as a whole does.

### Flexibility
Modules can be changed independently from one another, allowing for changes to be made to the functionality of the system without requiring other modules to change. In addition, modules can be combined in different ways to deliver new functionality.

A lot depends on how the module boundaries are formed. ... information hiding is a key technique to help get the most out of modular architectures.

### Cohesion
“the code that changes together, stays together.”

### Coupling
The whole point of a microservice is being able to make a change to one service and deploy it without needing to change any other part of the system.
This also means we probably want to limit the number of different types of calls from one service to another, because beyond the potential performance problem, chatty communication can lead to tight coupling.

### The Interplay of Coupling and Cohesion
Constantine’s law, named for structured design pioneer Larry Constantine, sums this up neatly:
"A structure is stable if cohesion is strong and coupling is low"

Cohesion applies to the relationship between things inside a boundary (a microservice in our context), whereas coupling describes the relationship between things across a boundary.
There is no absolute best way to organize our code; coupling and cohesion are just one way to articulate the various trade-offs we make around where we group code, and why.

Remember, the world isn’t static—it’s possible that as your system requirements change, you’ll find reasons to revisit your decisions. Sometimes parts of your system may be going through so much change that stability might be impossible.

### Types of Coupling

### Domain Coupling
Domain coupling describes a situation in which one microservice needs to interact with another microservice, because the first microservice needs to make use of the functionality that the other microservice provides.
In a microservice architecture, this type of interaction is largely unavoidable. A microservice-based system relies on multiple microservices collaborating in order for it to do its work. We still want to keep this to a minimum, though; whenever you see a single microservice depending on multiple downstream services in this way, it can be a cause for concern—it might imply a microservice that is doing too much.
As a general rule, domain coupling is considered to be a loose form of coupling, although even here we can hit problems. A microservice that needs to talk to lots of downstream microservices might point to a situation in which too much logic has been centralized.
Just remember the importance of information hiding. Share only what you absolutely have to, and send only the absolute minimum amount of data that you need.

### Temporal Coupling
temporal coupling refers to a situation in which concepts are bundled together purely because they happen at the same time. Temporal coupling has a subtly different meaning in the context of a distributed system, where it refers to a situation in which one microservice needs another microservice to do something at the same time for the operation to complete.
Temporal coupling isn’t always bad; it’s just something to be aware of. As you have more microservices, with more complex interactions between them, the challenges of temporal coupling can increase to such a point that it becomes more difficult to scale your system and keep it working. One of the ways to avoid temporal coupling is to use some form of asynchronous communication, such as a message broker.

### Pass-Through Coupling
“Pass-through coupling”9 describes a situation in which one microservice passes data to another microservice purely because the data is needed by some other microservice further downstream.
The major issue with pass-through coupling is that a change to the required data downstream can cause a more significant upstream change.
There are a few ways this can be fixed. The first is to consider whether it makes sense for the calling microservice to just bypass the intermediary.

### Common Coupling
Common coupling occurs when two or more microservices make use of a common set of data.
The main issue with common coupling is that changes to the structure of the data can impact multiple microservices at once.
If the schema of this database changed in a backward-incompatible way, it would require changes to each consumer of the database. In practice, shared data like this tends to be very difficult to change as a result.
If the schema of this database changed in a backward-incompatible way, it would require changes to each consumer of the database. In practice, shared data like this tends to be very difficult to change as a result.
Sources of common coupling are also potential sources of resource contention. Multiple microservices making use of the same filesystem or database could overload that shared resource, potentially causing significant problems if the shared resource becomes slow or even entirely unavailable

### Content Coupling
Content coupling describes a situation in which an upstream service reaches into the internals of a downstream service and changes its internal state.
When you allow an outside party to directly access your database, the database in effect becomes part of that external contract, albeit one where you cannot easily reason about what can or cannot be changed. You’ve lost the ability to define what is shared (and therefore cannot be changed easily) and what is hidden. Information hiding has gone out of the window.

### Code duplication
As I have said many times before, I am fairly relaxed about duplicated code across services. Which is to say that while in a single process boundary I will typically do whatever I can to refactor out duplication into suitable abstractions, I don't have the same reaction when confronted by duplication across services. This is mostly as I am often more worried about the potential for extracting shared code to lead to tight coupling between services - something I am more worried about than duplication in general. That said, there are certainly cases where this is warranted.
When the time does arise to extract shared code, there are two obvious options. The first, which is often cheapest but more fraught, is to extract a shared library of some sort. The reason this can be problematic is that shared libraries are a prime source of coupling, especially when used to generate client-libraries for calling downstream services. Nonetheless there are situations where this feels right - especially when the code being abstracted is purely a concern inside the service.
The other option is to extract out the shared functionality in a new service, which can work well if you can conceptualise the new service has something modeled around the domain in question.
A variation of this approach might be to push aggregation responsibilities to services further downstream.

### Just Enough Domain-Driven Design
### Ubiquitous language
Defining and adopting a common language to be used in code and in describing the domain, to aid communication.

### Aggregate
A collection of objects that are managed as a single entity, typically referring to real-world concepts.

### Bounded context
An explicit boundary within a business domain that provides functionality to the wider system but that also hides complexity.

### Hidden models
To be able to work out the valuation of the company, though, the finance employees need information about the stock we hold. The stock item then becomes a shared model between the two contexts. However, note that we don’t need to blindly expose everything about the stock item from the warehouse context.
So there is the internal-only representation and the external representation we expose.

### Mapping Aggregates and Bounded Contexts to Microservices
When starting out, as I’ve already mentioned, you want to reduce the number of services you work with. As a result, you should probably target services that encompass entire bounded contexts. As you find your feet and decide to break these services into smaller services, you need to remember that aggregates themselves don’t want to be split apart—one microservice can manage one or more aggregates, but we don’t want one aggregate to be managed by more than one microservice.

### Turtles all the way down
When considering the boundaries of your microservices, first think in terms of the larger, coarser-grained contexts, and then subdivide along these nested contexts when you’re looking for the benefits of splitting out these seams.
A trick here is that even if you decide to split a service that models an entire bounded context into smaller services later on, you can still hide this decision from the outside world—perhaps by presenting a coarser-grained API to consumers.  
This is another form of information hiding—we’ve hidden a decision about internal implementation in such a way that if this implementation detail changes again in the future, our consumers will be unaware.

### The Case for Domain-Driven Design for Microservices
Firstly, a big part of what makes DDD so powerful is that bounded contexts, which are so important to DDD, are explicitly about hiding information—presenting a clear boundary to the wider system while hiding internal complexity that is able to change without impacting other parts of the system.
Secondly, the focus on defining a common, ubiquitous language helps greatly when it comes to defining microservice endpoints.

The changes we implement to our system are often about changes the business wants to make in how the system behaves. We are changing functionality—capabilities—that are exposed to our customers. If our systems are decomposed along the bounded contexts that represent our domain, any changes we want to make are more likely to be isolated to a single microservice boundary. This reduces the number of places we need to make a change and allows us to deploy that change quickly.

Fundamentally, DDD puts the business domain at the heart of the software we are building. The encouragement that it gives us to pull the language of the business into our code and service design helps improve domain expertise among the people who build the software. This in turn helps build understanding and empathy for the users of our software and builds greater communication among technical delivery, product development, and the end users.

### Mixing Models and Exceptions
Organizational and domain-driven service boundaries are my own starting point. But that’s just my default approach. Typically, a number of the factors I’ve outlined here come into play, and which ones influence your own decisions will be based on the problems you are trying to solve. You need to look at your own specific circumstances to determine what works best for you—and hopefully I’ve given you a few different options to consider. Just remember, if someone says “The only way to do this is X!” they are likely just selling you more dogma. You can do better than that.


