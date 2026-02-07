## Product Choice

Yandex Go

<https://go.yandex/>

An application for ordering a taxi, food, groceries, goods, parcel delivery, car rental and viewing transport schedules.

## Main components

[Yandex Go Component Diagram](diagrams/src/yandex-go/architecture-component.puml)

![Yandex Go Component Diagram](<diagrams/out/yandex-go/architecture-component/Component Diagram.svg>)

API Gateway – Acts as the single entry point for all client requests, routing them to the appropriate internal services (likely using gRPC for communication).

Dispatch Service – Responsible for matching riders with available drivers and managing the assignment and coordination of ride requests in real time.

Pricing Service – Calculates ride fares based on factors such as distance, time, demand, and other dynamic pricing rules.

Cache (Redis) – Stores frequently accessed data in memory (like user sessions or driver locations) to reduce latency and improve system performance.

Yandex Maps – An external service that provides mapping, geolocation, and routing data to calculate distances, ETAs, and optimal driving routes for rides.

## Data flow

[Yandex Go Sequence Diagram](diagrams/src/yandex-go/architecture-sequence.puml)
![alt text](<diagrams/out/yandex-go/architecture-sequence/Sequence Diagram.svg>)

#### Steps 9–16: Estimate Ride & Calculate Options

In this group, after the user enters a destination, the Mobile App sends an RPC to the API Gateway with pickup and drop-off coordinates.
The request is routed to the Maps & Pricing Service, which:

- Fetches route and traffic data from an External Maps API,

- Retrieves tariff rules and checks demand data internally,

- Then returns ride options (e.g., Economy, Comfort) with price and route info to the app.

This involves the Mobile App, API Gateway, Maps & Pricing Service, and External Maps API exchanging coordinates, route data, tariff rules, and pricing options.

## Deployment

[Yandex Go Deployment](diagrams/src/yandex-go/architecture-deployment.puml)
![Yandex Go Deployment](<diagrams/out/yandex-go/architecture-deployment/Deployment Diagram.svg>)
Based on the deployment diagram, the components are deployed across three main environments:

1. User Devices – The Yandex Go Mobile App runs on iOS/Android smartphones, and the Web App runs inside users’ web browsers.

2. Kubernetes Cluster (Application Tier) – All core backend services (Notification Service, User Service, Dispatch Service, Pricing Service, Payment Service, Maps & Routing Service) are deployed as pods in a Kubernetes cluster, forming the application tier.

3. Data Storage Cluster – Data persistence and messaging components are deployed in a dedicated storage cluster, including:

    - Redis Cluster for caching

    - Message Broker Cluster (Kafka) for event streaming

    - ClickHouse for analytics data

    - YDB for operational database storage

## Assumptions

- Yandex Maps as an external service provides real‑time routing and traffic data
- Communication between services in the Kubernetes cluster uses gRPC

## Open questions

- How is real‑time driver location tracking and geospatial search optimized in the Dispatch Service?
- What specific failure‑handling and retry mechanisms are implemented for payment processing when the external payment service is temporarily unavailable?
