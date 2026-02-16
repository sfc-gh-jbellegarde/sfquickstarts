author: Fredrik Goransson, Joviane Bellegarde
id: agentic-audience-analytics
summary: Build an end-to-end Media & Entertainment subscriber analytics solution with audience segmentation, ML scoring, and Snowflake Intelligence for natural language queries
categories: snowflake-site:taxonomy/solution-center/certification/quickstart, snowflake-site:taxonomy/solution-center/certification/certified-solution, snowflake-site:taxonomy/industry/media-and-entertainment, snowflake-site:taxonomy/product/ai, snowflake-site:taxonomy/snowflake-feature/streamlit-in-snowflake, snowflake-site:taxonomy/snowflake-feature/cortex-ai
environments: web
language: en
status: Published
feedback link: https://github.com/Snowflake-Labs/sfguides/issues
tags: Getting Started, Media, Entertainment, Advertising, Streamlit, Snowflake Intelligence, Cortex, Semantic View, Audience Segmentation, LTV, Churn
fork repo link: https://github.com/Snowflake-Labs/sfguide-mea-subscriber-analytics

# Agentic Audience Analytics for Media & Entertainment

## Overview

Media & Entertainment companies need to understand their subscribers deeply to deliver personalized content, optimize ad campaigns, and reduce churn. This guide demonstrates how to build a comprehensive subscriber analytics solution using **Snowflake Intelligence** and **Streamlit in Snowflake**.

In this Guide, you will build a complete audience analytics platform that:
- Generates synthetic subscriber data with realistic demographics and behaviors
- Creates data transformation layers (Generate → Ingest → Harmonize → Analyze)
- Builds ML features for churn risk and lifetime value scoring
- Enables natural language queries via Snowflake Intelligence

### What You Will Build
- Synthetic data generation pipeline for 100K+ subscribers
- Multi-schema data architecture with governed data marts
- Four Streamlit applications for data exploration and audience segmentation
- Snowflake Intelligence Agent for natural language analytics
- Semantic View for structured data access

### What You Will Learn
- How to deploy Streamlit applications in Snowflake
- How to create Semantic Views for natural language queries
- How to configure Snowflake Intelligence Agents
- How to build audience segments with ML scoring
- How to implement a medallion architecture in Snowflake

### Prerequisites
- A Snowflake account with ACCOUNTADMIN privileges
- Go to the <a href="https://signup.snowflake.com/?utm_source=snowflake-devrel&utm_medium=developer-guides&utm_cta=developer-guides" target="_blank">Snowflake</a> sign-up page and register for a free account
- Basic familiarity with SQL and Python

<!-- ------------------------ -->
## Architecture Overview

### Solution Architecture

The Agentic Audience Analytics solution provides end-to-end visibility into subscriber behavior:

![Architecture Diagram](assets/architecture.png)

**Data Layer:**
- **GENERATE**: Synthetic data generation (subscribers, events, campaigns)
- **INGEST**: Raw data landing zone
- **HARMONIZED**: Business-ready data marts
- **ANALYSE**: ML features and scoring models

**Application Layer:**
- **Streamlit Dashboard**: Subscriber 360 view
- **Segment Builder**: Audience segmentation tool
- **Snowflake Intelligence Agent**: Natural language interface
- **Semantic View**: Structured query definitions

### Key Personas

| Persona | Focus Area | Key Metrics |
|---------|------------|-------------|
| **Data Analyst** | Subscriber exploration | Demographics, behavior patterns |
| **Marketing Manager** | Audience segmentation | Segment size, conversion rates |
| **Ad Sales Executive** | Campaign performance | CPM, fill rates, revenue |
| **Executive** | Business health | LTV, churn risk, subscriber growth |

### Data Model

The solution generates approximately:
- 100,000+ subscriber profiles
- 5M+ clickstream events
- 1,000+ ad campaigns
- Regional demographics across UK local authority districts

<!-- ------------------------ -->
## Setup Snowflake Environment

In this step, you'll create all the Snowflake objects needed for the Audience Analytics solution.

### Step 1: Create Database Objects

1. In Snowsight, click **Projects**, then **Workspaces** in the left navigation, or <a href="https://app.snowflake.com/_deeplink/#/workspaces?utm_source=quickstart&utm_medium=quickstart&utm_campaign=-us-en-all&utm_content=agentic-audience-analytics" target="_blank">click here</a> to go there directly
2. Click `+ Add new` to create a new Workspace
3. Click **SQL File** to create a new SQL file
4. Copy the setup script from <a href="https://github.com/Snowflake-Labs/sfguide-mea-subscriber-analytics/blob/main/scripts/sql/setup.sql" target="_blank">setup.sql</a> and paste it into your SQL file

### Step 2: Run Infrastructure Setup

