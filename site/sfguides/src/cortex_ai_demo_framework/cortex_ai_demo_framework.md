author: Joviane Bellegarde
id: cortex_ai_demo_framework
summary: Cortex AI Demo Framework - Build sophisticated Cortex-powered demos in ~5 minutes
categories: Cortex, AI, Demo Development, Getting-Started
environments: web
status: Published
feedback link: https://github.com/Snowflake-Labs/sfguides/issues
tags: Getting Started, Cortex, AI, Demo Development, Framework

# Cortex AI Demo Framework
<!-- ------------------------ -->

## Overview
Duration: 5

Demo development is crucial for businesses to showcase their AI capabilities and win new customers. Through rapid prototyping and professional presentation tools, businesses can transform weeks of development into minutes of setup, dramatically accelerating sales cycles and proof-of-concept delivery.

In this Quickstart, we will build a comprehensive demo development platform called "Cortex AI Demo Framework". This demonstrates how to use Snowflake Cortex AI functions to create synthetic data, build interactive analytics, deploy search capabilities, and generate complete demonstration environments.

This Quickstart showcases the complete Cortex AI Demo Framework with:
- **5-application integrated demo platform** with Synthetic Data Generator, Structured Tables, SQL to YAML Converter, Snow Demo, and Snow Viz
- **AI-powered data generation** using all Cortex functions
- **Advanced semantic search** and automated model creation
- **Cortex Search Service** for intelligent data discovery
- **Cortex Analyst integration** for natural language queries
- **Production-ready applications** with professional UI/UX


### What You Will Build
- Complete 5-application integrated demo platform
- AI-powered synthetic data generation system using Cortex functions
- Advanced semantic modeling and search capabilities
- Professional demo orchestration and configuration tools
- Interactive Cortex Search Service for semantic discovery
- Production-ready Streamlit applications with advanced visualizations
- Reusable framework for rapid demo creation across any industry

### What You Will Learn
- How to set up a production demo development pipeline with Snowflake
- How to use Snowflake Notebooks for complex AI demo workflows
- How to implement all Cortex AI functions (SENTIMENT, EXTRACT_ANSWER, COMPLETE)
- How to build scalable demo platforms with synthetic data
- How to create automated semantic models and search services
- How to deploy interactive Streamlit applications in Snowflake

