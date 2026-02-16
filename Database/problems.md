<details>
<summary>Top 2 Salaries Per Department</summary>


```text
Table:
employees(id, name, dept_id, salary)

use dense_rank --> cant use normal dense

dept salary
1     2nd highest
2     2nd highest
3     2nd highest
```

```text
SELECT *
FROM (
    SELECT *,
           DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
    FROM employees
) t
WHERE rnk <= 2;

```
</details>


<details>
<summary>multi table join</summary>

```text
Tables:
customers
| customer_id | name | city |

orders
| order_id | customer_id | order_date |

order_items
| order_item_id | order_id | product_id | quantity |

Find customers who ordered more than 5 total items (sum of quantity),
return --> customer_name, total_items
```

```text
customers->orders -> order_items
inner join gives --> | c.customer_id | c.name | c.city |o.order_id | o.customer_id | o.order_date |oi.order_item_id | oi.order_id | oi.product_id | oi.quantity |


select c.customer_name, sum(oi.quantity) from customers c 
inner join orders o 
on c.customer_id = o.customer_id

inner join order_items oi
on o.order_id = oi.order_id

group by c.customer_Id,c.customer_name having sum(oi.quantity)>5


imp --> group by c.customer_Id,c.customer_name   --> if we give id alone we cant pass name in select query

```
</details>


<details>
<summary>4 tables join </summary>

```text
Tables:

customers(customer_id, name)
orders(order_id, customer_id)
order_items(order_id, product_id, quantity)
products(product_id, category)

Find customers who ordered products from more than 2 different categories.
return -> customer_name, category_count

```

```sql

select c.name,count(distinct p.catageory) from customers c
inner join orders o on c.customer_id = o.customer_id
inner join order_items on oi o.order_id=oi.order_id
inner join products on p oi.product_id=p.product_id

group by c.customer_id,c.name having count(distinct p.catageory) >2
```

```sql
-- Find customers who have never placed any order.
SELECT *
FROM customers c
WHERE NOT EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
);

--using joins

select * from customers c 
left join orders o on c.customer_id=o.customer_id
where o.customer_id is null
```

```sql
I prefer NOT EXISTS because it clearly expresses anti-join intent and avoids NULL-related edge cases. From a performance perspective, modern optimizers usually generate similar execution plans for both NOT EXISTS and LEFT JOIN + IS NULL, so readability and correctness drive my choice.”
```

</details>


<details>
<summary>multi join date</summary>

```sql
Tables:

customers(customer_id, name)
orders(order_id, customer_id, order_date)
payments(payment_id, order_id, amount)
      
--Find customers whose total payment amount in 2024 is greater than 1,00,000.
-- result --> customer_name, total_payment
```
```sql
select c.name as customer_name, sum(p.amount)as  total_payment from customers c
inner join orders o on c.customer_id=o.customer_id
inner join payments p on o.order_id = p.order_id

WHERE o.order_date >= '2024-01-01'
  AND o.order_date <  '2025-01-01'

group by c.customer_id,c.name having sum(p.amount)>100000
```
</details>