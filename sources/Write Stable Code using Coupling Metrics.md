---
tags: source/todo
---

# [Write Stable Code using Coupling Metrics](https://codeopinion.com/write-stable-code-using-coupling-metrics/)

> ***Sponsor:** Do you build complex software systems? See how NServiceBus makes it easier to design, build, and manage software systems that use message queues to achieve loose coupling. [Get started for free](https://go.particular.net/codeopinion).*

Are you tired of changing one part of a system, only to have another part break? Write stable code! Writing stable code can seem impossible. Trying to keep things from breaking can be a nightmare if you’re unaware of how your system is coupled. Understanding Afferent & Efferent coupling can guide you into insights about your software architecture & design.

## YouTube

Check out my [YouTube channel](https://www.youtube.com/channel/UC3RKA4vunFAfrfxiJhPEplw) where I post all kinds of content that accompanies my posts including this video showing everything that is in this post.

To illustrate coupling, I’m going to talk about it at a project/package/boundary level. You could also think of these diagrams at a class level as well, however I think looking at it from a higher level such as project/packages of your system is a better place to start.

 [![Write Stable Code using Coupling Metrics](https://codeopinion.com/wp-content/uploads/2021/02/a1-1024x836.png)](https://codeopinion.com/wp-content/uploads/2021/02/a1.png)

Each blue box in the above diagram represents a boundary/project within the system. The direction of the arrows is to illustrate the coupling required from one boundary to another.

Shipping is dependent on Sales  
Billing is dependent on Sales  
Sales is dependent on CRM

## Afferent Coupling (Ca)

Who depends on you? Afferent coupling (denoted by Ca) is a metric that indicates the total number of other projects/boundaries that are dependent upon it.

So from the viewpoint of the Sales boundary, it has an Afferent Coupling of 2. Shipping and Billing are both dependent upon Sales.

From a .NET solution, this would be represented by project references. When you take the viewpoint of a particular project in a solution, what other projects reference it? The number of other projects that reference your project is the total of Afferent Coupling (Ca).

Afferent Coupling: Who depends on You?

## Efferent Coupling (Ce)

Who do you depend on? Efferent coupling (denoted by Ce) is another metric that is the verse of Afferent Coupling. It is the total number of projects that a given project depends on.

From the viewpoint of the Sales project/boundary, it has an Efferent Coupling (Ce) of 1. It depends on the CRM project/boundary.

From a .NET solution, this would represent all the references you have to other projects for a given project.

Efferent coupling: Who do you depend on?

## Coupling Metrics

If we count all the Afferent (Ca) and Efferent (Ce) coupling in the entire diagram, we get the following counts.

 [![Write Stable Code using Coupling Metrics](https://codeopinion.com/wp-content/uploads/2021/02/a2-1024x839.png)](https://codeopinion.com/wp-content/uploads/2021/02/a2.png)

If you were to do this with your solution/system, what could you derive from this data? How could you use this to better understand your system overall and the possible issues with it?

Having a high Afferent Coupling (Ca) will generally cause you pain when you make a non-compatible change. Meaning, if you make a change to a project that is used by a lot of other projects, you have a high risk of causing issues with all those other projects. You’ll know this if you make a change and break other parts of your system.

Having a high Efferent Coupling (Ce) will generally cause you pain when you need to change it. Because it’s so dependent on so many other projects/ boundaries, it’s can be difficult to change. Having a lot of dependencies could mean that it’s simply doing too much or that you actually have misaligned boundaries.

## Instability

Instability another metric that is a ratio.

**I = Ce / (Ce + Ca)**

This metric is a ratio between 0 and 1. With 0 meaning it’s totally stable and 1 meaning it’s unstable.

Note: Having a high ratio of instability isn’t unreasonable depending on the context of the project.

Here’s the calculated instability ratio for each project/boundary.

 [![Write Stable Code using Coupling Metrics](https://codeopinion.com/wp-content/uploads/2021/02/a3-1024x833.png)](https://codeopinion.com/wp-content/uploads/2021/02/a3.png)

CRM has an Instability of 0. Meaning it’s stable. And this makes sense because it has no dependencies. Nothing can affect it. In other words, no changes to any other projects can affect it. However, since it has a high Ca, changes to it can affect other parts of the system. We need to be cautious of change.

Billing & Shipping have Instability of 1. If sales changes in a way that is not handled by Billing, it can make it unstable. This isn’t inherently bad because we need to realize that it’s relative to the Ce for those projects. Having a dependency on 1 project is much different than having 10 dependencies. Also, we can change these projects freely and we will not affect any other projects.

Sales has a combination of both Ca and Ce. While these numbers are low, if you’re in a situation where a project has a high number of both Ca and Ce, it may be cause for concern in your design. This would be the center point of your diagram. It can be affected by many projects, and change to it can affect many projects. Not an ideal scenario.

## Coupling Metrics

Use coupling metrics of Afferent Coupling (Ca), Efferent Coupling (Ce), and Instability (I) as a way to review your system and your design goals. Let metrics guide your decision making in refactoring where needed.

[Follow @CodeOpinion on Twitter](https://twitter.com/codeopinion)