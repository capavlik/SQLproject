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
		else (totaltransactionrevenue::float/1000000) end revenue,visitid
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

Answer:
By Country:
1=United States($13,154),2=Israel($602),3=Australia($358),4=Canada($150),5=Switzerland($17)
By Country,City:





**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







