---
title:  "Patterns for Microservices Decomposition"
date:   2017-02-02 15:04:23
categories: [technical]
tags: [Microservices]
comments: true
---

Microservices are decoupled components that follows the Single Responsibility Principle (SRP), that is, a component does one thing but does it really well.
In this mini-book we will be discussing various patterns for decomposing a service into a microservice.


<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [What motivates developers to go Micro?](#what-motivates-developers-to-go-micro)
- [How to decompose your services?](#how-to-decompose-your-services)
- [Handling Cross-cutting concerns](#handling-cross-cutting-concerns)
- [Handling communication between micro-services ?](#handling-communication-between-micro-services-)
- [Service-Discovery - Where is my-service?](#service-discovery-where-is-my-service)
- [Handling failure across dependent services (Circuit breaking reliability)](#handling-failure-across-dependent-services-circuit-breaking-reliability)
- [Handling storage-dependencies for microservices](#handling-storage-dependencies-for-microservices)
- [Handling access to services](#handling-access-to-services)
- [Service Function Validation](#service-function-validation)
- [Logging,  Instrumentation, and health-checking (Observability)](#logging-instrumentation-and-health-checking-observability)
- [Is it really worth it?](#is-it-really-worth-it)

<!-- /TOC -->


## What motivates developers to go Micro?
Imagine the case where you hire someone to start hacking and adding code to your existing framework, I am sure you don't need me to tell you that it takes sh*t loads of time for them to even start adding one line of code. Forget about that, and lets consider the case where you need to develop your test cases? not so swift huh?! Okay, you are still not convinced, what if you want to reuse code components to build another application... yes, you need to consider modifying the whole application if you are on the Mono side. Akhh, you are not see easily convinced I see, fine, what if your application was built using C++ and you discovered this new awesome language and want to transform some of your application components to be built using this language? yes, you will need to change all of it :P. I got  you there didn't I.

So let's recap, whats the benefits of microservices?
- Easier barrier to entry for new developers (faster to start hacking)
- Since components are decoupled, you can apply version control for each component (i.e. mService) independently, you can also choose to deploy some components while leaving out others.
- You can scale your micorservice independently.
- Divorce the strong coupling between the service and the technology stack

So now you want to decompose your monolithic application, whats next? You need some patterns amigo!

In the rest of this document we will be going through patterns of microservices decomposition. If that didn't cover it for you and  you want to dig deeper, have a look at the references section, with a special recommendation of Chris Richardson's blog.


## How to decompose your services?
Thats a very tough question to answer because the answer is "it depends". Many people came up with  different methods for doing the decomposition, there is no "best" I would say, but below are some methods I found after researching a bit, I added some of my own as well.

### Decompose by Business capability
- Understanding the core components of the business (e.g., messaging, logging, user-interface, etc.)
- Build a service for each capability

N.B: You need to understand the core components very well.

### Decompose by locality
- Sometimes you will find that there are some services that communicate more with one another compared to other services.
- Find the services that communicate intensely with one another and reason if the can be merged as one service.
- Keep services that communicate less in separate components

N.B: you need to understand the weight of the links between each of your sub-components. If the weight is too high, then consider having them as one service component.

### Decompose by Greed
- Some components are more resource greedy than others, in fact, it would make a lot of sense to isolate the greedy components in their own services.
- Doing so, allows you to monitor the resource consumption more effectively and avoid overprovisioning resources.


N.B: you need to study the resource consumption of each sub-component before attempting the isolation

### More?
Of course there are more ways for decomposition, there is no silver bullet though, but there are best practices, and guts feeling :)

## Deployment patterns
Deployment decision matters, the way you deploy your services will affect your overall application performance. Lets talk some about the different methods for service deployment.

Now that you have decomposed your application, where should you place them? you have many options,
- **service-to-host**: that's a one to one mapping
  - the home-alone approach? a host dedicated to serving this service and this service alone (so help it god)
  - the one-of-a-kind approach? one microservice per host per service (more on it below)
- **service_s_-to-host**: thats a many to one mapping
  - container per service?
  - VM per service?
- **serverless**: porting out the infrastructure management


### Single service instance per host (service-to-host)
There are two ways to deploy a microservice per host.

- **The home-alone approach:**
If we choose to deploy a single service (i.e. microservice) per host, we seem to be obsessed about isolation, and resource conflict. Yea you can monitor, manage easier but common one service per host!! I don't know about you, but I won't do it.

- **The one-of-a-kind approach**: lets say we decomposed two applications x and y to x1, x1 and y1, y2. what we can do is put x1 and y1 on the same host and x2 and y2 in another host. What did we gain? well, if host1 fails you only lose x1 (a sub-component of you service), so better resilience compared to having x1 and x2 on the same host.

### Multiple service instances per-host (services-to-host)
Once you have your monolith decomposed to micros (i.e., microservices), you can decide to keep services of the same application on the same host, of course, that depends on the design principle you followed from the beginning. For example, if you made the decision to "Decompose by locality", then it makes sense to keep services that communicate a lot together on the same host.

When deciding to use this approach, make sure you understand the resource consumption model for each of your services. Isolation matters here. It makes sense to use VMs or lighter-weight containers for achieving this isolation, but again you will have to take the decision of whether to maintain a one-to-one mapping between your services and containers or not (basically the same decision process as the service/services-to-host just on a higher level)


An alternative is to choose to place each service on a separate host.

### Serverless deployment
Sometimes, you don't feel you have the skills/time for making decisions about how to place your services, well worry not, in that case a "Serverless" deployment is the recommended method.

A "Serverless" deployment offload the overhead of the "How" and "Where" to place your services to black-box remote infrastructres that are well equipped with schedulers for making the decisions for you. All you need to do is set some performance expectations and watch them happening in numbers. Some example? okay, here are few:
- Amazon AWS Lambda
- Google Cloud Functions
- Azure Functions

Life is not flowers and colors though, when you make the decision to go serverless you are giving up on ALOT of choices that you could have made take the "Server-based" deployment:
- You are limited to deploying application that Lambda can understand (i.e., Lambda supports only a few languages).
- You can't deploy stateful applications, only those applications that run in response to a request are deployable.
- Service communication is not very much supported (e.g., RabbitMQ)
- Is your application slow on start-up? then Lambda is not for you because it is boot-delay intolerant. That goes for any serverless deployment.
- Although a serverless architecture makes the placement decision for you, that does not mean that it won't time.
- A serverless architecture  is only reactive in-nature. When the load goes up, it will take some time to smooth the performance back. Pro-active techniques are not so much supported.

Eventually, a serverless architecture is also server-based behind the curtains (they have to run somewhere right?!), it is just that they (i.e., amazon, google, microsoft) do the magic of hiding the servers from you and only require that upload your code to have a fully functional decomposed-application.


## Handling Cross-cutting concerns
Some parts of your application can be easily decomposed because there is very little dependency on them. Other parts are being used across the whole application by most of the services in the decomposed architecture. These parts are often referred to as the "Cross-cutting concerns" of your application, examples of these are:
- Logging frameworks
- Database services
- Message brokers
- Service discovery

To avoid future complexity resulting from service-decomposition, it is highly advised that you incorporate design and support for cross-cutting concerns before starting to write your code.

Is there an easy way to do that? Well, you can port out the effort to **microservice chassis frameworks** such as Spring Boot, Dropizard Micro, etc. The easy way is not always price free, adapting a microservice chassis service will fix you to one programming language, _I have not seen yet chassis that are programming language independent._

Another way to do it is to **Externalize cross-concern configuration** such that a newly started service can read the configuration from the externalized store and start functioning (e.g., through the use of environment variables)

<!-- Containers are currently considered the lightest form of hosting for services, however, the way we build them at them moent results in huge image sizes that we don't need most of the time <describe here the process of speparaing build and run >
[The	split-image	approach	to	building	minimal
runtime	Docker	images](https://www.fpcomplete.com/blog/2015/12/docker-split-images)
 -->

## Handling communication between micro-services ?


Communication is a crucial element in building efficient microservices. However, communication can be a broad-term, therefore, lets divide microservices communication to more specific scopes, but before doing this, lets look at a generic example (the company example) to better understand the scoping logic.

## The company example
Imagine you are the CEO of a big company, being the CEO, you are responsible for communicating with other CEOs in other companies for striking some winning deals, they know nothing about your internal policy of management but yet they still can communicate with you, i.e., you are your company's entry point (the external reference). Alternatively, your company is divided into departments, these departments each have a manager, the managers from the different departments communicate together using a well defined company  internal communication model. Is that all? not really. Imagine that your company is huge, it is spanning multiple continents each continent branch acts as a separate entity and thus have a local CEO. The local CEO should be able to speak the CEO language because they act as entry points for their continent branch but still provide the internal mode for the branch communication.

Services are not different. Each service has external clients, thus they should provide method for external communication (e.g., REST, and RPC). On the other hand, the communication between the decomposed services should be swift enough to avoid degradation in performance (e.g., protocol buffers, event-based, etc)



### External communication  (inter-service communication)
It is the model in which external clients communicate with the overall service. Examples include:


### Internal communication (intra-service communication)
Communication between the decomposed parts of the services.


### Mixed-model
Decomposing the service into larger blocks each is recursively decomposed into smaller services. This results in:
- External communication between larger decomposed blocks
- Internal communication between smaller blocks

### Known communication methods:
Well, from what I have seen so far, there are three methods for services to communicate with one another:
- **Remote Procedural Invocation (RPI)**: examples of this method of communication would be REST, gRPC, Apache Thrift
- **Messaging**: using a messaging framework such as RabbitMQ, Apache Kafka, ZeroMQ
- **Domain-Specific protocols**: Use E-mail protocols such as SMTP and IMAP, or use a media streaming protocols such as RTMP, HLS, and HDS.


Each one of these methods can have its own model of communication:
- **point-to-point:** Mesh-like connection, each service establish a connection with all the other services it needs to communicate with.
- **publish-subscribe:** services only subscribe to messages that are relevant
- **stream-based processing:**  services process queries from other systems in a real-time model

The analysis of the advantages and disadvantages of each of these methods is out of the scope of this document.



## Service-Discovery - Where is my-service?
In a monolithic architecture,  components of a modular application usually reside on the same place and thus communicate together on the language level, through method calls or procedural calls. In the context of microservices, the case is much different, services are usually virtualized and isolated into containers or virtual machine which are dynamically placed and thus not statically addressed, which brings up the question of "where is my-service".

There are multiple methods to solve this problem:
- **Client-side discovery**: Make use of a service-registry which knows the location of all the decomposed services.
	- _Tight-coupling much between the client and the service-registry?_
	- _Service discovery needs to be implemented using the same language as the client_
- **Server-side discovery**: instead of building the service discovery communication logic at the client side (resulting in tight-coupling) add an extra-layer between the client and the service registry called the router which handle the communication between the client and the service registry.
	- _Adds an extra hop for communication compared to client-side discovery_

  - _Routers must support the communication protocols_
  - _The router is yet another component to be configured_
  - _e.g., kubernetes, AWS ELB, Marathon, etc._

Both server and client side discovery are required to communicate witha  service registry. So what is a service registry? It is a database that contains information about service instances. There are two ways for services to be updated in the registry:
- **Self-Registration**: it is the task for the service to make itself known to the registry. This puts the responsibility for registration and un-registration on the service which is something that is not scoped for the service as it adds a layer of coupling with the registry.
- **Registrar**: Detects when a service is up and does the registration and unregistration whenever the state changes. This would only work if the registrar have built-in methods for health-checks.B


## Handling failure across dependent services (Circuit breaking reliability)
Some microservices communication with other micorservices to achieve certain tasks. Failure in of of these services my result in the failure of the other services communicating with it. Therefore, it is necessary to detect these failures, and redirect the communication to other services that serve the same purpose. This procedure is called "Circuit Breaking", where failure in communication between two dependent services is handled by a timeout value just like a real-life circuit breaker.

## Handling storage-dependencies for microservices
One of the key-benefits of deploying a microservices architecture is loose-coupling. This property is what allows you to deploy and scale services independently.

A question that might arise is how to handle service storage? there are multiple possible answers, below are some solutions to consider:

- **Shared service storage**: allow services to share storage, adding freedom of access between data related to each service. For example, service 1 can access data from service 2
	- Coupling due to co-ordination of data-access between services
	- Fix technology for storage which leads to rigidity (some services might need relational while others NoSQL but that hinders it)
	- consistency between databases of different services is hard (one solution is to use an event-driven approach, services should send an update when data is changed for other services to be aware of the change)

- **Assign storage on a per service basis**: treat storage for each service independently, to achieve this you can make use of private tables, or assign a schema per service, most importantly, make use of an access control mechanism to proven overlapping of service data.
	- Harder to manage (many technologies are used)
	- Query distribution which results in harder to manage services

## Handling access to services
In a microservices architecture, services provide an interface for allowing clients to access them. However, should an client be allowed execute an service? that should not be the case, there must be a way to authenticate requests coming from clients. There are multiple ways for implementing this including the use of access tokens.

## Service Function Validation
Before deploying our microservices, we must ensure that these services will function as expected. This can be done via an automated continuous testing approach including stress tests, unit tests, integration tests, etc.

## Logging,  Instrumentation, and health-checking (Observability)
Problems are meant to occur whether a monolithic approach or a mirco-services approach is utilized. Detecting these problems is the tricky part.

- **Logging** is one very powerful method for find the root-cause of a problem, implementing logging aggregation across the different services is key to solving problems. Each service should follow a standard api for writing error data to log files.
	- Audit logging: providing accounting for actions among services.


- **Instrumentation** is a process for gathering statistics about individual operations. Usually, a metric services is running somewhere in the infrastructure and other services are either pushing metrics to the service or providing an interface for metrics to be pulled by the instrumentation service.
	- The infrastructure must be provide enough resources for the instrumentation services.

- **Health Checking** is important for circuit breaking (mentioned above) preventing the case where failures in one service affects services. Each service should provide a health check api exposing important parameters such as:
	- Connection status with other services
	- Resource status (e.g., disk usage, network utilization)


The task of health-checking can be done by the service registry or a separate health service.


## Is it really worth it?
Before jumping on the microservices bandwagon, it is crucial to identify the benefits associated with decomposition. Not understanding these benefits would render the whole transformation useless. In this seciton, we provide **anti-patterns** that we as developers / researchers should avoid when looking at microservices.

### Build a reference-point to start from
Sometimes, people working on the same project might not agree on what microservices would mean in terms of development. As a result, a multitude of opinions would merge-in leading to a spaghetti implementation of a complex UN-managable system.

Therefore, it is essential to define the goals, agree on a reference definition to avoid the confusion and frustration in later phases of the project.

### Don't give up on the API
Before getting caught up in the implementation define the API that your services will expose, these are contract that your services should respect.
When starting a new service, define a simple API, and evolve it as needed.

### Don't Choose any communication method
Communication is one of the key performance influencers. Therefore, choosing the right tool for the task matters. Exchanged messages can be encoded in a variety of ways, service consumption can happen in different ways as well.

Understand the service consumption model and requirements before choosing the communication method.

N.B: read the communication section of this document.

### Raise them to be independent
Coupling is one of the reason we are getting away from monolithic architectures, this way we can implement scaliny, reusability, automisty, and all the benefits mentioned before.

To avoid a tightely coupled architecture make sure that the concerns are separated (check the decomposition methods in this document).

### Don't neglect testing
Microservices are makes your system more complex to manage, as a result, if you don't test now (as in before deploying your service), you will surely regret it. Once you let your service spin without good testing, it might be hard to handle the errors resulting from it, and your whole system will be affected.

N.B: make use of continious delivery methods

### Don't Team out, Team up
Although microservices promotes productivity by separating concerns in service developement, it is still necessar for developer to co-ordinate. The less-coupled the services, the less communication needed :)


Do these and it is maybe worth it!

## References
- [Micro-service communication bla-bla](http://jonasboner.com/bla-bla-microservices-bla-bla/)
- [Microservices bla bla pdf](http://jonasboner.com/resources/bla-bla-microservices-bla-bla.pdf)
- [Protobuf as an alternative to REST](http://www.ensor.cc/2016/12/protobuf-alternative-to-rest-for.html)
- [The 7 deadly-sins of Microservices](https://opencredo.com/7-deadly-sins-of-microservices/)
- [Communication patterns in a Microservice world](https://medium.com/@diogo.lucas/communication-patterns-in-a-microservice-world-af07192b12d3#.x4vxshv70)
- [Inter-service Communication](http://howtocookmicroservices.com/communication/)
- [Micorservices.io](http://microservices.io/)
