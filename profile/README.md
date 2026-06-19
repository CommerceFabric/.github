# CommerceFabric

CommerceFabric is a portfolio project exploring distributed systems and microservice architecture using a production-inspired eCommerce domain. The goal is not just to build an online store, but to demonstrate architectural decision-making around service boundaries, data consistency, resilience, observability, and scalability.

---

## Architecture Overview

CommerceFabric is composed of independently deployable microservices representing distinct business capabilities within an eCommerce platform.

Each service is designed around a clear bounded context and communicates with others via synchronous APIs and asynchronous messaging where appropriate.

Core services include, for more information on each services tech stack, please click the links associated:

* [**UserService**](https://github.com/CommerceFabric/service-user/) – Authentication, user registration, identity management, and token issuance
* TODO - **ProductService** – Product catalogue and querying (planned)
* TODO - **OrderService** – Order creation, state transitions, and lifecycle management (planned)
* TODO - **InventoryService** – Stock levels and reservation logic (extension)
* TODO - **NotificationService** – Event-driven user notifications (extension eg send email on order completion/failure)
* TODO - **PaymentService** – Payment processing and validation (maybe extension - but payment will be spoofed)

* [**web-storefront**](https://github.com/CommerceFabric/web-storefront) - Angular Web Portal for the storefront, allowing users to login and make orders (planned)
* TODO - **desktop-storefront** - WPF Desktop Application for the storefront, duplicating logic of the web storefront (extension)

* TODO - **web-admin** - Admin web-portal to allow admins to add new products, manage stock, etc.
* TODO - **infra-observability** - Aspire/Grafana web portal to view health and status of the entire system

An architecture diagram and ERD will be added as the system evolves.

---

## Architecture Principles

### Service Autonomy

Uses Clean Architecture within each microservice.

Each microservice is independently designed, deployed, and maintained. This includes autonomy over:

* Data storage technology
* Data access strategy
* Internal domain modelling

This project intentionally explores polyglot persistence to demonstrate real-world trade-offs between consistency, performance, and development complexity.

### Communication Patterns

Services communicate using a mix of:

* Synchronous HTTP APIs for direct queries and commands
* Asynchronous messaging for domain events and cross-service workflows

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

## Planned Improvements After Finishing Core Plan

### Reliability & Consistency

* Database seeding automation per service
* Transactional Outbox pattern implementation
* Saga orchestration for distributed workflows
* Idempotency handling for APIs and message consumers

### Observability

* Structured logging using Serilog
* Distributed tracing with correlation IDs across services
* Centralised metrics and monitoring dashboards

### Architecture Evolution

* Evaluate CQRS patterns using MediatR
* Introduce API Gateway for unified external access
* Improve service discovery and routing for scaled deployments

### Scaling & Infrastructure

* Horizontal scaling of individual microservices
* Load balancing across service instances
* Container orchestration readiness (Docker-based scaling model)

### Testing Strategy

* Load and performance testing for critical services
* Chaos testing via controlled failure injection
* Resilience validation for downstream dependencies

### Developer Experience (Optional Future Work)

* Admin/observability dashboard for service health checks
* Integration with tools like Grafana or .NET Aspire
* Replacement of placeholder frontend with a modern React or Blazor UI

---

## Current Status

This project is under active development and serves as a systems design and distributed architecture learning exercise. Services and infrastructure are being incrementally introduced and refined.

---

## Notes

This system is intentionally designed to explore complexity rather than avoid it. The focus is on demonstrating understanding of distributed system challenges such as:

* Partial failure handling
* Eventual consistency
* Service boundaries
* Data ownership
* Operational observability
