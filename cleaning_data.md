What issues will you address by cleaning the data?

One primary investigation is whether to join the analytics and all_sessions tables. Both tables have 'fullvisitorid' and 'visitid'. I looked to see whether there was unique 
information from the analytics table that could be added to the all_sessions table for transactions, since the all_sessions table only captured 81 transactions. Indeed, 
there are transactions within the analytics table that cannot be found in all_sessions (31 additional) BUT they have no customer data or product data. They do offer: 
pageviews,timeonsite,date,channelgrouping. So, for the main questions this table offers no additional information.

Within all_sessions, most cities fields are not populated. I would strongly suggest altering the main questions to include only country. However, an additional question
can be answered with this information: Does adding city to an analysis add anything to the results? Perhaps a grouping by region might be better?


Queries:
Below, provide the SQL queries you used to clean your data.

--CLEANING FOR TABLE all-sessions.

select distinct fullvisitorid from all_sessions

--there are 14,147 rows returned min=1.22544e+14, max=9.99968e+18
--there are 15,134 rows of original data, so there may be duplicates.

select distinct visitid from all_sessions

--there are 14,556 rows returned
--there are 15,134 rows of original data, so there may be duplicates.

select time,fullvisitorid, visitid, count(visitid) from all_sessions
	group by visitid, fullvisitorid,time
	having count(visitid)>1
		order by visitid
	
--there are 549 rows where fullvisitorid and visitid are duplicated. 22 of those rows are duplicated more than
--once, for a total of 527+46=573. These are duplicates, but none are from purchasers. Adding other variables to
--the 'group by' only decreased duplicates once, to 222 rows. If it is decided to drop duplicate rows, use code:

delete from all_sessions a
	using all_sessions b
where
    a.id > b.id
    and a.visitid = b.visitid
	and a.fullvisitorid=b.fullvisitorid
	and a.time=b.time
	
--224 records would be deleted

select distinct id,country,city from all_sessions
	order by country

--need to change all values of '(not set)' to null for country and city.
--need to change all values of 'not available in demo dataset' to null for country and city.
--very few cities recorded (346='(not set)',8173='not available in demo dataset' ) 6391 values are in
--product price has to be /1,000,000.

select * from all_sessions
 	where transactionid is not null
	
--only 81 purchases!
--other useable variables include: totaltransactionrevenue,pageviews,channelgrouping,timeonsite,date,productsku

--run these cleaning steps before analysis:

select to_date (date,'YYYYMMDD') as session_date from all_sessions

--CLEANING FOR TABLE analytics

select distinct fullvisitid from analytics

--148,642 distinct visitid in over 4M rows!

select distinct fullvisitorid from analytics

--120,018 distinct visitorid

select distinct visitid,fullvisitorid from analytics

select timeonsite,date,visitnumber,visitid,count(visitid) from analytics
group by visitid,visitnumber,date,timeonsite
having count(visitid)>1
order by visitid

select unitssold,visitid, count(visitid) from analytics
where revenue != '0'
group by visitstarttime,userid,channelgrouping,socialengagementtype,visitid,visitnumber,date,timeonsite,revenue,
	unitssold,fullvisitorid,pageviews,bounces,unitprice
having count(visitid)>1
order by count(visitid)

--If duplicate values are to be deleted, use code:

delete from analytics a
	using analytics b
where
    a.id > b.id
    and a.visitid = b.visitid
	and a.fullvisitorid=b.fullvisitorid
	and a.visitstarttime=b.visitstarttime
	and a.timeonsite=b.timeonsite
	and a.pageviews=b.pageviews
	and a.channelgrouping=b.channelgrouping
	and a.unitssold=b.unitssold
	and a.visitnumber=b.visitnumber
	
-- then run a QA check to see if we still have all our distinct visitids

select distinct fullvisitid from analytics

select distinct revenue,fullvisitorid from analytics
	where revenue is not null
	
--there are 39 records

select distinct a.revenue,a.visitid,s.visitid,s.totaltransactionrevenue from analytics a	
	full outer join all_sessions s on a.visitid=s.visitid
	where revenue is not null or totaltransactionrevenue is not null
	order by a.visitid
	
--there are 119 records in the combined tables with transaction records

select a.revenue,a.visitid,s.visitid,s.totaltransactionrevenue from analytics a	
	left join all_sessions s on a.visitid=s.visitid
	where revenue is not null
	order by a.visitid

--in cases with matching visitid,revenue and totaltransactionrevenue are not equal, but by a small amount, maybe
--tax? You could combine these data, but analytics has so few useful fields.
--cases in analytics with purchase records that do NOT appear in all_sessions = 39(-8duplicates)

select * from analytics
	where revenue is not null
	order by visitid
	
--pageviews,timeonsite,date,channelgrouping - but no info on customer or products

