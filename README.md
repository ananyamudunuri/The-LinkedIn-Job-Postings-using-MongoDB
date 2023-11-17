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
## Application building steps
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


