# Data-225-lab2-group-project
# LinkedIn Job Analysis Queries README

## Summary
This repository utilizes MongoDB aggregation queries for the analysis of LinkedIn job data. It covers a wide range of scenarios, including the calculation of average salaries, identification of companies with high job views, and listing of job titles with the highest median salaries. Additionally, it features a comprehensive dashboard designed for project managers to track and analyze their project’s progress and performance. The dashboard provides a visual representation of various key metrics related to job applications, job vacancies, and salary ranges.

## Table of Contents
1. [Average Salary by Country and Work Type](#average-salary-by-country-and-work-type)
2. [Companies with Highest Job Views](#companies-with-highest-job-views)
3. [Job Titles and Locations with Highest Median Salary](#job-titles-and-locations-with-highest-median-salary)
4. [Job Titles with High Views-to-Applications Ratio](#job-titles-with-high-views-to-applications-ratio)
5. [Job Titles with Highest Increase in Applications](#job-titles-with-highest-increase-in-applications)
5. [Visualization of MongoDB data](#Visualization-of-MongoDB-data)

---

## Average Salary by Country and Work Type
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
This query performs an aggregation operation on the `LinkedinJobAnalysis` collection. It groups the data by `country` and `work_type`, and calculates the average `max_salary` for each group. The result is a list of countries, work types, and their corresponding average salaries. 

## Companies with Highest Job Views
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
The query groups the data by `company_name` and calculates the average number of `views` for each company. The results are then sorted in descending order of `avg_views`. Finally, it limits the output to the top 5 companies with the highest average views.


## Job Titles and Locations with Highest Median Salary

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
The query sorts the data in descending order based on the `med_salary` field. The query then projects or selects the `job_title`, `location`, and `med_salary` fields from the sorted data. Finally, it limits the output to the top 10 records with the highest median salaries.

## Job Titles with High Views-to-Applications Ratio

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
This query first filters the data to include only those records where `applies` is greater than 0. Then, it calculates the ratio of `views` to `applies` for each job and includes the `job_title` in the output. The results are sorted in descending order based on the `views_to_applications_ratio`. Finally, it limits the output to the top 10 records with the highest views-to-applications ratio.

## Job Titles with Highest Increase in Applications

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

The query sorts the documents based on the number of job applications in descending order. Then, it groups the documents by job title and retains the first document's number of applies as both the previous and current applies for each group. After that, it calculates the percentage increase in job applications for each job title. The documents are then sorted in descending order based on this calculated percentage increase. Finally, the output is limited to the top 10 documents with the highest percentage increase in job applications. 


# Visualization of MongoDB data 
## Project Manager Dashboard

This dashboard is a comprehensive tool designed for project managers to track and analyze their project's progress and performance. It provides a visual representation of various key metrics related to job applications, job vacancies, and salary ranges.

## Features

1. **Application Type based on Work Category**: This bar graph provides insights into the number of applications received for different work categories.

2. **Map view of countries with max vacancies**: This map gives a geographical representation of job vacancies, highlighting the countries with the most job openings.

3. **Salary Range based on Experience Level**: This line graph shows the salary range for different positions, providing a clear view of compensation based on experience levels.

4. **Project Manager Position based on Salary Range**: This bar graph presents the salary range for different positions within the project management domain.

## Usage

To use this dashboard, simply navigate through the various charts and graphs. Hover over specific data points for more detailed information. This tool is designed to aid project managers in making informed decisions based on real-time data.

##  Link to the Dashboard 

https://charts.mongodb.com/charts-project-0-hocab/public/dashboards/6ac354c5-8fb2-4b65-afa3-78a64c99edad


<img width="638" alt="Screenshot 2023-11-20 at 7 11 00 PM" src="https://github.com/ananyamudunuri/DATA-225-Lab2-Group2/assets/92011107/676e38c9-c651-4aa6-a241-d1878d92029b">

## Conclusion

In essence, this repository serves as a robust analytical tool for both job market analysis and project management. By leveraging MongoDB and data visualization, it provides valuable insights into job data and aids in effective project tracking and performance analysis. Whether you’re a data analyst looking to understand job market trends or a project manager aiming to monitor project progress, this repository can be used as a foundation for furture analysis and exploration of the dataset.

