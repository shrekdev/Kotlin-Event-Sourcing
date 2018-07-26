Event Sourcing with Kotlin
==========================

[![Build Status](https://img.shields.io/travis/bringmeister/event-sourcing-with-kotlin/master.svg)](https://travis-ci.org/bringmeister/event-sourcing-with-kotlin)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/bringmeister/ddd-with-kotlin/master/LICENSE)

This is a simple demo project to show Event Sourcing with Kotlin.

Note that this project is derived from [ddd-with-kotlin](https://github.com/bringmeister/ddd-with-kotlin) which shows a similar demo but without event sourcing.

## Run it

```
./gradlew bootRun
```

Then open the web UI in a browser: 

```
http://localhost:8080/index.html
```

You can submit requests there and see all events.

## Example Use Case

The subject of this demo is the `Product Service`.
The `Product Service` is located between five other components.
The `Master Data Service` will push new products to the `Product Service`.
This is the beginning of our business process.
The `Product Service` will register each new product at the `Media Data Service`.
After a product is registered, the `Product Service` will receive updates for this product from the `Media Data Service`. 
After an update was received, the `Product Service` will:
 - Update the `CDN` if media data has changed
 - Update the `Shop` and `search index` if master data has changed

```
+-------------+
| Master Data |
|   Service   |
+-------------+
   |                     << DEMO >>   
   +------------------► +---------+
    1: update product   | Product |
                        | Service |---------+---------+
               +------► +---------+         |         |
      3:       |             |      2:      |         |        5:
 push updates  |             | register new |         | update if master 
               |             |   product    |         | data has changed
   +------------+            |              |         |
   | Media Data | ◄----------+              |         +----------+
   |  Service   |                           |         |          |
   +------------+        4: update if media |         |          |
                          data has changed  |         |          |
                                            ▼         ▼          ▼
                                         +-----+  +------+  +--------+ 
                                         | CDN |  | Shop |  | Search |
                                         +-----+  +------+  +--------+
```

## What to see

- A domain entity called `Product.kt` which encapsulates business logic and throws domain events.
- A process flow with events ("something has happened") and commands ("now do something").
- Value objects such as `ProductNumber.kt` or `ProductInformation.kt`.
- A ports-and-adapters package layout.
- An anti-corruption layer for external events - they will be transformed to internal commands.
- A persistence based on event sourcing with a simple in-memory event store.

## Resources

### On Event Sourcing

- http://microservices.io/patterns/data/event-sourcing.html
  * A short description of the pattern including some sample code and additional resources. 
  This is a good starting point to get a first impression of event sourcing. 
  The page also shows related patterns as well as pros and cons.
- http://engineering.pivotal.io/post/event-source-kafka-rabbit-jpa
  * A demo from Pivotal showing a small example using DDD, event sourcing, commands and a nice CQRS implementation. 
  You will find the source code on GitHub.

### On DDD

- https://martinfowler.com/tags/domain%20driven%20design.html
  * A collection of articles by Martin Fowler. 
    Each article enlightens a different aspect of DDD.
- https://docs.microsoft.com/en-us/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/ddd-oriented-microservice
  * First article of series by Microsoft on how to use DDD for microservices.
    Although the series is using C#, the examples are easy to understand.    
