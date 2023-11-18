# DATA-225-Lab2-Group2
DATA-225 Sec 24 - Db Systems for Analytics Lab2 
DATA-225 Sec 24 - Db Systems for Analytics Lab2 MongoDB Queries Explanation


**1. Average Salary for Each Combination of Experience Level and Work Type**

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


**2. Most Common Specialty and Associated Companies**

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


**3. Top 10 Companies with the Most Job Listings**

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



**4. Ratio of Employee Count to Applications for Each Company**

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


**5. Job Postings with High Application-to-View Ratio**

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



**6. Lowest-Paying Job Posting for Each Company**

```
db.LinkedinJobAnalysisData.aggregate([
  {
    $match: {
      $and: [
        { "max_salary": { $ne: null } },
        { "min_salary": { $ne: null } }
      ]
    }
  },
  {
    $group: {
      _id: "$company_details.company_name",
      min_salary: { $min: { $min: ["$max_salary", "$min_salary"] } },
      job_postings: {
        $push: {
          job_title: "$job_title",
          max_salary: "$max_salary",
          min_salary: "$min_salary",
          job_posting_url: "$job_posting_url"
        }
      }
    }
  },
  {
    $project: {
      _id: 0,
      company_name: "$_id",
      lowest_salary: "$min_salary",
      job_postings: 1
    }
  },
  {
    $sort: { lowest_salary: 1 }
  }
]);
```

Explanation:

This query filters job postings where both max_salary and min_salary are not null.
It groups job postings by company_details.company_name.
For each company, it finds the lowest salary and creates an array of job postings with details.
Results are then sorted in ascending order based on the lowest salary.





