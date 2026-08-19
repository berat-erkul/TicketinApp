<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0F2027,50:203A43,100:2C5364&height=200&section=header&text=TaskFlow%20%2F%20TicketinApp&fontSize=46&fontColor=ffffff&animation=fadeIn&fontAlignY=36&desc=Microservices%20Rewrite%20of%20a%20Layered%20Monolith&descAlignY=54&descAlign=50" width="100%"/>

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=20&duration=2800&pause=900&color=2C5364&center=true&vCenter=true&width=700&lines=8+independent+services%2C+one+distributed+system;Service+discovery+%C2%B7+Config+Server+%C2%B7+API+Gateway;Keycloak+auth+%C2%B7+Vault%2FInfisical+secrets+%C2%B7+Zipkin%2FGrafana+tracing;Kotlin+%2F+Compose+mobile+%C2%B7+WebFlux+BFF+web" alt="Typing SVG" />

<br/>

<a href="https://github.com/berat-erkul/ticketing-project-data"><img src="https://img.shields.io/badge/v1_Monolith-ticketing--project--data-4D4D4D?style=for-the-badge" /></a>
<a href="https://www.linkedin.com/in/berat-erkul"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" /></a>

</div>

<br/>

## About

**TaskFlow** is a project-based task management system, rebuilt from the ground up as a distributed system. It's the "after" to [`ticketing-project-data`](https://github.com/berat-erkul/ticketing-project-data)'s "before" — the same product, this time split into independently deployable services, each owning its own database, wired together with service discovery, centralized configuration, an API gateway, and OAuth2 authentication.

Web and native mobile clients talk to the same backend through a single edge; every business service can be built, deployed, and scaled on its own.

<br/>

## Architecture

Each service is versioned in its own repository (linked below); this repo hosts the system overview and documentation.

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

    subgraph obs [Secrets & Observability]
        Vault["🔑 Vault / Infisical"]
        Tracing["📈 Zipkin · Prometheus · Grafana"]
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

    User -.-> obs
    Project -.-> obs
    Task -.-> obs

    style Gateway fill:#2C5364,stroke:#0F2027,color:#fff
    style Eureka fill:#203A43,stroke:#0F2027,color:#fff
    style Config fill:#203A43,stroke:#0F2027,color:#fff
    style obs fill:#0F2027,stroke:#0F2027,color:#fff
```

**Layers:**

- **Clients** — a native mobile app built with Kotlin/Compose, and a Spring WebFlux-based BFF (Backend-for-Frontend) web layer.
- **Authentication** — Keycloak handles OAuth2 with JWT/JWK; both clients authenticate against the same realm.
- **Edge / Gateway** — Spring Cloud Gateway routes requests dynamically via Eureka's `discovery-locator` — no hardcoded service addresses.
- **Control plane** — Config Server distributes centralized `.yml` configuration, Eureka handles service registration and discovery.
- **Business services** — User, Project, and Task services are fully independent, each with its own PostgreSQL database (database-per-service).
- **Secrets & observability** — Vault/Infisical manage runtime secrets; Zipkin, Prometheus, and Grafana cover distributed tracing and metrics.

<br/>

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

<br/>

## Screenshots

<table>
<tr>
<td width="65%" valign="top">

**Web UI**

<img src="assets/web-ui.png" alt="Web UI" width="100%" />

</td>
<td width="35%" valign="top">

**Mobile UI**

<img src="assets/mobile-ui.png" alt="Mobile UI" width="100%" />

</td>
</tr>
</table>

<br/>

## Tech Stack

<div align="center">

<img src="https://skillicons.dev/icons?i=java,spring,postgres,kotlin,docker,git,github,idea&theme=dark" />

<br/><br/>

![Keycloak](https://img.shields.io/badge/Keycloak-4D4D4D?style=for-the-badge&logo=keycloak&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?style=for-the-badge&logo=jsonwebtokens&logoColor=white)
![OAuth2](https://img.shields.io/badge/OAuth2-4285F4?style=for-the-badge&logo=auth0&logoColor=white)
![Vault](https://img.shields.io/badge/Vault-FFEC6E?style=for-the-badge&logo=vault&logoColor=black)
![Infisical](https://img.shields.io/badge/Infisical-000000?style=for-the-badge)
![Eureka](https://img.shields.io/badge/Eureka-6DB33F?style=for-the-badge&logo=spring&logoColor=white)
![Spring Cloud Gateway](https://img.shields.io/badge/Spring_Cloud_Gateway-6DB33F?style=for-the-badge&logo=spring&logoColor=white)
![Zipkin](https://img.shields.io/badge/Zipkin-FF6C37?style=for-the-badge&logo=zipkin&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white)

</div>

- **Backend:** Java, Spring Boot, Spring Cloud (Gateway, Eureka, Config Server, LoadBalancer)
- **Authentication:** Keycloak, JWT/JWK, OAuth2
- **Secrets management:** HashiCorp Vault, Infisical
- **Observability:** Zipkin (distributed tracing), Prometheus + Grafana (metrics)
- **Database:** PostgreSQL (one database per service)
- **Web client:** Spring WebFlux (BFF)
- **Mobile client:** Kotlin, Jetpack Compose

<br/>

<div align="center">

Part of a two-version rebuild — see the [layered monolith version](https://github.com/berat-erkul/ticketing-project-data) this project started from.

</div>
