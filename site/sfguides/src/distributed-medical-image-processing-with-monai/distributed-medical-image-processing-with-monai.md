author: Joviane Bellegarde
id: distributed-medical-image-processing-with-monai
summary: Build a distributed medical image registration pipeline using MONAI on Snowflake's Container Runtime with GPU acceleration
categories: snowflake-site:taxonomy/solution-center/certification/quickstart, snowflake-site:taxonomy/snowflake-feature/snowpark-container-services
environments: web
language: en
status: Hidden
feedback link: https://github.com/Snowflake-Labs/sfguides/issues
tags: Getting Started, MONAI, Medical Imaging, GPU, Container Runtime, Ray, ML, Deep Learning
fork repo link: https://github.com/Snowflake-Labs/sfguide-distributed-medical-image-processing-with-monai

# Distributed Medical Image Processing with MONAI
<!-- ------------------------ -->

## Overview
Duration: 5

Medical image registration is a critical task in healthcare AI, enabling the alignment of CT scans taken at different times or breathing phases. This guide demonstrates how to build a production-ready distributed training and inference pipeline using **MONAI** (Medical Open Network for AI) on **Snowflake's Container Runtime with GPU acceleration**.

In this Guide, you will build a complete medical image registration system that:
- Downloads and processes lung CT scans from public datasets
- Trains a LocalNet deep learning model using distributed Ray computing
- Registers the trained model in Snowflake's Model Registry
- Runs distributed inference across multiple GPUs

### What You Will Build
- Data ingestion pipeline for NIfTI medical images
- Distributed training system using Ray and MONAI
- Model registration workflow with Snowflake Model Registry
- Distributed inference pipeline with parallel processing
- Results table with registration quality metrics

### What You Will Learn
- How to use Snowflake Container Runtime Notebooks with GPU compute pools
- How to integrate MONAI medical imaging framework with Snowflake
- How to leverage Ray for distributed deep learning workloads
- How to use Snowflake Model Registry for ML model management
- How to store and retrieve medical images from Snowflake stages

