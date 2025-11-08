# Intelligence Suite: DealerIQ  
### Application & Integration Landscape (AI / ML / GenAI / Real-Time Focus)

## Overview

**DealerIQ** is the *Dealer & Distribution Intelligence* platform within the **Intelligence Suite**.  
It unifies **AI, ML, and Generative AI** capabilities to give dealers, distributors, and regional managers instant access to:
- real-time inventory insights,  
- predictive demand forecasting,  
- intelligent pricing, and  
- conversational AI support.

This document details how **DealerIQ’s AI and real-time ecosystem** is architected — describing all ML models, LLMs, pipelines, and integration services that power its intelligence capabilities.

## 1. Core AI / ML / GenAI Services

| **Service** | **Purpose** | **Technology Stack** |
|--------------|-------------|----------------------|
| **Dealer Catalog Copilot** | Conversational search for products, pricing, and compatibility. | Angular Chat UI → LLM-Orchestrator (LangChain + Vector DB) |
| **Smart Inventory Advisor** | Predicts reorder points and optimizes stock between warehouses. | XGBoost / Prophet + Feature Store |
| **Dynamic Pricing Optimizer** | Learns elasticity and suggests margin-optimized prices per SKU. | FastAPI + MLflow Model Registry |
| **Quote Generator AI** | Auto-generates quotes and PDFs with live prices. | GenAI templating with GPT + Pricing Service |
| **Dealer Sentiment Monitor** | Detects churn or dissatisfaction in dealer messages. | NLP Classifier + Support Logs |
| **Promotion Personalizer** | Suggests most effective promotions by dealer region. | ML Recommender + Kafka Trigger |

## 2. Real-Time Intelligence Components

| **Component** | **DealerIQ-Specific Role** | **Implementation** |
|----------------|---------------------------|--------------------|
| **Dealer Event Bus** | Captures every dealer click, search, and quote action. | Apache Kafka + Schema Registry |
| **Inventory Feed Processor** | Processes OEM and supplier feeds to update dashboards in real time. | Flink / Spark Streaming |
| **Realtime Feature Builder** | Builds ML features like "avg time between reorders" live. | Feast Online Feature Store |
| **Realtime Recommendation Engine** | Returns accessory or substitute parts under 200 ms. | Redis AI + gRPC microservice |
| **Dealer Alert Engine** | Pushes proactive notifications (“Low stock”, “New promotion”). | Node.js + Socket.IO |
| **Edge Sync Agent** | Supports offline dealer systems syncing back when online. | MQTT + SQLite cache |

## 3. Model Lifecycle & Pipeline Integrations

| **Pipeline / Module** | **Purpose** | **Integration** |
|------------------------|-------------|-----------------|
| **DealerIQ Model Training** | Train and retrain pricing, demand, and recommendation models. | Kubeflow + MLflow |
| **Fine-Tuning Studio** | Domain-specific fine-tuning for DealerIQ-LLM. | Hugging Face PEFT / LoRA |
| **Online Inference API** | Real-time predictions (pricing, reorder, promotions). | Triton / TensorFlow Serving |
| **Evaluation & Benchmark Pipeline** | Measures precision, recall, latency, cost. | EvalML + TruLens |
| **Prompt Repository** | Stores and versions prompt templates with success metrics. | PromptLayer + LangFuse |
| **Feedback-to-RLHF Loop** | Uses dealer feedback to improve model behavior weekly. | Kafka Feedback Topic + Hugging Face RLHF Trainer |

## 4. Knowledge & Data Integrations

| **Source System** | **Integration Type** | **Used By** |
|--------------------|----------------------|-------------|
| **ERP (SAP, Oracle, Odoo)** | API + Kafka Connector | Inventory & Pricing Models |
| **CRM (Salesforce, HubSpot)** | REST + Event Bridge | Dealer segmentation models |
| **Supplier Feeds (EDI/XML)** | Streaming via Kafka Connect | Stock & catalog updates |
| **Support Ticket Logs** | API + ETL | LLM fine-tuning & summarization |
| **Technical Manuals & PDFs** | Vector embeddings + RAG | DealerIQ-LLM grounding |
| **Public Catalog Data** | Batch import + embedding | Similar product discovery |

## 5. AI Integration Interfaces

| **Integration Type** | **Description** | **Consumers** |
|-----------------------|-----------------|---------------|
| **REST APIs** | DealerIQ core CRUD (dealer, product, pricing). | Dealer Portal UI |
| **GraphQL API** | Aggregated insights for dashboards. | Angular Analytics UI |
| **WebSocket API** | Streams LLM responses and alerts to the UI. | Conversational Copilot |
| **gRPC API** | Low-latency prediction endpoints. | Recommendation Engine |
| **Vector Query API** | Semantic embedding lookups. | Search-Service / LLM-Orchestrator |
| **Graph Query API** | Neo4j product compatibility reasoning. | Compatibility Checker |
| **Feedback Webhooks** | Push user feedback into MLflow retraining jobs. | Feedback-Service |

## 6. DealerIQ LLM Architecture

| **Layer** | **Purpose** | **Tech Stack** |
|------------|-------------|----------------|
| **LLM-Orchestrator** | Central brain routing requests across GPT, Claude, Mistral, DealerIQ-LLM. | LangChain + Spring AI |
| **DealerIQ-LLM** | Fine-tuned model on dealer language (product specs, support logs). | Llama-3 / Mistral-7B + LoRA |
| **RAG Retriever** | Fetches contextual data from Vector DB + Neo4j. | Weaviate / Pinecone + LangChain |
| **Embedding Service** | Generates embeddings for new docs and SKUs. | OpenAI / bge-large-en |
| **Prompt Management** | Template library + performance tracking. | LangFuse / PromptLayer |
| **Guardrails** | Filters hallucinations, PII, and unsafe prompts. | Guardrails.ai + Regex Policies |
| **LLM Cache Layer** | Response caching for high-volume queries. | Redis / LMCache |
| **Voice Layer (Optional)** | Converts speech queries to text for mobile dealers. | Whisper + LLM Pipeline |

## 🚀 7. Realtime AI Dataflow

```mermaid
flowchart LR
A[Dealer Portal UI] --> B[Search & Compare API]
B --> C[LLM Orchestrator]
C --> D[Vector DB Query]
C --> E[Neo4j Compatibility Graph]
C --> F[Pricing ML API]
D --> G[Response Assembly Engine]
F --> G
E --> G
G --> H[AI Insight Stream]
H --> I[Dealer Dashboard + Alerts]
I --> J[Feedback Service]
J --> K[MLOps Pipeline / RLHF Trainer]
K --> C
