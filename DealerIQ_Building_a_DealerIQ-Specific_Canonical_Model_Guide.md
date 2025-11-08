# Enterprise Data Model (EDM) Strategy – Intelligence Suite: DealerIQ

## Overview

The **Enterprise Data Model (EDM)** serves as the **semantic backbone** of *Intelligence Suite: DealerIQ*.  
It defines every core business entity, relationship, and data standard across dealers, distributors, suppliers, and internal intelligence systems.  

DealerIQ’s EDM ensures that data remains **consistent, interoperable, and explainable** across all domains — from transactional APIs to ML models and GenAI pipelines.

## 1. EDM Purpose

- Create a **unified data language** across all DealerIQ microservices.  
- Standardize schemas for integration with **ERP, CRM, OEM, and supplier systems**.  
- Align data pipelines with **industry data standards (GS1, SCOR, UN/CEFACT)**.  
- Serve as the foundation for **ML features, vector embeddings, and LLM context grounding**.  
- Enable **governed data lineage** and quality metrics across the data lifecycle.

## 2. EDM Foundations and Standards

| **Framework / Standard** | **Purpose** | **Relevance for DealerIQ** |
|---------------------------|-------------|-----------------------------|
| **GS1 (GDSN, GTIN, GLN)** | Product & supply chain identifiers | Unique SKU and warehouse identification |
| **UN/CEFACT CCL** | Global trade components | Dealer–Manufacturer orders and shipments |
| **SCOR (APICS)** | Supply chain reference processes | Aligning sales → order → delivery flow |
| **OAGIS** | Open manufacturing data model | Inventory and procurement event messages |
| **FIBO** | Financial ontology | Pricing, billing, and transaction alignment |
| **ISO 8000 / 22745** | Master data quality | Attribute standardization and validation |
| **Microsoft Common Data Model (CDM)** | Semantic schema repository | Quick-start for analytics integration |
| **Data Vault 2.0** | Enterprise warehouse design | Scalable modeling for multi-source pipelines |

## 3. Core DealerIQ Domains and Entities

### Dealer Domain
| **Entity** | **Attributes (Examples)** |
|-------------|---------------------------|
| `Dealer` | DealerID, Name, Tier, Territory, Segment, CreditLimit, EngagementScore |
| `DealerContact` | ContactID, DealerID, Name, Email, Role |
| `DealerRegion` | RegionID, Name, ParentRegion, Currency |

### Product Domain
| **Entity** | **Attributes (Examples)** |
|-------------|---------------------------|
| `Product` | SKU, GTIN, Name, Brand, Category, LifecycleStage, CompatibilitySet |
| `Supplier` | SupplierID, Name, Location, ReliabilityScore |
| `ProductSpec` | SpecID, SKU, Attribute, Value, Unit |
| `ProductImage` | ImageID, SKU, URL, EmbeddingVector |

### Inventory Domain
| **Entity** | **Attributes (Examples)** |
|-------------|---------------------------|
| `Inventory` | StockID, SKU, LocationID, Quantity, ReorderPoint, LeadTime |
| `Warehouse` | WarehouseID, Region, Capacity, Contact |
| `StockMovement` | MovementID, SKU, Source, Destination, Quantity, Date |

### Pricing Domain
| **Entity** | **Attributes (Examples)** |
|-------------|---------------------------|
| `PriceList` | PriceID, SKU, DealerID, BasePrice, DiscountRate, ValidFrom, ValidTo |
| `Promotion` | PromoID, ProductGroup, StartDate, EndDate, ROI, Region |
| `CurrencyRate` | Currency, Rate, EffectiveDate |

### Order Domain
| **Entity** | **Attributes (Examples)** |
|-------------|---------------------------|
| `Order` | OrderID, DealerID, SKU, Quantity, Price, OrderDate, Status |
| `Invoice` | InvoiceID, OrderID, TotalAmount, Tax, Currency, PaymentStatus |
| `Shipment` | ShipmentID, OrderID, Carrier, ETA, TrackingNumber |

### Support & Feedback Domain
| **Entity** | **Attributes (Examples)** |
|-------------|---------------------------|
| `SupportTicket` | TicketID, DealerID, Category, Severity, Status, AssignedTo |
| `Feedback` | FeedbackID, DealerID, Message, Sentiment, Timestamp |
| `KnowledgeArticle` | ArticleID, Topic, Summary, EmbeddingVector |

