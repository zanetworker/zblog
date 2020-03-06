---
title:  "Microservices in a nutshell"
date:   2017-01-27 15:04:23
categories: [technical]
tags: [Microservices]
---
In this post we will walk through the definitions, Properties and patterns for building Microservices.

## What are Microservices?
A microservice is really just a service that adheres to a slightly looser definition of the
**Single Responsibility Principle (SRP)** that arose from patterns for object-oriented design. _It is
a service that does one thing_.

### Properties of Microservices
- Small, and Focused on Doing One Thing Well
- **Autonomus**To do decoupling well, you’ll need to model your services right and get the APIs right. I’ll
be talking about that a lot
- **Technology Heterogeneity**: each service should be able to use its own technology stack without affecting other services
- **Resilience** A key concept in resilience engineering is the bulkhead. If one component of a system
fails, but that failure doesn’t cascade, you can isolate the problem and the rest of the
system can carry on working. Service boundaries become your obvious bulkheads. In a
monolithic service, if the service fails, everything stops working. With a monolithic
system, we can run on multiple machines to reduce our chance of failure, but with
Microservices, we can build systems that handle the total failure of services and degrade
functionality accordingly.
- **Scaling**: easier to scale a smaller piece when needed than scaling a large monolith.
- **Ease of Deployment**: A one-line change to a million-line-long monolithic application requires the whole
application to be deployed in order to release the change
- **Composability** a larger service can composed of these services
- **Optimizing for Replaceability** Don't get too emotionally attached to your services.
- **Loose Coupling** Changes on one service should not affect other services
<!-- - **Secure** services that provide sensitive data should be secure (i.e., AAA capable) -->

### What is the difference between SOA and Microservices?
- **Service Oriented Archiecture (SOA)** : Service-oriented architecture (SOA) is a design approach where multiple services
collaborate to provide some end set of capabilities
  - communication happens across the network rather than method calls
  - developed to combat the idea of a monolith
  - However,
    - no clear way of how to achieve, how to split a monolith into smaller entities.
    - no way to ensure that services are not strongly coupled.
- **Microservices**: an approach to implement SOA based on real-world use-cases, it follows clear patterns.


### What is NOT an implementation of Micro-services?

- **Shared libraries:**
  - technology dependant (e.g., java library / python libraries ...)
  - risks redeployment of the entire process when usinga new library
  - not enough support for enusring safety and system resieliency.
  - can become the point of coupling

- **Modules:**
  - OSGI (tries to plump java by introducing the concept of plugins but does not do a good job)
  - Erlang modules support Lifecycle management, modules can be stopped, restarted, upgraded without issue. _However the modules can become tightely coupled quite fast if we are not careful_.


### How to find the right language to program Microservices?
Microservices should be language dependandt, however, when writing your services the language preferably should be:

- Simple:
  - No compiling intermediary bytecode like Java or .NET, the ability to
produce native binaries.
- Open Source
- Open libraries
- Should with the right abstractions

###  Continious Integration / Delivery / Deployment
How to make sure your micro-services are continiously integrated?

- **Easy to fetch services** — provide a place where you can fetch your deployed services (i.e., deliverables)

- **Public display of test results** — changes to a specific decomposed serivce should pass the pre-defined tests and notify stake-holders of the output to take actions immediately.

- **Automate deployment** — Once you have a set of passing tests, the service should automatically self-deploy and be fetch-able on demand.

### Reference
- Building Microservices: Designing Fine-Grained Systems by Sam Newman
