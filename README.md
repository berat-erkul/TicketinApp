# TaskFlow — TicketinApp

A project-based task management system built with a microservices architecture. Backed by web and mobile clients, with service discovery, centralized configuration, and authentication across the whole distributed system.

This project is the microservices rewrite of my [Ticketing System — Monolithic Architecture](https://github.com/berat-erkul/ticketing-project-data) project. The goal was to split a single Spring Boot application into independently deployable services, each with its own database, while applying microservices practices such as service discovery, centralized configuration, an API Gateway, and load balancing.

## Architecture

Each service is versioned in its own GitHub repository (linked below); this repo hosts the system overview and documentation.

```mermaid
flowchart LR
    subgraph clients [Clients]
        Mobile["📱 Mobile<br/>Kotlin / Compose"]
        Web["🌐 BFF Web<br/>WebFlux · 8080"]
    end

    Keycloak["🔐 Keycloak<br/>realm cydeo-dev<br/>9090 · JWT/JWK"]
    Gateway["🚪 Gateway<br/>Edge · 8762<br/>discovery-locator"]

    subgraph control [Spring Cloud Control Plane]
        Config["⚙️ Config 8888<br/>Config-Repo yml"]
        Eureka["🧭 Eureka 8761<br/>service discovery"]
    end

    subgraph services [Business Services]
        User["👤 User-Service<br/>8081"]
        Project["📁 Project-Service<br/>8082"]
        Task["✅ Task-Service<br/>8383"]
    end

    UserDB[(Postgres)]
    ProjectDB[(Postgres)]
    TaskDB[(Postgres)]

    Mobile -.-> Keycloak
    Web -.-> Keycloak
    Mobile -.-> Gateway
    Web -.-> Gateway
    Keycloak -.-> Gateway

    Gateway -.-> User
    Gateway -.-> Project
    Gateway -.-> Task
    Eureka -.-> Gateway

    User --> UserDB
    Project --> ProjectDB
    Task --> TaskDB
```

**Layers:**

- **Clients:** a native mobile app built with Kotlin/Compose, and a Spring WebFlux-based BFF (Backend-for-Frontend) web layer.
- **Authentication:** Keycloak handles OAuth2 with JWT/JWK; both the mobile and web clients authenticate against the same realm.
- **Edge / Gateway:** Spring Cloud Gateway routes requests dynamically to services via Eureka's `discovery-locator` — no hardcoded service addresses.
- **Control plane:** Config Server distributes centralized `.yml` configuration, Eureka handles service registration and discovery.
- **Business services:** User, Project, and Task services are fully independent, each with its own PostgreSQL database (database-per-service).

## Service Repositories

| Service | Description | Repo |
| --- | --- | --- |
| config-service | Centralized configuration server | [config-service-LAB](https://github.com/berat-erkul/config-service-LAB) |
| discovery-service | Eureka service discovery | [discovery-service-LAB](https://github.com/berat-erkul/discovery-service-LAB) |
| gateway-service | Spring Cloud Gateway (edge) | [gateway-service-LAB](https://github.com/berat-erkul/gateway-service-LAB) |
| user-service | User management | [user-service-LAB](https://github.com/berat-erkul/user-service-LAB) |
| task-service | Task management | [task-service-LAB](https://github.com/berat-erkul/task-service-LAB) |
| project-service | Project management | [project-service-LAB](https://github.com/berat-erkul/project-service-LAB) |
| web-ui-service | BFF Web (WebFlux) | [web-ui-service-LAB](https://github.com/berat-erkul/web-ui-service-LAB) |
| mobile-app | Mobile client (Kotlin/Compose) | [mobile-ticketing-app-LAB](https://github.com/berat-erkul/mobile-ticketing-app-LAB) |

## Screenshots

### Web UI

![Web UI](assets/web-ui.png)

### Mobile UI

<img src="assets/mobile-ui.png" alt="Mobile UI" width="320" />

## Tech Stack

- **Backend:** Java, Spring Boot, Spring Cloud (Gateway, Eureka, Config Server, LoadBalancer)
- **Authentication:** Keycloak, JWT/JWK, OAuth2
- **Database:** PostgreSQL (one database per service)
- **Web client:** Spring WebFlux (BFF)
- **Mobile client:** Kotlin, Jetpack Compose
