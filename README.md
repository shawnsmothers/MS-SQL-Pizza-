
# Pizza Sales Analysis with SQL Queries


This repository contains SQL queries used to analyze sales data for a pizza company. The queries provide insights into various aspects of the business, including revenue, average order value, total pizzas sold, and trends in sales over different time periods.




## Description
 In this project, I delved into analyzing sales data for a mock pizza company using SQL queries on a Microsoft server. The goal was to extract insights into various aspects of the business, such as revenue, average order value, total pizzas sold, and sales trends over different timeframes. By examining daily and monthly trends and analyzing the distribution of sales by pizza category and size, I aimed to uncover patterns that could guide strategic decision-making. Additionally, I identified the top and bottom performers in terms of revenue, quantity sold, and number of orders to understand customer preferences and optimize offerings. Through this thorough analysis, I aimed to provide valuable insights to support business growth and enhance operational efficiency.
## Queries used
Select *
From pizza_sales;

-- 1) What is the total reveunue:  Sum of the tal price of all pizzas 

Select  sum(total_price) As total_revenue
	From pizza_sales;
	-- 81,7860.05

--2) Average order value: The average amount spent per oder, calulated by dividing the total revenue by the toal number of orders. 
	  -- using disttinct count because duplicate orderIds
Select Sum(total_price)/ Count(DISTINCT order_id) As avg_orderValue
	From pizza_sales;
	-- 38.30

--3 Total pizza sold: The sum of the quantitities of all orders. 
Select Sum(quantity) As total_pizzaSold
	From pizza_sales;
	-- 49574
-- 3) What is the total orders sold- sum of orders sold? using disttinct count because duplicate orderIds
Select Count(Distinct order_id) 
	From pizza_sales;
	-- 21,350
-- 4 Average Pizza Per order : Average number of pizzas sold per order, calculated by dividing the total number of 
-- pizza sold by the total number of orders 

Select 
Cast(Sum(quantity) As decimal(10,2)) / 
Cast(Count(Distinct order_id) As decimal(10,2)) As ave_pizzaPerOrder
	From pizza_sales;
	--2.32

--5) What is the daily trend for total orders. 
Select DATENAME(DW, order_date) As order_day, Count(Distinct order_id) As total_orders
	From pizza_sales 
	Group By Datename (DW,order_date)
	Order by total_orders desc;
	--Friday led with highest amount of pizzas ordered at 3538. The least was the Sunday - Wednesday.  This makes sense with most meeting up with friends
	-- or not wanting to cook going into the weekend.  Thu, Fri, Sat makes sense why they would lead in orders vs other days.

-- 6) Monthy trendof total orders
Select DATENAME(MONTH, order_date) As month_name, 
	Count(Distinct order_id) as monthy_orders
	From pizza_sales
	Group By Datename(Month,order_date)
	Order by monthy_orders desc;
	-- July had the highest orders with 1,935. This makes sense with pool parties, Fourth of July and other events.  

7) -- Percentage of sales by pizza category: 

Select pizza_category, 
Sum(total_price) * 100 / (Select Sum(total_price) 
From pizza_sales) As pct_Sales
From pizza_sales 
Group by pizza_category;
-- Classic sold the most with around 27 percent.

-- add to get total sales or gross revenue for a month and filter a month
Select pizza_category, Sum(total_price) as total_sales,
Sum(total_price) * 100 / (Select Sum(total_price) From pizza_sales Where Month(order_date) = 1) As pct_Sales
From pizza_sales 
Where Month(order_date) = 1
Group by pizza_category;

-- 8) percent of sales per pizza size using subquery and cast

Select pizza_size, Cast(sum(total_price) As Decimal(10,2)) as total_sales, 
Cast(Sum(total_price) * 100 / (Select Sum(total_price) from pizza_sales)
As decimal(10,2)) as pct_sales
From pizza_sales

Group By pizza_size
Order By pct_sales desc;
--  Large had the highest percent of sales at 46 percent.
-- lowest percent of sales was xxl at 12 percent

-- Adding quarter to the query
Select pizza_size, Cast(sum(total_price) As Decimal(10,2)) as total_sales, 
Cast(Sum(total_price) * 100 / (Select Sum(total_price) from pizza_sales Where Datepart(quarter, order_date) = 1)
As decimal(10,2)) as pct_sales
From pizza_sales
Where Datepart(quarter, order_date) = 1
Group By pizza_size
Order By pct_sales desc;


--9) top 5 best sellers by Revenue, total quantitly and toal orders. using Top keyword


Select Top 5 pizza_name, Cast(Sum(total_price) As decimal(10,2)) As total_revenue
From pizza_sales 
Group by pizza_name
Order By total_revenue desc;
-- Thai chicken pizzza had the highest rev 

-- bottom 5 pizza types by rev 
Select Top 5 pizza_name , Cast(Sum(total_price) As decimal(10,2)) As total_revenue
From pizza_sales 
Group by pizza_name
Order By total_revenue asc;
-- Brie carre pizza had the lowest rev 

-- top pizza types by qty 
Select Top 5 pizza_name,  Sum(quantity) As total_quatity
From pizza_sales 
Group by pizza_name
Order By  total_quatity desc;
-- classic deluxe pizza had the most qty sold with 2,453

-- bottom pizza qtl by pizza type
Select Top 5 pizza_name , Sum(quantity) As total_quatity
From pizza_sales 
Group by pizza_name
Order By  total_quatity asc;
-- breie carre pizza was the lowest in qty sold

-- top orders by type 
Select top 5 pizza_name, Count( order_id) as total_orders 
From pizza_sales
Group by pizza_name
Order By total_orders desc 
-- Classic deluxe pizza was the most ordered type of pizza with 2,329 total orders

Select top 5 pizza_name, Count( order_id) as total_orders 
From pizza_sales
Group by pizza_name
Order By total_orders asc
-- the least ordered pizza is The Brie Carre Pizza in 480 orders
