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

    
