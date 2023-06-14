# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
Explore, clean, and analyze the data.  

Exploration: 
- Inspect each table to get an idea of how much data cleaning will be required
- See which columns could be used as primary keys
- Confirm which tables columns and tables are useful or redundant

Cleaning: 
- Ensuring all columns are in a usable data type, 
- All duplicates rows removed 
- Tables are reduced and split into multiple tables when needed, 
- Values are in the correct units 

Analysis
- Gain meaningful business insights from the data

## Process
- Inspected each table and calculated min, max, avg, count unique, and count null for each column in order to determine what cleaning would need to be done
- Found that the all_sessions table could be normalized and split into 3 different tables, visits, visit_details, and transactions. 
- Decided to ignore the analytics table as it was very large, with many duplicates and redundant data that was already in other tables
- Decided to ignore the sales by sku and sales_report tables as they had data that was alreay in the products table.
###

## Results
- US is a major customer base for this company, contributing to over 50% of their website visits and sales.  The company may want to focus on other markets to try diversifying, or doubledown on the US and squeeze every last dime out of that market.
- The heaviest website traffic is between 02:00 and 06:00 UTC, with almost 30% of all visits happening in this range.  This makes sense as the US is 4-7 hours behind UTC, so the heaviest traffice in the US would be in the late evening when people would have time to online shop.  This is useful for resource planning (warehouse, customer support, servers, etc.)
- Website traffic is steady throughout the week, then drops by 50% on the weekends.  This is also helpful for resource planning, as it would allow for warehouse and customer service staff to work less hours on wekeends.

## Challenges 
- The data is very messy, and cleaning it in SQL was difficult at first for me.
- I am experienced in Python, so I found myself a little frustrated when I knew a really easy way to fix something in Python, but had to figure how to perform the same action in SQL, which was usually less straightforward.

## Future Goals
Spend a significant amount of time inspecting the analytics table, and attempting to fill in missing data from other tables.  It would be interesting to see how the results would differ from only using the all_sessions table.

