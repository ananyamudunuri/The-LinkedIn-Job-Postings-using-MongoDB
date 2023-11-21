# DATA-225-Lab2-Group2
DATA-225 Sec 24 - Db Systems for Analytics Lab2 
# MongoDB Project

This project uses MongoDB to build a NoSQL database architecture for linked job data and also performs analysis on it. The main objectives of this analysis queries is to identify job titles with the least competition, understand which job titles have the highest total salaries, and find out which job titles are least posted across all companies.

## Queries

1. **Identify Companies and Job Titles with Least Views and Applies**

   This query is useful for HR to understand how a company should not be and for the candidates to choose jobs with the least competition. It uses the `Jobs_T` and `Company_T` tables.

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
2.  **Find Unique Job Titles with Highest Total Salaries**

This query helps to understand which job titles are associated with the highest total salaries across all companies and helps the HR to know which type of labour is more expensive. It uses the `Jobs_T` table.

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


3. **Identify Least Posted Job Titles**

This query helps to identify job titles that are least posted across all the companies. These roles could be outsourced. It uses the `Jobs_T` table.

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
