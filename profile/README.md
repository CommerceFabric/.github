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

  * [IMPORTANT Cost Control](#important-cost-control)
  * [1. Select the Subscription](#1-select-the-subscription)
  * [2. Create the Resource Group](#2-create-the-resource-group)
  * [3. Create the Container Registry](#3-create-the-container-registry)
  * [4. Register Required Resource Providers](#4-register-required-resource-providers)
  * [5. Create the AKS Cluster](#5-create-the-aks-cluster)

    * [Optional Monitoring](#optional-monitoring)
  * [6. Connect to the AKS Cluster](#6-connect-to-the-aks-cluster)

    * [Get Cluster Information](#get-cluster-information)
    * [View `kubectl` Help](#view-kubectl-help)
    * [Check the Current Cluster](#check-the-current-cluster)

* [AKS Cost Management](#aks-cost-management)

  * [Start When Required](#start-when-required)
  * [Stop When Finished](#stop-when-finished)
  * [Check Whether AKS Is Stopped](#check-whether-aks-is-stopped)

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

**Subscription:** `CommerceFabric_Subscription`
**Resource Group:** `CommerceFabric-ResourceGroup`
**Region:** `UK South`
**Container Registry:** `commercefabricregistry`
**AKS Cluster:** `CommerceFabric-aks-cluster`
**AKS VM:** `Standard_D2lds_v6`
**Nodes:** `1`

## IMPORTANT Cost Control

> ⚠️ **IMPORTANT**
>
> **The AKS cluster must be stopped whenever it is not being used.**
>
> A running AKS cluster incurs compute charges continuously. With a limited monthly budget, leaving the cluster running unnecessarily can result in unexpected costs.
>
> **START → USE → STOP**
>
> Stopping AKS prevents the normal compute charges for the stopped nodes. Other Azure resources, such as the Container Registry or storage, may still incur charges.

---

## 1. Select the Subscription

```powershell
az account set --subscription "CommerceFabric_Subscription"
```

Verify:

```powershell
az account show --query "{Name:name, SubscriptionId:id, State:state}" --output table
```

---

## 2. Create the Resource Group

```powershell
az group create `
  --name CommerceFabric-ResourceGroup `
  --location uksouth
```

---

## 3. Create the Container Registry

```powershell
az acr create `
  --resource-group CommerceFabric-ResourceGroup `
  --name commercefabricregistry `
  --sku Basic
```

---

## 4. Register Required Resource Providers

```powershell
az provider register --namespace Microsoft.ContainerRegistry --wait
az provider register --namespace Microsoft.Insights --wait
az provider register --namespace Microsoft.OperationalInsights --wait
az provider register --namespace Microsoft.ContainerService --wait
az provider register --namespace Microsoft.Network --wait
az provider register --namespace Microsoft.Compute --wait
az provider register --namespace Microsoft.OperationsManagement --wait
az provider register --namespace Microsoft.Authorization --wait
az provider register --namespace Microsoft.Storage --wait
```

These only need to be registered once.

---

## 5. Create the AKS Cluster

```powershell
az aks create `
  --resource-group CommerceFabric-ResourceGroup `
  --name CommerceFabric-aks-cluster `
  --location uksouth `
  --node-count 1 `
  --node-vm-size Standard_D2lds_v6 `
  --generate-ssh-keys
```

### Optional Monitoring

If monitoring is required, add:

```text
--enable-addons monitoring
```

Monitoring can generate additional costs, so only enable it when required.

---

## 6. Connect to the AKS Cluster

Retrieve the AKS credentials:

```powershell
az aks get-credentials `
  --resource-group CommerceFabric-ResourceGroup `
  --name CommerceFabric-aks-cluster
```

This updates your local Kubernetes configuration and allows `kubectl` to communicate with the AKS cluster.

### Get Cluster Information

```powershell
kubectl cluster-info
```

### View `kubectl` Help

```powershell
kubectl --help
```

### Check the Current Cluster

```powershell
kubectl config current-context
```

This should show:

```text
CommerceFabric-aks-cluster
```

---

# AKS Cost Management

## Start When Required

```powershell
az aks start `
  --resource-group CommerceFabric-ResourceGroup `
  --name CommerceFabric-aks-cluster
```

## Stop When Finished

```powershell
az aks stop `
  --resource-group CommerceFabric-ResourceGroup `
  --name CommerceFabric-aks-cluster
```

## Check Whether AKS Is Stopped

```powershell
az aks show `
  --resource-group CommerceFabric-ResourceGroup `
  --name CommerceFabric-aks-cluster `
  --query "powerState.code" `
  --output tsv
```

Expected:

```text
Stopped
```

> **Daily rule: START → USE → STOP**
>
> Do **not** leave AKS running when it is not required.
>
> The cluster does **not** need to be deleted and recreated. It can be started and stopped as required, preserving the existing configuration.

---

## Notes

This system is intentionally designed to explore complexity rather than avoid it. The focus is on demonstrating understanding of distributed system challenges such as:

* Partial failure handling
* Eventual consistency
* Service boundaries
* Data ownership
* Operational observability
