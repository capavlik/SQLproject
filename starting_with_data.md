Question 1: How much of Nest product sales in each city and country? 

SQL Queries:
with 
sessions_clean as (
	select 	case when country = '(not set)' or country = 'not available in demo dataset' then null
		else country end country_clean,
		case when city = '(not set)' or city = 'not available in demo dataset' then null
		else city end city_clean,
		case when totaltransactionrevenue::float is null then 0
		else (totaltransactionrevenue::float/1000000) end revenue,
		sum (totaltransactionrevenue::float/1000000) over () allprodrevenue,
		visitid,productsku,v2productcategory,v2productname
	from all_sessions),
nest_sales as (
	select country_clean,city_clean,revenue,v2productname,productsku,visitid,allprodrevenue
		from sessions_clean
		where v2productname ilike 'nest%' and revenue > 0),
nest_revenue as (
	select 	distinct country_clean,city_clean,revenue,v2productname,productsku,allprodrevenue,
		sum(revenue) over (partition by city_clean) nestrevenue_city,
		sum(revenue) over (partition by country_clean) nestrevenue_country,
		sum(revenue) over () nestrevenue_total		
	from nest_sales)
	
select * from nest_revenue

with 
sessions_clean as (
	select 	case when country = '(not set)' or country = 'not available in demo dataset' then null
		else country end country_clean,
		case when city = '(not set)' or city = 'not available in demo dataset' then null
		else city end city_clean,
		case when totaltransactionrevenue::float is null then 0
		else (totaltransactionrevenue::float/1000000) end revenue,
		sum (totaltransactionrevenue::float/1000000) over () allprodrevenue,
		visitid,productsku,v2productcategory,v2productname
	from all_sessions),
nest_sales as (
	select country_clean,city_clean,revenue,v2productname,productsku,visitid,allprodrevenue
		from sessions_clean
		where v2productname ilike 'nest%' and revenue > 0),
nest_revenue as (
	select 	distinct country_clean,city_clean,revenue,v2productname,productsku,allprodrevenue,
		sum(revenue) over (partition by city_clean) nestrevenue_city,
		sum(revenue) over (partition by country_clean) nestrevenue_country,
		sum(revenue) over () nestrevenue_total		
	from nest_sales)
select distinct country_clean,city_clean,nestrevenue_total,
	case when revenue = 0 then 0
		else ((nestrevenue_city/nestrevenue_country)*100) end nestcitycountry,
	case when (revenue = 0) then 0
		else ((nestrevenue_country/nestrevenue_total)*100) end as nestcountrytotal,
	case when revenue = 0 then 0
		else ((nestrevenue_total/allprodrevenue)*100) end nestpercenttotal
	from nest_revenue

Answer:

A total of $6,550, which is 46% of all sales are from Nest brand products worldwide. Of those, 100% of sales from 2 of the 
top 5 largest buyers by country (Australia,Israel). 85% of Nest sales come from the US, where San
Francisco, Palo Alto, and Los Angeles are the biggest purchasers at 15%,11%,and 6% of sales.


Question 2: 

SQL Queries:

Answer:



Question 4: 

SQL Queries:

Answer:



Question 5: 

SQL Queries:

Answer:
