# TFX Standard Components Walkthrough

## Task 1. Enable Cloud Services

1. In Cloud Shell, run the command below to set the project id to your Google Cloud Project:

    ```bash
    PROJECT_ID=Project ID
    gcloud config set project $PROJECT_ID
    ```

2. Next, execute the following commands to enable the required cloud services:

    ```bash
    gcloud services enable \
    cloudbuild.googleapis.com \
    container.googleapis.com \
    cloudresourcemanager.googleapis.com \
    iam.googleapis.com \
    containerregistry.googleapis.com \
    containeranalysis.googleapis.com \
    ml.googleapis.com \
    dataflow.googleapis.com \
    notebooks.googleapis.com
    ```

3. Next, add the Editor permission for your Cloud Build service account:

    ```bash
    PROJECT_NUMBER=$(gcloud projects describe $PROJECT_ID --format="value(projectNumber)")
    CLOUD_BUILD_SERVICE_ACCOUNT="${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com"
    gcloud projects add-iam-policy-binding $PROJECT_ID \
      --member serviceAccount:$CLOUD_BUILD_SERVICE_ACCOUNT \
      --role roles/editor
    ```

4. Now, create a custom service account to give CAIP training job access to AI Platform Vizier service for pipeline hyperparameter tuning:

    ```bash
    SERVICE_ACCOUNT_ID=tfx-tuner-caip-service-account
    gcloud iam service-accounts create $SERVICE_ACCOUNT_ID  \
      --description="A custom service account for CAIP training job to access AI Platform Vizier service for pipeline hyperparameter tuning." \
      --display-name="TFX Tuner CAIP Vizier"
    ```

5. Grant your AI Platform service account additional access permissions to the AI Platform Vizier service for pipeline hyperparameter tuning:

    ```bash
    PROJECT_NUMBER=$(gcloud projects describe $PROJECT_ID --format="value(projectNumber)")
    CAIP_SERVICE_ACCOUNT="service-${PROJECT_NUMBER}@cloud-ml.google.com.iam.gserviceaccount.com"
    gcloud projects add-iam-policy-binding $PROJECT_ID \
      --member serviceAccount:$CAIP_SERVICE_ACCOUNT \
      --role=roles/storage.objectAdmin
    ```

    ```bash
    gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member serviceAccount:$CAIP_SERVICE_ACCOUNT \
    --role=roles/ml.admin
    ```

6. Grant service account access to Storage admin role:

    ```bash
    SERVICE_ACCOUNT_ID=tfx-tuner-caip-service-account
    gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member=serviceAccount:${SERVICE_ACCOUNT_ID}@${PROJECT_ID}.iam.gserviceaccount.com \
    --role=roles/storage.objectAdmin
    ```

7. Grant service acount access to AI Platform Vizier role:

    ```bash
    gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member=serviceAccount:${SERVICE_ACCOUNT_ID}@${PROJECT_ID}.iam.gserviceaccount.com \
    --role=roles/ml.admin
    ```

8. Grant your project's AI Platform Google-managed service account the Service Account Admin role for your AI Platform service account:

    ```bash
    gcloud iam service-accounts add-iam-policy-binding \
    --role=roles/iam.serviceAccountAdmin \
    --member=serviceAccount:service-${PROJECT_NUMBER}@cloud-ml.google.com.iam.gserviceaccount.com \
    ${SERVICE_ACCOUNT_ID}@${PROJECT_ID}.iam.gserviceaccount.com
    ```

## Task 2. Create an instance of AI Platform Pipelines

1. In the cloud console, search for AI Platform. and click on GO TO AI PLATFORM.

2. Click Pipelines.

3. Then click New Instance.

4. Click Configure.

5. On the Kubeflow Pipelines page, check Allow access to the following Cloud APIs, leave the name as is, and then click Create New Cluster.

    This should take 2-3 minutes to complete. Wait for the cluster to finish before proceeding to the next step. In the first tab opened, you can view the Cluster Creation taking place in the GKE section of the Cloud Console, or see the individual VMs spinning up in the GCE section of the Cloud Console.

6. When the cluster creation is complete, leave other settings unchanged, and then click Deploy. You will see the individual services of KFP deployed to your GKE cluster.

## Task 3. Create an instance of Cloud AI Platform Notebooks

1. In Cloud Shell, run the following commands to create a user-managed notebook:

    ```bash
    gcloud notebooks instances create tfx-on-googlecloud --vm-image-project=deeplearning-platform-release --vm-image-family=tf2-2-3-cpu --machine-type=e2-medium --location=zone_to_be_set
    ```

2. Click on the Navigation Menu. Navigate to Vertex AI, then to Workbench.

3. Click on USER-MANAGED NOTEBOOKS

4. The notebook creation will take 2-3 minutes to complete.

5. Click Open JupyterLab next to your notebook name. A JupyterLab window opens in a new tab.

## Task 4. Clone the example repo within your AI Platform Notebooks instance

1. In JupyterLab, click the Terminal icon to open a new terminal.

2. At the command-line prompt, type in the following command and press Enter:

    ```bash
    git clone https://github.com/GoogleCloudPlatform/mlops-on-gcp
    ```

### Navigate to the example notebook

1. In the JupyterLab, open a terminal and execute the following commands:

    ```bash
    cd mlops-on-gcp/workshops/tfx-caip-tf23
    ./install.sh
    ```

2. Now, in AI Platform Notebooks, navigate to `mlops-on-gcp/workshops/tfx-caip-tf23/lab-01-tfx-walkthrough/labs` and open `lab-01.ipynb.`

3. Clear all the cells in the notebook (Edit > Clear All Outputs) and then Run the cells one by one.

4. When prompted, come back to these instructions to check your progress.