Run the setup script to create:
- **Database**: **AME_AD_SALES_DEMO** with 8 schemas (GENERATE, INGEST, HARMONIZED, ANALYSE, DCR_ANALYSIS, ACTIVATION, DATA_SHARING, APPS)
- **Warehouse**: **APP_WH** for queries
- **Role**: **AME_AD_SALES_DEMO_ADMIN** with appropriate privileges
- **GitHub External Access Integration**: For automatic data loading from GitHub
- **Stages**: **DEMO_STAGE** for data files, **SEMANTIC_MODELS** for semantic views

The script automatically loads LAD (Local Authority District) demographic data from GitHub using the **LOAD_DATA_FROM_GITHUB()** procedure.

<!-- ------------------------ -->
## Generate Data

The setup script loads reference data automatically. Now you'll run the data generation scripts to create synthetic subscriber data.

### Step 1: Run Data Generation Scripts

Execute the following scripts in order by copying them from the repository:

| Script | Purpose | Estimated Time |
|--------|---------|----------------|
| `02-generate-subscriber-archetypes.sql` | Create subscriber persona archetypes | ~1 min |
| `03-generates-demographics-distributions.sql` | Load demographic distributions | ~1 min |
| `04-create-archetype-distributions.sql` | Map archetypes to demographics | ~1 min |
| `05-generate-subscribers.sql` | Generate 100K+ subscriber profiles | ~5 min |
| `06-generate-clickstream-events.sql` | Generate 5M+ clickstream events | ~15 min |
| `07a-generate-ad-campaigns.sql` | Generate ad campaigns | ~2 min |
| `07b-generate-ads-events.sql` | Generate ad impression/click events | ~10 min |
| `08-enrich-subscriber-demographics.sql` | Enrich with demographic data | ~3 min |

### Step 2: Verify Data Generation

After running the generation scripts, verify the record counts:

| Table | Expected Records |
|-------|------------------|
| GENERATE.SUBSCRIBER_FULL_PROFILES | ~100,000 |
| GENERATE.CLICKSTREAM_EVENTS | ~5,000,000 |
| GENERATE.AD_CAMPAIGNS | ~1,000 |
| GENERATE.ADS_EVENTS | ~2,000,000 |

<!-- ------------------------ -->
## Transform Data

Now transform the generated data through the Ingest and Harmonized layers.

### Step 1: Load to Ingest Layer

Execute these scripts to load data into the INGEST schema:

| Script | Purpose |
|--------|---------|
| `10-load-subscriber-full-profiles.sql` | Load subscriber profiles |
| `11-mount-demographics-profiles-share.sql` | Mount demographic data |
| `12-load-events.sql` | Load clickstream and ad events |
| `13-load-ad-campaigns-and-performance.sql` | Load campaign data |

### Step 2: Create Harmonized Marts

Execute these scripts to create business-ready data marts:

| Script | Purpose |
|--------|---------|
| `14-enrich-subscriber-profiles.sql` | Enrich profiles with demographics |
| `15-aggregate-ad-performance.sql` | Aggregate ad metrics |
| `16-calculate-ad-sales-regional-rates.sql` | Calculate regional ad rates |
| `17-create-harmonized-marts.sql` | Create final data marts |

<!-- ------------------------ -->
## Build ML Features

Create ML features for churn prediction and lifetime value scoring.

### Step 1: Feature Engineering

Execute these scripts to create ML features:

| Script | Purpose |
|--------|---------|
| `18-feature-engineering-procedures.sql` | Create feature engineering procedures |
| `19-subscriber-ltv-scoring.sql` | Calculate lifetime value scores |
| `20-subscriber-churn-scoring.sql` | Calculate churn risk scores |

### Step 2: Create Analysis Objects

Execute these scripts for advanced analytics:

| Script | Purpose |
|--------|---------|
| `21-create-clean-room-tool.sql` | Data clean room setup |
| `23-segment-stored-procedures.sql` | Audience segmentation procedures |
| `24-subscriber-attributes-dynamic-table.sql` | Dynamic subscriber attributes |

<!-- ------------------------ -->
## Deploy Streamlit Applications

The solution includes four Streamlit applications for interactive analytics.

### Step 1: Upload Streamlit Files

1. Navigate to **Data → Databases → AME_AD_SALES_DEMO → APPS**
2. Create a stage called **STREAMLIT_STAGE** if not exists
3. Upload the following files from the `scripts/streamlit/` folder:
   - `streamlit_dashboard.py`
   - `streamlit_dataset_explorer.py`
   - `streamlit_ingest_explorer.py`
   - `streamlit_segment_builder.py`
   - `environment.yml`

### Step 2: Create Streamlit Apps

For each application, create a Streamlit app in Snowsight:

1. Navigate to **Projects → Streamlit**
2. Click **+ Streamlit App**
3. Select the uploaded Python file
4. Choose **AME_AD_SALES_DEMO** database and **APPS** schema
5. Select **APP_WH** warehouse

### Available Applications

| Application | Purpose |
|-------------|---------|
| **Subscriber Dashboard** | 360-degree subscriber view with demographics, behavior, and scores |
| **Dataset Explorer** | Explore tables, columns, and data statistics |
| **Ingest Explorer** | Inspect raw data and stage contents |
| **Segment Builder** | Build and save audience segments |

