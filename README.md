# DATA-225-Lab2-Group2
DATA-225 Sec 24 - Db Systems for Analytics Lab2 
1:Find the average salary for each combination of formatted_experience_level and work_type

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




2:Most common specility and which companies

db.LinkedinJobAnalysisData.aggregate([
{
    $unwind: "$company.speciality" // If speciality is an array, use $unwind to deconstruct it
  },
  {
    $group: {
      _id: "$company.speciality",
      companyCount: { $sum: 1 },
      companies: { $addToSet: "$company.company_name" }
    }
  },
  {
    $sort: { companyCount: -1 } // Sort by company count in descending order
  },
  {
    $limit: 1 // Limit the result to the top specialty
  },
  {
    $project: {
      _id: 0, // Exclude the default _id field
      mostCommonSpeciality: "$_id",
      companyCount: 1,
      companies: 1
    }
  }
]);




3:10 Companies with the Most Job Listings:


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
 _id: 0, // Exclude the default _id field
 company_name: "$_id",
 jobCount: 1
 }
 }
]);




4:Ratio of the employee count to the applications 

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




5:Job Postings with High Application-to-View Ratio

db.LinkedinJobAnalysisData..aggregate([
  {
    $match: {
      "views": { $gt: 0 } 
    }
  },
  {
    $project: {
      _id: 0,
      job_posting_url: 1,
      application_to_view_ratio: { $divide: ["$applies", "$views"] }
    }
  },
  {
    $sort: { application_to_view_ratio: -1 }
  }
]);



6:Find the Lowest-paying job posting for each company
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
