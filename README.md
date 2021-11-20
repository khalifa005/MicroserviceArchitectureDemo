# MicroserviceArchitectureDemo
# ASP.NET Core

Let’s go through a popular way of Building Applications – Microservice Architecture in ASP.NET Core. In this detailed article, we will try understand what really Microservice architecture is? , How does it compare to the traditional way of building application? And also some advanced concepts like API Gateways with Ocelot, Unifying several Microservices, Health Checks and much more.

We will be building a Simple Microservice Application for demonstrating various Concepts including Microservice Architecture in ASP.NET Core, API Gateways, Ocelot, Ocelot Configuration and Routing and much more

--------------------------------

Table of Contents	
Microservice Architecture in ASP.NET Core – Overview

Monolith Architecture – Basics

So, What is a Microservice Architecture ?

Microservice vs Monolith Architecture

What we’ll Build?

Getting Started with the Implementation

Setting up the Microservices

Introduction to Ocelot API Gateway

Building an Ocelot API Gateway

Configuring Ocelot Routes

Summary

--------------------------------
Microservice Architecture In ASP.NET Core – Overview
Before getting started with the Microservices, let’s talk a bit about the traditional way of building applications that you are probably using right now in your current Solutions. It is called as Monolith Architecture


--------------------------------

Monolith Architecture – Basics
Monolith Architecture is the traditional and widely used architectural pattern while developing applications. It is designed in a way that the entire application components is ultimately a single piece, no matter how much you try to de-couple them by using Patterns and Onion / Hexagonal Architecture. All the services would be tightly coupled with the Solution. The major point to note is that while publishing the application, you would have to deploy them to a single server only.

