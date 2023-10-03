Answer the following questions and provide the SQL queries used to find the answer.

**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**

SQL Queries:

BY COUNTRY ONLY:
with 
sessions_clean as (
	select 	case when country = '(not set)' or country = 'not available in demo dataset' then null
		else country end country_clean,
		case when city = '(not set)' or city = 'not available in demo dataset' then null
		else city end city_clean,
		case when totaltransactionrevenue::float is null then 0
		else (totaltransactionrevenue::float/1000000) end revenue,visitid,productsku,productquantity,transactions
	from all_sessions),
revenue as (
	select sum(revenue) as total_revenue,country_clean as country_rev
	from sessions_clean
	group by country_rev),
sessions_working as(		
	select * from sessions_clean sc
	join revenue r on sc.country_clean=r.country_rev)
	
select country_clean,total_revenue,rank() over (order by total_revenue desc) as revenue_rank
	from sessions_working
where revenue > 0
group by total_revenue,country_clean

BY CITY AND COUNTRY:
with 
sessions_clean as (
	select 	case when country = '(not set)' or country = 'not available in demo dataset' then null
		else country end country_clean,
		case when city = '(not set)' or city = 'not available in demo dataset' then null
		else city end city_clean,
		case when totaltransactionrevenue::float is null then 0
		else (totaltransactionrevenue::float/1000000) end revenue,visitid
	from all_sessions),
revenue as (
	select sum(revenue) as total_revenue,city_clean as city_rev
	from sessions_clean
	group by city_rev),
sessions_working as(		
	select * from sessions_clean sc
	join revenue r on sc.city_clean=r.city_rev)	

select country_clean,city_clean,total_revenue,rank() over (order by total_revenue desc) as revenue_rank
	from sessions_working
where revenue > 0
group by total_revenue,country_clean,city_clean

Answer:
By Country:
1=United States($13,154)
2=Israel($602)
3=Australia($358)
4=Canada($82)
5=Switzerland($17)

By Country,City:
1=San Francisco,United States($1564)
2=Sunnyvale,United States($992)
3=Atlanta,United States($854)
4=Palo Alto,United States($608)
5=Tel Aviv-Yafo,Israel($602)


**Question 2: What is the average number of products ordered from visitors in each city and country?**

SQL Queries:
with 
sessions_clean as (
	select 	case when country = '(not set)' or country = 'not available in demo dataset' then null
		else country end country_clean,
		case when city = '(not set)' or city = 'not available in demo dataset' then null
		else city end city_clean,
		case when totaltransactionrevenue::float is null then 0
		else (totaltransactionrevenue::float/1000000) end revenue,visitid,productquantity
	from all_sessions),
prodquantity as (
	select avg(productquantity::int) as avg_unitnumber,city_clean as city_unit
	from sessions_clean
	group by city_unit),
sessions_working as(		
	select * from sessions_clean sc
	join prodquantity p on sc.city_clean=p.city_unit)	

select country_clean,city_clean,avg_unitnumber
	from sessions_working
where revenue > 0
group by avg_unitnumber,country_clean,city_clean	
order by avg_unitnumber desc

Answer:
Because unitnumbers were not recorded very often, there is not enough data to use this metric.
Order quantity in Atlanta (4), Houston (2), and New York (1.17) are the only ones that are not 1.

**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**

SQL Queries:

COUNT OF DISTINCT PRODUCTS ORDERED BY CITY AND COUNTRY:
with 
sessions_clean as (
	select 	case when country = '(not set)' or country = 'not available in demo dataset' then null
		else country end country_clean,
		case when city = '(not set)' or city = 'not available in demo dataset' then null
		else city end city_clean,
		case when totaltransactionrevenue::float is null then 0
		else (totaltransactionrevenue::float/1000000) end revenue,visitid,productsku
	from all_sessions),
products as (
	select count(distinct productsku) as total_products,city_clean as city_rev
	from sessions_clean
	where revenue>0
	group by city_rev),
sessions_working as(		
	select * from sessions_clean sc
	join products p on sc.city_clean=p.city_rev)	

select country_clean,city_clean,avg(total_products) as avg_products
	from sessions_working
where revenue > 0
group by country_clean,city_clean
order by avg_products desc

BY PRODUCT CATEOGORY:
with 
sessions_clean as (
	select 	case when country = '(not set)' or country = 'not available in demo dataset' then null
		else country end country_clean,
		case when city = '(not set)' or city = 'not available in demo dataset' then null
		else city end city_clean,
		case when totaltransactionrevenue::float is null then 0
		else (totaltransactionrevenue::float/1000000) end revenue,visitid,productsku,v2productcategory,v2productname
	from all_sessions)

select count(v2productcategory),city_clean,country_clean,v2productcategory from sessions_clean
	where revenue>0
	group by v2productcategory,country_clean,city_clean
	order by country_clean,count desc

Answer:
Disinct Products ordered By Country,City:
1=San Francisco,United States(12)
2=Mountain View,United States(8)
3=New York,United States(8)
4=Sunnyvale,United States(4)
5=Palo ALto,United States(3)

Category of Products ordered by Country,City:
Only one category of product was ordered by any country other than the US and Canada, and that was 
for the Nest camera.
Therefore, there doesn't seem to be enough orders for other products to see clear differences.


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**

SQL Queries:
with 
sessions_clean as (
	select 	case when country = '(not set)' or country = 'not available in demo dataset' then null
		else country end country_clean,
		case when city = '(not set)' or city = 'not available in demo dataset' then null
		else city end city_clean,
		case when totaltransactionrevenue::float is null then 0
		else (totaltransactionrevenue::float/1000000) end revenue,visitid,productsku,v2productname
	from all_sessions)
select count(productsku) as total_products,city_clean as city_tp,v2productname,productsku
	from sessions_clean
	where revenue>0
	group by city_tp,productsku,v2productname
	order by total_products desc

Answer:

The top-selling product for every country is the Nest camera. We need to do a better job of 
advertising products in other product categories.

Most diversity in the number of products sold was within the US, where 31 different products were 
sold, including apparel. However, the only product sold in other countries (except Canada), was the
Nest camera.

The only productsku that was ordered in any country/city combo more than once was:
Nest Learning Thermostat Stainless(2) and Nest Protect Smoke Alarm (2) and they both had 'null'
values for city within the US.


**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:

Most of our sales come from the US.





