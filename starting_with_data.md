Question 1: When is web traffic highest throughout the day?  When is web traffic highest during the week?

SQL Queries:

  -- Visits by hour of day
  select time/(60*60), count(*)
  from visits
  where time is not null
  group by time/(60*60)
  order by time/(60*60);

Answer: Between 00:00 and 06:00 UTC



Question 2: Where are most visitors from? Top 10 countries?

SQL Queries:

  select country, count(*)
  from visits 
  group by country
  order by count(*) desc



Answer: 

"country"	"count"
"United States"	8372
"India"	696
"United Kingdom"	647
"Canada"	617
"Germany"	327
"Japan"	235
"Australia"	218
"France"	210
"Taiwan"	165
"Netherlands"	154



Question 3: What products have the best conversion rate?  Sales per visit?  Sales per minute/second on site?

SQL Queries:

Answer:
