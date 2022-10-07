
# Project-Three-NY-Taxi-End-To-End

This is an end-to-end project which involves the following technologies. 

:PYTHON, AIRFLOW, dbt, GCP, BQ, TABLEAU


## Intro

This project is made to analyze Newyork Yellow and Green taxi data, from website 

https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page 

From this data regarding Yellow and Green taxi are ingested to google cloud storage bucket
via a python script that iterates the ingestion per month for 2019 and 2020. The python script is 
run as a Dag in Airflow, the setup of how to use airflow is described in the Airflow folder. 

After that the data is transformed into staging table and fact and dimension tables. The data thus transformed is connected to TABLEAU
and two dashboards are made to help personas like a cabbie to understand where and at what time most number of cabs are required.
And to understand managers about the revenue collected over two year period.




## Setup of Airflow

### First DAG: " *data_ingestion_gcs_dag.py* "

The first dag is this 

[data_ingestion_gcs_dag.py](https://github.com/AmanGuptAnalytics/Project-Three-NY-Taxi-End-To-End/blob/main/airflow/dags/data_ingestion_gcs_dag.py)

This script is used to make dags such that this execution flow runs 
```
     download_dataset_task >> local_to_gcs_task >> rm_task
```

The above flow runs for two taxi data which is named as yellow_taxi_data_v2 and green_taxi_data_v2. Different parameters for the dag are set
such as schedule date, end date, start date, default args, catchup, max active runs and tags. The data is loaded to google cloud bucket storage.


### Second DAG: " *gcs_to_bq_dag.py* "

The second dag has three tasks:
``` 
     move_files_gcs_task >> bigquery_external_table_task >> bq_create_partitioned_table_job
```

The second dag is this : 
[gcs_to_bs_dag](https://github.com/AmanGuptAnalytics/Project-Three-NY-Taxi-End-To-End/blob/main/airflow/dags/gcs_to_bq_dag.py)

Below are the descriptions of tasks in this DAG:

+ The above dag moves the files from gcs raw folder to organized gcs folders, this is done bigquery_external_table_taskmove_files_gcs_task.
+ Next, external table is made in bigquery by using the task bigquery_external_table_task which takes the data from organized gcs buck and creates external tables.
+ The third task is to create partitioned tables in bigquery, this is done by the task:  bq_create_partitioned_table_job. 
*** Modeling in dbt

After the data is written into bigquery tables, it must be adequately modeled to serve the needs of the business. For this data, the dbt cloud was used dbt cloud is an intuitive and easy-to-use tool for transformations related to information.


The first step was making ***staging tables***:
***stg_green_tripdata.sql***
     + It is a staging table where the data was first ranked using the window function row_number(), partitioned by vendorid and lpep_pickup_datetime. A surrogate key was also made using dbt_utils.surrogate_key and was called tripid.
     + Next, the columns are assigned respective data types using the CAST function.
***stg_green_tripdata.sql***
     + Similarly to the above staging table, the data is first passed over a window function, and then various columns are assigned proper datatypes using the CAST function.
Testing using schema.yml +

