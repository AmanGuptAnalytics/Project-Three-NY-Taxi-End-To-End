
# Project-Three-NY-Taxi-End-To-End

This is an end-to-end project which involves the following technologies. 

<img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" />
<img src="https://img.shields.io/badge/airflow-000000?style=for-the-badge&logo=apacheairflow&logoColor=white" />
<img src="https://img.shields.io/badge/Google Cloud-4885ed?style=for-the-badge&logo=googlecloud&logoColor=white" />
<img src="https://img.shields.io/badge/dbt-FFFFFF?style=for-the-badge&logo=dbt&logoColor=orange" /> 
<img src="https://img.shields.io/badge/Tableau-FFFFFF?style=for-the-badge&logo=tableau&logoColor=blue" />


## Intro

This project is made to analyze Newyorks Yellow and Green taxi data, from this [website](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) 


The data is ingested to google cloud storage bucket via a python script that iterates the ingestion per month for 2019 and 2020. The python script is 
run as a DAG in Airflow, the setup of how to use airflow is described in the Airflow folder. 

After that the data is transformed into staging table and fact and dimension tables. The data thus transformed is connected to TABLEAU
and two dashboards are made to help personas like a cabbie to understand where and at what time most number of cabs are required.
And to understand managers about the revenue collected over two year period.




## **Airflow**

### First DAG: " *data_ingestion_gcs_dag.py* "

The first dag is this 

[data_ingestion_gcs_dag.py](https://github.com/AmanGuptAnalytics/Project-Three-NY-Taxi-End-To-End/blob/main/airflow/dags/data_ingestion_gcs_dag.py)

Order of tasks 
```
     download_dataset_task >> local_to_gcs_task >> rm_task
```

The above flow runs for two taxi data which is named as yellow_taxi_data_v2 and green_taxi_data_v2.

### Second DAG: " *gcs_to_bq_dag.py* "

The second dag is this : 
[gcs_to_bs_dag](https://github.com/AmanGuptAnalytics/Project-Three-NY-Taxi-End-To-End/blob/main/airflow/dags/gcs_to_bq_dag.py)

Order of tasks
``` 
     move_files_gcs_task >> bigquery_external_table_task >> bq_create_partitioned_table_job
```


Below are the descriptions of tasks in this DAG:

+ The above dag moves the files from gcs raw folder to organized gcs folders, this is done bigquery_external_table_taskmove_files_gcs_task.
+ Next, external table is made in bigquery by using the task bigquery_external_table_task which takes the data from organized gcs bucket and creates external tables.
+ The third task is to create partitioned tables in bigquery, this is done by the task:  bq_create_partitioned_table_job. 

### **Data Transformation**

After the data is written into bigquery tables, it must be adequately modeled to serve the needs of the business. For this data, dbt cloud was used as it is an intuitive and easy-to-use tool for data transformations.


The first step was making ***staging tables***:

The following Tables are made
+   stg_green_tripdata
+   stg_yellow_tripdata


***stg_green_tripdata.sql***
     + It is a staging table where the data was first ranked using the window function row_number(), partitioned by vendorid and lpep_pickup_datetime. A surrogate key was also made using dbt_utils.surrogate_key and was called tripid.
     + Next, the columns are assigned respective data types using the CAST function.
***stg_green_tripdata.sql***
     + Similarly to the above staging table, the data is first passed over a window function, and then various columns are assigned proper datatypes using the CAST function.

The first step in modelling is to create the staging tables from the data lake. There are two staging tables created in this project which are for green_tripdata and yellow_tripdata respectively.

The purpose of staging table here is to make the source tables for fact and dimension table. Also to normalize or denormalize the data as per the users need in this case we have normalized the data.

In this project the columns to be used in fact and dimension tables were ingested to the staging table and assigned the datatype using cast function. 

### **Testing the data**

A step while staging the tables is testing. dbt offers several types of testing out of which the following tests are used:

+ unique 
+ not_null
+ relationships
+ accepted_values

In detail how and to which columns these tests are applied can be found payment_type_values [here](https://github.com/AmanGuptAnalytics/Project-Three-NY-Taxi-End-To-End/tree/main/dbt%20cloud%20model/models/staging).


### **Fact & Dimension tables**

The fact and dimension tables are the next and final step of the data transformation.
This is the Flow of the data between various tables to make the fact and dimension tables.
Some of the fields in the tables are again checked via testing and are available [here](https://github.com/AmanGuptAnalytics/Project-Three-NY-Taxi-End-To-End/blob/main/dbt%20cloud%20model/models/core/schema.yml).

The following tables were made:
+ dim_zones
+ dm_monthly_zone_revenue.sql 
+ fact_trips


![Flow of Data](https://github.com/AmanGuptAnalytics/Project-Three-NY-Taxi-End-To-End/blob/main/airflow/docs/Fact_Trips.png)

