# Introduction
Dive into the data job market! Focusing on data analyst roles, this project explores top-paying jobs, in-demand skills, and data where high demand meets high salary in data analytics.

SQL queries? Check them out here: [project_sql folder](/project_sql/)

# Background
Driven by a quest to navigate the data analyst job market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.

Data hails from Luke Barousse's [SQL Course](https://www.lukebarousse.com/sql). It's packed with insights on job titles, salaries, locations and essential skills.

### The queries I wanted to answer through my SQL queries were:

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in-demand for data analysts?
. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

# Tools I Used
For my deep dive into the data analyst job market, I harnesssed the power of several key tools:

- **SQL:** The backbone of my analysis, allowing me to query the database and unearth critical insights.
- **PostgreSQL:** The chosen database management system, ideal for handling the job posting data.
- **Visual Studio Code:** My go-to for database management and executing SQL queries.
- **Git & GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaborations and project tracking.

# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here's how I approached each question:

### 1. Top Paying Data Analyst Jobs
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id 
WHERE
    job_title_short = 'Data Analyst' AND
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY 
    salary_year_avg DESC
LIMIT 10;
```
Here's the breakdown of the top data analyst jobs in 2023:
- **Wide Salary Range:** Top 10 paying data analyst roles span from $184,000 to $650,000, indicating significant salary potential in the field.
- **Diverse Employers:** Companies like SmartAsset, Meta and AT&T are among those offering high salaries, showing a broad interest across different industries.
- **Job Title Variety:** There's a high diversity in job titles, from Data Analyst to Director of Analytics, reflecting varied roles and specializations within data analytics.

![Top Paying Roles](/assets/1_top_paying_roles.png) 
*Bar graph visualizing the salary for the top 10 salaries for data analysts; ChatGPT generated this graph from my SQL query results*

### 2. Top Paying Job Skills for Data Analysts
To identify the highest-paying skills, I joined job postings with their associated skills and filtered them by the highest average yearly salaries. This allowed me to pinpoint which technical skills are most commonly found in the highest-paying roles across companies.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id 
    WHERE
        job_title_short = 'Data Analyst' AND
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY 
        salary_year_avg DESC
    LIMIT 10
)
SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```
Here's the breakdown of the top paying job skills for Data Analysts in 2023:

- **SQL, Python, and Tableau** are the most in-demand and frequently listed skills in top-paying data analyst roles, indicating their critical importance.

- **High-paying jobs favor multi-skilled analysts** proficient in modern tools like Pandas, PySpark, Snowflake, and cloud platforms such as AWS and Azure.

- **Leadership roles demand enterprise tools** (e.g., Jira, Confluence, PowerPoint), highlighting the value of project management and cross-team collaboration skills.

### 3. Top Demanded Skills for Data Analysts
To identify the most demanded skills for data analysts, I joined 3 tables to link job postings with their required skills. By filtering for Data Analyst roles that offer remote work, I can count the occurrences of each skill and sort them in descending order to highlight the most sought-after skills.

```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Analyst' AND
    job_work_from_home = true
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```

Here's the breadown of the most demanded skills for Data Analysts in 2023:

- **SQL:** Essential for querying and managing relational databases, SQL remains the most sought-after skill in data analytics.

- **Excel:** Despite the rise of advanced tools, Excel continues to be a fundamental tool for data manipulation and analysis.

- **Python:** With its versatility and powerful libraries, Python is increasingly preferred for data analysis and automation tasks.

- **Tableau:** A leading data visualization tool, Tableau helps in creating interactive and insightful dashboards.

- **Power BI:** Microsoft's Power BI is widely used for business intelligence and data visualization, integrating seamlessly with other Microsoft products.

| Skill     | Demand Count |
|-----------|--------------|
| SQL       | 7,291        |
| Excel     | 4,611        |
| Python    | 4,330        |
| Tableau   | 3,745        |
| Power BI  | 2,609        |

*Table visualizing the 5 most demanded skills for Data Analysts. ChatGPT created this table from my SQL query results*

### 4. Highest Paying Skills for Data Analysts

To identify the top 25 highest-paying skills, I joined 3 tables. I also filtered for roles with specified average annual salaries and remote work options, then calculated the average salary for each skill. Finally, I sorted the results in descending order and limited the output to the top 25 skills, providing insights into the most lucrative skills in the field.

```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Analyst' 
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 25;
```
Here's the breakdown of the highest paying skills for Data Analysts in 2023:

- **Advanced Tools and Platforms:** Skills like PySpark, Bitbucket, and Couchbase command the highest average salaries, indicating a premium on expertise in big data processing, version control, and NoSQL databases.

- **Cloud and DevOps Expertise:** Proficiency in Kubernetes, Linux, and GCP is highly valued, reflecting the industry's shift towards cloud-native architectures and containerization.

- **Programming Proficiency:** Languages such as Python, Scala, and Golang are associated with substantial earnings, highlighting the demand for strong programming skills in data analytics.

- **Data Science and Machine Learning:** Tools like Databricks, Scikit-learn, and Jupyter are prevalent among high-paying roles, underscoring the importance of machine learning and data science capabilities.

- **Business Intelligence Tools:** Familiarity with platforms like Tableau, Power BI, and MicroStrategy remains crucial, as they facilitate data visualization and business intelligence functions.

