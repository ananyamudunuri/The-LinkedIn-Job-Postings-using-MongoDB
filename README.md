# Data-225 Sec 24 Db Systems for Analytics lab2


The LinkedIn Job Postings dataset from Kaggle is a comprehensive collection of job postings from different companies from the LinkedIn platform. This dataset includes information about job titles,skill required, company details, job locations, job descriptions, and more.

Our objective of this project is to perform  data analysis using NoSQL MongoDB. 

To build the entire application, following steps were performed for this project.
   - Data Collection and clean up
   - Function analysis
   - Conceptual schema design
   - Conversion of the data into JSON and schema building for NoSQL database
   - Data Upload in to MongoDB Compass, Database and Collection creation
   - Perform queries on MongoDB
   - Conncetion to MongoDB Cloud
   - User creation and authentication
   - Performance measure

### Linkedin Job Analysis dataset contains 3 categories of dataset:

#-------------------------------------------------------------------------------------------------------------------------#
### Input Data set 
#-------------------------------------------------------------------------------------------------------------------------#

### Company_details: 
     companies.csv
     company_industries.csv
     company_specialities.csv
     employee_counts.csv

### job_details:
      benefits.csv
      job_industries.csv
      job_skills.csv
### job_postings:
      job_postings.csv

#-------------------------------------------------------------------------------------------------------------------------#
##  Application Building Steps
#-------------------------------------------------------------------------------------------------------------------------#
### Data Collection and clean up
#-------------------------------------------------------------------------------------------------------------------------#

In the previous Lab1 project we habe already preprocessed all these datasets using Jupyter notebook and for this Lab2 we will be using those csv files again.
Reference: 

      -   ReadMe_Job_postings.ipynb has elaborated details of preprocessing of Job Postings file.
      -   README_JOBPOSTINGS.ipynb

#-------------------------------------------------------------------------------------------------------------------------#
### Functional Analysis and USE case diagram design
#-------------------------------------------------------------------------------------------------------------------------#

#### Functional Analysis requires identification of functional elements. Here we assume the database system will be accessed by two type of users. 
#### a. Job Seeker

A job seeker can have multiple requirements from the system.
                            
####      1. a job seeker can search for:
                          i. a job on the platform based on skills, experience level, job title, job location, salary etc.
                          ii. a company on the platform based on company name, company size,benefits etc.
####      2. a job seeker can view and apply for a job:
                          i. if the job post has application link posted on the platform job seeker can apply
                          ii. if not, job seeker can view the job post via platform
####  b. HR Manager

An HR manager can have multiple requirements from the system.

 ####       1. HR manager can add job post on the platform
 ####       2. HR manager can view performance of the job post, keep track of applications on the platform


#-------------------------------------------------------------------------------------------------------------------------#
### Conceptual schema design
#--------------------------------------------------------------------------------------------------------------------------#

While designing conceptual schema we took reference from Lab1 database creation. 

![EER Diagram_v0 1](https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/42118282/03b2696c-5edc-4152-8f49-a0745faf15dc)

In Lab1 we designed 8 normalised tables and defined their cardinalities. However, for NoSQL database we will be designing denormalised tables/Collections.
Primarily, we have deisgned two Collections for Lab2 analysis: 
  
####   CompanyJobDetail : This collection is for all the job requirements from different companies. 
      
       -  Document Structure of this collection is designed from JOB table schema created in Lab1 along with Company table schema. 
       
       -  Company table schema is inserted as Embedded document under Company details. 
          
       -  This embedded document also has company industry and speciality added as a list elements to reduce data redundancy.
       
       -  Also, this document structure has Jobskills added as a list element to reduce load of data redundancy.
       
       -  Entire collection is limited to these tables to optimize query performance and data load.

Here is a snippet of CompanyJobDetails JSON Schema structure for one document.    

<img width="779" alt="Company_Job_Detail_Schema" src="https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/42118282/8fd2d702-7f49-449a-8626-45d13cefed50">


####   JobPostFromCompany : This collection is for all the job post details posted on LinkedIn Website from different companies. 
      
       -  Document Structure of this collection is designed from JOBPOST table schema created in Lab1. 
       
       -  Company name and id from Company table schema is inserted as Embedded document under Company details. 
          
       -  This embedded document also has employee count and follower count from EMP_CNT table schema added from Lab1.
       
       -  Also, this document structure has Benefits added as a list element to reduce load of data redundancy.
       
       -  Entire collection is limited to these tables to optimize query performance and data load.

 Here is a snippet of CompanyJobDetails JSON Schema structure for one document.    

<img width="786" alt="JobPostFromCompany_Schema" src="https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/42118282/902ae47c-d954-4e0e-9f54-c1cde78b0a01">


#-------------------------------------------------------------------------------------------------------------------------#
### Conversion of the data into JSON and schema building for NoSQL database
#-------------------------------------------------------------------------------------------------------------------------#

To create Collections in MongoDB database, we converted our csv data into JSON format through pandas Dataframe and developed the schema structure using Jupyter notebook.
'SchemaDesign_And_JSON_Conversion.ipynb' showcases all the conversion steps in detail.


#-------------------------------------------------------------------------------------------------------------------------#
### Data Upload in to MongoDB Compass, Database and Collection creation
#-------------------------------------------------------------------------------------------------------------------------#

We have created Clusters and Database in MongoDB. And uploaded JSON data into two collections:
- CompanyJobDetail : This collection has 15520 documents.
- JobPostFromCompany : This collection has 15470 documents.

#-------------------------------------------------------------------------------------------------------------------------#
### Perform queries on MongoDB
#-------------------------------------------------------------------------------------------------------------------------#

