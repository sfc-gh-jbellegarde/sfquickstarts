author: Lee Gould, Joviane Bellegarde
id: gaming-platform-analytics
language: en
summary: GameVerse Gaming Platform Analytics - Exploring Player Engagement Data with Snowflake Intelligence
categories: snowflake-site:taxonomy/solution-center/certification/quickstart, snowflake-site:taxonomy/product/ai, snowflake-site:taxonomy/snowflake-feature/cortex-llm-functions, snowflake-site:taxonomy/snowflake-feature/build
environments: web
status: Published
feedback link: https://github.com/Snowflake-Labs/sfguides/issues
tags: Getting Started, Cortex, AI, Intelligence Agents, Streamlit, Gaming Analytics, Player Engagement

# Exploring Player Engagement Data with Snowflake Intelligence
<!-- ------------------------ -->

## Overview

Player engagement analytics is crucial for gaming companies to understand their player base and create targeted retention campaigns. Through comprehensive data analysis and AI-powered insights, organizations can uncover patterns in player behavior, identify high-value segments, and generate actionable marketing strategies.

In this Guide, we will explore how **GameVerse**, a fictitious multi-platform gaming company, uses Snowflake Intelligence to transform 300,000+ player profiles into actionable marketing campaigns through natural language conversations.

This Guide showcases a complete player engagement platform with:
- **Unified Player 360** - 300k players with demographics, TransUnion enrichment, and ML predictions
- **Semantic Search** - Find players by natural language descriptions using Cortex Search
- **AI Analytics** - Ask business questions in plain English using Cortex Analyst
- **Action-Taking Agent** - Intelligence Agent that not just provides insights but saves player segments for activation
- **Interactive Dashboard** - Streamlit application with 7 modules for data exploration

### What You Will Build
- Complete player engagement analytics platform with Intelligence Agent
- AI-powered semantic search for player discovery using Cortex Search
- Natural language analytics using Cortex Analyst with semantic models
- Custom action tool (Python UDF) for saving player segments
- Production-ready Streamlit application with advanced visualizations
- End-to-end analytics-to-activation pipeline

### What You Will Learn
- How to set up Snowflake Intelligence Agents with multiple tools
- How to use Snowflake Notebooks for complex AI processing workflows
- How to implement Cortex Search for semantic player discovery
- How to build semantic models for Cortex Analyst
- How to create custom action tools using Python stored procedures
- How to deploy interactive Streamlit applications in Snowflake