| Skill        | Average Salary (USD) |
|--------------|----------------------|
| PySpark      | 208,172              |
| Bitbucket    | 189,155              |
| Couchbase    | 160,515              |
| Watson       | 160,515              |
| DataRobot    | 155,486              |
| GitLab       | 154,500              |
| Swift        | 153,750              |
| Jupyter      | 152,777              |
| Pandas       | 151,821              |
| Elasticsearch| 145,000              |
| Golang       | 145,000              |
| NumPy        | 143,513              |
| Databricks   | 141,907              |
| Linux        | 136,508              |
| Kubernetes   | 132,500              |
| Atlassian    | 131,162              |
| Twilio       | 127,000              |
| Airflow      | 126,103              |
| Scikit-learn | 125,781              |
| Jenkins      | 125,436              |
| Notion       | 125,000              |
| Scala        | 124,903              |
| PostgreSQL   | 123,879              |
| GCP          | 122,500              |
| MicroStrategy| 121,619              |

*Table visualizing the 25 highest paying skills for Data Analysts. ChatGPT created this table from my SQL query results*

### 5. Most Optimal Skills for Data Analysts

To identify the top 25 most optimal skills for remote Data Analyst roles, I joined 3 tables to link job postings with their associated skills. I filtered for Data Analyst positions that offer remote work and have specified average annual salaries. By grouping the data by skill and calculating the average salary for each skill, I identified which skills are both in high demand and command higher salaries.

```sql
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Analyst' 
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id)>10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```
Here's the breakdown of the most optimal skills for Data Analysts in 2023:

- **Go, Confluence, and Hadoop** are among the highest-paying skills for data analysts, with salaries above $110K.

- **Python and Tableau** have the highest demand, with over 200 job postings each, but slightly lower average salaries (~$100K).

- Cloud and big data skills like **Azure, Snowflake, AWS, BigQuery, and Redshift** also show strong demand with solid salaries around $100K+.

- Traditional BI tools like **Looker, Qlik, SSRS, and SAS** remain important, with consistent demand and average salaries near $99K-$103K.

- Programming and database skills such as **Java, SQL Server, C++, and JavaScript** maintain moderate demand with competitive pay.

| Skill ID | Skill       | Demand Count | Average Salary (USD) |
|----------|-------------|--------------|---------------------|
| 8        | go          | 27           | 115320              |
| 234      | confluence  | 11           | 114210              |
| 97       | hadoop      | 22           | 113193              |
| 80       | snowflake   | 37           | 112948              |
| 74       | azure       | 34           | 111225              |
| 77       | bigquery    | 13           | 109654              |
| 76       | aws         | 32           | 108317              |
| 4        | java        | 17           | 106906              |
| 194      | ssis        | 12           | 106683              |
| 233      | jira        | 20           | 104918              |
| 79       | oracle      | 37           | 104534              |
| 185      | looker      | 49           | 103795              |
| 2        | nosql       | 13           | 101414              |
| 1        | python      | 236          | 101397              |
| 5        | r           | 148          | 100499              |
| 78       | redshift    | 16           | 99936               |
| 187      | qlik        | 13           | 99631               |
| 182      | tableau     | 230          | 99288               |
| 197      | ssrs        | 14           | 99171               |
| 92       | spark       | 13           | 99077               |
| 13       | c++         | 11           | 98958               |
| 186      | sas         | 63           | 98902               |
| 7        | sas         | 63           | 98902               |
| 61       | sql server  | 35           | 97786               |
| 9        | javascript  | 20           | 97587               |

*Table visualizing the 25 most optimal skills for Data Analysts. ChatGPT created this table from my SQL query results*

# What I learned
Throughout this adventure, I've turbocharged my SQL toolkit with some serious firepower:

- **Complex Qurey Crafting:** Developed proficiency in crafting complex SQL queries by effectively joining multiple tables and utilizing Common Table Expressions (CTEs) through the WITH clause to enhance query readability and maintainability.
- **Data Aggregation Techniques:** Mastered the use of GROUP BY in conjunction with aggregate functions such as COUNT() and AVG() to summarize and analyze data efficiently.
- **Analytical Problem-Solving:** Enhanced my ability to translate real-world analytical challenges into structured SQL queries, effectively solving data-related puzzles and deriving actionable insights.

# Conclusions
### Insights

- Top-paying data analyst jobs range widely ($184K to $650K) and come with diverse roles from Data Analyst to Director of Analytics at companies like Meta and AT&T.

- These top-paying roles demand skills like SQL, Python, Tableau, plus advanced tools such as Pandas, PySpark, Snowflake, and cloud platforms like AWS and Azure; leadership tools like Jira and Confluence are valued too.

- The most in-demand skills for data analysts are SQL, Excel, Python, Tableau, and Power BI, combining foundational and modern data analysis tools.

- Higher salaries are linked to expertise in advanced tools (PySpark, Bitbucket), cloud/DevOps (Kubernetes, Linux, GCP), programming languages (Python, Scala, Golang), and data science/machine learning tools (Databricks, Scikit-learn).

- The most optimal skills to learn balance demand and pay, including Go, Confluence, Hadoop, Python, Tableau, cloud/big data platforms (Azure, Snowflake, AWS), BI tools (Looker, Qlik, SSRS, SAS), and programming/database skills (Java, SQL Server, C++, JavaScript).

### Closing Thoughts
This project helped develop and further enhance my SQL skills, while also providing valuable insights into the job market. The findings from the analysis serve as a guide to prioritizing skill development and job search efforts. This exploration highlights the importance of contionous learning and adaptation to emerging trends in the field of data analytics.