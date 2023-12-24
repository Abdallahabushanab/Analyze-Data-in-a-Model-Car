# Analyze-Data-in-a-Model-Car


## Are there products with high inventory but low sales? How can we optimize the inventory of such products?

SELECT productCode, productName, quantityInStock, total_orders, (quantityInStock - total_orders) as Inventory_shortage
 FROM (
 select 
 p.productCode,
 p.productName,
 p.quantityInStock,
 sum(t2.quantityOrdered) as total_orders
 from products as p
 left join
 orderdetails as t2 on p.productCode = t2.productCode
 GROUP BY
  p.productCode,
 p.productName,
 p.quantityInStock
 ) AS Inventry_data
 where (quantityInStock - total_orders) > 0
 order by Inventory_shortage ;


 ## Are all the warehouses currently in use still necessary? How can we review warehouses that have low or inactive inventory?

select
w.warehouseCode, 
w.warehousename,
sum(p.quantityInStock) as total_inventory
from products as p
join warehouses as w on p.warehouseCode = w.warehouseCode
group by w.warehouseCode, w.warehousename
order by total_inventory DESC;

select
w.warehouseCode, 
p.productname,
w.warehousename,
sum(p.quantityInStock) as total_inventory
from products as p
join warehouses as w on p.warehouseCode = w.warehouseCode
group by w.warehouseCode, p.productname, w.warehousename
order by total_inventory;


## Is there a relationship between product prices and their sales levels? How can price adjustments impact sales?


select
p.productCode, 
p.productname,
p.buyPrice,
sum(t1.quantityOrdered) as total_order
from products as p
left join
 orderdetails as t1 on p.productCode = t1.productCode
group by 
p.productCode, 
p.productname,
p.buyPrice
order by buyPrice desc;

## Who are the customers contributing the most to sales? How can sales efforts be focused on these valuable customers?


select
c.customerNumber, 
c.customerName,
count(o.orderNumber) as total_sales
from customers as c
join orders as o on c.customerNumber= o.customerNumber
group by 
c.customerNumber, 
c.customerName
order by total_sales DESC;


## How can the performance of sales employees be evaluated using sales data?



select
e.employeeNumber,
e.firstName, 
e.lastName,
e.jobTitle, 
count(od.priceEach * od.quantityOrdered) as total_sales
from employees as e
left join
 customers as c on e.employeeNumber = c.salesRepEmployeeNumber
left join
 orders as o on c.customerNumber = o.customerNumber
 left join
 orderdetails as od on o.orderNumber = o.orderNumber
group by 
e.employeeNumber,
e.firstName, 
e.lastName,
e.jobTitle
order by total_sales DESC;


##  How can customer payment trends be analyzed? What credit risks need to be considered, and how can cash flow be managed?


select
c.customerNumber, 
c.customerName,
p.paymentDate, 
p.amount as payment_amount
from customers as c
left join
 payments as p on c.customerNumber= p.customerNumber
order by payment_amount DESC;


## How can the performance of various product lines be compared? Which products are the most successful, and which ones need improvement or removal?


select
p.productLine, 
pl.textDescription,
p.buyPrice,
sum(p.quantityInStock) as total_inventory,
sum(od.quantityOrdered) as total_sales,
sum(od.priceEach * od.quantityOrdered) as total_revenue,
(sum(od.quantityOrdered)/ sum(p.quantityInStock))*100 as sales_to_inventory_precentage
from products as p
left join
 productlines as pl on p.productLine = pl.productLine
left join
 orderdetails as od on p.productCode = od.productCode
group by 
p.productCode, 
p.productname,
p.buyPrice
order by sales_to_inventory_precentage desc;


## How can the company’s credit policies be evaluated? Are there any customers with credit issues that need to be addressed?



select
c.customerNumber, 
c.customerName,
c.creditLimit,
sum(p.amount) as payment_amount,
(sum(p.amount) - c.creditLimit) as credit_limit_diff
from customers as c
left join
 payments as p on c.customerNumber= p.customerNumber
group by 
c.customerNumber, 
c.customerName,
c.creditLimit
Having 
sum(p.amount) < c.creditLimit 
order by payment_amount ;


Recommendation

1. Inventory Optimization:

Identified products with high inventory and low sales.
Recommendation: Reduce inventory by adjusting order quantities or assessing actual demand.
Benefits: Lower storage costs and improved resource allocation.

2. Warehouse Review:

Noted warehouses with low or inactive inventory.
Recommendation: Conduct further reviews; close or consolidate inefficient warehouses.
Benefits: Reduced rental costs and optimized inventory allocation.

3. Product Pricing Evaluation:

Found a correlation between product prices and sales performance.
Recommendation: Carefully review and adjust prices for low-selling products.
Benefits: Increased customer attraction, boosting sales.

4. Customer Analysis:

Identified valuable customers contributing significantly to sales.
Recommendation: Focus sales efforts on these customers, provide special incentives.
Benefits: Strengthen customer relationships, align offerings with preferences.
