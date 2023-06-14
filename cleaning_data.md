What issues will you address by cleaning the data?





Queries:
Below, provide the SQL queries you used to clean your data.

/* CODE FOR CLEANING ALL_SESSIONS TABLE.  THIS TABLE HAS MANY REDUNDANT AND/OR UNNECESSARY VALUES WHICH WILL BE ELIMINATED BY 
SPLITTING THE DATA INTO SEPARATE TABLES (NORMALIZATION) */

    -- Create visits table from all_sessions to normalize data (single row for each visitid)
    drop table if exists visits;
    create table visits as
      select visitid, max(fullVisitorId) as fullVisitorId, max(channelGrouping) as channelGrouping, 
      max(time) as time, max(country) as country, max(city) as city, max(timeOnSite) as timeOnSite, 
      max(pageviews) as pageviews, max(sessionQualityDim) as sessionQualityDim, max(date) as date, 
      max(type) as type, max(pagePathLevel1) as pagePathLevel1, max(eCommerceAction_type) as eCommerceAction_type, 
      max(eCommerceAction_step) as eCommerceAction_step
      from original_all_sessions
      group by visitid
      order by visitid;

    -- Create transactions table from all_sessions by filtering out visitids that did not result in a transaction.
    drop table if exists transactions;
    create table transactions as 
      select visitid, totaltransactionrevenue, productsku, productquantity, productprice, currencycode, date
      from original_all_sessions
      where totaltransactionrevenue is not null
      or productquantity is not null;

  
-- CODE FOR CLEANING VISITS AND VISIT_DETAILS TABLES 

    -- Set primary key for visits table
    alter table visits 
      add primary key (visitid);

    -- Convert data types for columns in visits table
    alter table visits
    alter column time type int using time::int,
    alter column timeonsite type int using timeonsite::int,
    alter column pageviews type int using pageviews::int,
    alter column sessionqualitydim type int using sessionqualitydim::int,
    alter column date type date using date::date,
    alter column ecommerceaction_type type int using ecommerceaction_type::int,
    alter column ecommerceaction_step type int using ecommerceaction_step::int;

    -- Convert time values in visits table, assuming values under 86400 (the number of seconds in a day) are in seconds and 
    -- values above 86400 are in milliseconds.  Converting zeros to null, as zeros will skew time distribution analysis.
    update visits set time = null where time = 0;
    update visits set time = time/1000 where time >= 86400;

    -- Convert countries and cities in visits table to null where needed
    update visits 
      set country = null where country in ('(not set)', 'not available in demo dataset');
    update visits 
      set city = null where city in ('(not set)', 'not available in demo dataset');

    -- Convert null timeonsite values in visits table to average
    update visits
      set timeonsite = (select avg(timeonsite) from visits)
      where timeonsite is null;

    -- Create visit_details table from all_sessions (has a row for every product page visited during each visit)
    drop table if exists visit_details;
    create table visit_details as 
      select visitid, productsku, pagetitle
      from original_all_sessions;

    -- Add primary key for visit_details
    drop sequence if exists sequence1;
    CREATE SEQUENCE sequence1
      start 1
      increment 1;

    alter table visit_details
      drop column if exists visit_details_id;
    alter table visit_details
      add column visit_details_id int;

    update visit_details  
      set visit_details_id = nextval('sequence1');

    alter table visit_details 
      add primary key (visit_details_id);


-- CODE FOR CLEANING FOR TRANSACTIONS TABLE

    -- Add primary key for transactions table
    drop sequence if exists sequence2;
    CREATE SEQUENCE sequence2
      start 1
      increment 1;

    alter table transactions
      drop column if exists transactions_id;
    alter table transactions
      add column transactions_id int;

    update transactions  
      set transactions_id = nextval('sequence2');

    alter table transactions 
      add primary key (transactions_id);

    -- Assume null values in transactions productquantity column are equal to 1
    update transactions
    set productquantity = 1
    where productquantity is null;

    -- Convert totaltransactionrevenue and productprice to USD (divide by 1 million)
    update transactions
      set totaltransactionrevenue = cast(totaltransactionrevenue as numeric)/1000000;
    update transactions
      set productprice = cast(productprice as numeric)/1000000;

    -- Fill currencycode nulls as USD
    update transactions
    set currencycode = 'USD'
    where currencycode is null;

    -- Convert column data types
    alter table transactions
    alter column totaltransactionrevenue type numeric(10,2) using totaltransactionrevenue::numeric(10,2),
    alter column productquantity type int using productquantity::int,
    alter column productprice type numeric(10,2) using productprice::numeric(10,2),
    alter column date type date using date::date;
    
    
-- CODE FOR CLEANING PRODUCTS TABLE

    -- Restore products table to its original state
    drop table if exists products;
    create table products as select * from original_products;

    -- Remove whitespace from name column
    update products 
    set name = trim(name);

    -- Convert orderedquantity, stocklevel, restockingleadtime, sentimentscore, sentimentmagnitude to numeric
    alter table products
    alter column orderedquantity set data type int
    using orderedquantity::int,
    alter column stocklevel set data type int
    using stocklevel::int,
    alter column restockingleadtime set data type int
    using restockingleadtime::int,
    alter column sentimentscore set data type numeric
    using sentimentscore::numeric,
    alter column sentimentmagnitude set data type numeric
    using sentimentmagnitude::numeric;

    -- Convert sentimentscore null values to 0
    update products 
    set sentimentscore = 0
    where sentimentscore is null;

    -- Convert sentimentmagnitude null values to average
    update products
    set sentimentmagnitude = (select avg(sentimentmagnitude) from products)
    where sentimentmagnitude is null;

    -- show table
    select * from products

