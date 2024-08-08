# TFX on Cloud AI Platform Pipelines

## Task 1. Create an instance of AI Platform Pipelines

1. In the Google Cloud Console, on the Navigation menu, scroll down to AI Platform and pin the section for easier access later in the lab.

2. Navigate to AI Platform > Pipelines.

3. Then click New Instance.

4. Click Configure.

5. To create cluster select Zone as `us-central1-f` then check Allow access to the following Cloud APIs, leave the name as is, and then click Create New Cluster.

6. Leave everything as default and click Deploy. You will see the individual services of KFP deployed to your GKE cluster. Wait for the deployment to finish before proceeding to the next task.

7. In Cloud Shell, run the following to configure kubectl command line access

    ```bash
    gcloud container clusters get-credentials cluster-1 --zone us-central1-f --project qwiklabs-gcp-02-df0ea1170d00
    ```

8. In Cloud Shell, run the following to get the ENDPOINT of your KFP deployment

    ```bash
    kubectl describe configmap inverse-proxy-config | grep googleusercontent.com
    ```

## Task 2. Access AI Platform Notebooks

## Task 3. Clone the example repo within your AI Platform Notebooks instance

1. In JupyterLab, click the Terminal icon to open a new terminal.

2. At the command-line prompt, type in the following command and press Enter:

    ```bash
    git clone https://github.com/GoogleCloudPlatform/mlops-on-gcp
    ```

3. Confirm that you have cloned the repository by double clicking on the `mlops-on-gcp` directory and ensuring that you can see its contents. The files for all the Jupyter notebook-based labs throughout this course are available in this directory.

## Task 4. Navigate to the lab notebook

1. From the `mlops-labs/workshops/tfx-caip-tf23` folder execute the `install.sh` script to install TFX and KFP SDKs:

    ```bash
    cd mlops-on-gcp/workshops/tfx-caip-tf23
    ./install.sh
    ```

2. In the AI Platform Notebook, navigate to `mlops-labs/workshops/tfx-caip-tf23/lab-02-tfx-pipeline/labs` and open `lab-02.ipynb`.

3. Clear all the cells in the notebook (look for the Clear button on the notebook toolbar) and then Run the cells one by one.

4. When prompted, come back to these instructions to check your progress in the Task 5. Run your training job in the cloud section.