### Intelligence & Forecast Domain
| **Entity** | **Attributes (Examples)** |
|-------------|---------------------------|
| `Forecast` | ForecastID, SKU, Region, PredictedDemand, ModelVersion |
| `Insight` | InsightID, DealerID, KPI, ConfidenceScore, GeneratedDate |
| `Embedding` | EmbeddingID, SourceType, Vector, ModelUsed, LastUpdated |

## 4. Integration Schema and Canonical Events

**Canonical Schema Format:** JSON Schema / Avro / Parquet  
**Integration Channels:** Kafka Topics, REST APIs, GraphQL

| **Topic / API** | **Purpose** | **Schema Example** |
|------------------|-------------|--------------------|
| `dealer-updates` | Dealer master data changes | Dealer entity updates from CRM |
| `inventory-events` | Stock level updates | Real-time feed from warehouse system |
| `pricing-changes` | Price and promotion updates | Dynamic pricing broadcast |
| `order-stream` | New orders and status changes | Dealer–OEM order flow |
| `feedback-stream` | Dealer feedback for RLHF | Used in fine-tuning and analysis |

## 5. EDM Implementation Strategy for DealerIQ

| **Stage** | **Implementation Step** | **Tools / Platform** |
|------------|-------------------------|----------------------|
| **1. Data Modeling** | Design conceptual, logical, and physical data layers | ER/Studio, Hackolade, Draw.io |
| **2. Canonical Schema Definition** | Define JSON-LD or Avro schemas | Kafka Schema Registry |
| **3. Data Integration** | Map ERP, CRM, OEM feeds to EDM | Airbyte / dbt |
| **4. Metadata Catalog** | Manage business glossary & lineage | DataHub / Apache Atlas |
| **5. Data Quality Enforcement** | Apply validation & rules | Great Expectations |
| **6. Master Data Management (MDM)** | Synchronize dealers & products | AWS Glue / Informatica MDM |
| **7. ML Feature Alignment** | Map EDM attributes → ML Feature Store | Feast |
| **8. Vector & Graph Mapping** | Sync EDM entities to Weaviate and Neo4j | Spring Boot connectors |
| **9. Data Governance** | Define stewardship & access control | DAMA DMBoK policies |
| **10. Change Management** | Version control EDM schemas | Git + DVC + CI/CD pipelines |

## 6. EDM Deliverables

| **Deliverable** | **File / Format** | **Purpose** |
|------------------|------------------|-------------|
| Conceptual Model Diagram | `EDM_Conceptual_Model.drawio` | High-level domain overview |
| Canonical Schema | `EDM_Canonical_Schema.json` | JSON-based entity definitions |
| Data Lineage Map | `EDM_Data_Lineage.yaml` | Tracks source → transformation → target |
| Data Glossary | `EDM_Data_Glossary.csv` | Business term definitions |
| Schema Registry Files | `.avsc` | Kafka event schema definitions |
| Database Schema | `.sql` | PostgreSQL / Neo4j / Weaviate schema creation |
| Governance Policy | `EDM_Data_Policy.md` | Access, quality, and retention policies |

## 7. EDM Diagram – DealerIQ Canonical Entities

Below is a high-level entity-relationship view of the **DealerIQ Enterprise Data Model**, showing relationships across all domains.

![DealerIQ EDM Diagram](./docs/EDM_DealerIQ_Canonical_Model.svg)

## 8. EDM Verification Checklist

| **Dimension** | **Verification Goal** |
|----------------|-----------------------|
| Coverage | All core business domains are modeled (Dealer, Product, Inventory, Pricing, Order, Support). |
| Consistency | Same entity names and definitions across APIs, ML, and DB layers. |
| Interoperability | Compatible with ERP, CRM, OEM, and external APIs. |
| Extensibility | Schema supports adding new attributes without breaking integration. |
| Traceability | Each ML feature links to a source EDM attribute. |
| Governance | Stewardship, access levels, and lineage defined. |
| Compliance | Meets ISO 8000 and GDPR requirements. |
| Performance | Optimized for transactional (OLTP) and analytical (OLAP) workloads. |

## Summary

The **DealerIQ Enterprise Data Model** ensures:
- Semantic consistency across all microservices and data systems.  
- A single canonical structure for both **operational and analytical** workflows.  
- Direct mapping between **business data, ML features, and AI embeddings**.  
- Alignment with **global standards** (GS1, UN/CEFACT, SCOR, ISO).  
- Scalable, governed, and extensible foundation for continuous AI-driven intelligence.
