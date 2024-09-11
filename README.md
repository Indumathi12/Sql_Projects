--Data cleaning
--1. Remove Duplicate
--2. Standardize the data
--3. Null values or Blank values
--4. Remove any Columns or Rows


-- Created duplicate table
Select * into [Portfolio_Projects].dbo.[layoffs_Staging]
From [layoffs]

Select * from [Portfolio_Projects].dbo.[layoffs_Staging]

--To find Column names and data types

SELECT COLUMN_NAME, DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'layoffs_Staging';
  
-- Find Duplicate
Select *, 
ROW_NUMBER() OVER(PARTiTION BY company,[location],industry,total_laid_off,percentage_laid_off,[date],stage,country,funds_raised_millions Order By company) As [Row_number]
From [Portfolio_Projects].dbo.[layoffs_Staging]

-- Deleted the Duplicate rows
WITH CTE_Duplicate AS
(
Select *, 
ROW_NUMBER() OVER(PARTiTION BY company,[location],industry,total_laid_off,percentage_laid_off,[date],stage,country,funds_raised_millions Order By company) As [Row_number]
From [Portfolio_Projects].dbo.[layoffs_Staging]
)
Delete from CTE_Duplicate
Where [Row_number] > 1

/*Casper,Cazoo, Hibob,Wildlife Studios, Yahoo*/

Select * from [Portfolio_Projects].dbo.[layoffs_Staging]
Where Company = 'Wildlife Studios'

--Created New table layoffs_Staging_2
/*
USE [Portfolio_Projects]
GO

/****** Object:  Table [dbo].[layoffs_Staging]    Script Date: 8/9/2024 12:50:51 PM ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[layoffs_Staging_2](
	[company] [nvarchar](50) NOT NULL,
	[location] [nvarchar](50) NOT NULL,
	[industry] [nvarchar](50) NULL,
	[total_laid_off] [nvarchar](25) NULL,
	[percentage_laid_off] [nvarchar](25) NULL,
	[date] [nvarchar](25) NOT NULL,
	[stage] [nvarchar](50) NOT NULL,
	[country] [nvarchar](50) NOT NULL,
	[funds_raised_millions] [nvarchar](25) NULL,
	[Row_num] [int]
) ON [PRIMARY]
GO
*/

Select * from [dbo].[layoffs_Staging_2]

INSERT INTO [Portfolio_Projects].dbo.[layoffs_Staging_2]
Select *, 
ROW_NUMBER() OVER(PARTiTION BY company,[location],industry,total_laid_off,percentage_laid_off,[date],stage,country,funds_raised_millions Order By company) As row_num
From [Portfolio_Projects].dbo.[layoffs_Staging]

-- Without Duplicate table
Select * from [Portfolio_Projects].dbo.[layoffs_Staging_2]
Where company = 'E Inc.'


--STANDERTIZING THE DATA
--First check We have extra space in the column

Select company , len(company) from [Portfolio_Projects].dbo.[layoffs_Staging_2]
Where company = 'E Inc.'
order by company desc

select len(company), len(TRIM(company)) from [Portfolio_Projects].dbo.[layoffs_Staging_2] 
Where len(company) != len(TRIM(company))

select len(Industry), len(TRIM(Industry)) from [Portfolio_Projects].dbo.[layoffs_Staging_2] 
Where len(Industry) != len(TRIM(Industry))

select len(total_laid_off), len(TRIM(total_laid_off)) from [Portfolio_Projects].dbo.[layoffs_Staging_2] 
Where len(total_laid_off) != len(TRIM(total_laid_off))

select len(percentage_laid_off), len(TRIM(percentage_laid_off)) from [Portfolio_Projects].dbo.[layoffs_Staging_2] 
Where len(percentage_laid_off) != len(TRIM(percentage_laid_off))

-- haven't use trim function. The length of the table is good

Select DISTINCT [industry] 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
order by 1

-- In the industry column we have Crypto, Crypto Currency and Cryptocurrency like 3 columns. changing name as a Crypto instead of Cryptocurrency

