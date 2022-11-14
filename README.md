# spring-boot-microservices

This project is a guide to learning Microservices, specifically, using Spring Boot.

## Table of Contents

[1. Building Microservices](#1-building-microservices)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.1 Building REST Services](#11-building-the-services)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.2 Communication between Microservices](#12-communication-between-microservices)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.2.1 Synchronous Communication](#121-synchronous-communication)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.2.2 Asynchronous Communication](#122-asynchronous-communication)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.2.3 Implementation (WebClient, OpenFeign)](#123-implementation-webclient-openfeign)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.3 Service Discovery](#13-service-discovery)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.3.1 Implementation (Eureka)](#131-implementation-eureka)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.4 API Gateway](#14-api-gateway)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.4.1 Implementation (Spring Cloud Gateway)](#141-implementation-spring-cloud-gateway)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.5 Security](#15-security)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.5.1 Implementation (Keycloak)](#151-implementation-keycloak)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.6 Circuit Breaker](#16-circuit-breakers)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.6.1 Implementation (Resilience4J, Hystrix)](#161-implementation-resilience4j-hystrix)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.7 Centralize Configuration](#17-centralize-configuration)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.7.1 Implementation (Cloud Config)](#171-implementation-spring-cloud-config)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.8 Distributed Tracing](#18-distributed-tracing)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.8.1 Implementation (Spring Cloud Sleuth & Zipkin)](#181-implementation-spring-cloud-sleuth--zipkin)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.9 Message Brokers/Queues](#19-message-brokersqueues)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.9.1 Implementation (Kafka, RabbitMQ)](#191-implementation-kafka-rabbitmq)
<br>
[2. Deploying Microservices](#2-deploying-microservices)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[2.1 Packaging / Containerizing the Application](#21-packaging--containerizing-the-application)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[2.1.1 Jib](#211-jib)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[2.1.2 Docker](#212-docker)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[2.2 Managing the Application](#22-managing-the-application)
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[2.2.1 Kubernetes](#221-kubernetes)
<br>

## 1. Building Microservices

Introduction to this topic: [Introduction to Microservices, Docker, and Kubernetes (YouTube/JamesQuigley)](https://www.youtube.com/watch?v=1xo-0gCVhTU)
<br>

Spring Boot Microservices Architecture:
![spring-boot-microservices](microservices.jpg)

Link to: [the Basic Microservices Architecture with Spring Cloud (devo.to/BrunoDrugowick)](https://dev.to/brunodrugowick/project-overview-the-basic-microservices-architecture-with-spring-cloud-2e8e)
<br>

### 1.1 Building the Services

The Microservices Example developed in this project comes from: [Spring Boot Microservices Project Example - Part 1 | Building Services (YouTube/ProgrammingTechie)](https://www.youtube.com/watch?v=lh1oQHXVSc0&list=PLSVW22jAG8pBnhAdq9S8BpLnZ0_jVBj0c)
<br>

Spring Boot Microservices Project Example Architecture:
![microservices-app-architecture](microservices-app-architecture.png)

To build the microservices, we must first separate the components that would be present in a Monolithic application
into smaller, independent applications. 

For example, instead of building a "Store App" with Model, Repository, Service and Controller layers for Product, Order and Inventory, we extract these into their own individual applications.
Here, we will end up with product-service, order-service and inventory-service applications.

We must not forget to carry out appropriate tests for each microservice, this is typically carried out in the form of integration tests.
For testing, we can make use of Testcontainers which allows us to run JUnit tests in lightweight throwaway instances of databases that can be run in Docker containers!

Furthermore, it will be more functional to adopt a Maven multi-module project to manage the microservices/applications easier.

### 1.2 Communication between Microservices

![interservice-communication](interservice-comms.png)

If you are working with a Spring Boot project which involves multiple microservices, 
You might have felt the need to communicate from one microservice to another. 
Depending upon business use-cases, this communication can be of synchronous or asynchronous type.

### 1.2.1 Synchronous Communication

In the case of Synchronous Communication, the client sends a request and waits for a response from the service. 
The important point here is that the protocol (HTTP/HTTPS) is synchronous and the client code can only continue 
its task when it receives the HTTP server response.

An example of where this might be used is in an e-commerce application, if a customer searches for a particular 
product to purchase then that product’s availability needs to be validated in the inventory by making a request 
to product availability service. 
Because customer should know about the current availability of the product to place the order.
In this case, you can use synchronous communication to get the product’s real-time availability 
in inventory and price information.

For Synchronous Communication: Can make use of REST Template, OpenFeign or WebClient.

### 1.2.2 Asynchronous Communication

In the case of Asynchronous Communication, The client sends a request and does not wait for a response from the service. 
The client will continue executing its task - It does not wait for the response from the service.

An example of where this might be used is in the banking domain, loan request should be processed and needs approval at multiple levels. 
So in this case, when a user raises a request for the loan then the loan request service will provide some reference number immediately. 
Once all the approvals are done, will persist the loan request details in the database. 
So in this scenario, we can use asynchronous communication.

For Asynchronous Communication: Can make use of Message Brokers / Message Queues such as RabbitMQ and Apache Kafka.

### 1.2.3 Implementation (WebClient, OpenFeign)

WebClient is an interface representing the main entry point for performing web requests.
It was created as part of the Spring Web Reactive module and will be replacing the classic RestTemplate in these scenarios.

Link to WebClient examples: 
<br>
[Calling REST from Java with Spring WebClient (YouTube/vaadinofficial)](https://www.youtube.com/watch?v=-U_dDUAw_OM)
<br>
[Using WebClient to make API calls (YouTube/JavaBrains)](https://www.youtube.com/watch?v=F3uJyeAyv5g)

OpenFeign, originally known as Feign and sponsored by Netflix, is designed to allow developers to use a declarative 
way to build HTTP clients by means of creating annotated interfaces without writing any boilerplate code.

Link to OpenFeign examples:
<br>
[Feign Client Using Spring Boot (YouTube/PlayJava)](https://www.youtube.com/watch?v=tlshVRtbS_c)

Implementation of Asynchronous Communication is covered in Section X. Message Brokers/Queues.

### 1.3 Service Discovery

Microservices are dynamic in nature. This means multiple instances of a single Microservice 
will be co-existing. 
Most probably your instances will either have a different IP address or a different port or both. 
And the number of instances will be dynamic too. This brings up loads of questions —
- How do I know the location of any Microservice instance?
- How can I keep a track of all the instances?
- How do I select a Microservice instance?
- What happens if the Microservice instance goes down?

When building REST APIs, We are assuming the URL (e.g. http://localhost:8080/product/**) to be constant 
but in reality, it is dynamic. All parts of it — be it hostname or the port .

Service Discovery comes to the rescue. This provides a mechanism that keeps track of all the services and their instances. 
All the Microservices register to it and keep updating their network information regularly.

![eureka-service-discovery](eureka-service-discovery.png)

The problem can be resolved by creating a Discovery Server where our microservices can register themselves. 
The discovery server will keep a track of all the instances, their hosts, ports, and health status. 
If a service needs to communicate with another service, it needs to get the instance info from this 
discovery server. However, Load balancing is a must as the discovery server does not handle it automatically.

Above describes Client-side Service Discovery but, you can also have Server-side Service Discovery.
Server-side is similar to the option above but the load balancing is not happening at the client side. 
We have a dedicated proxy server that takes care of this.

Link to more info:
<br>
[Spring Boot Microservices — Developing Service Discovery (Medium/LalVerma)](https://lalverma.medium.com/spring-boot-microservices-implementing-service-discovery-cfc98e49b74f)

### 1.3.1 Implementation (Eureka)

Can make use of client-side service discovery via “Spring Cloud Netflix Eureka”. We need to ensure the correct dependencies and configurations
are entered on both the Server and Client(s). Load balancing is a must as the discovery server does not handle it automatically.

Client-side service discovery allows services to find and communicate with each other without hard-coding 
the hostname and port. The only ‘fixed point' in such an architecture is the service registry, with 
which each service has to register.

Link to Eureka examples:
<br>
[Spring Boot Microservices Project Example - Part 3 | Service Discovery (YouTube/ProgrammingTechie)](https://www.youtube.com/watch?v=0TQliqoX6Kc&list=PLSVW22jAG8pBnhAdq9S8BpLnZ0_jVBj0c&index=3)

### 1.4 API Gateway

![api-gateway-2](api-gateway-2.png)
![api-gateway](api-gateway.png)

An API gateway is the single entry point for all clients. The API gateway handles requests in one of two ways.
Some requests are simply proxied/routed to the appropriate service.
It handles other requests by fanning out to multiple services.

Rather than provide a one-size-fits-all style API, the API gateway can expose a different API for each client.
For example, the Netflix API gateway runs client-specific adapter code that provides each client with an API
that’s best suited to its requirements.

The API gateway might also implement security, e.g. verify that the client is authorized to perform the request

### 1.4.1 Implementation (Spring Cloud Gateway)

Spring Cloud Gateway provides a library for building API gateways on top of Spring and Java. 
It provides a flexible way of routing requests based on a number of criteria, as well as focuses on 
cross-cutting concerns such as security (authentication), resiliency (load balancing), and monitoring.

Link to Spring Cloud Gateway examples:
<br>
[Spring Boot Microservices Project Example - Part 4 | API Gateway (YouTube/ProgrammingTechie)](https://www.youtube.com/watch?v=0TQliqoX6Kc&list=PLSVW22jAG8pBnhAdq9S8BpLnZ0_jVBj0c&index=4)

### 1.5 Security

An important aspect of securing your applications is authorization and access to the API resources. 
If you think about web app authorization, the first approach that probably comes to your mind is OAuth 2.0 
or OpenID Connect. OAuth 2.0 is the industry-standard protocol for authorization. 
Of course, it is supported by Spring Security. 
There are also multiple OAuth2 providers (Google, Github, etc.) you can integrate your application with. One of them is Keycloak.

### 1.5.1 Implementation (Keycloak)

Keycloak is an open-source identity and access management solution which makes it easy to secure modern applications  with less code.
Keycloak provides both SAML and OpenID protocol solutions which are industry standard so definitely
Building an application that is integrated with Keycloak will provide you a more secure and stable solution.

We are going to enable and configure OAuth 2.0 support on the API gateway via Keycloak on a Docker container. 
Must ensure configurations such as Issuer URI and SecurityConfig.class is added.
When using POSTMAN for communicating with the REST API's, we must select OAuth2.0
in Authorization Type and complete the configuration options as per our KeyCloak settings.
This will allow us to use JWT's issued by KeyCloak.

Link to Keycloak examples:
<br>
[Spring Boot Microservices Project Example - Part 5 | Security (YouTube/ProgrammingTechie)](https://www.youtube.com/watch?v=0TQliqoX6Kc&list=PLSVW22jAG8pBnhAdq9S8BpLnZ0_jVBj0c&index=5)
<br>
[Securing Spring Boot Microservices with Keycloak using OpenID | OAuth2.0 (YouTube/JavaTechie)](https://www.youtube.com/watch?v=La082JsJoH4)
<br>

### 1.6 Circuit Breakers

![circuit-breaker](circuit-breaker.png)

In microservices, an application or service makes a lot of remote calls to applications running in different services, 
usually on different machines across a network. If there are many callers to an unresponsive service, you 
can run out of critical resources leading to cascading failures across multiple systems.

Consider an example that multiple users log in to a banking application and the account service is down. 
The authentication service will wait on the account service and now a lot of user threads are waiting for 
a response thereby exhausting the CPU on the authentication service as well as the account service. 
As a result, the system cannot serve any of the users.

Circuit breakers are a design pattern to create resilient microservices by limiting the impact of service 
failures and latencies. The major aim of the Circuit Breaker pattern is to prevent any cascading failure in 
the system. In a microservice system, failing fast is critical.

In the circuit breaker, there are 3 states Closed, Open, and Half-Open. These different states are triggered
based on the configuration we apply. There are 2 types of circuit breaker patterns, Count-based and Time-based.
A count-based circuit breaker switches state from closed to open if the last N number of calls failed or 
were slow. A time-based circuit breaker switches to an open state if the responses in the last N seconds 
failed or were slow.

If there are failures in the Microservice ecosystem, then you need to fail fast by opening the circuit. 
This ensures that no additional calls are made to the failing service so that we return the fall back logic 
we have implemented (in our service) immediately.

### 1.6.1 Implementation (Resilience4J, Hystrix)

Spring Cloud Circuit Breaker supports many different circuit breaker implementations including, 
Resilience4J, Hystrix, Sentinal, and Spring Retry. 
In this guide we will be using the modern alternative to Hystrix, Resilience4J.

Resilience4j is a lightweight fault tolerance library inspired by Netflix Hystrix, but designed for 
functional programming. 
Resilience4j provides higher-order functions (decorators) to enhance any functional interface, 
lambda expression or method reference with a Circuit Breaker, Rate Limiter, Retry or Bulkhead.

Link to Resilience4J examples:
<br>
[Spring Boot Microservices Project Example - Part 6 | Resilience4J Circuit Breaker (YouTube/ProgrammingTechie)](https://www.youtube.com/watch?v=0TQliqoX6Kc&list=PLSVW22jAG8pBnhAdq9S8BpLnZ0_jVBj0c&index=6)
<br>

### 1.7 Centralize Configuration

![centralized-config](centralized-config.webp)

In micro-service world, managing configurations of each service separately is a tedious and time-consuming 
task. In other words, if there are many number of modules, and managing properties for each module with the 
traditional approach is very difficult.

### 1.7.1 Implementation (Spring Cloud Config)

Central configuration server provides configurations (properties) to each microservice connected. 
As mentioned in the above diagram, Spring Cloud Config Server can be used as a central cloud config server 
by integrating to several environments.

Link to Spring Cloud Config examples:
<br>
[Microservices using SpringBoot | Full Example (YouTube/DailyCodeBuffer)](https://www.youtube.com/watch?v=BnknNTN8icw&t=3750s)
<br>

### 1.8 Distributed Tracing

![distributed-tracing](distributed-tracing.png)

Distributed Tracing is the process of tracing every single request from the point of origin up to 
all the services it touches by analyzing the data. 
Every request will have a Trace ID, timestamp, and other useful metadata.

### 1.8.1 Implementation (Spring Cloud Sleuth & Zipkin)

Spring Cloud Sleuth allows you to aggregate and track log entries as requests move through a 
distributed software system by adding trace and Span ID’s on the appropriate HTTP request headers.

Zipkin is an open source project that provides mechanisms for sending, receiving, storing, and 
visualizing traces. This allows us to correlate activity between servers and get a much clearer picture of 
exactly what is happening (by using the UI) in our services.

Link to Sleuth & Zipkin examples:
<br>
[Spring Boot Microservices Project Example - Part 7 | Distributed Tracing (YouTube/ProgrammingTechie)](https://www.youtube.com/watch?v=0TQliqoX6Kc&list=PLSVW22jAG8pBnhAdq9S8BpLnZ0_jVBj0c&index=7)
<br>

### 1.9 Message Brokers / Queues

![event-driven-microservices](event-driven-microservices.jpeg)

Event-driven architecture (EDA) is a software design pattern in which decoupled applications can asynchronously 
publish and subscribe to events via an event broker/message broker.
In an Event-Driven Architecture, applications communicate with each other by sending and/or receiving 
events or messages.

### 1.9.1 Implementation (Kafka, RabbitMQ)

In the above architecture, OrderService, StockService, and EmailService microservices are independent of each other. 
OrderService is a Producer (and Topic) application that sends an event to the Message Broker. 
StockService and EmailService are Consumers who will consume the events from the Message Broker.

In this tutorial, we will also see how multiple consumers will subscribe to a single Kafka topic to 
consume the events/messages.

Link to Kafka examples:
<br>
[Kafka Tutorial - Spring Boot Microservices (YouTube/AmigosCode)](https://www.youtube.com/watch?v=SqVfCyfCJqw)
<br>
[Event-Driven Microservices using Spring Boot and Kafka (javaguides)](https://www.javaguides.net/2022/07/event-driven-microservices-using-spring-boot-and-apache-kafka.html)

## 2. Deploying Microservices

### 2.1 Packaging / Containerizing the Application

- Can reduce the size of docker image using layers
- Can do without dockerfile using Jib
- Once images are on dockerhub, use command docker compose
- Must configure all the docker containers for each service/image in the Microservice application
- Mongo, KeyCloak, Zookeeper & Kafka, KeyCloak & MySQL, Zipkin, Eureka Server, all SB apps need docker-compose configs
- application-docker.properties
- need to renew client secret from KeyCloack everytime docker container restarted.

### 2.1.1 Jib

### 2.1.2 Docker

Installing Docker: [How to Install Docker on Mac (2022) (YouTube/AmitThinks)](https://www.youtube.com/watch?v=SGmFGYCuJK4)
<br>

What is Docker?

How to use Docker?

### 2.2 Managing the Application

### 2.2.1 Kubernetes

Installing Kubernetes: [How to Install Kubernetes on Mac (YouTube/ResearchRocks)](https://www.youtube.com/watch?v=gFI8PI-gMqQ)
<br>

What is Kubernetes? [Kubernetes in 5 mins YouTube/VMwareCloudNativeApps](https://www.youtube.com/watch?v=PH-2FfFD2PU)
<br>

How to use Kubernetes?

