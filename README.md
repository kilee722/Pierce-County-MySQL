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
