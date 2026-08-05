![CommerceFabric Logo](images/CommerceFabricLogo.png)

CommerceFabric is a portfolio project exploring distributed systems and microservice architecture using a production-inspired eCommerce domain. The goal is not just to build an online store, but to demonstrate architectural decision-making around service boundaries, data consistency, resilience, observability, and scalability.

---

# Table of Contents

* [Architecture Overview](#architecture-overview)

  * [Core Services](#core-services)

* [Architecture Principles](#architecture-principles)

  * [Service Autonomy](#service-autonomy)
  * [Communication Patterns](#communication-patterns)

* [Data and Persistence Strategy](#data-and-persistence-strategy)

* [Azure Infrastructure Setup](#azure-infrastructure-setup)

* [Notes](#notes)

---

## Architecture Overview

```text
TODO: add to this diagram as I go

API Gateway
     |
 ┌───┴───────────┐
 │               │
UserService   ProductService
 │               │
PostgreSQL      MySQL
        \       /
        Message Broker
              |
        OrderService
              |
           MongoDB
```

CommerceFabric is composed of independently deployable microservices representing distinct business capabilities within an eCommerce platform.

Each service is designed around a clear bounded context and communicates with others via synchronous APIs and asynchronous messaging where appropriate.

### Core Services

For more information on each service's technology stack, please click the associated links:

* [**UserService**](https://github.com/CommerceFabric/service-user/) – Authentication, user registration, identity management, and token issuance

* [**ProductService**](https://github.com/CommerceFabric/service-products) – Product catalogue and querying (planned)

* TODO - **OrderService** – Order creation, state transitions, and lifecycle management (planned)

* TODO - **InventoryService** – Stock levels and reservation logic (extension)

* TODO - **NotificationService** – Event-driven user notifications (extension, e.g. sending an email on order completion or failure)

* TODO - **PaymentService** – Payment processing and validation (possible extension; payment will be spoofed)

* [**Storefront WebApp**](https://github.com/CommerceFabric/web-storefront) – Angular web portal for the storefront, allowing users to log in and place orders (planned)

* TODO - **Storefront Desktop App** – WPF desktop application for the storefront, duplicating the logic of the web storefront (extension)

* TODO - **Admin Web Portal** – Admin web portal allowing administrators to add new products, manage stock, etc.

* TODO - **Service Health Portal** – Aspire/Grafana web portal to view the health and status of the entire system

An architecture diagram and ERD will be added as the system evolves.

---

## Architecture Principles

### Service Autonomy

Each microservice is independently designed, deployed, and maintained. This includes autonomy over:

* Data storage technology
* Data access strategy
* Internal domain modelling
* Service architecture

This project intentionally explores polyglot persistence to demonstrate real-world trade-offs between consistency, performance, and development complexity.

### Communication Patterns

Services communicate using a mix of:

* Synchronous HTTP APIs for direct queries and commands
* Asynchronous messaging for domain events and cross-service workflows (RabbitMQ)

Long-running business processes (e.g. order → payment → fulfilment) are designed to use Saga-based coordination.

---

## Data and Persistence Strategy

CommerceFabric intentionally does not enforce a single persistence standard across all services.

Examples include:

* EF Core with relational databases (e.g. MySQL) for strongly structured domains
* Dapper with PostgreSQL for performance-oriented query workloads

This approach is used to explore:

* ORM vs micro-ORM trade-offs
* Query flexibility vs developer productivity
* Schema evolution strategies per bounded context

---

# Azure Infrastructure Setup

[Azure Infrastructure Setup Documentation](https://github.com/CommerceFabric/infra-platform/blob/main/docs/SettingUpAzureResources.md)

---

## Notes

This system is intentionally designed to explore complexity rather than avoid it. The focus is on demonstrating understanding of distributed system challenges such as:

* Partial failure handling
* Eventual consistency
* Service boundaries
* Data ownership
* Operational observability
