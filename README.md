# Pierce_County_MySQL
Extract information from the Pierce County property dataset with SQL queries in MySQL
Data retrieved from https://www.co.pierce.wa.us/736/Data-Downloads
## Query 1
Average sale price over the year



```
select  year(sale_date) Year, 
	sum(Sale_Price) as Total_sales
from sale_df
group by 1
order by 2 desc;
```


| Year| Total_sales |                                                                                                                                     
|-----------|-------------|
|	2018	|	683505715	|
|	2011	|	643486619	|
|	2005	|	532610371	|
|	2020	|	497813211	|
|	2006	|	441521844	|
|	2013	|	439750355	|
|	2016	|	438861062	|
|	2004	|	433853134	|
|	2010	|	432894178	|
|	2015	|	372505980	|
|	2019	|	365431541	|
|	2001	|	343208462	|
|	2017	|	337680944	|
|	2002	|	277734364	|
|	2007	|	251873415	|
|	2014	|	212755494	|
|	2009	|	196400503	|
|	2012	|	179802322	|
|	2008	|	152102063	|
|	2003	|	124468571	|
|	1998	|	78928390	|
|	2000	|	67988045	|
|	2021	|	60692980	|
|	1999	|	57678051	|
|	1997	|	32839184	|



## Query 2
Median of sales price by attribute

```
with a as (SELECT l.attribute, s.Sale_Price 
   	 from sale_df s
	inner join land_df l on s.Parcel_Number = l.Parcel_Number)
  
SELECT
   t.attribute as Property_attribute, AVG(t.sale_price) as median_sale_price
FROM
   (SELECT attribute, sale_price,
    row_number() over(partition by attribute order by sale_price) rn,
    count(*) over(partition by attribute) cnt
    from a
    ) t
where rn in ( FLOOR((cnt + 1) / 2), FLOOR( (cnt + 2) / 2) )
group by attribute
order by 2 desc;
```

|Property_attribute|	median_sale_price |
|-----------|-----------|
|	C MA 4 TACOMA N	|	17502500	|
|	C MA 9 CENTRAL	|	14000000	|
|	C MA 8 PORT	|	3500000	|
|	C MA 3 PENINSULA	|	3268000	|
|	C MA 5 NORTH	|	750000	|
|	C STREETS	|	480000	|
|	C UTILITIES	|	434875	|
|	R WATERFRONT	|	429500	|
|	C AMENITIES	|	421750	|
|	C ECONOMIC	|	375000	|
|	C MA 7 CBD	|	314900	|
|	R VIEW	|	305000	|
|	R SIZE	|	287450	|
|	R AMENITIES	|	265000	|
|	R FUNCTIONAL	|	239975	|
|	R ECONOMIC	|	233900	|
|	R UTILITIES	|	204120	|
|	C ZONING	|	172000	|
|	C USE	|	170000	|
|	C MA 2 TACOMA S	|	100000	|
|	R SITE DEVELOPMENT	|	100000	|
|	C FUNCTIONAL	|	99500	|
|	R STREETS	|	46250	|



## Query 3
The average difference between current market value and sales price at the sales event

```
select year(s.sale_date) as sale_year, 
	avg(Total_Market_Value_Current_Year - s.sale_price) as Avg_price_difference
from tax_df t
join sale_df s on t.Parcel_Number = s.Parcel_Number
group by 1
order by 2 desc;
```


|sale_year | Avg_price_difference|
|-----------|-----------|
|	2000	|	275410.7692	|
|	1999	|	238040.9474	|
|	1998	|	225573.3333	|
|	2001	|	163827.2727	|
|	1997	|	149950	|
|	2003	|	145458	|
|	2008	|	116214.8571	|
|	2005	|	91655.6111	|
|	2015	|	68346.6667	|
|	2018	|	34064.4	|
|	2002	|	33200	|
|	2019	|	-11466.8462	|
|	2009	|	-90820.5	|
|	2021	|	-141648.75	|
|	2012	|	-162260.8182	|
|	2014	|	-163962	|
|	2013	|	-211662.6429	|
|	2020	|	-213568.75	|
|	2011	|	-281195.1538	|
|	2004	|	-354599.3529	|
|	2007	|	-444029.9333	|
|	2016	|	-477959.05	|
|	2006	|	-674992.2	|
|	2017	|	-836626.4706	|
|	2010	|	-3315342	|

## Query 4
Regroup house size into small, medium, and large, and average sale price and average current market value by house size
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
Average sale price by appraisal account type
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
Comparison of average sale price of current quarter and of previous quarter

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