### Prerequisites
- Familiarity with Python and SQL
- Familiarity with Streamlit applications
- Go to the [Snowflake](https://signup.snowflake.com/?utm_cta=quickstarts_) sign-up page and register for a free account

<!-- ------------------------ -->
## Setup Snowflake Environment  
Duration: 5

In this step, you'll create the Snowflake database objects and prepare for framework deployment.

### Step 1: Create Database Objects

1. In Snowsight, click `Worksheets` in the left navigation
2. Click `+` in the top-right corner to open a new Worksheet
3. Copy the setup script from [setup.sql](https://github.com/Snowflake-Labs/sfguide-cortex-demo-developer-framework/blob/main/scripts/setup.sql) and paste it into your worksheet, then run it

The setup script creates:
- **Database**: `CORTEX_FRAMEWORK_DB` with `BRONZE_LAYER`, `SILVER_LAYER`, `APPS`, and `CONFIGS` schemas
- **Role**: `CORTEX_FRAMEWORK_DATA_SCIENTIST` with all necessary permissions  
- **Warehouse**: `CORTEX_FRAMEWORK_WH` for compute resources
- **Stages**: `FRAMEWORK_DATA_STAGE`, `SEMANTIC_MODELS`, and `DEMO_CONFIGS` for file uploads
- **File Formats**: `CSV_FORMAT`, `YAML_FORMAT`, and `JSON_FORMAT` for data processing
- **AI Access**: `SNOWFLAKE.CORTEX_USER` role for Cortex functions

### Step 2: Download Required Framework Files

Download these framework files from the GitHub repository:

| File | Purpose |
|------|---------|
| [**01_ai_framework_synthetic_data_generator.py**](https://github.com/Snowflake-Labs/sfguide-getting-started-with-cortex-ai-demo-framework/blob/main/scripts/01_ai_framework_synthetic_data_generator.py) | AI-powered synthetic data creation |
| [**02_ai_framework_structured_tables.py**](https://github.com/Snowflake-Labs/sfguide-getting-started-with-cortex-ai-demo-framework/blob/main/scripts/02_ai_framework_structured_tables.py) | Data structuring and transformation |
| [**03_ai_framework_sql_to_yaml_converter.py**](https://github.com/Snowflake-Labs/sfguide-getting-started-with-cortex-ai-demo-framework/blob/main/scripts/03_ai_framework_sql_to_yaml_converter.py) | SQL to YAML configuration converter (generates semantic models) |
| [**04_ai_framework_snow_demo.py**](https://github.com/Snowflake-Labs/sfguide-getting-started-with-cortex-ai-demo-framework/blob/main/scripts/04_ai_framework_snow_demo.py) | Demo configuration and runner |
| [**05_ai_framework_snow_viz.py**](https://github.com/Snowflake-Labs/sfguide-getting-started-with-cortex-ai-demo-framework/blob/main/scripts/05_ai_framework_snow_viz.py) | Advanced visualization components |
| [**environment.yml**](https://github.com/Snowflake-Labs/sfguide-getting-started-with-cortex-ai-demo-framework/blob/main/scripts/environment.yml) | Conda environment for Streamlit dependencies |

### Step 3: Upload Framework Files to Stages

1. In Snowsight, change your role to `cortex_ai_demo_data_scientist`

2. Navigate to `Catalog` → `Database Explorer` → `AI_FRAMEWORK_DB` → `APPS` → `Stages`

**Upload framework applications to their respective stages:**

3. For each stage, click `Enable Directory Table` and upload **both files**:
   - `SYNTHETIC_DATA_GENERATOR_START_HERE`: Upload `01_ai_framework_synthetic_data_generator.py` + `environment.yml`
   - `STRUCTURED_TABLES`: Upload `02_ai_framework_structured_tables.py` + `environment.yml`
   - `SQL_TO_YAML_CONVERTER`: Upload `03_ai_framework_sql_to_yaml_converter.py` + `environment.yml`
   - `SNOW_DEMO`: Upload `04_ai_framework_snow_demo.py` + `environment.yml`
   - `SNOW_VIZ`: Upload `05_ai_framework_snow_viz.py` + `environment.yml`

**⚠️ Important:** Each stage needs both the Python file AND `environment.yml` for dependencies.

### Step 4: Import the Framework Setup Notebook

1. **Download the notebook**: [cortex_ai_demo_framework_setup.ipynb](https://github.com/Snowflake-Labs/sfguide-getting-started-with-cortex-ai-demo-framework/blob/main/notebooks/cortex_ai_demo_framework_setup.ipynb)

2. **Import into Snowflake**:
   - Navigate to `Projects` → `Notebooks` in Snowsight
   - Click the down arrow next to `+ Notebook` and select `Import .ipynb file`
   - Choose `cortex_ai_demo_framework_setup.ipynb` from your downloads

3. **Configure the notebook settings**:
   - **Role**: Select `cortex_ai_demo_data_scientist`
   - **Database**: Select `AI_FRAMEWORK_DB`
   - **Schema**: Select `BRONZE_LAYER`  
   - **Query Warehouse**: Select `cortex_ai_demo_wh`
   - **Notebook Warehouse**: Select `cortex_ai_demo_wh`

4. **Click `Create`** to import the notebook

The notebook contains the data processing workflow and Streamlit deployment commands for the complete framework.

<!-- ------------------------ -->
## Run Framework Demo Notebook
Duration: 8

### Execute the Framework Deployment Workflow

1. Go to `Projects` → `Notebooks` in Snowsight
2. Click on `CORTEX_FRAMEWORK_DEMO` Notebook to open it
3. Click `Run all` to execute all cells in the notebook at once

**What the notebook does:**
- Creates sample customer survey data tables
- Processes data with Cortex AI functions (SENTIMENT, EXTRACT_ANSWER, COMPLETE)
- Deploys all 8 Streamlit applications from the uploaded stage files using environment.yml for dependencies
- Sets up the complete framework for immediate demo creation

The notebook processes sample data and deploys the complete framework application suite.

<!-- ------------------------ -->
## Explore Framework Applications
Duration: 15

### Access Your Framework Applications

1. Navigate to `Projects` → `Streamlit` in Snowsight
2. You'll see multiple applications deployed from the framework:

#### **Cortex Framework Demo**
- **Purpose**: Framework overview and pipeline explanation
- **Features**: Interactive framework tour, component explanations, use case examples
- **Best for**: Understanding the framework capabilities and architecture

#### **Cortex Framework Visualizer** 
- **Purpose**: Interactive analytics dashboard
- **Features**: Data visualization, filtering, export capabilities, AI insights
- **Best for**: Exploring data patterns and creating customer-facing demos

#### **Synthetic Data Generator**
- **Purpose**: AI-powered realistic data creation
- **Features**: Custom prompts, multiple data types, configuration management
- **Best for**: Creating realistic demo datasets for any use case

#### **Data Provider**
- **Purpose**: Data ingestion and management
- **Features**: File upload, data transformation, integration setup
- **Best for**: Connecting external data sources to the framework

#### **Structured Tables**
- **Purpose**: Table structure and schema management  
- **Features**: Schema design, relationship mapping, data modeling
- **Best for**: Designing optimal data structures for demos

#### **YAML Wizard**
- **Purpose**: SQL to YAML configuration converter
- **Features**: Automated conversion, configuration templates, validation
- **Best for**: Creating semantic model configurations quickly

#### **Semantic Model Creator**
- **Purpose**: Automated semantic model generation
- **Features**: AI-enhanced models, table analysis, YAML generation
- **Best for**: Building Cortex Analyst-ready semantic models

#### **Snow Demo**
- **Purpose**: Demo configuration and execution
- **Features**: Demo orchestration, configuration management, execution workflows
- **Best for**: Running complete demo scenarios

#### **Snow Viz**
- **Purpose**: Advanced visualization components
- **Features**: Multiple chart types, interactive dashboards, custom visualizations
- **Best for**: Creating sophisticated analytical interfaces

### Framework Workflow

**Typical demo creation workflow:**
1. **Generate Data** - Use Synthetic Data Generator for realistic datasets
2. **Design Schema** - Use Structured Tables for optimal data organization  
3. **Create Models** - Use Semantic Model Creator for Cortex Analyst integration
4. **Build Visualizations** - Use Snow Viz for interactive dashboards
5. **Configure Demo** - Use Snow Demo for complete demo orchestration
6. **Present** - Use any application for customer-facing demonstrations

<!-- ------------------------ -->
## Build Your First Demo
Duration: 10

### Quick Demo Creation Exercise

Let's create a simple customer analytics demo using the framework:

#### **Step 1: Generate Sample Data**
1. Open the **Synthetic Data Generator** application
2. Use the customer survey template
3. Generate 50-100 sample customer records
4. Save the configuration for reuse

#### **Step 2: Create Visualizations**
1. Open the **Cortex Framework Visualizer** application  
2. Explore the sample data with different filters
3. Create charts for satisfaction trends and regional analysis
4. Export insights for presentation

#### **Step 3: Set Up AI Analysis**
1. The framework automatically processes data with Cortex AI functions
2. View sentiment analysis results in the visualizer
3. Review AI-generated business recommendations
4. Test the natural language query capabilities

#### **Step 4: Configure Demo Flow**
1. Open the **Snow Demo** application
2. Create a demo configuration combining your data and visualizations
3. Set up the demo sequence and key talking points
4. Save the configuration for repeated use

### Demo Ready!
Your demo is now ready for presentation. The entire process from idea to demo took approximately 5 minutes, demonstrating the power of the framework approach.

<!-- ------------------------ -->
## Advanced Framework Usage
Duration: 10

### Creating Custom Use Cases

The framework is designed to support any industry or use case. Here are examples:

#### **Retail Analytics Demo**
- **Data**: Customer transactions, product catalog, reviews
- **Processing**: Sentiment analysis on reviews, recommendation engine
- **Visualization**: Sales dashboards, customer journey analysis
- **Time**: ~5 minutes

#### **Financial Services Demo**  
- **Data**: Transaction data, customer profiles, risk indicators
- **Processing**: Fraud detection patterns, customer segmentation
- **Visualization**: Risk dashboards, compliance reporting
- **Time**: ~5 minutes

#### **Healthcare Analytics Demo**
- **Data**: Patient records, treatment outcomes, satisfaction surveys
- **Processing**: Outcome prediction, satisfaction analysis
- **Visualization**: Patient journey, outcome tracking
- **Time**: ~5 minutes

### Framework Extensions

**Add Custom Components:**
1. Create new Streamlit applications following the framework pattern
2. Upload to the `FRAMEWORK_DATA_STAGE`
3. Deploy using the notebook deployment cells
4. Integrate with existing framework workflows

**Custom Data Sources:**
1. Use the Data Provider application to connect external sources
2. Configure data transformation and validation rules
3. Integrate with the synthetic data generator for augmentation
4. Set up automated data refresh workflows

<!-- ------------------------ -->
## Framework Architecture
Duration: 5

### Technical Architecture

The Cortex AI Demo Framework follows a layered architecture:

#### **Data Layers**
- **Bronze Layer**: Raw data ingestion and synthetic data generation
- **Silver Layer**: Processed data with AI enhancements and analytics
- **Apps Layer**: Deployed Streamlit applications and configurations
- **Configs Layer**: YAML files, semantic models, and configuration data

#### **Application Components**
- **Core Framework**: Demo, Visualizer, and pipeline explanation apps
- **Data Tools**: Synthetic generator, data provider, structured tables
- **AI Tools**: YAML wizard, semantic model creator
- **Presentation Tools**: Snow demo, snow viz, advanced visualizations

#### **Integration Points**
- **Cortex AI**: Full integration with SENTIMENT, EXTRACT_ANSWER, COMPLETE
- **Cortex Search**: Semantic search across any dataset
- **Cortex Analyst**: Natural language query interface with YAML models
- **Snowpark**: Session management and data processing
- **Streamlit**: Native Snowflake app deployment and management

### Performance Optimization

**Framework Performance:**
- **Setup Time**: ~5 minutes from setup.sql to deployed applications
- **Demo Creation**: ~5 minutes from idea to presentable demo
- **Data Generation**: Real-time synthetic data creation with AI
- **Visualization**: Interactive dashboards with sub-second response times
- **Scalability**: Handles datasets from hundreds to millions of records

<!-- ------------------------ -->
## Clean Up Resources
Duration: 3

### Remove All Created Objects

When you're ready to remove all the resources created during this quickstart:

1. Open the [setup.sql](https://github.com/Snowflake-Labs/sfguide-cortex-demo-developer-framework/blob/main/scripts/setup.sql) script
2. Scroll to the bottom to find the "TEARDOWN SCRIPT" section
3. Uncomment the teardown statements
4. Run the freshly uncommented script to remove all databases, warehouses, roles, and objects

This will clean up all framework components while preserving any other work in your Snowflake account.

<!-- ------------------------ -->
## Conclusion and Resources
Duration: 2

Congratulations! You've successfully built the complete Cortex AI Demo Framework using Snowflake Cortex AI!

### What You Learned
- **8-Application Demo Platform**: How to build Framework Overview, Synthetic Data Generator, Data Provider, Structured Tables, YAML Wizard, Semantic Model Creator, Demo Orchestrator, and Advanced Visualizations
- **Advanced AI Processing**: How to implement complete Cortex AI integration with SENTIMENT, EXTRACT_ANSWER, and COMPLETE functions
- **Cortex Search Service**: How to create semantic search across synthetic data with natural language queries
- **Production-Ready Streamlit Apps**: How to develop complete interactive demo platform with advanced visualizations
- **Rapid Demo Development**: How to transform weeks of development into minutes of setup

### Resources
- [Snowflake Cortex AI Functions](https://docs.snowflake.com/user-guide/snowflake-cortex/llm-functions)
- [Cortex Search](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-search/cortex-search-overview)
- [Cortex Analyst](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst)
- [Streamlit in Snowflake](https://docs.snowflake.com/developer-guide/streamlit/about-streamlit)
- [Snowflake Notebooks](https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks)
