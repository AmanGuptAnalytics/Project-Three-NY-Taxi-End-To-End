### Concepts

 [Airflow Concepts and Architecture](../week_2_data_ingestion/airflow/docs/1_concepts.md)

### Workflow

 ![](docs/gcs_2_bq_dag_graph_view.png)
 
 ![](docs/gcs_2_bq_dag_tree_view.png)
 
### Setup - Official Version
 (For the section on the Custom/Lightweight setup, scroll down)

 #### Setup
  [Airflow Setup with Docker, through official guidelines](1_setup_official.md)

 #### Execution
 
  1. Build the image (only first-time, or when there's any change in the `Dockerfile`, takes ~15 mins for the first-time):
     ```shell
     docker-compose build
     ```
   
     or (for legacy versions)
   
     ```shell
     docker build .
     ```

 2. Initialize the Airflow scheduler, DB, and other config
    ```shell
    docker-compose up airflow-init
    ```

 3. Kick up the all the services from the container:
    ```shell
    docker-compose up
    ```

 4. In another terminal, run `docker-compose ps` to see which containers are up & running (there should be 7, matching with the services in your docker-compose file).

 5. Login to Airflow web UI on `localhost:8080` with default creds: `airflow/airflow`

 6. Run your DAG on the Web Console.

 7. On finishing your run or to shut down the container/s:
    ```shell
    docker-compose down
    ```

    To stop and delete containers, delete volumes with database data, and download images, run:
    ```
    docker-compose down --volumes --rmi all
    ```

    or
    ```
    docker-compose down --volumes --remove-orphans
    ```
    
    This is how the docker is setup to run Airflow, the dags are run in Airflow as shown above.

