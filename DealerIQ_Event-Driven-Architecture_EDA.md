# Event-Driven Architecture (EDA) – Intelligence Suite: DealerIQ

## Overview

DealerIQ uses an **event-driven, microservice-based architecture** that unifies transactional operations and AI/ML/GenAI pipelines through a shared event bus.  
Events enable **real-time reaction**, **loose coupling**, and **continuous learning** across all intelligence and operational layers.

## Event Bus & Infrastructure

| **Component** | **Purpose** | **Technology Stack** |
|----------------|-------------|----------------------|
| **Kafka / Pulsar Cluster** | Primary event backbone (transaction + ML/AI). | Apache Kafka / Apache Pulsar |
| **Schema Registry** | Enforce canonical Avro/JSON schemas. | Confluent Schema Registry |
| **Event Gateway** | Handles ingestion and routing (REST → event stream). | Spring Cloud Stream / Kafka Connect |
| **Stream Processor** | Stateful stream computation. | Apache Flink / Spark Streaming |
| **Event Storage** | Persist raw and processed events. | S3 + MinIO + Delta Lake |
| **Notification Hub** | Push real-time events to UI and mobile. | WebSocket / Socket.IO |
| **Event Replay Service** | Re-run historical streams for debugging or model evaluation. | Kafka Replay / Stream Replayer |

## Transactional Events Layer

| **Event Type** | **Producer** | **Consumer(s)** | **Purpose** |
|----------------|---------------|-----------------|--------------|
| `dealer.created` | Dealer-Service | CRM, Analytics, LLM-Orchestrator | Register new dealer, trigger embedding generation. |
| `dealer.updated` | Dealer-Service | Analytics, Forecasting | Update engagement metrics and segmentation. |
| `product.updated` | Product-Service | Vector DB, Search-Service | Update embeddings and product search index. |
| `inventory.changed` | Inventory-Service | Dealer Dashboard, Forecast Model | Reflect real-time stock changes. |
| `order.placed` | Dealer Portal | Inventory, Pricing, Forecasting | Trigger fulfillment and update demand model. |
| `shipment.dispatched` | Order-Service | Dealer Portal, Insight-Service | Update delivery KPIs. |
| `price.changed` | Pricing-Service | Catalog, Dealer Portal, ML retraining pipeline | Adjust visible pricing and retrain elasticity model. |
| `promotion.activated` | Marketing-Service | Dealer Portal, Forecast Model | Influence predicted dealer behavior. |
| `ticket.created` | Support-Service | LLM-Orchestrator, Analytics | Feed support queries into GenAI pipeline. |
| `feedback.submitted` | Dealer Portal | Feedback-Service, RLHF Trainer | Used for model fine-tuning and sentiment tracking. |

## ML / AI Event Layer

| **Event Type** | **Producer** | **Consumer** | **Purpose** |
|----------------|---------------|--------------|--------------|
| `feature.updated` | Feature Store | Training Pipelines | Keep ML models up-to-date with real-time features. |
| `model.trained` | MLflow / Kubeflow | Registry, Deployment | Register and version new models. |
| `model.deployed` | Deployment Service | AIOps, Insight-Service | Notify consumers that a new model is live. |
| `model.performance.reported` | Monitoring Agent | MLflow, AIOps | Push accuracy/latency metrics. |
| `model.drift.detected` | Drift Detector | MLOps Pipeline | Trigger retraining automatically. |
| `embedding.generated` | Embedding Worker | Vector DB | Persist new product/doc embeddings. |
| `embedding.drift.alert` | Validator Service | Embedding Retrain Job | Update embedding models. |
| `forecast.generated` | Forecast Service | Dealer Dashboard | Provide predictive insights. |
| `forecast.error.reported` | Forecast Evaluator | MLOps | Evaluate and retrain forecast model. |
| `recommender.output` | Recommender Engine | Dealer Portal, Insight-Service | Real-time personalized recommendations. |

## GenAI Event Layer

| **Event Type** | **Producer** | **Consumer** | **Purpose** |
|----------------|---------------|--------------|--------------|
| `llm.prompt.requested` | Dealer Portal / API | LLM-Orchestrator | Record incoming dealer queries. |
| `llm.response.generated` | LLM-Orchestrator | Feedback-Service, Logging | Capture model response for audit. |
| `llm.feedback.received` | Dealer Portal | RLHF Trainer | Feed human feedback into reinforcement loop. |
| `prompt.template.updated` | Prompt Repository | GenAIOps Monitor | Notify about prompt version changes. |
| `fine_tune.started` | Fine-Tuning Studio | MLOps / MLflow | Record new fine-tuning job. |
| `fine_tune.completed` | Fine-Tuning Studio | Model Registry | Register new DealerIQ-LLM version. |
| `genai.cost.metric` | GenAIOps Agent | AIOps | Monitor token usage and cost. |
| `prompt.evaluation.completed` | Evaluation Pipeline | GenAIOps Dashboard | Update prompt success rates. |
| `context.embedding.requested` | RAG Layer | Vector DB | Retrieve embeddings for new LLM session. |
| `context.embedding.updated` | Vector DB | LLM-Orchestrator | Refresh RAG grounding cache. |

