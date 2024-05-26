# Training and Deploying a TensorFlow Model in Vertex AI

![image](https://cdn.qwiklabs.com/9o%2FVIaICB83dXmHVwnBlArxUkhQU61Bkj7776Wsa45Y%3D)

## Prework

- List the active account name:

```bash
gcloud auth list
```

- List the project ID:

```bash
gcloud config list project
```

> Note: Full documentation of gcloud is available in the [gcloud CLI overview guide](https://cloud.google.com/sdk/gcloud).

## Task 1. Enable Google Cloud services

```bash
gcloud services enable \
  compute.googleapis.com \
  iam.googleapis.com \
  iamcredentials.googleapis.com \
  monitoring.googleapis.com \
  logging.googleapis.com \
  notebooks.googleapis.com \
  aiplatform.googleapis.com \
  bigquery.googleapis.com \
  artifactregistry.googleapis.com \
  cloudbuild.googleapis.com \
  container.googleapis.com
```

## Task 2. Create Vertex AI custom service account for Vertex Tensorboard integration

- Create custom service account:

```bash
SERVICE_ACCOUNT_ID=vertex-custom-training-sa
gcloud iam service-accounts create $SERVICE_ACCOUNT_ID  \
    --description="A custom service account for Vertex custom training with Tensorboard" \
    --display-name="Vertex AI Custom Training"
```

- Grant it access to Cloud Storage for writing and retrieving Tensorboard logs:

```bash
PROJECT_ID=$(gcloud config get-value core/project)
gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member=serviceAccount:$SERVICE_ACCOUNT_ID@$PROJECT_ID.iam.gserviceaccount.com \
    --role="roles/storage.admin"
```

- Grant it access to your BigQuery data source to read data into your TensorFlow model:

```bash
gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member=serviceAccount:$SERVICE_ACCOUNT_ID@$PROJECT_ID.iam.gserviceaccount.com \
    --role="roles/bigquery.admin"
```

- Grant it access to Vertex AI for running model training, deployment, and explanation jobs:

```bash
gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member=serviceAccount:$SERVICE_ACCOUNT_ID@$PROJECT_ID.iam.gserviceaccount.com \
    --role="roles/aiplatform.user"
```

## Task 3. Launch Vertex AI Workbench notebook

## Task 4. Clone the lab repository

```bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
```

## Task 5. Install lab dependencies and Run the Notebook

```bash
cd training-data-analyst/self-paced-labs/vertex-ai/vertex-ai-qwikstart
```

```bash
pip3 install --user -r requirements.txt
```

## Task 6. Run Notebook

Check Jupyter Notebook:

[![Jupyter](https://img.shields.io/badge/-Jupyter-black?style=for-the-badge&logo=jupyter)](./notebooks/lab_exercise_long.ipynb)
