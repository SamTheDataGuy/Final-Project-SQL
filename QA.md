What are your risk areas? Identify and describe them.

`The biggest risk is not having enough data to make meaningful conclusions.  This data set is very messy, with some contradicting values (e.g. quantity ordered) and unclear column names.  There are also plenty of errors with values like typos, extra whitespce, and simply incorrect values (such as Los Angeles being in Australia).  The best way to solve these would be to be more familiar with the business so that one could more easily spot inconsistencies, and cross reference the data with other tools (like order management systems).
`

QA Process:
Describe your QA process and include the SQL queries used to execute it.

`
I inspected all tables and determined which data was going to be ignored due to it being redundant, messy, empty, or too large in volume (analytics table).  Here is what I determined:

```
**All_sessions table
**  - **FullVisitorId column**
    - has very large numeric values, I will leave it as string values to save space
    - Has some duplicates
  - **Time column** 
    - Values are numeric, I will convert.  
    - There are 86,400 seconds in a day, 42% of values are below this, 25% are above, and 33% have a value of zero.  I assumed that these values are in seconds, in UTC time zone. 
    - For the values above 86,400 I assumed there were issues that added extra days, and so I converted them to a time by subtracting multiples of 86,400 until they became lower than 86,400.  I only did this for values below 6*86400, the rest I converted to null.
    - I converted zeros to null so that they wouldn’t affect distribution analysis
  - **Country column**
    - 24 values of “(not set)”, I converted these to null.
  - **City column** 
    - has lots of “(not set)” and “not available in demo dataset” values.  I converted these to null.
  - **TotalTransactionRevenue**, 
    - Only 81 sessions have transactions
  - **TimeOnSite**, 
    - Many null values
  - **SessionQualityDim**, 
    - Mostly null values
  - **Date**
    - In YYYYMMDD format, easy to deal with 
  - **Visitid**
    - Duplicates
    - Need to create a visit_line_items table
  - **ProductRefundAmount**, 
    - Totally null, removed
  - **productRevenue**, 
    - Mostly null values
  - **transactionRevenue**
    - Mostly null
  - **transactionId** 
    - Mostly null values
  - **ProductQuantity**
    - Mostly null
  - **itemQuantity**, 
    - Totally null
  - **itemRevenue**, 
    - Totally null
  - **searchKeyword** column 
      - completely blank (no values)
  - **Productprice**
      - Not clear what currency/unit productPrice values are in, and there are some zeros
  - **ProductVariant** 
    - Only has one distinct value of “(not set)”, it can be removed
  - **CurrencyCode** 
    - has some blank values, but only 1 other distinct value “USD”.  Could probably fill in the blanks with the same value. 
  - **V2ProductCategory and pageTitle **
    - Columns look messy.  v2ProductCategory has some “(not set)” values, and one blank value.
  - **eCommerceAction_type and eCommerceAction_step **
    - Might be boolean.
  - **eCommerceAction_option** 
    - Mostly blank but has some values

**Analytics table**  
  - Very large table, and has a lot of duplicated data.  
  - I will ignore it and see if I can get by using the all_sessions table instead.

**Products table**
  - **Sku**
    - Ensured every value is unique (1092 rows and unique values)
    - 170 start with a ‘9’, 922 start with a ‘G”
    - Thought about removing skus starting with a ‘9’ as they seem like duplicates to other products, but there may be a good reason for them.  I will leave them as is.
  - **Name**
    - Remove unneeded white space in strings
    - 1092 rows, but only 309 unique values, what’s going on here?  Assuming these products with different skus but same name have different sizes, and that the dataset is missing that data.
  - **SKU column **
    - Has numeric and string values, relates to sales_by_sku
  - **orderedQuantity column **
    - Converted to int
    - Seems similar to total_ordered column from sales_by_sku table, and might be redundant data
    - Confirmed no values less than zero
  - **Stocklevel**
    - Converted to int
    - Confirmed no values less than zero
  - **restockingLeadTime** 
    - Converted to int
    - Confirmed no values less than zero
    - Doesn’t specify units, so I assumed it was number of days
  - **sentimentScore** 
    - has blank value in first row, and 
    - meaning of this column is unclear at first glance
    - 102 negative values, 819 positive values, and 1 null value
    - After some research, sentiment scores from Google are usually between -1 and 1, with 1 being the most positive.  
    - I replaced the null value with 0, and confirmed that all values are between -1 and 1.
  - **sentimentMagnitude** 
    - 1 null value, and the rest are between 0.1 and 2
    - Sentiment magnitudes should be between 0 and positive infinity, so this data is good
    - Filled null value with average sentiment magnitude.

**Sales_by_sku table**
  - This table was deleted because all data is repeated in the sales_report table

**Sales_report**
  - This table was deleted because the data is already repeated in the products table.
  - The values in the total_ordered column from the sales_report are much lower than the values in the orderedQuantity column from the products table, so I assumed that the products table was updated more recently.
