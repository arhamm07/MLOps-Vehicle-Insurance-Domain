## 🌟 Project Overview: Vehicle Data MLOps Pipeline

This project implements a complete **Machine Learning Operations (MLOps)** pipeline for vehicle data analysis. It covers **setup**, **data management** (MongoDB), **ML workflow** (Data Ingestion to Model Trainer), **cloud integration** (AWS S3), and **CI/CD** (GitHub Actions, Docker, ECR, EC2).

---

## 🛠️ Setup and Installation

### Core Components and Configuration

* **Template Generator:** `template.py`
* **Package Configuration:** `setup.py`, `pyproject.toml`
* **Local Dependencies:** `requirements.txt`
* **Configuration Info:** `crashcourse.txt`

1.  **Project Initialization:** Execute `python template.py`.
2.  **Package Configuration:** Write the code on **`setup.py`** and **`pyproject.toml`** to import local packages. (Refer to **`crashcourse.txt`**).
3.  **Virtual Environment & Dependencies:**
    * Create: `conda create -n vehicle python=3.10 -y`
    * Activate: `conda activate vehicle`
    * Install: `pip install -r requirements.txt`
4.  **Verification:** Run `pip list` to ensure local packages are installed.

---

## 🌐 MongoDB Database Setup

### Database Components

* **Database Service:** **MongoDB Atlas** 
* **Data Artifact:** `Dataset`
* **Demonstration:** `notebook/mongoDB_demo.ipynb`

1.  **Setup MongoDB Atlas:** Sign up, create a project and an M0 cluster.
2.  **Security:** Set up a **DB User** (username/password) and add **Network Access** (`0.0.0.0/0`).
3.  **Connection String:** Get the connection string (Python driver) and save it (replace `<password>`).
4.  **Initial Data Push:**
    * Create folder: `notebook`.
    * Add: `Dataset` (to `notebook` folder).
    * Create file: `mongoDB_demo.ipynb` (select `vehicle` kernel).
    * Push your data to MongoDB from the notebook.
5.  **Verification:** Go to MongoDB Atlas >> Database >> browse collection to see the data.

**Set `MONGODB_URL` Environment Variable:**
This is crucial for the ingestion component to connect to the database.

| OS/Shell | Command to Set |
| :--- | :--- |
| **Bash** | `export MONGODB_URL="mongodb+srv://..."` |
| **PowerShell** | `$env:MONGODB_URL = "mongodb+srv://..."` |

---

## 💻 Core Development Steps

### Development Utilities

* **Logging:** Logger file, tested on **`demo.py`**
* **Exception Handling:** Exception file, tested on **`demo.py`**
* **Exploratory Analysis:** **EDA and Feature Engg notebook**

### ⚙️ Machine Learning Pipeline Components

#### 1. Data Ingestion Component

* **Configuration Artifacts:** **`constants.__init__.py`**, **`configuration.mongo_db_connections.py`**
* **Data Access Layer:** **`data_access/proj1_data.py`**
* **Entity Definition:** **`entity.config_entity.py`** (till `DataIngestionConfig`), **`entity.artifact_entity.py`** (till `DataIngestionArtifact`)
* **Core Component:** **`components.data_ingestion.py`**
* **Execution:** Training Pipeline, `demo.py` (Run after setting `MONGODB_URL`)

#### 2. Data Validation Component

* **Utility/Schema:** **`utils.main_utils.py`**, **`config.schema.yaml`** (contains dataset information for validation)
* **Workflow:** Complete **entities**, **component**, and **pipeline** integration.

#### 3. Data Transformation Component

* **Entity Artifact:** **`entity/estimator.py`** (for transformation estimator)
* **Workflow:** Complete **entities**, **component**, and **pipeline** integration.

#### 4. Model Trainer Component

* **Entity Artifact:** **`entity/estimator.py`** (for model trainer class)
* **Workflow:** Complete **entities**, **component**, and **pipeline** integration.

---

## ☁️ Cloud Integration (AWS)

### AWS Services Setup

* **Cloud Service:** **AWS S3**
* **IAM User:** `firstproj` (with `AdministratorAccess`)
* **Model Storage:** S3 Bucket (`my-model-mlopsproj`)
* **S3 Utility Files:** **`src.configuration.aws_connection.py`**, **`src.aws_storage`**, **`entity/s3_estimator.py`**
* **Constants:** `MODEL_EVALUATION_CHANGED_THRESHOLD_SCORE`, `MODEL_BUCKET_NAME`, `MODEL_PUSHER_S3_KEY`

1.  **IAM User & Credentials:** Create IAM user (`firstproj`), attach `AdministratorAccess`, create Access Key, and set environment variables: `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`.
2.  **S3 Bucket Creation:** Create the S3 bucket: **`my-model-mlopsproj`** (Region: `us-east-1`), ensuring "Block all public access" is **unchecked**.
3.  **Implement S3 Logic:** Add code to connect to AWS and implement model pull/push functionalities in the S3 utility files.

### 5. Model Evaluation and Pusher Components

* **Core Components:** **Model Evaluation**, **Model Pusher**
* **Artifacts:** Use S3 utilities to fetch/push the model from/to the S3 bucket.

---

## 🚀 Deployment Pipeline

### Prediction Pipeline

* **Structure:** **`Prediction Pipeline`** code structure
* **Application Server:** **`app.py`**
* **Web Assets:** **`static`** and **`template`** directories

### CI/CD Pipeline Setup

* **Containerization:** **`dockerfile`**, **`.dockerignore`**
* **CI/CD Workflow:** **`.github/workflows/aws.yaml`**
* **Deployment Target:** **AWS EC2** (Ubuntu Server) 
* **Image Registry:** **AWS ECR** (`vehicleproj` repository)
* **CI/CD IAM User:** `usvisa-user` (for GitHub Actions)

1.  **Setup AWS ECR:** Create ECR repository named `vehicleproj`. Copy the **repository URI**.
2.  **Setup AWS EC2:** Launch Ubuntu Server (`T2 Medium`), create a key pair, and allow traffic on **ports 80/443**.
3.  **Install Docker on EC2:** Run the necessary commands on the EC2 instance to install and configure Docker.
4.  **GitHub Self-Hosted Runner:** Configure the EC2 instance as a self-hosted runner for GitHub Actions and start it (`./run.sh`).
5.  **GitHub Secrets:** Set the following repository secrets:
    * `AWS_ACCESS_KEY_ID` (for `usvisa-user`)
    * `AWS_SECRET_ACCESS_KEY` (for `usvisa-user`)
    * `AWS_DEFAULT_REGION` (`us-east-1`)
    * `ECR_REPO` (your ECR URI)
6.  **Activate Deployment:** CI-CD pipeline will trigger on the next commit/push.
7.  **Final App Launch:**
    * Open EC2 Security Group and add a Custom TCP rule for **Port 5080** (Source: `0.0.0.0/0`).
    * Access the application via `http://<EC2_Public_IP>:5080`.
    * Model training can be triggered via the `/training` route.