<!-- ------------------------ -->
## Explore the Dashboards

### Subscriber Dashboard

The Subscriber Dashboard provides a comprehensive view of individual subscribers:

**Key Features:**
- **Subscriber Search**: Find subscribers by ID or attributes
- **Demographics Panel**: Age, location, income, household composition
- **Behavior Metrics**: Clickstream patterns, content preferences
- **ML Scores**: LTV score, churn risk, engagement index

![Subscriber Dashboard](assets/dashboard_subscriber.png)

### Segment Builder

The Segment Builder enables marketing teams to create targeted audiences:

**Key Features:**
- **Filter Builder**: Multi-attribute filtering with AND/OR logic
- **Segment Preview**: Real-time count and sample profiles
- **Save Segments**: Persist segments as tables for activation
- **Export Options**: Download segment lists for external systems

![Segment Builder](assets/segment_builder.png)

<!-- ------------------------ -->
## Snowflake Intelligence

### Create Semantic View

Upload the semantic view YAML file to enable natural language queries:

1. Navigate to **Data → Databases → AME_AD_SALES_DEMO → ANALYSE → Stages → SEMANTIC_MODELS**
2. Upload `semantic_view.yaml` from the repository
3. The semantic view defines:
   - Table relationships
   - Column descriptions
   - Business metrics
   - Verified queries

### Create Snowflake Intelligence Agent

1. Navigate to **AI & ML → Snowflake Intelligence**
2. Click **+ Create Agent**
3. Configure the agent:
   - **Name**: `AME_AD_SALES_ANALYST`
   - **Description**: `Audience analytics agent for subscriber insights and campaign performance`
   - **Semantic View**: Select the uploaded semantic view
   - **Warehouse**: `APP_WH`

![Navigate to Snowflake Intelligence](assets/snowflake-intelligence-nav.png)

### Test the Agent

Try these persona-specific queries:

### Data Analyst

**1. Subscriber Demographics**

Understand your subscriber base composition.

**`What is the age distribution of our subscribers by region?`**

**2. Content Preferences**

Identify popular content categories.

**`Which content categories have the highest engagement rates?`**

### Marketing Manager

**1. High-Value Segments**

Find subscribers with high lifetime value.

**`Show me subscribers with LTV score above 80 who are at risk of churning`**

**2. Campaign Targeting**

Build targeted campaign audiences.

**`How many subscribers in London are interested in sports and have high engagement?`**

### Ad Sales Executive

**1. Campaign Performance**

Analyze ad campaign effectiveness.

**`What are the top 10 campaigns by click-through rate this month?`**

**2. Regional Rates**

Understand regional ad pricing.

**`Compare CPM rates across different regions`**

### Executive

**1. Business Health**

Get a high-level view of subscriber health.

**`What is our overall churn risk distribution?`**

**2. Growth Metrics**

Track subscriber growth and engagement.

**`Show subscriber growth trends by month`**

<!-- ------------------------ -->
## Cleanup

To remove all resources created by this guide:

1. Open your **[setup.sql](https://github.com/Snowflake-Labs/sfguide-mea-subscriber-analytics/blob/main/scripts/sql/setup.sql)** Workspace
2. Run the following teardown commands:

```sql
-- TEARDOWN SCRIPT
-- Uncomment and run to remove all resources

-- DROP DATABASE IF EXISTS AME_AD_SALES_DEMO;
-- DROP DATABASE IF EXISTS SNOWFLAKE_INTELLIGENCE;
-- DROP WAREHOUSE IF EXISTS APP_WH;
-- DROP ROLE IF EXISTS AME_AD_SALES_DEMO_ADMIN;
-- DROP INTEGRATION IF EXISTS GITHUB_DATA_ACCESS_INTEGRATION;
```

<!-- ------------------------ -->
## Conclusion and Resources

Congratulations! You have successfully built a comprehensive Media & Entertainment audience analytics solution with Snowflake Intelligence.

### What You Learned
- How to structure subscriber data in a medallion architecture
- How to generate realistic synthetic data for analytics
- How to build ML features for LTV and churn scoring
- How to deploy interactive Streamlit dashboards
- How to create Semantic Views for natural language access
- How to configure Snowflake Intelligence Agents

### Business Value

| Capability | Benefit |
|------------|---------|
| **Unified Subscriber View** | 360-degree visibility into subscriber behavior |
| **Self-Service Segmentation** | Marketing teams can build audiences without SQL |
| **Natural Language Analytics** | Executives can query data conversationally |
| **ML-Powered Insights** | Predictive churn and LTV scoring |

### Related Resources

**Snowflake Documentation:**
- <a href="https://docs.snowflake.com/en/user-guide/snowflake-intelligence/overview" target="_blank">Snowflake Intelligence</a>
- <a href="https://docs.snowflake.com/en/developer-guide/streamlit/about-streamlit" target="_blank">Streamlit in Snowflake</a>
- <a href="https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst" target="_blank">Cortex Analyst</a>