## Ops & Governance Event Layer

| **Event Type** | **Producer** | **Consumer** | **Purpose** |
|----------------|---------------|--------------|--------------|
| `data.pipeline.started` | Airflow / dbt | DataOps Monitor | Log data ingestion events. |
| `data.validation.failed` | Great Expectations | DataOps Monitor | Trigger data quality alerts. |
| `data.contract.violation` | Schema Registry | Governance Engine | Block non-compliant event payloads. |
| `ml.pipeline.failed` | Kubeflow / MLflow | AIOps | Auto-open incident for failure. |
| `drift.alert` | Drift Detector | MLOps / AIOps | Initiate retraining and notify Ops. |
| `incident.created` | AIOps | Governance, Slack Bot | Open operational incident. |
| `incident.resolved` | AIOps | Insight-Service | Log MTTR and RCA data. |
| `metric.anomaly.detected` | Prometheus / Dynatrace | AIOps | Trigger predictive alerts. |
| `cost.spike.detected` | LangSmith | Finance Dashboard | Notify cost overrun. |
| `audit.event.logged` | Security Service | Governance Dashboard | Record all model and data access logs. |

## Event Processing Pipelines

| **Pipeline Name** | **Function** | **Key Components** |
|--------------------|--------------|--------------------|
| **Transactional Stream** | Real-time dealer operations. | Kafka → Flink → Redis / PostgreSQL |
| **Feature Stream** | Online feature updates for ML models. | Kafka → Feast → MLflow |
| **Forecast Stream** | Predictive demand forecasts. | Flink → S3 → Insight-Service |
| **Feedback Stream** | RLHF data ingestion. | Kafka → Python Worker → MLflow |
| **Embedding Stream** | Continuous embedding refresh. | Python Worker → Weaviate |
| **Observability Stream** | Performance and latency monitoring. | Prometheus → Grafana → AIOps |
| **Governance Stream** | Compliance, audit, and lineage. | Schema Registry → DataHub |

## Event Patterns Used in DealerIQ

| **Pattern** | **Description** | **Used For** |
|--------------|----------------|--------------|
| **Event Notification** | Services notify others of state changes. | Price changes, stock updates. |
| **Event-Carried State Transfer** | Payload includes data snapshot. | Dealer, product, or order updates. |
| **Event Sourcing** | Reconstruct state from event log. | Orders, Inventory movements. |
| **CQRS (Command Query Responsibility Segregation)** | Separate command (write) and query (read) models. | Dealer portal and analytics dashboards. |
| **Event Choreography** | Event flow without central orchestrator. | Inventory → Pricing → Forecast chain. |
| **Orchestration with Sagas** | Coordinated long-running transactions. | Order → Payment → Shipment workflows. |
| **Streaming ETL** | Transform and join data in motion. | Real-time feature computation. |
| **Change Data Capture (CDC)** | Capture DB changes as events. | ERP/CRM integrations. |

## Event Governance & Observability

| **Aspect** | **Implementation** |
|-------------|--------------------|
| **Schema Management** | Confluent Schema Registry enforcing Avro schemas. |
| **Lineage Tracking** | DataHub + Kafka Connect metadata. |
| **Replay & Time Travel** | Kafka log retention + timestamp offsets. |
| **Event Catalog** | Stored in Data Catalog as part of EDM. |
| **Security & Access** | OAuth2 tokens + topic-level ACLs. |
| **Monitoring** | Prometheus metrics + Grafana dashboards. |
| **Error Handling** | Dead Letter Queues (DLQ) + AIOps alerting. |
| **Auditing** | All event reads/writes logged to AuditTrail. |

```text
Dealer Portal
↓ (feedback.submitted)
Kafka Topic → Feedback-Service
↓
Kafka Topic → RLHF Trainer
↓
Fine-Tuning Studio
↓ (fine_tune.completed)
Model Registry
↓ (model.deployed)
LLM-Orchestrator → Dealer Portal
```

This pattern ensures **closed-loop learning** where every interaction directly improves DealerIQ intelligence.

## Key Benefits of DealerIQ’s EDA

- **Unified Real-Time Backbone** connecting transactional, analytical, and AI domains.  
- **Loose Coupling:** Microservices evolve independently.  
- **Scalability:** Event partitioning enables parallel ML retraining and forecasting.  
- **Observability:** Full lineage and replayability of all events.  
- **Continuous Learning:** Dealer feedback → LLM fine-tune → redeploy within hours.  
- **Cross-Cloud Ready:** Kafka, Flink, and Schema Registry integrate seamlessly with AWS, Azure, GCP.  

## Integration with EDM & MLOps

- Each event conforms to **DealerIQ’s Enterprise Data Model (EDM)** schema.  
- ML events automatically populate **Feature Store (Feast)** and **MLflow**.  
- Drift and anomaly events connect to **AIOps pipelines**.  
- GenAI feedback and prompt metrics feed **GenAIOps dashboards**.  

## Summary

DealerIQ’s **Event-Driven Architecture** bridges operational data, ML pipelines, GenAI intelligence, and observability.  
It enables real-time, data-centric, and self-healing enterprise AI — the foundation of a **continuously learning dealer ecosystem**.

