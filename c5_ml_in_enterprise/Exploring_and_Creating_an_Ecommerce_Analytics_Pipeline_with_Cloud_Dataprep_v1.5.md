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

## Task 2. Open Cloud Dataprep

1. Run the following command in the Cloud Shell to create a required Service Account which will allow you to successsfully access Dataprep.

    ```bash
    gcloud beta services identity create --service=dataprep.googleapis.com
    ```

## Task 3. Connect BigQuery data to Cloud Dataprep

## Task 4. Explore ecommerce data fields with a UI

![image](https://cdn.qwiklabs.com/XvknvNGV4DFB2vDqo%2BhlqURR%2BC5zNvsA2pE7lfw1Cec%3D)

![image](https://cdn.qwiklabs.com/9L1l%2FNknuj40nXW1EGNMVz%2FWr%2BplV4anLgNWJfZvHj4%3D)

![image](https://cdn.qwiklabs.com/o%2BaeuOZk6%2BwUgw%2F2gf60BFCvHlk7V0AyEZ23jryc%2B3Y%3D)

![image](https://cdn.qwiklabs.com/Gddqosl4E0xkae7pSJSI2kePjp%2BV9J7RoAgyg3gyru4%3D)
![image](https://cdn.qwiklabs.com/OQeZsCroQLukI8sQgySErq4zDIWQ94XFuHMAjo5V24g%3D)

![image](https://cdn.qwiklabs.com/m%2FnSrZIMLfP9NOEimu9Svqmgajog6U2c9zfLH%2FuK%2Fs4%3D)

## Task 5. Clean the data

![image](https://cdn.qwiklabs.com/A%2BMiHhzhYiiAgcyhyUbQPKFx%2BWheTpA2jBttCKwP8LM%3D)

![image](https://cdn.qwiklabs.com/mNDpRfHOA8BomAEY1QYHj3waBulnEZk7%2BSyq0vN%2BGG0%3D)

![image](https://cdn.qwiklabs.com/ohup2bmk4ksUUS49y2ygHEUYnGD9I70JZVMUh5EaDYA%3D)

## Task 6. Enrich the data

Create a new column for a unique session ID
As you discovered, the dataset has no single column for a unique visitor session. Create a unique ID for each session by concatenating the fullVisitorID and visitId fields.

Click on New Step.

For Search transformation, type concat, and then select Merge columns.

For Columns, select fullVisitorId and visitId.

For the New column name, type unique_session_id, and leave the other inputs as their default values and click Add.

### Create a case statement for the ecommerce action type

The eCommerceAction_type field is an integer that maps to actual ecommerce actions performed in that session like 3 = “Add to Cart” or 5 = “Check out.” Create a calculated column that maps to the integer value.

1. Click on New Step.

2. In the Transformation panel, type case, and then select Conditional column.

3. Select Case on single column from the drop-down.

4. For Column to evaluate, specify `eCommerceAction_type`.

5. Next to Cases (X), click Add 8 times for a total of 9 cases.

6. For each Case, specify the following mapping values (including the quotes).

    | Value to compare | New value |
    | ---------------- | --------- |
    | 1                | 'Click through of product lists' |
    | 2                | 'Product detail views' |
    | 3                | 'Add product(s) to cart' |
    | 4                | 'Remove product(s) from cart' |
    | 5                | 'Check out' |
    | 6                | 'Completed purchase' |
    | 7                | 'Refund of purchase' |
    | 8                | 'Checkout options' |
    | 0                | 'Unknown' |

7. For New column name, type eCommerceAction_label, and then click Add.

8. Review the Recipe and compare it to this example:

    ![image](https://cdn.qwiklabs.com/%2Ffa2EXxErTjo28zR%2Fw3TyzxCQGG4t9weehVB74y%2B8cU%3D)

## Task 7. Run Cloud Dataprep job to load BigQuery

1. From the Transformer page, in the upper right, click Run.

2. In the Publishing Actions section, hover over on Create-CSV then click Edit.

3. Select BigQuery in the left panel and go into your ecommerce dataset, and then click Create a new table.

4. Name the output table `apparel_revenue` and select Drop the table every run in the right panel.

5. Click Update.

6. Click Run.

7. Click Job history in the left panel to monitor your Cloud Dataprep job.

8. Wait 1 - 2 minutes for your job to run

After your Cloud Dataprep job finishes, refresh your BigQuery page and confirm that the output table `apparel_revenue` exists.

![image](https://cdn.qwiklabs.com/BQpMTlZF9yy4E2mRj226ox1vSskVnh%2B3%2BAEp9BtRo%2BU%3D)

![image](https://cdn.qwiklabs.com/mLqGzoZF790G2E%2Fb0C0%2F9Q%2B7LVZmgDfL9MNccmpw86g%3D)
