# Understanding event driven microservice patterns

!!! abstract
    In this article, we are detailing some of the most import event-driven patterns to be used during your microservice implementation and when adopting kafka as an event backbone.

Adopting messaging (Pub/Sub) as a microservice communication backbone involves using at least the following patterns:

* [Decompose by subdomain](https://microservices.io/patterns/decomposition/decompose-by-subdomain.html), event driven microservices are still microservices, so we need to find them, and the domain-driven subdomains is a good approach to identify and classify business function and therefore microservices. With the event storming method, aggregates help to find those subdomain of responsibility. 
* [Database per service](https://microservices.io/patterns/data/database-per-service.html) to enforce each service persists data privately and is accessible only via its API. Services are loosely coupled limiting impact to other service when database schema changes. The database technology is selected from business requirements. The implementation of transactions that span multiple services is complex and enforce using the Saga pattern. Queries that goes over multiple entities is a challenge and CQRS represents an interesting solution. 
* [Strangler pattern](#strangler-pattern) is used to incrementally migrate an existing, monolytic application by replacing a set of features to a microservice but keep both running in parallel. Applying a domain driven design approach, you may strangle the application using bounded context. But then aS soon as this pattern is applied, you need to assess the co-existence between existing bounded contexts and the new microservices. One of the challenges will be to define where the write and read operations occurs, and how data should be replicated between the contexts. This is where event driven architecture helps. 
* [Event sourcing](event-sourcing.md) persists the state of a business entity such an Order as a sequence of state-changing events. 
* [Command Query Responsibility Segregation](cqrs.md) helps to separate queries from commands and help to address queries with cross-microservice boundary.
* [Saga pattern:](saga.md) Microservices publish events when something happens in the scope of their control like an update in the business entities they are responsible for. A microservice interested in other business entities, subscribe to those events and it can update its own states and business entities when receiving such events. Business entity keys needs to be unique, immutable.


## Strangler pattern

### Problem

How to migrate a monolytics application to microservice without doing a big bang, redeveloping the application from white page. Replacing and rewritting an existing application can be a huge investment. Rewritting a subset of business functions while running current application in parallel may be relevant and reduce risk and velocity of changes. 

### Solution

The approach is to use a "strangler" interface to dispatch request to new or old features. Existing features to migrate are selected by trying to isolate sub components. 

One of main challenge is to isolate data store and how the new microservices and the legacy application are accessing the shared data. Continuous data replication can be a solution to propagate write model to read model. Write model will most likely stays on the monolitic application, change data capture can be used, with event backbone to propagate change to read model.

The facade needs to be scalable and not a single point of failure. It needs to support new APIs (RESTful) and old API (most likely SOAP).
