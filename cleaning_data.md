What issues will you address by cleaning the data?

One primary investigation is whether to join the analytics and all_sessions tables. Both tables have 'fullvisitorid' and 'visitid'. I looked to see whether there was unique 
information from the analytics table that could be added to the all_sessions table for transactions, since the all_sessions table only captured 81 transactions. Indeed, 
there are transactions within the analytics table that cannot be found in all_sessions (31 additional) BUT they have no customer data or product data. They do offer: 
pageviews,timeonsite,date,channelgrouping. So, for the main questions this table offers no additional information.

Within all_sessions, most cities fields are not populated. I would strongly suggest altering the main questions to include only country. However, an additional question
can be answered with this information: Does adding city to an analysis add anything to the results? Perhaps a grouping by region might be better?


Queries:
Below, provide the SQL queries you used to clean your data.
