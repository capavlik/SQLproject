# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
Gain overall experience using SQl to input, explore, and clean data.
Gain experience with PostgreSQL and pgAdmin4 specifically.

## Process
1)import tables successfully into PostgreSQL using pgAdmin4
2)familiarize myself with the data tables and columns
3)develop queries to clean the data
4)determine potential primary and foreign keys and experiment with putting the data together - ERD
5)write queries to answer structured questions
6)further explore data to develop additional questions
7)write queries to answer data-led questions
---)throughout: QA all steps to ensure that what you think you understand is actually happening with the data.

## Results
Theough there was limited sales data linked to sales location and date (81 records), you could still look at sales based on country and city, as well as 
  by year. It is not clear how representative this dataset is in relation to the entire subset of sales.
There was some qualitative data with no explanation (ie. sentiment scores) that could be used in the future to determine how these score might be linked
  to overall sales, which could drive reorder numbers or inform on discontinuing some items in future.

## Challenges 
Most sales had no data associated with it that might help inform impact of any advertising or marketing initiatives.
The largest table (Analytics) seemed to be of little use, as there were no productids or skus associated with the information.
The smallest table (sales_by_sku) was mostly (but not entirely) redundant to the data contained in the sales_report table.
Many products were listed in multiple categories and with multiple prices which would take quite a lot of time to correct within a real company.

## Future Goals
What I think I see in the data is a HUGE order of items coming in that doesn't seem to be based on documented sales numbers. I would be interested
to calculate what proportion of current sales revenue would have to be allocated to paying off those orders, and how long it might take to sell all 
of that additional product based on sales numbers contained in these tables.
