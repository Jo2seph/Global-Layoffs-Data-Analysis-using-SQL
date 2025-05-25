# Global-Layoffs-Data-Analysis-using-SQL

## Table of Contents
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Tools](#tools)
- [Key Questions asked](#key-questions-asked)
- [Data Cleaning](#data-cleaning)
- [Exploratory data analysis](#exploratory-data-analysis)
- [Conclusion](#conclusion)
- [References](#conclusion)
- 
### Project Overview
The primary goal of this project is to analyze global layoff data to identify trends, patterns, and insights. This analysis will help understand the factors influencing layoffs, the industries most affected and geographical trends.
### Data Source
The data used in this project exists in the ‘world layoffs’ database containing detailed information about the number of layoffs and the percentage layoffs made by each company, the dataset records layoff data spanning from 2020-03-11 to 2023-03-06.. The database consists of one table,’layoffs’, which consists of 4,722 records of global company employee layoffs. Each record is a specific period that the company had laid off a number of their employees. The ‘layoffs’ table is made up of nine columns.
The full data fields descriptions are below:

- company (text): Name of the company that is undergoing layoffs.

- location (text): The city or specific location where the layoffs are taking place.

- industry (text): The sector or industry to which the company belongs.

- total_laid_off (int): The total number of employees being laid off by the company.

- percentage_laid_off (float): The percentage of the company’s workforce that was laid off

- date (text): The date when the layoffs occurred.

- stage (text): The stage of the company in terms of its funding and development.

- country (text): The country the layoffs are taking place in.

- funds_raised_millions (int): The total amount of funds the company as raised in millions of dollars.

### Tools 
MYSQL Server – Data cleaning and analysis [Download here (https://www.google.com/url?sa=t&source=w)]

### Key Questions asked

- How many total layoffs occurred in each year ?

- Are layoffs increasing or decreasing over time (monthly/quarterly trend) ?

- Which quarters had the highest and lowest layoffs ?

- What Companies had the highest total layoffs ?

- What percentage of total layoffs does each top company contribute ?

- Which industries are most affected by layoffs ?

- Are tech companies more affected than other sectors ?

- which countries/regions had the highest number of layoffs in these 3 years ?

- Checking the locations in the country having the most number of layoffs ?

- Are there countries or regions with very low layoffs ?

- Are start-ups or large companies more affected ?

- Total layoffs with respect to stage of the business ?

- Is there a correlation between company size and layoffs ?
-
### Data Cleaning 

#### Creating a Staging Table
Before performing any data manipulation to my original ‘layoffs’ table, I created a staging table to work with. This table, ‘layoffs_staging’, is a direct copy of my original raw data table but, changes made to this data will not affect the root table


    CREATE TABLE layoff_staging   
    LIKE layoffs;
    SELECT *
    FROM layoff_staging;
    INSERT  layoff_staging
    SELECT *
    FROM layoff_staging ; ‘‘‘

#### Removing Duplicates
In order to find duplicate records without having a unique identifier, I used a window function to assign a row number partitioned by all nine columns of the table. Only where all of the values match exactly, the row number is greater than one. I then use a CTE to query only those duplicate rows, obtaining five duplicate rows. The rows having row_num greater than 1 are deleted

    WITH duplicates_cte AS
    (
    SELECT *,
    ROW_NUMBER () OVER (
    PARTITION BY company,location,
    industry,total_laid_off,percentage_laid_off,'date',stage,
    country,funds_raised_millions) AS row_num
    FROM layoff_staging
    )
    SELECT *
    FROM duplicates_cte 
    WHERE row_num >1;
#### Standardizing the rows
The industry column contained entries such as 'Crypto', 'Cryptocurrency', and 'Crypto Currency', each referring to the same sector which I consolidated into a single term, 'Crypto'. Similarly for the country column I consolidated 'United States' and 'United States.' into 'United States'.
I changed the data type of  'date' into date from 'text'.

    SELECT `date`
    FROM layoffs_staging2;
    ALTER TABLE layoffs_staging2
    MODIFY COLUMN `date` DATE;

#### Removing null and blank values
I found cases where companies had multiple entries, some of which were missing industry information while others were correctly categorized. To rectify these inconsistencies, I used self join to automatically fill all the null values in industry section.

    UPDATE layoffs_staging2 t1 
    INNER JOIN layoffs_staging2 t2 
    ON t1.company=t2.company
    AND t1.location=t2.location
    SET t1.industry=t2.industry
    WHERE (t1.industry is NULL OR t1.industry  = '')
    AND   t2.industry is not NULL;

#### Removing unwanted data and columns
Rows where total_laid_off and perentage_laid_off are null were deleted.
row_num column was also deleted.
      
    DELETE
    FROM layoffs_staging2
    WHERE total_laid_off is NULL
    AND percentage_laid_off is NULL;

### Exploratory data analysis

The dataset records layoff data spanning from 2020-03-11 to 2023-03-06. This period captures significant global economic events, including the impacts of the COVID-19 pandemic, which began in early 2020, and the subsequent economic recovery phases. Analyzing data from this timeframe provides valuable insights into how different industries and regions were affected by these challenges.

#### Layoffs Per Year
2022 faced the greatest impact of layoffs with over 160,000 layoffs from over 900 different companies. In 2023, the largest corporations were affected the most, laying off thousands at a time, with the second-most layoffs in total while just being in the first quarter. 

![Total Layoffs Per Year2](https://github.com/user-attachments/assets/2880a35d-eb46-487d-8a35-3635c4972a97)
g).

Out of all 383,159 layoffs, 75% of the layoffs occurred in 2022 and 2023.

![Total layoffs per month](https://github.com/user-attachments/assets/e22f0c6b-69b4-46f6-9e3e-87e1c8d2f237)

As shown from the line graph, throughout the three years there was four major spikes in layoffs. Beginning in April and May of 2020, more than 25 thousand layoffs occured per month. The last quarter of 2020 saw a steady decline in the rate of layoffs entering into 2021, this reduced rate of layoffs would remain till the first quarter of 2022  with a small spike during February  of 6,813 layoffs. March 2022 would see an increase in layoffs, with 12,885 employees being laid off. This would mark the start of an exponential explosion in the rate of layoffs across the globe. From march 2022 to october 2022, 69,679 employees were laid off, with the layoff of the last two months almost equaling that number, with 53,339 employees being laid off in November and 10,329 being laid off in December. . Begining 2023, January faced the highest peak of layoffs, 84,714 employees, and February followed with another 36,493 layoffs. In all, the number of layoffs reached 383,159 layoffs in total out of the three years in the dataset.

#### Layoffs by company
The dataset showed an exponential increase in layoffs in the last 3 years with some companies laying off their total work force and shutting down. Companies that laid off their entire workforce (100% layoffs) were majorly startups because the stage mentioned in the dataset indicated they were in the  early stages and did not have much funds for the company.

![Top 10 companies with the most layoff and their stages2](https://github.com/user-attachments/assets/cf51502c-d282-4eb5-97ab-915d4f73f028)

The top 10 largest firings occurred mostly in 2023 and 2022, with 47% taking place in 2023. The MAMA (Meta, Amazon, Microsoft, and Apple ) companies made up 4 of the top 5 largest layoffs.
Companies with the highest layoff in a single day are Google(12000), Meta(11000), Amazon(10000), Microsoft(10000), Ericsson(8500).

#### Layoffs by Country
. United states had the highest number of layoffs in these 3 years, with 256,559 employees laid off in the United States. Out of 51 countries in the dataset the U.S Accounted for 70% of layoffs in the 3years timesframe.

![Top 10 countries with the most layoffs2](https://github.com/user-attachments/assets/66834715-d3c9-4653-9ba3-fc24e812eb85)

Checking the locations in US having the most number of layoffs

![United States Layoffs By Locations ](https://github.com/user-attachments/assets/f9c98e41-fcf9-4049-b9b3-430a993a07c7)
The San Francisco Bay Area had the highest Number of layoffs in the united states 

### Key Insights and Statistics
1. Total Layoffs: Across all companies in the dataset, a total of 383,159 employees were laid off, with a total of 1,995 different layoffs.
2. Total Companies: The layoffs affected 1,640 companies worldwide.
3. Countries Affected: A total of 51 countries experienced layoffs.
4. Most Affected Country: The United States was the most affected, with 256,559 employees laid off.
5. Most Affected Industry: The Consumer industry faced the highest number of layoffs, with 45,182 employees.
6. Yearly Layoffs:
* 2020: 80,998 layoffs
* 2021: 15,823 layoffs
* 2022: 160,661 layoffs
* 2023: 125,677 layoffs (up to the point of data collection)
7. Top Countries by Layoffs:
* United States: 256,059 layoffs
* India: 35,993 layoffs
* Netherlands: 17,220 layoffs
8. Top Industries by Layoffs:

* Consumer: 45,182 layoffs
* Retail: 43,613 layoffs
* Transportation: 33,748 layoffs
9. Layoffs by Funding Stage:
Post-IPO companies experienced the most layoffs, with 204,132 employees affected.
Other notable stages include Unknown (40,716 layoffs) and Acquired (27,576 layoffs)
