# Global Layoff Analysis(2020-2023)


### Project Overview

The Data Analysis Project focuses on providing insights into global layoffs that occurred between 2020 and 2023.


### Data Source:

Layoff Data: The primary dataset used for this analysis is the 'layoffs.csv' file, which contains detailed information on layoffs by country, industry, and year.

[Download here](https://github.com/AlexTheAnalyst/Excel-Tutorial/blob/main/Data%20Cleaning%20Excel%20Tutorial.xlsx)


### Tools Used:

•	SQL: For data cleaning and analysis.

•	Power BI: For report creation and visualization.


### Data Cleaning/Preparation

In the data preparation phase, the following tasks were performed:

1.	Loading and inspecting the data.
	
2.	Handling missing values.
	
3.	Cleaning and formatting the dataset.


### Exploratory Data Analysis (EDA)

The EDA phase explored key questions such as:

1.	Which year experienced the most layoffs?
   
2.	Which companies had the highest layoffs?
   
3.	Which industries were hit hardest by layoffs?


### Data Analysis

[To view the data cleaning and analysis code, click here](https://github.com/Indumathi12/Sql_Projects/blob/main/sql_Portfolio_Projects_code.txt)

Include some interesting code/features worked with

```
-- SQL
-- Top 10 companies by layoffs per year
WITH company_year AS (
    SELECT company, YEAR(TRY_CAST([date] AS DATE)) AS year, 
           SUM(CAST(total_laid_off AS INT)) AS total_laid_off 
    FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
    GROUP BY company, YEAR(TRY_CAST([date] AS DATE))
), Company_Year_Rank AS (
    SELECT *, 
           DENSE_RANK() OVER (PARTITION BY year ORDER BY total_laid_off DESC) AS ranking
    FROM company_year
    WHERE year IS NOT NULL
)
SELECT * FROM Company_Year_Rank
WHERE ranking <= 10;
```

### Results and Findings

The analysis identified trends in layoffs by year and company:

1.	In 2020, the travel and transportation sectors were hit hardest by COVID-19, resulting in significant layoffs.

2.	In 2021, industries like real estate, construction, and food services experienced higher layoffs.

3.	While companies like Amazon and Meta hired extensively in 2020 due to the shift to online operations, both companies laid off a large number of employees in 2022, refocusing on AI and automation.

4.	In 2023, companies such as Google and Microsoft laid off more employees, reflecting a continued focus on AI and automation

### Visualization

[Click here to view the visualizations of the results](https://github.com/Indumathi12/Sql_Projects/blob/main/portfolio_bi_report2.pdf)



