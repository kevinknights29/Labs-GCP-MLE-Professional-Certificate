# Introduction to Vertex Pipelines

![image](https://cdn.qwiklabs.com/mDPbWIHsInsU%2BMnStNQfE20gxmkwQajRVs1eZGKbLp0%3D)

## Prework

### Activate Cloud Shell

- List the active account name:

```bash
gcloud auth list
```

- List the project ID:

```bash
gcloud config list project
```

> Note: Cloud Shell has a few environment variables, including GOOGLE_CLOUD_PROJECT which contains the name of our current Cloud project. We use this in various places throughout this lab.

```bash
echo $GOOGLE_CLOUD_PROJECT
```

> Note: Full documentation of gcloud is available in the [gcloud CLI overview guide](https://cloud.google.com/sdk/gcloud).

### Enable Google Cloud services

```bash
gcloud services enable compute.googleapis.com         \
                       containerregistry.googleapis.com  \
                       aiplatform.googleapis.com  \
                       cloudbuild.googleapis.com \
                       cloudfunctions.googleapis.com
```

## Task 1. Create a Cloud Storage bucket

1. Run the following commands in your Cloud Shell terminal to create a bucket:

    ```bash
    BUCKET_NAME=gs://$GOOGLE_CLOUD_PROJECT-bucket
    gsutil mb -l us-west1 $BUCKET_NAME
    ```

2. Run the following command to add this permission

    ```bash
    gcloud projects describe $GOOGLE_CLOUD_PROJECT > project-info.txt
    PROJECT_NUM=$(cat project-info.txt | sed -nre 's:.*projectNumber\: (.*):\1:p')
    SVC_ACCOUNT="${PROJECT_NUM//\'/}-compute@developer.gserviceaccount.com"
    gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT --member serviceAccount:$SVC_ACCOUNT --role roles/storage.objectAdmin
    ```

## Task 2. Set up your environment

Enable the Vertex AI API by clicking `Click Enable All Recommended APIs.` on Vertex AI.

## Task 3. Launch a Vertex AI Notebooks instance

## Task 4. Clone a course repo within your Vertex AI Notebooks instance

```bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
```

## Task 5. Introduction to Vertex pipelines

> :memo: **Note:** You can preview the file [here!](./notebooks/intro_to_vertex_pipelines.ipynb)