### Prerequisites
- Familiarity with Python and deep learning concepts
- Familiarity with medical imaging (helpful but not required)
- A Snowflake account with access to Container Runtime and GPU compute pools
- Go to the [Snowflake](https://signup.snowflake.com/?utm_source=snowflake-devrel&utm_medium=developer-guides&utm_cta=developer-guides) sign-up page and register for a free account

<!-- ------------------------ -->
## Architecture Overview
Duration: 3

### Solution Architecture

The MONAI medical image processing solution consists of three notebooks running on Snowflake Container Runtime with GPU acceleration:

![Architecture Diagram](assets/architecture.png)

1. **Data Ingestion** (`01_ingest_data.ipynb`)
   - Downloads paired lung CT scans from public dataset
   - Uploads NIfTI files to encrypted Snowflake stages
   
2. **Model Training** (`02_model_training.ipynb`)
   - Initializes Ray cluster for distributed computing
   - Trains LocalNet registration model
   - Saves checkpoints to Snowflake stages
   - Registers model in Snowflake Model Registry

3. **Model Inference** (`03_model_inference.ipynb`)
   - Loads model from Model Registry
   - Runs distributed inference across GPUs
   - Saves registered images and metrics to Snowflake

### Key Technologies

| Technology | Purpose |
|------------|---------|
| **MONAI** | Medical imaging transforms, networks, and losses |
| **Ray** | Distributed computing across GPU nodes |
| **PyTorch** | Deep learning framework |
| **Snowflake Container Runtime** | GPU-accelerated notebook execution |
| **Snowflake Model Registry** | Model versioning and deployment |

<!-- ------------------------ -->
## Setup Snowflake Environment
Duration: 10

In this step, you'll create all the Snowflake objects needed for the MONAI solution.

### Step 1: Create Database Objects

1. In Snowsight, click `Projects`, then `Workspaces` in the left navigation
2. Click `+ Add new` to create a new Workspace
3. Click `SQL File` to create a new SQL file
4. Copy the setup script from [setup.sql](https://github.com/Snowflake-Labs/sfguide-distributed-medical-image-processing-with-monai/blob/main/scripts/setup.sql) and paste it into your SQL file

### Step 2: Run Infrastructure Setup (Sections 1-7)

Run the first part of the setup script to create:
- **Role**: `MONAI_DATA_SCIENTIST` with appropriate privileges
- **Warehouse**: `MONAI_WH` (SMALL size)
- **Database**: `MONAI_DB` with `UTILS` and `RESULTS` schemas
- **Stages**: `NOTEBOOK`, `MONAI_MEDICAL_IMAGES_STG`, `RESULTS_STG`
- **Network Rule + External Access Integration**: For pip install
- **GPU Compute Pool**: `MONAI_GPU_ML_M_POOL` (GPU_NV_M instances)

### Step 3: Upload Notebooks to Stage

Download the notebooks from the [GitHub repository](https://github.com/Snowflake-Labs/sfguide-distributed-medical-image-processing-with-monai/tree/main/notebooks).

The setup.sql script includes commented PUT commands. Update the path and run them using SnowSQL or Snow CLI:

```bash
# From SnowSQL - update /path/to/ to your local notebooks folder
PUT file:///path/to/notebooks/01_ingest_data.ipynb @MONAI_DB.UTILS.NOTEBOOK AUTO_COMPRESS=FALSE OVERWRITE=TRUE;
PUT file:///path/to/notebooks/02_model_training.ipynb @MONAI_DB.UTILS.NOTEBOOK AUTO_COMPRESS=FALSE OVERWRITE=TRUE;
PUT file:///path/to/notebooks/03_model_inference.ipynb @MONAI_DB.UTILS.NOTEBOOK AUTO_COMPRESS=FALSE OVERWRITE=TRUE;
```

### Step 4: Create Container Runtime Notebooks

Continue running the **remaining sections of setup.sql** (after the PUT commands). The script creates three GPU-enabled notebooks with external access for package installation.

<!-- ------------------------ -->
## Run Data Ingestion Notebook
Duration: 15

### Step 1: Open the Notebook

1. In Snowsight, navigate to `Data` → `Notebooks`
2. Find `MONAI_01_INGEST_DATA` in the `MONAI_DB.UTILS` schema
3. Click to open the notebook

### Step 2: Start Container Runtime

1. Click the **Start** button in the top-right corner
2. Wait for the Container Runtime to initialize (this may take 2-3 minutes on first run)
3. You should see the notebook kernel become active

### Step 3: Run All Cells

Click **Run All** or execute cells sequentially:

1. **Install MONAI**: Installs the MONAI medical imaging library
2. **Initialize Session**: Connects to Snowflake and sets query tags
3. **Setup Directories**: Creates temporary directories for data
4. **Download Data**: Downloads paired lung CT scans from Zenodo (~266MB)
5. **Upload to Stages**: Uploads NIfTI files to Snowflake stages

![Data Ingestion](assets/01_ingest_data.png)

### Expected Output

After successful execution, you should see:
- 20 paired lung CT scan cases uploaded
- Files organized in `lungMasksExp`, `lungMasksInsp`, `scansExp`, `scansInsp` folders
- All files stored with Snowflake Server-Side Encryption

<!-- ------------------------ -->
## Run Model Training Notebook
Duration: 30

### Step 1: Open the Training Notebook

1. Navigate to `Data` → `Notebooks`
2. Open `MONAI_02_MODEL_TRAINING`
3. Click **Start** to initialize Container Runtime

### Step 2: Understand the Training Pipeline

The notebook executes these key steps:

1. **Ray Cluster Setup**: Initializes distributed computing with 4 worker nodes
2. **Dependency Installation**: Installs MONAI, PyTorch, nibabel on all nodes
3. **Data Loading**: Reads paired CT scan paths from stages
4. **Model Definition**: Creates LocalNet registration network
5. **Training Loop**: Trains with Mutual Information + Bending Energy loss
6. **Model Registry**: Saves best model to Snowflake Model Registry

### Step 3: Monitor Training Progress

The training loop displays:
- Epoch number and total loss
- Similarity loss (image alignment quality)
- Regularization loss (deformation smoothness)
- Validation Dice score (segmentation overlap)

![Training Progress](assets/02_training.png)

### Step 4: Verify Model Registration

After training completes, verify the model is registered:

```sql
SHOW MODELS IN SCHEMA MONAI_DB.UTILS;
```

You should see `LUNG_CT_REGISTRATION` with version `v1`.

<!-- ------------------------ -->
## Run Model Inference Notebook
Duration: 20

### Step 1: Open the Inference Notebook

1. Navigate to `Data` → `Notebooks`
2. Open `MONAI_03_MODEL_INFERENCE`
3. Click **Start** to initialize Container Runtime

### Step 2: Run Distributed Inference

The notebook:

1. **Loads Model**: Retrieves trained model from Model Registry
2. **Configures Ray Workers**: Sets up parallel inference actors
3. **Processes Images**: Runs registration on all test cases
4. **Saves Results**: Writes registered images to stages and metrics to table

### Step 3: Review Results

After inference completes, query the results table:

```sql
SELECT case_id, model_version, dice_score, ssim_score
FROM MONAI_DB.RESULTS.MONAI_PAIRED_LUNG_RESULTS
ORDER BY dice_score DESC;
```

![Inference Results](assets/03_inference.png)

### Step 4: Access Registered Images

Registered images are saved to the results stage:

```sql
LIST @MONAI_DB.UTILS.RESULTS_STG PATTERN='.*registered.*';
```

<!-- ------------------------ -->
## Cleanup
Duration: 2

To remove all resources created by this guide, stop any running notebooks first, then uncomment and run:

```sql
-- USE ROLE ACCOUNTADMIN;
-- DROP NOTEBOOK IF EXISTS MONAI_DB.UTILS.MONAI_01_INGEST_DATA;
-- DROP NOTEBOOK IF EXISTS MONAI_DB.UTILS.MONAI_02_MODEL_TRAINING;
-- DROP NOTEBOOK IF EXISTS MONAI_DB.UTILS.MONAI_03_MODEL_INFERENCE;
-- DROP COMPUTE POOL IF EXISTS MONAI_GPU_ML_M_POOL;
-- DROP EXTERNAL ACCESS INTEGRATION IF EXISTS MONAI_ALLOW_ALL_EAI;
-- DROP DATABASE IF EXISTS MONAI_DB CASCADE;
-- DROP WAREHOUSE IF EXISTS MONAI_WH;
-- DROP ROLE IF EXISTS MONAI_DATA_SCIENTIST;
```

<!-- ------------------------ -->
## Conclusion and Resources
Duration: 2

Congratulations! You have successfully built a distributed medical image registration pipeline using MONAI on Snowflake.

### What You Learned
- How to configure Snowflake Container Runtime with GPU compute pools
- How to use MONAI for medical image processing tasks
- How to leverage Ray for distributed training and inference
- How to manage ML models with Snowflake Model Registry
- How to store and process medical images in Snowflake stages

### Related Resources

**Snowflake Documentation:**
- [Container Runtime Notebooks](https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-container-runtime)
- [GPU Compute Pools](https://docs.snowflake.com/en/developer-guide/snowpark-container-services/compute-pool)
- [Model Registry](https://docs.snowflake.com/en/developer-guide/snowpark-ml/model-registry/overview)
- [Snowflake Stages](https://docs.snowflake.com/en/user-guide/data-load-overview)

**MONAI Resources:**
- [MONAI Documentation](https://docs.monai.io/)
- [MONAI Tutorials](https://github.com/Project-MONAI/tutorials)
- [Medical Image Registration](https://docs.monai.io/en/stable/networks.html#registration-networks)

**Ray Documentation:**
- [Ray on Snowflake](https://docs.snowflake.com/en/developer-guide/snowpark-ml/ray)
- [Ray Train](https://docs.ray.io/en/latest/train/train.html)
