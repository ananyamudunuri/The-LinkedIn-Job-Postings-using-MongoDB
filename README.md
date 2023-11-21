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

A researcher analyst can have multiple requirements from the system.
#### a. Researcher Analyst
                            
####       1. Researcher Analyst can analyse country/state wise amount job requirements using the platform
####       2. Researcher Analyst can analyse industry wise job requirements using the platform and many more for analysis purpose.
                        
       
####  b. admin user

An admin user can have multiple requirements from the system.

 ####       1. Admin user can update the records on the platform
 ####       2. Admin user can retrieve or view analysis data on the platform


#-------------------------------------------------------------------------------------------------------------------------#
### Conceptual schema design
#--------------------------------------------------------------------------------------------------------------------------#

While designing conceptual schema we took reference from Lab1 database creation. 

![EER Diagram_v0 1](https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/42118282/03b2696c-5edc-4152-8f49-a0745faf15dc)

In Lab1 we designed 8 normalised tables and defined their cardinalities. However, for NoSQL database we will be designing denormalised tables/Collections.
Primarily, we have deisgned two Collections for Lab2 analysis: 

![NoSQL Schema _Conceptual Design](https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/42118282/9c2ca41c-20d2-4275-880a-5d76b44bb504)
  
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

- Analysis_1:
  Linkedin Job Posting shows multiple job post from various companies across United States. So a Job Seeker on Linkedin platform wants to check which US State has highest job opportunities.
  
```bash
db.CompanyJobDetail.aggregate([{
  $unwind: "$company"
}, {
  $group: {
    _id: "$company.state",
    jobcount: {
      $count: {}
    }
  }
}]).sort({
  jobcount: -1
})
```


Query results :

<img width="915" alt="Screenshot 2023-11-17 at 12 19 40 PM" src="https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/42118282/f68c206e-4c4c-4226-acf8-13f8596facd8">

Linkedin Data set is showing California state alone has highest job requirement in USA, and this requirement is greater than the combined requirement from Texas and New York, which are from East coast.

- Analysis_2:
  Since California has highest job requirement across US, Job Seeker on Linkedin platform wants to check which Job title has highest job requirement in California.

``` bash
db.CompanyJobDetail.aggregate([{
  $match: {
    "job_location": {
      $regex: 'CA'
    }
  }
}, {
  "$group": {
    _id: "$job_title",
    "jobcount": {
      $count: {}
    }
  }
}, {
  "$project": {
    "_id": 1,
    "jobcount": 1
  }
}]).sort({
  jobcount: -1
})
```

Query results:

<img width="976" alt="Screenshot 2023-11-17 at 12 21 00 PM" src="https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/42118282/8f301f11-737a-4515-8d31-66d75ad45d04">

Query result shows Sales Director(owner/operator) title has highest job requirement in California state followed by Executive Assistant title.

- Analysis_3:
 Linked user wants to check number of job posted from companies having highest followers. AlsoA are the companies having high volume of followers generating enough jobs?

``` bash
{
  db.JobPostFromCompany.aggregate([{
    $unwind: "$company_details"
  }, {
    "$group": {
      _id: "$company_details.company_name",
      maxfollower: {
        $max: "$company_details.follower_count"
      }
    }
  }, {
    $sort: {
      "maxfollower": -1
    }
  }, {
    $limit: 5
  }])
}
```

Query results:

<img width="973" alt="Screenshot 2023-11-17 at 12 24 44 PM" src="https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/42118282/b6e799a5-5a42-4223-90c9-81c7c6fa4b5b">

Amazon has highest number of followers followed by Google and Unilever. We will explore now how many jobs were created by these top following companies.

``` bash
db.JobPostFromCompany.aggregate([{
    $unwind: "$company_details"
  }, {
    $match: {
      "company_details.company_name": {
        $in: ["Amazon", "Google", "Unilever", "Apple"]
      }
    }
  }, {
    "$group": {
      _id: "$company_details.company_name",
      "jobcount": {
        $count: {}
      }
    }
  }])
```

Query results:

<img width="982" alt="Screenshot 2023-11-17 at 12 26 41 PM" src="https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/42118282/cd6d51c9-8c38-4a82-91d6-c60ec9b082ed">

So, Amazon and Google has created 93 jobs whereas Apple has created 15 and Unilever created just 2 jobs. 

#-------------------------------------------------------------------------------------------------------------------------#
### END of Analysis
#-------------------------------------------------------------------------------------------------------------------------#
