## Data-225 Sec 24 Db Systems for Analytics lab2


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


### Input Data set 


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


##  Application Building Steps

### Data Collection and clean up


In the previous Lab1 project we habe already preprocessed all these datasets using Jupyter notebook and for this Lab2 we will be using those csv files again.
Reference: 

      -   ReadMe_Job_postings.ipynb has elaborated details of preprocessing of Job Postings file.
      -   README_JOBPOSTINGS.ipynb


### Functional Analysis and USE case diagram design


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



### Conceptual schema design


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


### Conversion of the data into JSON and schema building for NoSQL database


To create Collections in MongoDB database, we converted our csv data into JSON format through pandas Dataframe and developed the schema structure using Jupyter notebook.
'SchemaDesign_And_JSON_Conversion.ipynb' showcases all the conversion steps in detail.



### Data Upload in to MongoDB Compass, Database and Collection creation


We have created Clusters and Database in MongoDB. And uploaded JSON data into two collections:
- CompanyJobDetail : This collection has 15520 documents.
- JobPostFromCompany : This collection has 15470 documents.


## Perform queries on MongoDB


#### 1. Average Salary for Each Combination of Experience Level and Work Type

```
db.LinkedinJobAnalysisData.aggregate([
  {
    $group: {
      _id: {
        experience_level: "$formatted_experience_level",
        work_type: "$work_type"
      },
      avg_salary: { $avg: { $add: ["$max_salary", "$min_salary"] } }
    }
  }
]);
```

Explanation:

This query groups job postings by the combination of formatted_experience_level and work_type.
It calculates the average salary (avg_salary) for each group by summing up max_salary and min_salary and then taking the average.

This query can be used to find the average salary for each combination of experience level and work type. From this query we can say that experience level Director seems to be having the highest average salary with work type as other, followed by executive experience level.


#### 2. Most Common Specialty and Associated Companies

```
db.LinkedinJobAnalysisData.aggregate([
  {
    $unwind: "$company.speciality"
  },
  {
    $group: {
      _id: "$company.speciality",
      companyCount: { $sum: 1 },
      companies: { $addToSet: "$company.company_name" }
    }
  },
  {
    $sort: { companyCount: -1 }
  },
  {
    $limit: 1
  },
  {
    $project: {
      _id: 0,
      mostCommonSpeciality: "$_id",
      companyCount: 1,
      companies: 1
    }
  }
]);
```

Explanation:

This query identifies the most common specialty by unwinding the company.speciality array.
It counts the occurrences of each specialty and lists associated companies.
Results are sorted by company count in descending order, and only the most common specialty is selected.

The above query can be used to find which specialty is most in demand and what all companies have this speciality.From the output , community is the most common speciality with companies like Lyft ,The Moms project, etc. having this specialty


#### 3. Top 10 Companies with the Most Job Listings

```
db.LinkedinJobAnalysisData.aggregate([
  {
    $group: {
      _id: "$company_details.company_name",
      jobCount: { $sum: 1 }
    }
  },
  {
    $sort: { jobCount: -1 }
  },
  {
    $limit: 10
  },
  {
    $project: {
      _id: 0,
      company_name: "$_id",
      jobCount: 1
    }
  }
]);
```

Explanation:

This query groups job postings by company_details.company_name.
It calculates the total job count for each company and sorts the results in descending order.
The top 10 companies with the most job listings are then selected.

The company that has the greatest number of postings are from the company ‘null’. From this we can tell that there are lot of job postings where this company name is not mentioned. From the second company in the output is City Lifestyle which a total postings of 161 offers.



#### 4. Ratio of Employee Count to Applications for Each Company

```
db.LinkedinJobAnalysisData.aggregate([
  {
    $match: {
      $and: [
        { "company_details.employee_count": { $ne: null } },
        { "applies": { $ne: null } }
      ]
    }
  },
  {
    $group: {
      _id: "$company_details.company_name",
      total_employee_count: { $first: "$company_details.employee_count" },
      total_applications: { $sum: "$applies" }
    }
  },
  {
    $project: {
      _id: 0,
      company_name: "$_id",
      employee_to_application_ratio: { $divide: ["$total_employee_count", "$total_applications"] }
    }
  },
  {
    $sort: { employee_to_application_ratio: -1 }
  }
]);
```

