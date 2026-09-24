# Architecture & Systems Thinking

## Fase I — Aprender a pensar como arquitecto

### Semana 1 — Systems Thinking: dejar de pensar en código

**Objetivo:** Desarrollar una visión sistémica del software, pasando de pensar en funciones y componentes aislados a comprender problemas, límites, actores, flujos, dependencias y restricciones como partes de un sistema.

**Temas:**
- Systems thinking aplicado al software
- Sistema vs. componente
- Límites del sistema
- Actores y stakeholders
- Inputs / outputs
- Flujos de información
- Dependencias
- Feedback loops
- Complejidad
- Constraints
- Functional vs. non-functional requirements

### Semana 2 — Requirements & Constraints

**Objetivo:** Aprender a transformar necesidades de negocio y requisitos ambiguos en condiciones arquitectónicas concretas, medibles y utilizables para tomar decisiones de diseño.

**Temas:**
- Functional requirements
- Non-functional requirements
- Quality attributes
- Constraints
- Assumptions
- Business requirements
- Workload characteristics
- Scale
- Latency
- Availability
- Consistency
- Security
- Cost constraints

### Semana 3 — Software Architecture & Modularity

**Objetivo:** Comprender la arquitectura como la definición de límites, responsabilidades y dependencias entre partes del sistema, y evaluar distintas estrategias de modularidad.

**Temas:**
- Architecture vs. design
- Layered architecture
- Hexagonal architecture
- Clean architecture
- Modular architecture
- Coupling
- Cohesion
- Separation of concerns
- Dependency direction
- Bounded contexts
- Modular monolith
- Monolith vs. microservices

## Fase II — Sistemas distribuidos

### Semana 4 — Distributed Systems Fundamentals

**Objetivo:** Comprender las propiedades y problemas fundamentales de los sistemas distribuidos, especialmente latencia, fallos parciales, consistencia, disponibilidad y coordinación entre componentes.

**Temas:**
- Network as a boundary
- Latency
- Partial failure
- Timeouts
- Retries
- Idempotency
- Distributed state
- Replication
- Consistency
- Availability
- CAP theorem
- Failure modes

### Semana 5 — APIs & Communication Architecture

**Objetivo:** Aprender a seleccionar mecanismos de comunicación síncronos y asíncronos según las necesidades del sistema, evaluando sus implicaciones en acoplamiento, resiliencia y escalabilidad.

**Temas:**
- REST
- RPC / gRPC
- Synchronous communication
- Asynchronous communication
- Messaging
- Queues
- Pub/Sub
- Events
- Webhooks
- API gateways
- Rate limiting
- Idempotency
- Contracts

### Semana 6 — Data Architecture

**Objetivo:** Desarrollar la capacidad de diseñar la arquitectura de datos considerando workloads, ownership, consistencia, disponibilidad, almacenamiento, replicación y evolución de la información.

**Temas:**
- Relational vs. NoSQL
- OLTP vs. analytical workloads
- Data ownership
- Data lifecycle
- Replication
- Partitioning
- Sharding
- Caching
- Consistency
- Transactions
- Eventual consistency
- Data pipelines
- Data as a system dependency

## Fase III — Escala, resiliencia y operación

### Semana 7 — Scalability & Performance

**Objetivo:** Comprender cómo diseñar sistemas capaces de crecer en volumen y carga, identificando cuellos de botella y seleccionando mecanismos de escalabilidad y optimización adecuados.

**Temas:**
- Vertical scaling
- Horizontal scaling
- Stateless architecture
- Load balancing
- Caching
- CDN
- Connection pooling
- Backpressure
- Queues
- Partitioning
- Bottlenecks
- Throughput
- Latency
- Capacity planning

### Semana 8 — Reliability, Availability & Resilience

**Objetivo:** Diseñar sistemas capaces de tolerar fallos y recuperarse de ellos mediante redundancia, degradación controlada, recuperación ante desastres y objetivos medibles de confiabilidad.

**Temas:**
- Availability
- Reliability
- Resilience
- Fault tolerance
- Redundancy
- Failure domains
- Health checks
- Timeouts
- Retries
- Circuit breakers
- Graceful degradation
- Disaster recovery
- RTO
- RPO
- SLI
- SLO
- SLA

### Semana 9 — Cloud, On-Premise & Hybrid Architecture

**Objetivo:** Comprender las implicaciones arquitectónicas de desplegar sistemas en infraestructura cloud, on-premise o híbrida, evaluando control, escalabilidad, seguridad, operación, costos y dependencia de proveedores.

**Temas:**
- On-premise
- Cloud
- Hybrid
- Multi-cloud
- IaaS
- PaaS
- Managed services
- Containers
- Serverless
- Kubernetes — conceptualmente
- Networking
- Compute
- Storage
- Identity
- Infrastructure as Code — conceptualmente

## Fase IV — Arquitectura como disciplina de decisión

### Semana 10 — Cost, Performance & Trade-offs

**Objetivo:** Desarrollar la capacidad de evaluar alternativas arquitectónicas a partir de objetivos y restricciones, entendiendo que cada decisión implica beneficios, costos y compromisos.

**Temas:**
- Cost vs. performance
- Cost vs. reliability
- Complexity vs. scalability
- Consistency vs. availability
- Build vs. buy
- Managed vs. self-managed
- Simplicity vs. flexibility
- Short-term vs. long-term
- Technical debt
- Operational complexity
- Vendor lock-in

### Semana 11 — Architecture Decision-Making

**Objetivo:** Aprender a estructurar, justificar y comunicar decisiones arquitectónicas de forma explícita, considerando drivers, alternativas, restricciones, riesgos y consecuencias.

**Temas:**
- Architecture Decision Records (ADR)
- Decision drivers
- Alternatives
- Constraints
- Assumptions
- Consequences
- Architecture principles
- Risk analysis
- Technical debt
- Reversibility
- One-way vs. two-way decisions

### Semana 12 — Architecture Evolution

**Objetivo:** Comprender la arquitectura como un sistema que evoluciona con el negocio, la tecnología y las restricciones, y aprender principios para planificar cambios arquitectónicos incrementales.

**Temas:**
- Evolutionary architecture
- Architecture runway
- Incremental migration
- Strangler pattern
- Monolith → modular monolith
- Modular monolith → services
- Migration strategies
- Compatibility
- Technical debt
- Architecture fitness
- Evolution under changing requirements
