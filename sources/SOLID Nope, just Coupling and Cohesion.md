---
tags: source/todo
---

# [SOLID? Nope, just Coupling and Cohesion](https://codeopinion.com/solid-nope-just-coupling-and-cohesion/)

> ***Sponsor:** Do you build complex software systems? See how NServiceBus makes it easier to design, build, and manage software systems that use message queues to achieve loose coupling. [Get started for free](https://go.particular.net/codeopinion).*

How do we avoid writing spaghetti code so our systems don’t turn into a hot mess? For me Coupling and Cohesion. Some people focus on things like SOLID principles and Clean Architecture. While I don’t necessarily have a problem with that if you’re pragmatic, I don’t ever really think about either of those explicitly.

## YouTube

Check out my [YouTube channel](https://www.youtube.com/channel/UC3RKA4vunFAfrfxiJhPEplw) where I post all kinds of content that accompanies my posts including this video showing everything in this post.

With over 20 years of professional software development experience, I’m mostly thinking about coupling and cohesion as a guide to software design. I’m not explicitly thinking about SOLID principles or clean architecture.

 [![yin yang](https://codeopinion.com/wp-content/uploads/2022/07/image-150x150.png)](https://codeopinion.com/wp-content/uploads/2022/07/image.png)

Coupling and cohesion are like the yin-yang of software design. They push and pull against each other. You’re striving for high cohesion and low coupling, however, you’re trying to find a balance. We’re always fighting against coupling.

There are many different forms of coupling but to narrow it down for simplicity’s sake:

> “degree of interdependence between software modules”
> 
> ISO/IEC/IEEE 24765:2010 Systems and software engineering — Vocabulary

At a micro level, this could be the interdependence between classes or functions, and at the macro level, this could be the interdependence between services.

When you’re thinking about spaghetti code you’re likely actually referring to a system that has a high degree of coupling at the macro and micro levels. This makes changes difficult to make or they break other parts of the system because there’s a high degree of coupling. Check out my post on [Write Stable Code using Coupling Metrics](https://codeopinion.com/write-stable-code-using-coupling-metrics/) for more on coupling.

Cohesion refers to:

> “degree to which the elements inside a module belong together”
> 
> Structured Design: Fundamentals of a Discipline of Computer Program and Systems Design

You can look at cohesion both from a micro and macro level. From a micro level, this can be viewed as how do all the methods relate in a class? Or how do all the functions of a module relate? From a macro level, how do all the features relate to a service? Check out my post on [Highly COHESIVE Software Design to tame Complexity](https://codeopinion.com/highly-cohesive-software-design-to-tame-complexity/) for more on cohesion.

But what does “belong together” mean? For me, this is about functional cohesion. Grouping-related options of a task. Not grouping based on data (which is informational cohesion). Yes, data is important, however, data is required by the functionality that is exposed.

## Business Capabilities

Speaking of functionality, let’s jump to the Single Responsibility Principle for a second as this might clarify why functional cohesion is important.

> When you write a software module, you want to make sure that when changes are requested, those changes can only originate from a **single person**, or rather, a single **tightly coupled group of people** representing a single narrowly defined business function. You want to isolate your modules from the complexities of the organization as a whole, and design your systems such that each module is responsible (responds to) the needs of just that **one business function**.
> 
> [https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html)

This might seem a bit more clear how the single responsibility principle at its root addresses coupling and cohesion.

If we focus on business capabilities and group them together, we’ll end up with a service. That’s why I always define a service as:

> **A Service is the authority of a set of business capabilities.**

At a macro level, we’re trying to have a high degree of cohesion by grouping by business capabilities. Behind those capabilities is data ownership.

Unfortunately, many are still focused purely on data and what I call “entity services”. This was a screenshot from a post on Reddit. The question was if this data model diagram should be implemented as a service per “entity”.

 [![entity services](https://codeopinion.com/wp-content/uploads/2022/07/image-1.png)](https://codeopinion.com/wp-content/uploads/2022/07/image-1.png)

This type of thinking is based on informational cohesion, not functional cohesion. The focus solely on data and not thinking at all about functionality and behavior leads to design generally around CRUD.

As an example, you’ll often have “Manager”, and “Repository” classes that look similar to this.

 [![IProductRepository](https://codeopinion.com/wp-content/uploads/2022/07/image-2-1024x470.png)](https://codeopinion.com/wp-content/uploads/2022/07/image-2.png)

The Product “entity” is really just a data model with a bunch of properties. There is no behavior exposed, they are just data buckets.

So where is the actual functionality of a product? In a large system, for example in a distribution domain, a product isn’t just a product.

A product means different things to different people within that domain.

[![Service Boundaries by Functional Cohesion](https://codeopinion.com/wp-content/uploads/2022/07/1-2-1024x318.png)](https://codeopinion.com/wp-content/uploads/2022/07/1-2.png)

Sales are thinking about the Sale Price of a product and are customer-centric. Purchasing/Procurement is thinking about the cost and is vendor centeric. The warehouse is concerned about shipping and receiving. They all have different business capabilities, and because those capabilities are different, they care about different data behind those capabilities.

A product isn’t just a product that has to live within a single boundary. It’s a concept that can live within multiple different service boundaries.

Organizing related business capacities (features) into services allows us to decide how we want to handle technical concerns within each service or to a specific feature. Dependencies shared concerns such as data access, validation, etc. All of these decisions can be localized and made per service.

[![Services with features with high functional cohesion](https://codeopinion.com/wp-content/uploads/2022/07/4-1024x346.png)](https://codeopinion.com/wp-content/uploads/2022/07/4.png)

## Loose Coupling

So you’ve defined boundaries based on functional cohesion, but they must have some interdependence between each other?

[![Tight Coupling between Services](https://codeopinion.com/wp-content/uploads/2022/07/2-1-300x181.png)](https://codeopinion.com/wp-content/uploads/2022/07/2-1.png)

Having a free for all where any service can be coupled to any other service is still a hot mess of spaghetti. In other words, tight coupling. The system will still be hard to change and fragile to change.

You want to remove the tight coupling by having service boundaries be independent and not directly coupled to other services. One way of achieving this is through loose coupling provided by messaging.

[![Loose Coupling between Services](https://codeopinion.com/wp-content/uploads/2022/07/3-1-300x194.png)](https://codeopinion.com/wp-content/uploads/2022/07/3-1.png)

Because you’re grouping by business capabilities, and the data behind those capabilities, each service should have all the data it needs. This means that you aren’t coupling services because you need to fetch data from them to perform an action.

Services together need to create workflow and exchange information. As an example from the diagram above, if the Warehouse has a “Quantity on Hand”, you might think that Sales would need that so it knows if it can sell a given product. However, Sales actually has its own concept called ATP (Available to Promise) which is a business function that is the projected amount of inventory it can sell. This consists of what’s in stock in the Warehouse, not allocated existing orders (Invoicing), as well as purchase orders and expected receipts (Purchasing).

Sales can maintain their ATP by consuming events from other services. It does not need to make directly coupled to an API to make calls at runtime to calculate ATP for a given product. It maintains and owns the ATP for all products based on events it’s consuming that are published from other services. When Invoicing publishes an OrderInvoiced event, it can subtract an amount from ATP. If the warehouse does a stock count and publishes an InventoryAdjusted event, Sales will update the ATP accordingly.

## Responsibility

From a lower-level code perspective, the coupling can be challenging when we have to deal with many different technical concerns. Web, Authorization, Validation, Data Access, and Business Logic. But as mentioned earlier, each feature/capability can define how each of these concerns is handled.

While you can share between features, such as a domain model, this naturally starts limiting the coupling between a set of features.

One approach to handle the common concerns is by using the pipes & filters pattern. Specifically, the Russian doll model which the request passes through a filter that can call (or not) the next filter. This allows you to separate various concerns and create a pipeline for a request.

[![Pipes & Filters](https://codeopinion.com/wp-content/uploads/2022/07/5-1-1024x146.png)](https://codeopinion.com/wp-content/uploads/2022/07/5-1.png)

For more check out my post on [Separating Concerns with Pipes & Filters](https://codeopinion.com/separating-concerns-with-pipes-filters/)

## Coupling and Cohesion

Building a large system without any boundaries (low cohesion) and a high degree of coupling is ultimately building a big turd pile of a system.

[![Big Turd Pile](https://codeopinion.com/wp-content/uploads/2022/07/6-1-300x286.png)](https://codeopinion.com/wp-content/uploads/2022/07/6-1.png)

It will be hard to add new functionality, and hard to change existing functionality without breaking and causing regressions.

Focusing on having highly cohesive services that provide specific business capabilities and loosely coupling between those services to provide workflow and business processes will allow you to build a system that is more resilient to change.

[![Smaller Turd Piles](https://codeopinion.com/wp-content/uploads/2022/07/7-300x156.png)](https://codeopinion.com/wp-content/uploads/2022/07/7.png)

Decomposing a large system into smaller independent logical service boundaries allows you to make different decisions that are localized to an individual service. No system will be perfect, especially over time and it will need to evolve. Focus on coupling and cohesion as a guide.

## Join!

Developer-level members of my [YouTube channel](https://www.youtube.com/channel/UC3RKA4vunFAfrfxiJhPEplw/join) or [Patreon](https://www.patreon.com/codeopinion) get access to a private Discord server to chat with other developers about Software Architecture and Design as well as access to source code for any working demo application that I post on my blog or YouTube. Check out the [YouTube Membership](https://www.youtube.com/channel/UC3RKA4vunFAfrfxiJhPEplw/join) or [Patreon](https://www.patreon.com/codeopinion) for more info.

-   [Highly COHESIVE Software Design to tame Complexity](https://codeopinion.com/highly-cohesive-software-design-to-tame-complexity/)
-   [Write Stable Code using Coupling Metrics](https://codeopinion.com/write-stable-code-using-coupling-metrics/)
-   [Separating Concerns with Pipes & Filters](https://codeopinion.com/separating-concerns-with-pipes-filters/)
-   [Finding your service boundaries: a practical guide](https://particular.net/webinars/finding-your-service-boundaries-a-practical-guide?utm_source=codeopinion&utm_medium=paid&utm_term=solid_principles&utm_content=additional_resources&utm_campaign=blog_sponsor)
-   [Finding service boundaries, illustrated in healthcare](https://particular.net/videos/finding-service-boundaries-illustrated-in-healthcare?utm_source=codeopinion&utm_medium=paid&utm_term=solid_principles_cont&utm_content=additional_resources&utm_campaign=blog_sponsor)

[Follow @CodeOpinion on Twitter](https://twitter.com/codeopinion)