While it is still an awesome way to build applications, there are a few drawback associated with the Monolith Architecture. Any Small to Mid Scaled Applications would do just fine with this Architecture , but when you suddenly start to scale up even further, you would have to make a few compromises as well as face certain downtimes while deploying new versions / fixes.
![image](https://user-images.githubusercontent.com/29863643/142728316-dd98fa7a-40ac-4741-a10f-93b5c1e621f2.png)
From the diagram it is quite evident that whatever logics or infracture you want to integrate with your application, it ends up being physically a part of the application itself. For larger Applications, this may has un-desired effects in the loger run
--------------------------------

So, What is a Microservice Architecture ?
Microservice Arcihtecture is an architecture where the application itself is divided into various components, with each component serving a particular purpose. Now these components are called as Microservices collectively. Get it? The components are no longer dependent on the application itself. Each of these components are literally and physically independent. Because of this awesome separation, you can have dedicated Databases for each Components, aka Microservices as well as deploy them to separate Hosts / Servers.

--------------------------------

To Make it clear, Let me take a small example. Imagine we are going to build an Ecommerce Application is ASP.NET Core. Let’s segregrate it more practically. Imagine we need to build an API for the eCommerce Application. How would you go about with it?

A traditional Approach would be do Build a Single Solution on Visual Studio and then Seperate the Concerns via Layers. Thus you would probably have Projects like eCommerce.Core, eCommerce.DataAccess and so on. Now these seperations are just at the level of code-organization and is effecient only while developing. When you are done with the application, you will have to publish them to a single server where you can no longer see the seperation in the production environment, right?
--------------------------------

Now, this is still a cool way to build applications. But let’s take a practical scenario. Our eCommerce API has, let’s say , endpoints for customer management and product management, pretty common, yeah? Now down the road, there is a small fix / enhancement in the code related to the Customer endpoint.

If you had built using the Monolith Architecture, you would have to re-deploy the entire application again and go through several tests that guarentee that the new fix / enhancement did not break anything else. A DevOps Engineer would truly understand this pain.
--------------------------------
But if you had followed a Microservice Architecture, you would have made Seperate Components for Customers, Products and so on. That means you have 2 API Projects that are specific to what a Customer Endpoint needs and what a Product Endpoint needs. Now, these Microservices can be deployed anywhere in the Web and need not go along with the Main Application. In this way, we create a clean seperation and physically decouple the Application into various parts.

This may sound easy to achieve, but in reality, it is quite a complex architecture. Some complexities that can arise is, ‘ How can you make the Microservices interact with each other? , What about the authentication? and so on. That is exactly why the knowledge Microservice Architecture is in great demand.

Here is how a Microservice Architecture would look like.


![image](https://user-images.githubusercontent.com/29863643/142730417-775cbef1-1b7a-41b7-a02b-676929a8d3ec.png)

--------------------------------

Let’s go through this Diagram and certain Key-Words.

As mentioned earlier, we are trying to build an ASP.NET Core API Solution for an eCommerce Application. Now, we identified the possible components that makes sense to be a Microservice. The Components identified are Products, Customers and orders. Now each of this Microservice will be a Standalone WebApi Project. Clear? Meaning we will have 3 ASP.NET Core WebApis in the picture as Microservices. This is the central black that you can see in the diagram.

These components / microservices DO NOT have to be built on ASP.NET Core itself. It can be highly Flexible where the Customer Microservice is built use Node & Express.js connected to a MongoDb and the other services could be built using ASP.NET Core, depending on the availability of the Team. This Architecture make things quite simple and flexible, yeah?

Microservices are free enough to use a shared database or even have a dedicated database of it’s preference.

Now that we have the Microservices , let’s think about it’s deploymenet. Ideally you dont need a single DEVOPS team to be responsible for the entire deployment. But you have multiple unit of DevOps teams who are responsible for one / many Microservices.

Let’s say Product Microservice is deployed to localhost:11223, Customer Microservice to localhost:22113 and so on. In this diagram the client denoted the Main Application. In our case, the eCommerce Application. You do not want to confuse the client by presenting so many Microservices upfront. Imagine have 50 Microservices and the client has to know about each of them. Not Practical, yeah?

To overcome this, we introduce an API Gateway placed right in between the client and the Microservices. This gateway re-routes all the api endpoints and unifies it under a single domain, so that the client now no longer cares about the 50 Microservices.

localhost:11223 will be re-routed to localhost:40001/api/product/
localhost:22113 will be re-routed to localhost:40001/api/customer/

Where localhost:40001 is the location of your API Gateway. In this way, your client connects to the gateway, which in turn re-routes the data / endpoints to / from the Microservices. This is quite enough for now. We will go to more detail later in this article, once we get started with the development. I hope that the Diagram makes quite a lot of sense now.
--------------------------------
What We’ll Build?
As mentioned earlier, here is the requirement. We will have to build a simple Microservice Architecture in ASP.NET Core with API Gateways. For starters, let’s have 2 Microservice WebAPIs that perform CRUD Operations for Customer and Product. We will also need an API Gateway that is responsible to re-direct the incoming request from client (Here, a browser or Postman would be the client) to and from the Microservices. To be more clear, the client has to access the API Gateways and perform the operation over the Microservices.

Getting Started With The Implementation
We will be building this Implementation completely with ASP.NET Core 3.1. Open up Visual Studio 2019 and Create a New Blank Solution. You can search for Blank Solution and Click Next.

![image](https://user-images.githubusercontent.com/29863643/142732930-74c82491-51d0-45a2-bf0d-61b8f2646952.png)

In the next Dialog, let’s name our Solution as Microservices.WebApi.
![image](https://user-images.githubusercontent.com/29863643/142732938-589446bf-d9c5-4027-b84c-a30cb68966af.png)

Visual Studio Creates a new Blank Solution for you. Here, Add a New Folder and Name it Microservices. This is folder where we will add all the Microservices. Right Click on the Microservices folder and add a new Project. Let’s add the Product Microservice First. This is going to be an ASP.NET Core WebApi Project. I will name it Product.Microservice.

![image](https://user-images.githubusercontent.com/29863643/142732984-0152dfac-1223-47b4-9465-24e530fa4121.png)
Product.Microservice
--------------------------------

--------------------------------

--------------------------------

--------------------------------

--------------------------------
--------------------------------

