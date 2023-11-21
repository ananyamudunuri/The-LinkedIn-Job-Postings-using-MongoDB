# DATA-225-Lab2-Group2
## MongoDB Queries Explanation

---

### 1. Calculate the average salary for each experience level

**Overview:**
This query utilizes the `LinkedinJobAnalysisData` collection to determine the average salary associated with different experience levels in job postings. The calculation involves aggregating data based on the `formatted_experience_level` field. The average salary is derived from the mean of the minimum and maximum incomes for each experience level.

**Query:**
```
db.LinkedinJobAnalysisData.aggregate([
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

**Purpose:**
This approach offers insights into how salary expectations vary across different experience levels within the job market, aiding in understanding compensation trends across various professional tiers.

---

### 2. Finding the most common job title

**Overview:**
The provided query is designed to identify the most frequently occurring job title within the `LinkedinJobAnalysisData` collection. It conducts a count of occurrences for each distinct job title, grouping the data by 'job_title'. The output is then sorted in descending order of counts, revealing the most common job title along with the total number of postings for that title.

**Query:**
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

**Purpose:**
This query aims to identify the job title that appears most frequently in the dataset, providing an understanding of the prevailing or popular job roles within the analyzed job postings.

---

### 3. Identify Job Titles with the Most Significant Salary Differences

**Overview:**
This query examines the `LinkedinJobAnalysisData` collection to identify job titles that exhibit the largest disparities between their maximum and minimum salary offers. It computes the wage differential for each job title, sorting the results in descending order based on the average salary difference. The output is limited to the top 10 job titles with the most substantial salary variability.

**Query:**
```
db.LinkedinJobAnalysisData.aggregate([
  {
    $group: {
      _id: "$job_title",
      avg_salary_difference: { $avg: { $subtract: ["$max_salary", "$min_salary"] } }
    }
  },
  {
    $sort: { avg_salary_difference: -1 }
  },
  {
    $limit: 10
  },
  {
    $project: {
      _id: 0,
      job_title: "$_id",
      avg_salary_difference: 1
    }
  }
]);
```

**Purpose:**
This query assists in pinpointing job titles with the widest salary ranges available in the job market, offering insights into roles that exhibit significant variations in offered compensations.

---


### JMeter SQL Performance Measurement
This project focuses on utilizing JMeter for measuring the performance of SQL queries. It aims to evaluate the efficiency and scalability of SQL databases under various workloads and stress conditions. The study provides insights into query execution and database responsiveness, enabling optimization and performance enhancements.

Features:

Load testing SQL queries under diverse workloads

Assessing database performance under stress conditions

Identifying and resolving SQL query performance bottlenecks

Enhancing SQL query execution efficiency through optimization techniques

Please refer to the respective SQL and ipynb files for detailed information

![WhatsApp Image 2023-11-20 at 6 21 19 PM](https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/144860707/8b305d19-059f-4839-bdde-da62dad293df)
