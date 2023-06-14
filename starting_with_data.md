Question 1: When is web traffic highest throughout the day?  When is web traffic highest during the week?

SQL Queries:

  ```
  -- Visits by hour of day
  select time/(60*60), count(*)
  from visits
  where time is not null
  group by time/(60*60)
  order by time/(60*60);
  ```
  
```
-- Visits by day of week
select extract(dow from date) as day_of_week, count(*) as visits
from visits
where time is not null
group by extract(dow from date)
order by extract(dow from date);
```
    

Answer: 
`Between 00:00 and 06:00 UTC`
``



Question 2: Where are most visits from? Top 5 countries?

SQL Queries:

```
-- Visits by Country
select country, count(*)
from visits 
group by country
order by count(*) desc
limit 5
```



Answer: 
```
"United States"	        8372 visits
"India"	                696 visits
"United Kingdom"        647 visits
"Canada"	        617 visits
"Germany"	        327 visits
```



Question 3: What products have the best conversion rate?  Sales per visit?  Sales per minute/second on site?

SQL Queries:

```
-- Conversion rate by product name
select *, 
cast(cast(quantity_ordered as numeric)/cast(visits as numeric) as numeric(10,3)) as conversion_rate
from
	(select v2productname, count(*) as visits, coalesce(sum(t.productquantity),0) as quantity_ordered
	from visit_details vd
	left join transactions t
	on vd.productsku = t.productsku
	and vd.visitid = t.visitid
	left join products p
	on vd.productsku = p.sku
	group by v2productname
	order by quantity_ordered) as temp
order by conversion_rate desc
limit 10
```

Answer:
```
Top 5 products best conversion rate (quantity sold / visits)

"Leatherette Journal"	                         1.912
"Waze Dress Socks"	                           0.500
"Google Men's Bike Short Sleeve Tee Charcoal"	 0.333
"Red Spiral Google Notebook"                    0.216
"Google Youth Short Sleeve T-shirt Green"       0.200
```
