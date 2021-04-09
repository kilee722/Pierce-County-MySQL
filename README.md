# Pierce-County-MySQL
Extract information with SQL queries in MySQL


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

| Year        | Total_sales |                                                                                                                                     
|-----------|-------------|
|2020|1000|
