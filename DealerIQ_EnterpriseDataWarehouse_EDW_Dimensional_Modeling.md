# Enterprise Data Warehouse (EDW) & Dimensional Modeling – Intelligence Suite: DealerIQ

## Overview

The **DealerIQ Enterprise Data Warehouse (EDW)** serves as the analytical backbone of the platform,  
providing a **single source of truth** for all business metrics, dealer performance analytics, and AI feature enrichment.

It harmonizes data from:
- **Transactional systems** (Dealer, Product, Orders, Pricing, Inventory)
- **ML/AI systems** (Model metrics, forecasts, insights)
- **GenAI systems** (LLM feedback, embeddings, responses)
- **Ops systems** (DataOps, MLOps, AIOps)

The EDW follows a **Dimensional Modeling** approach (Kimball-style) with:
- **Conformed Dimensions** for unified reference data.  
- **Fact Tables** for detailed measurable business events.  
- **Analytical Data Marts** for forecasting, recommendations, and executive insights.

## EDW Architecture Overview

| **Layer** | **Purpose** | **Technologies** |
|------------|--------------|------------------|
| **Source Layer** | Ingest operational & AI data from Kafka, APIs, and databases. | Kafka Connect, Airbyte, dbt |
| **Staging Layer** | Clean, normalize, and structure incoming data. | S3 / Delta Lake / PostgreSQL staging |
| **Integration Layer (EDM Alignment)** | Map data to canonical EDM entities. | dbt + EDM canonical schema |
| **Dimensional Layer** | Build star/snowflake schema models. | Snowflake / BigQuery / Redshift |
| **Data Marts Layer** | Domain-specific analytical models. | dbt models + BI semantic layer |
| **Consumption Layer** | Feed dashboards, AI, and reports. | Power BI / Looker / Superset / LLM Contextual RAG |

## Dimensional Modeling Principles

| **Design Element** | **Purpose** |
|---------------------|-------------|
| **Star Schema** | Simplified, high-performance structure for BI queries. |
| **Conformed Dimensions** | Shared dimensions across fact tables (Dealer, Product, Date). |
| **Slowly Changing Dimensions (SCD)** | Track changes in dealer tiers, product specs, or regions. |
| **Fact Tables** | Record measurable events (sales, orders, forecasts). |
| **Aggregations & OLAP Cubes** | Precomputed metrics for faster insights. |
| **Metadata & Lineage** | Link to EDM and governance layer for traceability. |

## Core Dimensions

### Dealer Dimension (`dim_dealer`)
| **Field** | **Description** |
|------------|----------------|
| Dealer_Key | Surrogate primary key |
| Dealer_ID | Natural business key |
| Dealer_Name | Dealer name |
| Region | Assigned region or territory |
| Tier | Gold, Silver, Bronze classification |
| Segment | Dealer market segment |
| Engagement_Score | Derived metric from interactions |
| Active_Flag | Active or inactive status |
| Effective_Date / Expiry_Date | SCD Type 2 tracking |

### Product Dimension (`dim_product`)
| **Field** | **Description** |
|------------|----------------|
| Product_Key | Surrogate key |
| SKU | Stock Keeping Unit |
| Product_Name | Name / description |
| Brand | Brand or manufacturer |
| Category | Product category hierarchy |
| Compatibility_Group | Related parts or assemblies |
| Lifecycle_Stage | New / Active / Discontinued |
| Supplier_Key | FK to supplier dimension |
| Effective_Date / Expiry_Date | Versioning support |

### Pricing Dimension (`dim_pricing`)
| **Field** | **Description** |
|------------|----------------|
| Price_Key | Surrogate key |
| SKU | Associated SKU |
| Dealer_Key | Linked dealer |
| Base_Price | Default list price |
| Discount_Rate | % applied per dealer |
| Promotion_Flag | Y/N for active campaigns |
| Valid_From / Valid_To | Date validity |
| Currency | Transaction currency |

### Inventory Dimension (`dim_inventory`)
| **Field** | **Description** |
|------------|----------------|
| Inventory_Key | Surrogate key |
| Warehouse_Key | FK to warehouse dimension |
| SKU | Product reference |
| Location | Warehouse / region |
| Capacity | Total stock capacity |
| Current_Stock | Units in stock |
| Reorder_Point | Trigger threshold |
| Lead_Time | Days to replenish |
| Updated_Timestamp | Last refresh time |

### Date Dimension (`dim_date`)
| **Field** | **Description** |
|------------|----------------|
| Date_Key | Surrogate key |
| Calendar_Date | Full date |
| Day_Name | Day of week |
| Month_Name | Month |
| Quarter | Quarter (Q1–Q4) |
| Year | Calendar year |
| Fiscal_Year | Fiscal period |
| Weekend_Flag | Boolean flag |

### Other Conformed Dimensions
| **Dimension** | **Purpose** |
|----------------|-------------|
| `dim_region` | Defines hierarchical regions (zone → region → territory). |
| `dim_promotion` | Promotion campaigns metadata. |
| `dim_feedback` | Dealer sentiment classification. |
| `dim_forecast_model` | AI model reference and versioning. |
| `dim_support_agent` | Technical and sales support contacts. |
| `dim_supplier` | Manufacturer or OEM suppliers. |
| `dim_currency` | Exchange rate reference. |

