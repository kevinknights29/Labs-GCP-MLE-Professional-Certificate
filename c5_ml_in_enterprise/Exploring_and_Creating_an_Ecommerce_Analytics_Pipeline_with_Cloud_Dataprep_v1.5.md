# Exploring and Creating an Ecommerce Analytics Pipeline with Cloud Dataprep v1.5

![image](https://cdn.qwiklabs.com/O2n0exH9RUENSsK99EJIUKFgk%2BX8HlTC95mpNxwqZcM%3D)

## Task 1. Create an empty BigQuery dataset

In this task, you create a new BigQuery dataset to receive the output table of your new pipeline.

1. In the left pane, click View actions (View actions icon) next to your project ID and select Create dataset.

2. In the Create dataset dialog:

    - For Dataset ID, type ecommerce.

    - Leave the other values at their defaults.

3. Click Create dataset.

4. Copy and paste this SQL query into the Query editor text field

    ```sql
    #standardSQL
    CREATE OR REPLACE TABLE ecommerce.all_sessions_raw_dataprep
    OPTIONS(
    description="Raw data from analyst team to ingest into Cloud Dataprep"
    ) AS
    SELECT * FROM `data-to-insights.ecommerce.all_sessions_raw`
    WHERE date = '20170801'; # limiting to one day of data 56k rows for this lab
    ```

5. Click Run.

6. Confirm that the new raw data table exists in your project.

## s