UPDATE [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET industry = 'Crypto'
Where industry like 'Crypto%'


Select DISTINCT [country] 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
order by 1
-- We have the United States, United States. like 2 rows. Need to reed off the United States.

UPDATE [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET country = 'United States'
Where country like 'United States%'

---***********************RECONSIDER AGAIN **************************----
 -- Changing [date] column data type varchar to DATE data type

Update [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET [date] = TRY_CONVERT(DATE,[date] )
WHERE TRY_CONVERT(DATE, [date]) IS NOT NULL -- one row is NULL so does not convert date  so we have to used this where condition

/*SELECT *
FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
WHERE TRY_CONVERT(DATE, [date]) IS NULL;*/

SELECT * FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]


--Update [total_laid_off] data type to int
Update [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET [total_laid_off] = TRY_CONVERT(int,[total_laid_off] )
WHERE TRY_CONVERT(int,[total_laid_off] ) is not NULL

--Update [percentage_laid_off] data type to float
Update [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET [percentage_laid_off] = TRY_CONVERT(float,[percentage_laid_off] )
WHERE TRY_CONVERT(float,[percentage_laid_off] ) is not NULL

--Update [funds_raised_millions] data type to float
Update [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET funds_raised_millions = TRY_CONVERT(int,funds_raised_millions )
WHERE TRY_CONVERT(int,funds_raised_millions ) is not NULL

-- Now we need to modify column data type

Alter table [Portfolio_Projects].dbo.[layoffs_Staging_2]
Alter COLUMN [date] DATE

Alter table [Portfolio_Projects].dbo.[layoffs_Staging_2]
Alter COLUMN [total_laid_off] int
----------------------------------***************************---------------------------

SELECT * FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
Where [total_laid_off] = 'NULL' 
and [percentage_laid_off] = 'NULL'  -- Here [total_laid_off] and [percentage_laid_off] is in varchar data type, Values are more so will take care at the end

-- Let's check Industry column


Select distinct company from [Portfolio_Projects].dbo.[layoffs_Staging_2]
order by [industry]

SELECT * FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
Where [industry] = 'NULL' or  [industry] is NULL

--company = Airbnb,Bally's Interactive,Carvana,Juul

SELECT * FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
Where company like 'Bally%'

/*
Airbnb	SF Bay Area	 NULL	30	NULL	2023-03-03	Post-IPO	United States	6400	1
Airbnb	SF Bay Area	Travel	1900	0.25	2020-05-05	Private Equity	United States	5400	1
*/
-- For the 2 rows company name is Airbnb but industry column , one row has NULL value, and another row has 'Traval'. Now need to populate 'Travel' to other column also. Same way 
--need to populate value other company name.


SELECT b.company,a.industry, b.industry FROM [Portfolio_Projects].dbo.[layoffs_Staging_2] as a
join [Portfolio_Projects].dbo.[layoffs_Staging_2] as b 
on a.company = b.company 
Where (a.[industry] = 'NULL' or  a.[industry] is NULL)
   and b.industry is not NULL

UPDATE a
SET a.industry = b.industry
FROM [Portfolio_Projects].dbo.[layoffs_Staging_2] a
INNER JOIN [Portfolio_Projects].dbo.[layoffs_Staging_2] b
    ON a.company = b.company 
WHERE (a.[industry] = 'NULL' OR a.[industry] IS NULL)
  AND b.industry IS NOT NULL;

--validation
SELECT * FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
Where company like 'Bally%' or company = 'Airbnb'

-- We have NULL in [total_laid_off] and [percentage_laid_off] but cannot populate value. Because we don't have valid information So delate those data

--Removing bad data

SELECT * FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
Where [total_laid_off] = 'NULL' 
and [percentage_laid_off] = 'NULL' 

Delete [Portfolio_Projects].dbo.[layoffs_Staging_2]
Where [total_laid_off] = 'NULL' 
and [percentage_laid_off] = 'NULL'

Alter table [Portfolio_Projects].dbo.[layoffs_Staging_2]
DROP column Row_num

-- Clean data
SELECT * FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]

/*
--Identify non integer values
SELECT *
FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
WHERE ISNUMERIC([YourColumn]) = 0 AND [YourColumn] IS NOT NULL;

--Handling Null
UPDATE [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET [YourColumn] = NULL
WHERE ISNUMERIC([YourColumn]) = 0;

--After this we can change data type
*/

--[total_laid_off]
SELECT *
FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
WHERE ISNUMERIC([total_laid_off]) = 0 AND [total_laid_off] IS NOT NULL;

UPDATE [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET [total_laid_off] = 0
WHERE ISNUMERIC([total_laid_off]) = 0;

UPDATE [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET [total_laid_off] = '0'
WHERE ([total_laid_off]) like '99%';

--[percentage_laid_off]
SELECT *
FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
WHERE ISNUMERIC([percentage_laid_off]) = 0 AND [total_laid_off] IS NOT NULL;

UPDATE [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET [percentage_laid_off] = 0
WHERE ISNUMERIC([percentage_laid_off]) = 0;

--[funds_raised_millions]
SELECT *
FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
WHERE ISNUMERIC([funds_raised_millions]) = 0 AND [funds_raised_millions] IS NOT NULL;

UPDATE [Portfolio_Projects].dbo.[layoffs_Staging_2]
SET [funds_raised_millions] = 0
WHERE ISNUMERIC([funds_raised_millions]) = 0;

--Exploring data
SELECT *
FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]
Order by cast([total_laid_off] as int) desc

SELECT MAX(cast([total_laid_off] as int)) as max_laidoff, MAX(cast([percentage_laid_off] as float)) as max_perlaidoff
FROM [Portfolio_Projects].dbo.[layoffs_Staging_2]

--100% laid off
Select * from [Portfolio_Projects].dbo.[layoffs_Staging_2]
Where [percentage_laid_off] = '1'
order by [date] desc

Select * from [Portfolio_Projects].dbo.[layoffs_Staging_2]
Where total_laid_off = '12000'

Select * from [Portfolio_Projects].dbo.[layoffs_Staging_2]
order by cast([funds_raised_millions] as float) desc

Select company, sum(cast(total_laid_off as int)) as total_laid_off 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
group by company 
order by sum(cast(total_laid_off as int)) desc

--Finding min and max date
select max(try_cast([date] as DATE)) as max_date ,min(try_cast([date] as DATE)) as min_date
from [Portfolio_Projects].dbo.[layoffs_Staging_2]


Select industry, sum(cast(total_laid_off as int)) as total_laid_off 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
group by industry 
order by sum(cast(total_laid_off as int)) desc

Select country, sum(cast(total_laid_off as int)) as total_laid_off 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
group by country 
order by sum(cast(total_laid_off as int)) desc

Select (try_cast([date] as DATE)) as [date], sum(cast(total_laid_off as int)) as total_laid_off 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
group by (try_cast([date] as DATE) )
order by sum(cast(total_laid_off as int)) desc

Select stage, sum(cast(total_laid_off as int)) as total_laid_off 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
group by stage 
order by sum(cast(total_laid_off as int)) desc

/*
Seed: The initial stage where a company is just starting out, typically raising its first round of funding to develop the product or service.

Series A, B, C, D, E, F, G, H, I, J: These refer to successive rounds of funding that a company might go through as it grows. Each series usually indicates the company’s progression:

Series A: Early-stage funding, after a company has a proven concept and looks to scale.
Series B: Expansion funding, often to grow the team, product offerings, or enter new markets.
Series C and beyond: Typically used for scaling operations, entering international markets, or preparing for an IPO (Initial Public Offering).
Post-IPO: The stage after a company has gone public and is now listed on a stock exchange.

Private Equity: A later-stage investment where a company might receive capital from private equity firms, which often buy significant shares in more mature companies.

Acquired: Indicates that the company has been bought by another company.

Subsidiary: Refers to a company that is owned or controlled by another company.

Unknown: The stage is not known or was not provided.*/
  

-- finding data year and month wise


Select substring([date],1,7) as [date], sum(cast(total_laid_off as int)) as total_laid_off 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
group by substring([date],1,7)
order by 1 asc




With Rolling_total as
(
Select substring([date],1,7) as month_year, sum(cast(total_laid_off as int)) as total_laid_off 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
group by substring([date],1,7)

)
Select month_year, total_laid_off , Sum(total_laid_off) over(order by month_year ) as rolling
From Rolling_total
order by month_year



Select company, year((try_cast([date] as DATE))),sum(cast(total_laid_off as int)) as total_laid_off 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
group by company ,year((try_cast([date] as DATE)))
order by company

--1st 5 company and year wise  laid off
With company_year AS
(
Select company, year((try_cast([date] as DATE))) as years,sum(cast(total_laid_off as int)) as total_laid_off 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
group by company ,year((try_cast([date] as DATE)))
 ) , Company_Year_Rank AS
 (
Select *, DENSE_RANK() OVER(PARTITION BY years order by total_laid_off DESC) AS RANKING
FROM company_year
Where years is not null

)
Select * From Company_Year_Rank
Where RANKING < = 10


--1st 5 country and year wise  laid off
With country_year AS
(
Select country, year((try_cast([date] as DATE))) as years,sum(cast(total_laid_off as int)) as total_laid_off 
from [Portfolio_Projects].dbo.[layoffs_Staging_2]
group by country ,year((try_cast([date] as DATE)))
 ) , country_Year_Rank AS
 (
Select *, DENSE_RANK() OVER(PARTITION BY years order by total_laid_off DESC) AS RANKING
FROM country_year
Where years is not null

)
Select * From country_Year_Rank
Where RANKING < = 10