Explanation:

This query filters job postings where both company_details.employee_count and applies are not null.
It calculates the total employee count and total applications for each company.
The ratio of employee count to applications is then computed, and results are sorted in descending order.

Here the company PWC has the highest ration with 273293, followed by Ericsson, McDonald and Starbucks. However, it is important to keep in mind that if there are lot of job postings this subsequently leads to a lot of job applications. 


#### 5. Job Postings with High Application-to-View Ratio

```
db.LinkedinJobAnalysisData.aggregate([
  {
    $match: {
      "views": { $gt: 0 },
      "applies": { $gt: 0 },
      $expr: { $gt: ["$views", "$applies"] }
    }
  },
  {
    $project: {
      _id: 0,
      job_posting_url: 1,
      company_name: "$company_details.company_name",
      views: 1,
      applies: 1,
      application_to_view_ratio: { $divide: ["$applies", "$views"] }
    }
  },
  {
    $sort: { application_to_view_ratio: -1 }
  }
]);
```

Explanation:

This query filters job postings where the number of views is greater than 0.
It projects a new document that includes the job_posting_url , company name , views , applies and calculates the application_to_view_ratio.
Results are sorted in descending order based on the application_to_view_ratio.

Similarly, like the above query this find outs the ration of Application to view of the job postings. Here we can understand the relationship of the views and the applies for that particular job posting.

#### 6. Average Salary by Country and Work Type
```bash
db.LinkedinJobAnalysis.aggregate([
    {
        $group: {
            _id: { country: "$company_details.country", work_type: "$formatted_work_type" },
            avg_salary: { $avg: "$max_salary" }
        }
    },
    {
        $project: {
            country: "$_id.country",
            work_type: "$_id.work_type",
            avg_salary: 1,
            _id: 0
        }
    }
]);
```
 Explanation : 
 
This query performs an aggregation operation on the `LinkedinJobAnalysis` collection. It groups the data by `country` and `work_type`, and calculates the average `max_salary` for each group. The result is a list of countries, work types, and their corresponding average salaries. 

#### 7. Companies with Highest Job Views
```bash 
db.LinkedinJobAnalysis.aggregate([
    {
        $group: {
            _id: "$company_details.company_name",
            avg_views: { $avg: "$views" }
        }
    },
    {
        $sort: { avg_views: -1 }
    },
    {
        $limit: 5
    }
]);
```
 Explanation : 
 
The query groups the data by `company_name` and calculates the average number of `views` for each company. The results are then sorted in descending order of `avg_views`. Finally, it limits the output to the top 5 companies with the highest average views.



#### 8. Job Titles and Locations with Highest Median Salary

```bash 
db.LinkedinJobAnalysis.aggregate([
    {
        $sort: { "med_salary": -1 }
    },
    {
        $project: {
            job_title: 1,
            location: 1,
            med_salary: 1
        }
    },
    {
        $limit: 10
    }
]);
```
 Explanation : 
 
The query sorts the data in descending order based on the `med_salary` field. The query then projects or selects the `job_title`, `location`, and `med_salary` fields from the sorted data. Finally, it limits the output to the top 10 records with the highest median salaries.

#### 9. Job Titles with High Views-to-Applications Ratio

```bash 
db.LinkedinJobAnalysis.aggregate([
    {
        $match: {
            applies: { $gt: 0 }
        }
    },
    {
        $project: {
            job_title: 1,
            views_to_applications_ratio: { $divide: ["$views", "$applies"] }
        }
    },
    {
        $sort: { views_to_applications_ratio: -1 }
    },
    {
        $limit: 10
    }
]);

```
 Explanation : 
 
This query first filters the data to include only those records where `applies` is greater than 0. Then, it calculates the ratio of `views` to `applies` for each job and includes the `job_title` in the output. The results are sorted in descending order based on the `views_to_applications_ratio`. Finally, it limits the output to the top 10 records with the highest views-to-applications ratio.

#### 10. Job Titles with Highest Increase in Applications

