# Pierce-County-MySQL
Extract information with SQL queries in MySQL


## Query 4

```
with house_size as (
	select Parcel_Number, Land_Gross_Square_Feet, 
	case when Land_Gross_Square_Feet > 100000 then 'Large'
	when Land_Gross_Square_Feet < 10000 then 'Small'
	else 'Medium' end as House_size
	from appraisal_df ) 

select h.House_size, avg(s.sale_price) avg_sale_price, avg(t.Total_Market_Value_Current_Year) avg_current_market_value
from house_size h
join tax_df t on h.Parcel_Number = t.Parcel_Number
join sale_df s on s.Parcel_Number = t.Parcel_Number
group by 1;


```

| House_Size| avg_sale_price | avg_current_market_value|                                                                                                                             
|-----------|-------------|---|
|Small|236348.5714|283314.2857|
|Large|3269.0000|75400.0000|
|Medium|134000.0000|297100.0000|



## Query 5

```
select a.Appraisal_Account_Type, avg(s.Sale_price) as Avg_Sale_Price
from appraisal_df as a 
inner join sale_df as s on a.Parcel_Number = s.Parcel_number
group by Appraisal_Account_Type;

```

| Appraisal_Account_Type| Avg_Sale_Price |                                                                                                                             
|-----------|-------------|
|Residential|550281.5960|
|Commercial|3838125.0625|
|Condominium|422179.5714|
|Industrial|2876038.3333|
|Com Multi Unit|1176634.5000|
|Com Condo|824305.0000|



## Query 6

```
select year(Sale_Date) as Year , 
		QUARTER(Sale_Date) as Qrt, 
		avg(Sale_Price) as Current_Sale_Price, 
        		lag(avg(Sale_Price), 1) over ( ORDER BY year(Sale_Date) , QUARTER(Sale_Date)) Previous_Qrt_sales,
		LEAD(avg(Sale_Price), 1) over ( ORDER BY year(Sale_Date) , QUARTER(Sale_Date)) Next_Qrt_sales
from sale_df
where year(Sale_Date) in (2019,2020,2021)
group by Year, Qrt
order by Year desc, Qrt desc
```

| Year        | Qrt | Current_Sale_Price       | Previous_Qrt_Sales |                                                                                                                                    
|-----------|-------------|------|-----|
|2021|1|831410.6849|1264245.9603|
|2020|4|1264245.9603|736341.7518|
|2020|3|736341.7518|465842.0737|
|2020|2|465842.0737|1933844.0300|
|2020|1|1933844.0300|959113.2366|
|2019|4|959113.2366|649872.2672|
|2019|3|649872.2672|913902.4706|
|2019|2|913902.4706|1112788.5000|
|2019|1|1112788.5000|NULL|
