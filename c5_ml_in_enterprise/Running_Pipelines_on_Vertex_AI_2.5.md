# Running Pipelines on Vertex AI 2.5

## Task 1. Set up the project environment

Run this code block in the Cloud Shell to create a bucket in your project and two folders each with an empty file:

```bash
gcloud storage buckets create gs://qwiklabs-gcp-02-afc0761fce72
touch emptyfile1
touch emptyfile2
gcloud storage cp emptyfile1 gs://qwiklabs-gcp-02-afc0761fce72/pipeline-output/emptyfile1
gcloud storage cp emptyfile2 gs://qwiklabs-gcp-02-afc0761fce72/pipeline-input/emptyfile2
```

Download the AI Pipeline from the lab assets folder:

```bash
wget https://storage.googleapis.com/cloud-training/dataengineering/lab_assets/ai_pipelines/basic_pipeline.json
```

> :memo: **Note:** You can preview the file [here!](./misc/basic_pipeline.json)

## Task 2. Configure and inspect the Pipeline code

1. First you must make an adjustment to update the output folder for the AI Pipeline execution. In the Cloud Shell use the Linux Stream EDitor (sed) command to adjust this setting:

    ```bash
    sed -i 's/PROJECT_ID/qwiklabs-gcp-02-afc0761fce72/g' basic_pipeline.json
    ```

2. Inspect basic_pipeline.json to confirm that the output folder is set to your project:

    ```bash
    tail -20 basic_pipeline.json
    ```

    The key sections of code in `basic_pipeline.json` are the `deploymentSpec` and command blocks.

3. You can explore the entire file by issuing the command below:

    ```bash
    more basic_pipeline.json
    ```

4. Next, move the updated basic_pipeline.json file to the Cloud Storage bucket created earlier so that it can be accessed to run an AI Pipeline job:

    ```bash
    gcloud storage cp basic_pipeline.json gs://qwiklabs-gcp-02-afc0761fce72/pipeline-input/basic_pipeline.json
    ```

## Task 3. Execute the AI Pipeline
