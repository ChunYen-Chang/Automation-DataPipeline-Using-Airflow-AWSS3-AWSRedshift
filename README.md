<p align="center">
  <img width="730" height="370" src="https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/project_logo.jpg">
</p>

# Automatic DataPipeline between Cloud Data Lake and Cloud Data Warehouse
### *PROJECT BACKGROUND AND GOAL*
- #### BACKGROUND
    A startup company recently moves its data storage and data processing server to AWS cloud data lake (AWS S3) and AWS cloud data processing cluster (AWS spark cluster). The introduction of AWS cloud services improves this company's data storage and data processing ability. Now, this company wants more. There are two things this company wants to achieve in this project.

    First, use AWS Redshift to build a cloud data warehouse. In the past, it is hard for other departments to access the user data or the song data which is collected by this company. If the data can be shared by other departments, it may benefit this company's business. Thus, this company decides to set up a cloud data warehouse that stores clean and structured data. By having this cloud data warehouse, other departments can easily access the data and use the data to improve this company's business.

    Second, use AWS serverless services, AWS Glue and AWS Athena, to build an immediate data searching and analysis service for data analysis team.** As in the early days, the data analysis team only can check the data or conduct data analysis tasks when data arrives in the database or data warehouse. It is hard for them to check the data or conduct the data analysis when data just arrives at the data lake. If the data analysis team can check the data or conduct the data analysis when data just arrives, it may find some valuable points and these points help data engineers to improve the data pipeline. Therefore, this company considers introducing AWS Glue and AWS Athena to create an immediate data searching and analysis service which allows the data analysis team to use SQL queries to check or analyze the data in the data lake.

- #### PROJECT GOAL
1. *Launch a cloud data warehouse by using AWS Redshift :* 

    Considering the query efficiency in the cloud data warehouse, we decide to use star schema to store our data. And, we build an automatic data pipeline between cloud data lake (AWS S3) and cloud data warehouse (AWS Redshift) by using Apache Airflow. This data pipeline automatically extracts data from the cloud data lake, clean the data, dump data in a stage table in Redshift, execute the data quality check, and store data in the final table in Cloud data lake with a star schema. This automatic data pipeline helps engineers to reduce the burden of executing and maintaining the ETL data pipeline manually.

2. *Build an immediate data searching and analysis service :*

    It allows the data analysis team to use SQL query to check or analyze the data in the data lake.

<br>

------------

### *SYSTEM ARCHITECTURE*
<p align="center">
  <img width="850" height="550" src="https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/system_architecture_new.jpeg">
</p>

<br>

### DATA PIPELINE DETAILS
- #### Airflow DAG
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/DAG.jpg)
- #### Each DAG Task Explanation
1. **Begin_execution :** 
    Start the data pipeline.  
    
2. **Stage_events :**
    Create Stage_events table in Redshift, load events data from S3 and dump the data in Stage_event table  
    
3. **stage_songs :** 
    Create Stage_songts table in Redshift, load songs data from S3 and dump the data in Stage_song table  
    
4. **Load_songplays_fact_table :** 
    Create songplay table in Redshift, Load data from Stage_event and Stage_song table, join these two tables, and dump the joined result in songplay table  
    
5. **Load_user_dim_table :**
    Create user table in Redshift, Load user data from songplay table, and dump the data in user table  
    
6. **Load_song_dim_table :** 
    Create song table in Redshift, Load song data from songplay table, and dump the data in song table  
    
7. **Load_time_dim_table :** 
    Create time table in Redshift, Load time data from songplay table, and dump the data in time table  
    
8. **Load_artist_dim_table :** 
    Create artist table in Redshift, Load artist data from songplay table, and dump the data in artist table  
    
9. **Data_quality_checks_users :**
    Check how many rows in user table, if it is less than 1, raise an error.  
    
10. **Data_quality_checks_songs :** 
    Check how many rows in song table, if it is less than 1, raise an error.  
    
11. **Data_quality_checks_time :** 
    Check how many rows in time table, if it is less than 1, raise an error.  
    
12. **Data_quality_checks_artists :** 
    Check how many rows in artist table, if it is less than 1, raise an error.  

<br>

### *DATA MODELING*
- #### *Star Schema*
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/datamodel.jpg)

------------
### *FILES IN THE REPOSITORY*
1. **README.md**: An introduction to this project

2. **image directory**: A directory for keeping images for this project

3. **airflow directory**: A directory for keeping all scripts
    - **dags folder**: contain the python script which is executed by Apache Airflow
        - **datapipeline_between_s3_redshift.py**: a python script which is used for defining the DAG
    - **plugins folder**: contain functions are used by Sparkify_datapipeline_dag.py
        - **helpers folder**: contain the python script of sql_queries function
        - **operators folder**: contain the python script about each operator used in Sparkify_datapipeline_dag.py

------------
### *HOW TO EXECUTE THE PROJECT*
The project has two parts. The first part is executing the automatic data pipeline by airflow. The second part is setting up serverless AWS services, AWS glue and AWS Athena.

**PART ONE: Execute the automatic data pipeline by airflow.**
1. change directory to the folder you install your airflow. Then, find start.sh file. In this project, the start.sh file is located on **/opt/airflow/start.sh**. Thus, we type `/opt/airflow/start.sh` in our terminal to launch the Airflow server.

2. access to Airflow webUI. Generally speaking, you can access to the webUI through this address **http://localhost:8080**

3. configure AWS credentials (it allows us to access to AWS account)
3-1. Admin > Connections
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/1.jpg)

3-2. Click Create
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/2.jpg)

3-3. type **AWS access key id** and **secret access key**
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/3.jpg)  

4. configure Redshift connection
4-1. Admin > Connections
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/1.jpg)

4-2. Click Create
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/2.jpg)

4-3. type Redshift information in these columns
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/4.jpg)  

5. start the data pipeline
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/5.jpg)

6. monitor the data pipeline
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/6.jpg)

<br>

**PART TWO: Set up serverless AWS services, AWS glue and AWS Athena.**
1. Find AWS Glue in AWS web dashboard
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_01.png)

2. Click Add tables using a crawler
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_02.png)

3. Give a crawler name
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_03.png)

4. Tell this crawler the source type
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_04.png)

5. Choose a data directory, crawler will go there and get the metadata 
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_05.png)
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_06.png)

6. Click Next
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_07.png)

7. Choose No, then, Click Next
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_08.png)

8. Choose IAM role
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_09.png)

9. Create a scheduler for this crawler, choose run on demane
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_10.png)

10. Define the output, in this example, the metadata will be in startup_sond DB
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_11.png)

11. Review all settings, and Finish
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_12.png)

12. Click Run Crawler
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_13.png)

13. Wait for the task
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_14.png)

14. Now, Crawler finished its job, and the meta data is in DB, and we have a table.
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/glue_15.png)

15. Go to AWS web dashboard, find AWS Athena service
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/athena_01.png)

16. Click settings, and type the S3 directory you want to save the query result
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/athena_03.png)

17. Typr SQL query, and you can search and filter the data store in AWS S3
![](https://github.com/ChunYen-Chang/Automatic-DataPipeline-between-Cloud-datalake-and-Cloud-datawarehouse/blob/master/images/athena_04.png)