```bash
db.LinkedinJobAnalysis.aggregate([
    {
        $sort: { "applies": -1 }
    },
    {
        $group: {
            _id: "$job_title",
            previous_applies: { $first: "$applies" },
            current_applies: { $first: "$applies" }
        }
    },
    {
        $project: {
            job_title: "$_id",
            percentage_increase: { $multiply: [{ $divide: [{ $subtract: ["$current_applies", "$previous_applies"] }, "$previous_applies"] }, 100] }
        }
    },
    {
        $sort: { percentage_increase: -1 }
    },
    {
        $limit: 10
    }
]);

```
 Explanation : 

The query sorts the documents based on the number of job applications in descending order. Then, it groups the documents by job title and retains the first document's number of applies as both the previous and current applies for each group. After that, it calculates the percentage increase in job applications for each job title. The documents are then sorted in descending order based on this calculated percentage increase. Finally, the output is limited to the top 10 documents with the highest percentage increase in job applications. 

####  11.  Identify Companies and Job Titles with Least Views and Applies**

   ```javascript
   db.Jobs_T.aggregate([
       {
           $lookup:
           {
               from: "Company_T",
               localField: "company.company_name",
               foreignField: "company_details.company_name",
               as: "company_info"
           }
       },
       {
           $unwind: "$company_info"
       },
       {
           $project: 
           {
               _id: 0,
               company_name: "$company.company_name",
               job_title: 1,
               views: "$company_info.views",
               applies: "$company_info.applies"
           }
       },
       {
           $sort: 
           {
               views: 1,
               applies: 1
           }
       }
   ]).pretty()
```

Explanation:

This query is useful for HR to understand how a company should not be and for the candidates to choose jobs with the least competition. It uses the `Jobs_T` and `Company_T` tables.
####  12.  Find Unique Job Titles with Highest Total Salaries

```javascript 
db.Jobs_T.aggregate([
    {
        $group:
        {
            _id: "$job_title",
            total_max_salary: { $sum: "$max_salary" }
        }
    },
    {
        $sort:
        {
            total_max_salary: -1
        }
    }
]).pretty()

```
Explanation:

This query helps to understand which job titles are associated with the highest total salaries across all companies and helps the HR to know which type of labour is more expensive. It uses the `Jobs_T` table.


####  13. Identify Least Posted Job Titles

```javascript
db.Jobs_T.aggregate([
    {
        $group:
        {
            _id: "$job_title",
            count: { $sum: 1 }
        }
    },
    {
        $sort:
        {
            count: 1
        }
    }
]).pretty()
```
Explanation: 

This query helps to identify job titles that are least posted across all the companies. These roles could be outsourced. It uses the `Jobs_T` table.

#### 14. Average salary for each experience 
```bash 
level.db.LinkedinJobAnalysisData.aggregate([
  {
    $group: {
      _id: "$formatted_experience_level",
      avg_salary: { $avg: { $add: ["$max_salary", "$min_salary"] } }
    }
  },
  {
    $project: {
      _id: 0,
      experience_level: "$_id",
      avg_salary: 1
    }
  }
]);
```
Explanation:

This query calculates the average salary for each experience

#### 15.Common Job Titles

```
db.LinkedinJobAnalysisData.aggregate([
  {
    $group: {
      _id: "$job_title",
      count: { $sum: 1 }
    }
  },
  {
    $sort: { count: -1 }
  },
  {
    $limit: 1
  },
  {
    $project: {
      _id: 0,
      most_common_job: "$_id",
      postings_count: "$count"
    }
  }
]);
```
Explanation:

This query finds the most common job title 

#### 16. Query to Identify Job Titles with the Most Significant Salary Differences:

```
db.LinkedinJobAnalysisData.aggregate([
  {
    $project: {
      job_title: 1,
      salary_difference: { $subtract: ["$max_salary", "$min_salary"] }
    }
  },
  {
    $sort: { salary_difference: -1 }
  },
  {
    $limit: 10
  },
  {
    $project: {
      _id: 0,
      job_title: 1,
      salary_difference: 1
    }
  }
]);

```

Explanation: 

This query finds job titles with the greatest difference between their maximum and minimum salary offerings. This can highlight roles with highly variable pay scales.


#### Aryama Queries 

#### Connecting to cloud 

##### Visualization of MongoDB data

#### NoSQL performance Measurement and compare with MySQL