## Fact Tables

### Fact Sales (`fact_sales`)
| **Measure** | **Description** |
|--------------|----------------|
| Sales_Key | Primary identifier |
| Dealer_Key | Dealer dimension link |
| Product_Key | Product reference |
| Date_Key | Transaction date |
| Quantity_Sold | Units sold |
| Sales_Amount | Total amount |
| Discount_Applied | Total discount value |
| Margin | Profit per sale |
| Promotion_Key | FK to promotion |
| Forecast_Key | FK to forecast |

### Fact Inventory Snapshot (`fact_inventory`)
| **Measure** | **Description** |
|--------------|----------------|
| Snapshot_Key | Primary key |
| Date_Key | Date reference |
| Product_Key | SKU link |
| Warehouse_Key | Location reference |
| On_Hand_Qty | Units currently available |
| Reserved_Qty | Allocated units |
| Stock_Out_Flag | Boolean for out-of-stock |
| Reorder_Trigger | Boolean if reorder required |

### Fact Orders (`fact_order`)
| **Measure** | **Description** |
|--------------|----------------|
| Order_Key | Primary key |
| Dealer_Key | FK to dealer |
| Product_Key | FK to product |
| Date_Key | FK to date |
| Order_ID | Natural order key |
| Order_Status | Current status |
| Total_Value | Value of order |
| Fulfillment_Days | Lead time for order |
| Shipment_Key | Linked shipment data |

### Fact Forecast (`fact_forecast`)
| **Measure** | **Description** |
|--------------|----------------|
| Forecast_Key | Primary key |
| Model_Key | Forecast model reference |
| SKU | Product reference |
| Region_Key | Regional link |
| Predicted_Demand | Forecasted volume |
| Actual_Demand | Real observed demand |
| Error_MAE | Mean absolute error |
| Error_RMSE | Root mean square error |
| Forecast_Date | Prediction date |

### Fact Feedback (`fact_feedback`)
| **Measure** | **Description** |
|--------------|----------------|
| Feedback_Key | Primary key |
| Dealer_Key | FK to dealer |
| Date_Key | FK to date |
| Sentiment | Positive, Neutral, Negative |
| Feedback_Text | Text snippet |
| Response_Time | Time to respond |
| Satisfaction_Score | Derived rating |
| RLHF_Used_Flag | Mark if used in fine-tuning |

### Fact Insight (`fact_insight`)
| **Measure** | **Description** |
|--------------|----------------|
| Insight_Key | Primary key |
| Dealer_Key | FK to dealer |
| KPI | Type of insight generated |
| KPI_Value | Calculated metric |
| Confidence_Score | AI-generated confidence |
| Model_Version | Originating AI model |
| Generated_Timestamp | Timestamp of insight |

## Analytical Data Marts

| **Data Mart** | **Purpose** |
|----------------|-------------|
| **Dealer Performance Mart** | Tracks dealer KPIs, revenue, churn, and engagement. |
| **Product Profitability Mart** | Evaluates margins and demand per product and region. |
| **Inventory Optimization Mart** | Forecasts restocking needs and warehouse capacity. |
| **Pricing & Promotion Mart** | Analyzes discount impact and campaign ROI. |
| **Forecast Accuracy Mart** | Monitors model performance metrics (MAE, RMSE). |
| **Feedback & Sentiment Mart** | Tracks dealer satisfaction and support response times. |
| **AI Model Operations Mart** | Summarizes ML/LLM metrics for governance dashboards. |

## ETL / ELT & Data Pipeline Orchestration

| **Process** | **Tool / Platform** | **Description** |
|--------------|----------------------|-----------------|
| Ingestion | Airbyte / Fivetran | Pulls data from ERP, CRM, Supplier APIs |
| Transformation | dbt | Applies EDM-aligned transformations |
| Feature Generation | Feast | Syncs ML features into Fact tables |
| Data Quality | Great Expectations | Validates schema and metrics consistency |
| Lineage & Governance | Apache Atlas / DataHub | Tracks source → transformation → consumption |
| Orchestration | Airflow / Argo Workflows | Manages ETL scheduling |
| Warehouse Storage | Snowflake / BigQuery / Redshift | Central data warehouse |
| Visualization | Power BI / Looker / Superset | BI dashboards and reporting |
| RAG Integration | Weaviate / Pinecone | Expose analytical context to LLMs |

## Fact-Dimension Relationship Diagram

(Refer to `/docs/EDW_DealerIQ_Dimensional_Model.svg` for the visual diagram.)

Core relationships:

```text
dim_dealer ─┬── fact_sales ─── dim_product
├── fact_order ─── dim_pricing
├── fact_feedback
├── fact_forecast ─── dim_forecast_model
└── fact_insight ─── dim_date
```

All fact tables share **conformed dimensions** for consistent analysis across domains.

## Summary

DealerIQ’s **EDW and Dimensional Model**:
- Consolidates **operational + AI + GenAI** data into a single analytical ecosystem.  
- Ensures **semantic alignment with EDM** and **lineage visibility** across data sources.  
- Provides **real-time, AI-enhanced business insights** and **LLM-ready analytical grounding**.  
- Scales seamlessly for **predictive analytics, explainability, and executive dashboards**.
