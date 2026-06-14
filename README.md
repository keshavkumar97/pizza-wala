# 🍕 Real-Time Pizza Shop Order & Tracker App

This is a comprehensive, hands-on architectural project designed to showcase end-to-end event streaming, structural object creation, distributed caching, containerisation, and local cloud orchestration using a Domino's-style real-time tracking pipeline.

---

## 🛠️ Complete Technical Specification

### 1. Core Component Stack
*   **Frontend UI:** React (Vite ecosystem, JavaScript/TypeScript, TailwindCSS for layout).
*   **Backend API:** Java 17 (or 21) & Spring Boot 3.x.
*   **Message Broker:** Apache Kafka (Distributed commit log & streaming backbone).
*   **Data Serialization:** Apache Avro (Binary serialization protocol) backed by Confluent Schema Registry.
*   **Caching Engine:** Redis Cache (In-memory key-value data structure store).
*   **Container Platform:** Docker & Docker Desktop.
*   **Orchestration Environment:** Minikube (Local Single-Node Kubernetes Cluster).

### 2. Mandatory Local Installations
Ensure you run the commands matching your native operating system before coding:

*   **Mac (Using Homebrew):**
    ```bash
    brew install openjdk@17 maven node minikube kubernetes-cli
    ```
*   **Windows (Using Chocolatey in Administrative PowerShell):**
    ```powershell
    choco install openjdk17 maven nodejs-lts minikube kubernetes-cli
    ```
*   **Linux (Debian/Ubuntu Core Utilities):**
    ```bash
    sudo apt update
    sudo apt install openjdk-17-jdk maven nodejs npm
    # Minikube/Kubectl installation via direct curl binary download per official docs
    ```

---

## 📐 Architectural Patterns & Design Principles Used

To make this a highly professional project, the architecture explicitly implements these critical software design paradigms:

### 1. Structural Pattern: The Decorator Pattern
*   **How it is applied:** Instead of creating hundreds of individual classes for every combination of pizza toppings (e.g., `CheesePizza`, `PepperoniAndJalapenoPizza`), you create a base `PlainPizza` class. When a user checks toppings on the React UI, your Spring Boot controller dynamically wraps (decorates) the base pizza object at runtime to calculate the final price and description before sending it to Kafka.
*   **Why it matters:** It satisfies the **Open/Closed Principle**. You can add 20 new pizza toppings in the future by just creating new decorator classes. You never have to touch or risk breaking your existing `PlainPizza` core code.

### 2. CQRS (Command Query Responsibility Segregation)
*   **How it is applied:** The application completely separates data write pathways from data read pathways.
    *   *The Command Path:* The user posts an order (`POST /orders`). This writes exclusively to the Kafka Event Log. It does not wait for a database or state engine update.
    *   *The Query Path:* The user requests the current status (`GET /orders/{id}`). This reads exclusively from the ultra-fast Redis Cache.
*   **Why it matters:** It prevents read operations from locking up write operations, ensuring your system remains highly available even during massive traffic spikes.

### 3. Event-Driven Architecture (EDA) & Choreography
*   **How it is applied:** Components communicate asynchronously via events. The Spring Boot backend acts as two completely decoupled services bundled together: a Producer that issues messages to a `pizza-orders` topic, and a Consumer that independently listens, reacts, and increments processing stages in the background.
*   **Why it matters:** Eliminates tight coupling. If the consumer kitchen service fails, the API can still accept client orders because Kafka stores them safely in an immutable queue.

### 4. Schema Governance (Data Contract Principle)
*   **How it is applied:** Instead of passing loose, unstructured JSON payloads across the network, the project enforces an **Apache Avro Schema contract** controlled via the Confluent Schema Registry.
*   **Why it matters:** It guarantees type safety, slashes payload network size via binary encoding, and enforces backward/forward compatibility. Downstream systems will never break due to unexpected field changes.

### 5. Cache-Aside Pattern
*   **How it is applied:** The system uses the Redis In-Memory cluster to house short-lived, volatile tracking data. The React UI continuously polls this high-performance layer instead of generating heavy, repetitive database queries.
*   **Why it matters:** Protects your persistent database layers from read exhaustion during high-frequency status checking.

### 6. SOLID Principles Highlighted
*   **Single Responsibility Principle (SRP):** Controllers only handle incoming transport payloads; Producers only handle transmission mechanics; Consumers only handle business logic state changes.
*   **Dependency Inversion Principle (DIP):** Spring’s `@Autowired` core is used to depend on abstractions, keeping the business logic isolated from your infrastructure tools (like Kafka and Redis).

---

## 🏗️ System Architecture & Services

[ React UI Dashboard ]
│ (1) HTTP POST /orders (Write Command)
▼
[ Spring Boot API (Producer) ]
│ (2) Serializes payload to Avro -> Publishes event
▼
[ Apache Kafka Broker ] (Topic: pizza-orders)
│ (3) Asynchronous Stream Processing
▼
[ Spring Boot Core (Consumer) ]
│ (4) Simulates cooking time -> Mutates State
▼
[ Redis Cache Cluster ] <─── (5) HTTP GET Status Polling (Read Query) ─── [ React UI Dashboard ]

---

## 📋 Comprehensive Execution Plan

### 🟩 Phase 1: Infrastructure Initialization
*   [ ] Verify native system runtimes are completely installed (`java -version`, `mvn -v`, `node -v`).
*   [ ] Boot up the local isolation pod setup: `minikube start --cpus=2 --memory=4096`.
*   [ ] Launch the visual monitoring dashboard panel: `minikube dashboard`.
*   [ ] Build a localized workspace testing environment using standard Docker images.

### 🟨 Phase 2: Core Backend Engine Development
*   [ ] Scaffold a Maven project using Spring Initializr with Web, Kafka, and Redis dependencies.
*   [ ] Add the `avro-maven-plugin` configuration elements inside the compilation sector of the `pom.xml`.
*   [ ] Create your strict schema contract definition file: `src/main/resources/avro/pizza-order.avsc`.
*   [ ] Run `mvn clean compile` to activate auto-generation of Java Avro data bindings.
*   [ ] Write the decoupled structural classes: `PizzaController`, `PizzaProducer`, and `PizzaConsumer`.
*   [ ] Inject custom serialization configurations into `application.yml` targeting your internal serializers.

### 🟦 Phase 3: React Client Interface Assembly
*   [ ] Setup an isolated UI directory structure via `npm create vite@latest pizza-ui -- --template react`.
*   [ ] Install TailwindCSS or basic CSS modules for layout styling.
*   [ ] Implement the Order Form Component linking standard browser submission actions to the backend POST endpoint.
*   [ ] Create a Real-Time Tracking Progress Bar module using React hooks (`useEffect`) and an asynchronous timing interval (`setInterval`) to fetch matching status strings from the GET endpoint.

### 🟪 Phase 4: Containerisation & Local Cloud Orchestration
*   [ ] Write an optimized multi-stage `Dockerfile` to compile and containerise the Spring Boot jar artifact.
*   [ ] Write a production-ready `Dockerfile` targeting the React Vite environment.
*   [ ] Design the Kubernetes deployment architecture using descriptive YAML manifests inside a `/k8s` directory:
    *   `redis-deployment.yaml` + `redis-service.yaml`
    *   `kafka-deployment.yaml` + `kafka-service.yaml`
    *   `backend-deployment.yaml` + `backend-service.yaml`
    *   `ui-deployment.yaml` + `ui-service.yaml`
*   [ ] Direct the cluster environment to build and reference configurations using `kubectl apply -f k8s/`.
*   [ ] Use port-forwarding or Minikube service commands to access your application via your local browser.
