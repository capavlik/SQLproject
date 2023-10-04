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


Question 2: --Q2 how did order revenue change from 2016 to 2017?

SQL Queries:

with
sessions_clean as (
	select 	case when country = '(not set)' or country = 'not available in demo dataset' then null
				else country end country_clean,
			case when city = '(not set)' or city = 'not available in demo dataset' then null
				else city end city_clean,
			case when totaltransactionrevenue::float is null then 0
				else (totaltransactionrevenue::float/1000000) end revenue,
			case when productprice::float is null then 0
				else (productprice::float/1000000) end productprice,
			visitid,to_date(date,'YYYYMMDD') as ord_date
	from all_sessions
--	where totaltransactionrevenue is not null
),
revenue_analysis as (
	select 	country_clean,city_clean,revenue,productprice,visitid,ord_date,
		sum(revenue) over (partition by city_clean) revenue_city,
		sum(revenue) over (partition by country_clean) revenue_country,
		sum(revenue) over () revenue_total,
		extract(month from ord_date) as month,
		extract(year from ord_date) as year,
		extract(day from ord_date) as day
	from sessions_clean)
	
select max(ord_date),min(ord_date) from revenue_analysis
	group by year
 	
--select sum(revenue) as yearly_revenue,year,country_clean from revenue_analysis
--	group by country_clean,year
	
--select count (distinct month),year from revenue_analysis
--	group by year

Answer:
There were only 5 months of data collection in 2016 (August 1 to December 31), and 7 in 2017 (January 1 to August 1).
NO orders were placed outside of the US in 2016, while 4 additional countries ordered in 2017 (Australia,Switzerland,Canada, and Israel).
Revenue in the US and overall more than doubled in 2017:
US $4364 to $8790
Overall $4364 to $9850


Question 3: What are the total sales overall for all products?

SQL Queries:

	with 
all_products as (
	select p.sku,p.name,p.orderedquantity::int as onsupplyorder,p.stocklevel::int,
		case when ss.total_ordered::int is null then 0
			else ss.total_ordered::int end total_sold,
		case when sr.ratio::float is null then 0
			else sr.ratio::float end
	from sales_report sr
	full outer join sales_by_sku ss on ss.product_sku = sr.productsku
	full outer join products p on ss.product_sku = p.sku
	where sku is not null and sku not like '9%'
	),
productprice_1 as (
	select distinct productsku,
	case when productprice::float is null then 0
		else (productprice::float/1000000) end price
	from all_sessions
	where productsku not like '9%' and productsku not like '1%'
	),
productprice as (
	select productsku,max(price) as price
	from productprice_1
	group by (productsku)
	)
select sum(total_sold*price) as grand_total_revenue
	from all_products ap
	join productprice pp on ap.sku=pp.productsku

Answer:

$138,965.12 for all products recorded.

PART B: What is the value of all products that they currently have on order?

$3,255,124.36 assuming standard 100% mark-up, they're on the hook for $1,627,562.

IF I HAD MORE TIME: How long it might take to burn through current inventory assuming that the company has been selling products for 1 year.
BONUS: How long to burn through current + ordered inventory assuming same.
BONUS BONUS: What kind of loan would they need to be able to pay for ordered inventory, and how much of current revenue will be diverted 
     to that purpose based on continuing these sales trends?
