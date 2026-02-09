## Project Overview
This project showcases essential data cleaning techniques including:

Removing duplicate records
Standardizing data formats
Handling NULL values and blanks
Removing unnecessary rows and columns

## Objectives
The main goals of this data cleaning project are to:

Ensure data integrity by removing duplicates
Standardize formats across all fields
Handle missing values appropriately
Prepare clean data ready for analysis

##  Dataset

The project uses a layoffs dataset containing the following fields:

company - Company name
location - Location of layoffs
industry - Industry sector
total_laid_off - Total number of employees laid off
percentage_laid_off - Percentage of workforce affected
date - Date of layoff event
stage - Company stage (e.g., Series A, B, C)
country - Country where layoffs occurred
funds_raised_millions - Funding raised by company

##  Technologies Used

MySQL

##  Project Structure
data-cleaning-project/
├
├── sql/
│   ├── 01_database_setup.sql
│   ├── 02_remove_duplicates.sql
│   ├── 03_standardize_data.sql
│   ├── 04_handle_nulls.sql
│   └── 05_final_cleanup.sql
└── docs/
    └── data_cleaning_steps.md

##  Getting Started

## database_setup
```sql
  create database data_cleaning_project;
  use data_cleaning_project;
  select*from layoffs;
  
  create table layoffs_staging
  like layoffs;
  
insert layoffs_staging
select*
from layoffs;
```
## Removing Duplicates (identifying of duplicates)

```sql
SELECT *,
    ROW_NUMBER() OVER(
        PARTITION BY company, location, industry, total_laid_off, 
                     percentage_laid_off, `date`, stage, country, 
                     funds_raised_millions
    ) AS row_num
FROM layoffs_staging;


with duplicate_cte as(SELECT *,
    ROW_NUMBER() OVER(
        PARTITION BY company, location, industry, total_laid_off, 
                     percentage_laid_off, `date`, stage, country, 
                     funds_raised_millions
    ) AS row_num
FROM layoffs_staging)
select * 
from duplicate_cte
where row_num > 1;

CREATE TABLE `layoffs_staging2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` int
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

select* from layoffs_staging2;

insert into layoffs_staging2
SELECT *,
    ROW_NUMBER() OVER(
        PARTITION BY company, location, industry, total_laid_off, 
                     percentage_laid_off, `date`, stage, country, 
                     funds_raised_millions
    ) AS row_num
FROM layoffs_staging;

set sql_safe_updates = 0;

delete
from layoffs_staging2
where row_num >1;

select * from layoffs_staging2;
```
## Standardizing Data Formats

```sql
select distinct(company)
from layoffs_staging2;

select distinct trim(company)
from layoffs_staging2;

select company, trim(company)
from layoffs_staging2;

update layoffs_staying2
set company = trim(company);

select industry
from layoffs_staging2;

select distinct industry from layoffs_staging2;

select distinct industry from layoffs_staging2 order by 1;

SELECT *
FROM layoffs_staging2
WHERE industry LIKE 'crypto%';

update layoffs_staging2
set industry='crypto'
where industry like 'crypto%';

select distinct location
from layoffs_staging2
order by 1;

select distinct country
from layoffs_staging2
order by 1;

select distinct country , trim(country)
from layoffs_staging2
order by 1;

SELECT DISTINCT 
    country, 
    TRIM(TRAILING '.' FROM country) AS country_trimmed
FROM layoffs_staging2
ORDER BY 1;

UPDATE layoffs_staging2
SET country = TRIM(TRAILING '.' FROM country)
WHERE country LIKE 'United State%';

select * from layoffs_staging2;

SELECT `date`,
    STR_TO_DATE(`date`, '%m/%d/%Y')
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

SELECT `date`
FROM layoffs_staging2;

ALTER TABLE layoffs_staging2
MODIFY COLUMN `date` DATE;
```
## Working with NULL Values and blanks (Look at the null values or Blanks values)
```sql
select*
from layoffs_staging2
where total_laid_off is null;

select * 
from layoffs_staging2
where total_laid_off is null
and percentage_laid_off is null;
```
##  Removal of unnecessary rows,columns,nulls and blanks
```sql
SELECT COUNT(*) AS null_or_blank_count
FROM layoffs_staging2
WHERE industry IS NULL OR industry = ''; 

         
update layoffs_staging2
set funds_raised_millions = '0' 
where funds_raised_millions is null or funds_raised_millions = '';     


ALTER TABLE layoffs_staging2
DROP COLUMN row_num;
```     
