### Prerequisites
- Familiarity with Python and SQL
- Familiarity with Streamlit applications
- Go to the [Snowflake](https://signup.snowflake.com/?utm_cta=quickstarts_) sign-up page and register for a free account

<!-- ------------------------ -->
## Setup Snowflake Environment  

In this step, you'll create the Snowflake database objects and upload all necessary files for the GameVerse Gaming Platform Analytics demo.

### Step 1: Create Database Objects

1. In Snowsight, navigate to **Projects** > **Worksheets**
2. Create a new SQL worksheet
3. Copy the setup script from [setup.sql](https://github.com/Snowflake-Labs/sfguides-gaming-analytics-with-snowflake-intelligence/blob/main/scripts/setup.sql) and paste it into your worksheet
4. Run the script

The setup script creates:
- **Role**: `GAMING_ANALYTICS` with all necessary permissions
- **Warehouse**: `GAMING_ANALYTICS_WH` for compute resources
- **Database**: `GAMEVERSE_DB` with `GAMEVERSE` schema
- **2 Stages**: 
  - `GAMEVERSE_DATA` (for CSV data + Streamlit files)
  - `SEMANTIC_MODELS` (for YAML files)
- **Base Table**: `PLAYER_PROFILE` structure (110 columns)
- **AI Access**: `SNOWFLAKE.CORTEX_USER` database role grant

### Step 2: Download Required Files

Download these files from the GitHub repository:

| File | Purpose | Download Link |
|------|---------|---------------|
| **Setup Script** | SQL setup script for infrastructure | [setup.sql](https://github.com/Snowflake-Labs/sfguides-gaming-analytics-with-snowflake-intelligence/blob/main/scripts/setup.sql) |
| **Data File** | GameVerse player profiles (300k records) | [player_profile.csv.gz](https://github.com/Snowflake-Labs/sfguides-gaming-analytics-with-snowflake-intelligence/blob/main/scripts/player_profile.csv.gz) |
| **Streamlit App** | Interactive analytics dashboard | [gaming_dashboard.py](https://github.com/Snowflake-Labs/sfguides-gaming-analytics-with-snowflake-intelligence/blob/main/scripts/gaming_dashboard.py) |
| **Environment File** | Streamlit dependencies | [environment.yml](https://github.com/Snowflake-Labs/sfguides-gaming-analytics-with-snowflake-intelligence/blob/main/scripts/environment.yml) |
| **Semantic Model** | Cortex Analyst semantic model | [PLAYER_ENGAGEMENT_ANALYTICS.yaml](https://github.com/Snowflake-Labs/sfguides-gaming-analytics-with-snowflake-intelligence/blob/main/scripts/PLAYER_ENGAGEMENT_ANALYTICS.yaml) |
| **Notebook** | Complete setup notebook | [gaming_analytics_setup.ipynb](https://github.com/Snowflake-Labs/sfguides-gaming-analytics-with-snowflake-intelligence/blob/main/notebooks/gaming_analytics_setup.ipynb) |

### Step 3: Upload Files to Stages

Now you'll upload the downloaded files to 2 stages via the Snowsight UI.

1. **Switch to the Gaming Analytics role:**
   - In Snowsight, click your profile icon (top right)
   - Select `Switch Role` → `GAMING_ANALYTICS`

2. **Navigate to Database Explorer:**
   - Go to `Catalog` → `Database Explorer` → `GAMEVERSE_DB` → `GAMEVERSE` schema

3. **Upload to GAMEVERSE_DATA (3 files):**
   - Click `Stages` in the left sidebar
   - Click on `GAMEVERSE_DATA`
   - Click the `+ Files` button (top right)
   - Select and upload these 3 files together:
     - `player_profile.csv.gz` (~79MB - main data file)
     - `gaming_dashboard.py` (~100KB - Streamlit app)
     - `environment.yml` (~72 bytes - Python dependencies)
   - Wait for all uploads to complete

4. **Upload to SEMANTIC_MODELS (1 file):**
   - Go back to `GAMEVERSE` schema → `Stages`
   - Click on `SEMANTIC_MODELS`
   - Click the `+ Files` button
   - Select and upload:
     - `PLAYER_ENGAGEMENT_ANALYTICS.yaml` (~39KB - Cortex Analyst semantic model)
   - Wait for upload to complete

### Step 4: Import and Configure the Setup Notebook

1. **Import into Snowflake**:
   - Navigate to `Projects` → `Notebooks` in Snowsight
   - Click the down arrow next to `+ Notebook` and select `Import .ipynb file`
   - Choose `gaming_analytics_setup.ipynb` from your downloads

2. **Configure the notebook settings**:
   - **Notebook Name**: `GAMING_ANALYTICS_SETUP`
   - **Location**: `GAMEVERSE_DB.GAMEVERSE`
   - **Query Warehouse**: Select `GAMING_ANALYTICS_WH`
   - **Notebook Warehouse**: Select `GAMING_ANALYTICS_WH`

3. **Click `Create`** to import the notebook

The notebook contains 9 sections that will:
- Load 300k player profiles from CSV
- Create searchable table with enriched SEARCH_FIELD
- Create Cortex Search service for semantic player discovery
- Create player segments infrastructure and save procedure
- Create Intelligence Agent with 3 tools
- Deploy Streamlit dashboard

<!-- ------------------------ -->
## Run Setup Notebook

### Execute the Complete Setup Workflow

1. Go to `Projects` → `Notebooks` in Snowsight
2. Click on `GAMING_ANALYTICS_SETUP` to open it
3. Click `Run all` to execute all cells in the notebook

**What the notebook does:**
- **Section 1-2**: Verifies infrastructure and loads 300k+ player profiles
- **Section 3**: Creates searchable table with enriched SEARCH_FIELD (~1-2 min)
- **Section 4**: Creates Cortex Search service (indexes in background ~5 min)
- **Section 5-6**: Creates player segments infrastructure and save procedure
- **Section 7**: Creates Intelligence Agent with 3 tools
- **Section 8**: Deploys Streamlit dashboard with 7 modules

**Processing time:** ~5-7 minutes total (Cortex Search indexing happens in parallel)

<!-- ------------------------ -->
## Launch Analytics Dashboard

### Access Your Gaming Platform Analytics

1. Navigate to `Projects` → `Streamlit` in Snowsight
2. Find and click on `Gaming Platform Dashboard`
3. Explore your 7-module analytics dashboard

Your platform includes:
- **Overview** - Executive metrics with time series and ranked grids
- **Product** - Genre-level insights (RPG, Shooter, Strategy, Casual)
- **VS** - Side-by-side comparative analysis
- **Top N** - Ranked leaderboards by any dimension
- **Self Service** - Custom query builder for ad-hoc analysis
- **Search** - Cortex Search integration for semantic player discovery
- **AI Assistant** - Cortex Analyst for natural language queries

<!-- ------------------------ -->
## Snowflake Intelligence

### Access Snowflake Intelligence

1. Navigate to `AI & ML` → `Intelligence` in Snowsight
2. Find and click on `PLAYER_ENGAGEMENT_ASSISTANT`
3. Try the sample questions below

### Demo Flow: 7 Key Questions

Ask these questions in order to demonstrate the full analytics-to-activation workflow:

---

**1. Monetization Tier Demographics Analysis**

```
How many of our players are in Free tier vs paid tiers (Premium and VIP)?
```

**What It Shows:** Basic Cortex Analyst analytics—quick monetization tier segmentation with clear numerical results

---

**2. Comparative Segment Analysis**

```
Can you analyze and tell me which group we are appealing to more in terms of engagement metrics like games played, session time, and monetization stats?
```

**What It Shows:** Advanced multi-metric analysis across segments—demonstrates the agent's ability to combine engagement, value, and demographic data

---

**3. Retention Strategy Development**

```
If I want to target the paid tier players (Premium and VIP) and develop a customized campaign to increase their lifetime value and retention, how should I go about doing it? Give me a plan.
```

**What It Shows:** Strategic AI reasoning—the agent provides actionable campaign recommendations and engagement tactics

---

**4. Audience List Creation**

```
Give me a list of player IDs to use in this initial campaign.
```

**What It Shows:** Data activation capability—insights immediately convert to actionable audience lists

---

**5. Advanced Semantic Search with Propensity Scoring**

```
Can you identify anyone who is VIP tier and plays RPG games frequently and give me the propensity score for them to make in-game purchases or renew subscriptions?
```

**What It Shows:** Cortex Search integration + custom action—semantic player discovery combined with ML-powered propensity scoring

---

**6. Player Segment Save - Analytics to Activation ⭐**

```
Now save these VIP RPG players with high purchase propensity as a player segment called "VIP RPG Whales" for our exclusive in-game event and limited cosmetics release.
```

**What It Shows:** Analytics-to-activation pipeline—the agent doesn't just provide insights, it takes ACTION by saving the identified audience directly to the database

---

**7. Verify Segment Creation**

```
Query the PLAYER_SEGMENTS table and show me all saved segments with their names, total players, and average lifetime value.
```

**What It Shows:** Segment verification using Cortex Analyst—the agent queries the PLAYER_SEGMENTS table and returns a formatted report showing all saved segments with key metrics

<!-- ------------------------ -->
## Clean Up Resources

### Remove All Created Objects

When you're ready to remove all the resources created during this guide:

1. Open a new SQL worksheet in Snowsight
2. Copy and paste the teardown script below
3. Run the script to remove all databases, warehouses, roles, and objects

```sql
-- Teardown Script
USE ROLE ACCOUNTADMIN;

DROP DATABASE IF EXISTS GAMEVERSE_DB CASCADE;
DROP WAREHOUSE IF EXISTS GAMING_ANALYTICS_WH;
DROP ROLE IF EXISTS GAMING_ANALYTICS;
```

This will clean up all gaming platform components while preserving any other work in your Snowflake account.

<!-- ------------------------ -->
## Conclusion and Resources

Congratulations! You've successfully built the complete GameVerse Gaming Platform Analytics using Snowflake Intelligence!

### What You Learned
- **Intelligence Agent with Multi-Tool Orchestration**: How to build agents that combine Cortex Analyst, Cortex Search, and custom actions
- **Analytics-to-Activation Pipeline**: How to go from natural language questions to saved player segment lists in minutes
- **Semantic Search**: How to create Cortex Search services for semantic player discovery
- **Cortex Analyst Integration**: How to build semantic models for natural language SQL generation
- **Custom Action Tools**: How to create Python stored procedures that enable agents to take actions, not just provide insights
- **Production-Ready Streamlit**: How to develop interactive dashboards with advanced visualizations

### Key Takeaways
1. **Snowflake Intelligence** combines multiple Cortex services for powerful AI workflows
2. **Semantic models** make Cortex Analyst business-context aware
3. **Custom tools** (Python UDFs) enable agents to take actions, not just provide insights
4. **Multi-tool orchestration** allows agents to analyze, discover, and act in one conversation
5. **Action-taking agents** bridge the gap from insight to activation in marketing workflows

### Resources
- [Snowflake Cortex AI Functions](https://docs.snowflake.com/user-guide/snowflake-cortex/llm-functions)
- [Cortex Search](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-search/cortex-search-overview)
- [Cortex Analyst](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst)
- [Intelligence Agents](https://docs.snowflake.com/en/user-guide/snowflake-intelligence)
- [Streamlit in Snowflake](https://docs.snowflake.com/developer-guide/streamlit/about-streamlit)
- [Fork Notebook on GitHub](https://github.com/Snowflake-Labs/sfguides-gaming-analytics-with-snowflake-intelligence/blob/main/notebooks/gaming_analytics_setup.ipynb)
