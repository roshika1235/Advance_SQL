**SQL> -- 1. Retrieve customers who ordered all available products.**



SQL> select c.customer\_id,c.name from Customers c ,Orders o, Order\_items oi

&#x20; 2  where c.customer\_id = o.customer\_id and o.order\_id = oi.order\_id

&#x20; 3  group by c.customer\_id, c.name

&#x20; 4  having count(distinct oi.product\_id) = (select count(\*) from Products);



no rows selected





**SQL> -- 2. List products that have never appeared in any order.**



SQL> select p.product\_id, p.product\_name

&#x20; 2  from Products p

&#x20; 3  where p.product\_id not in ( select distinct oi.product\_id from Order\_items oi);



PRODUCT\_ID PRODUCT\_NAME

\---------- -----------------------------------------------------------------------

&#x20;       15 Power Bank

&#x20;       12 RAM

&#x20;       14 Router

&#x20;       13 Printer

&#x20;       11 SSD

**SQL> -- 3. Find orders that contain only one item, but that item is the most expensive product.**



SQL> select \* from (select oi.order\_id,

&#x20; 2         		max(p.product\_id)   as product\_id,

&#x20; 3         		max(p.product\_name) as product\_name,

&#x20; 4         		max(p.price)        as price

&#x20; 5  			from Order\_items oi,Products p

&#x20; 6  		        where oi.product\_id = p.product\_id

&#x20; 7  			group by oi.order\_id

&#x20; 8  			having count(distinct oi.product\_id) = 1

&#x20; 9  			order by price desc

&#x20;10		   )

&#x20;11  where rownum =1;



&#x20; ORDER\_ID PRODUCT\_ID PRODUCT\_NAME                                                                                              PRICE

\---------- ---------- ---------------------------------------------------------------------------------------------------- ----------

&#x20;        7          8 Tablet                                                                                                    20000





**SQL> --4. Identify customers whose first-ever order has more than 3 items.**





SQL> select c.name, sub.customer\_id,sub.order\_id,

&#x20; 2  count(distinct oi.product\_id) as prod\_cnt

&#x20; 3  from (select order\_id,

&#x20; 4             customer\_id,

&#x20; 5             order\_date,

&#x20; 6             row\_number() over( partition by customer\_id order by order\_date) as rn

&#x20; 7      from Orders

&#x20; 8  )sub,

&#x20; 9  Order\_items oi,

&#x20;10  Customers c

&#x20;11  where sub.order\_id=oi.order\_id

&#x20;12  and sub.customer\_id=c.customer\_id and sub.rn=1

&#x20;13  group by c.name,sub.customer\_id,sub.order\_id

&#x20;14  having count(distinct oi.product\_id)>3

&#x20;15  order by sub.customer\_id;



NAME                                                                                                 CUSTOMER\_ID   ORDER\_ID   PROD\_CNT

\---------------------------------------------------------------------------------------------------- ----------- ---------- ----------

Roshika                                                                                                       26         26          4

Ananya                                                                                                        27         27          4

Vikram                                                                                                        28         28          4





**SQL> -- 5. Find customers who placed orders but never made a payment.**



SQL> select c.customer\_id,c.name,

&#x20; 2  o.order\_id

&#x20; 3  from Customers c, Orders o

&#x20; 4  where c.customer\_id=o.customer\_id

&#x20; 5  and not exists (

&#x20; 6             select p.order\_id

&#x20; 7                     from Payments p

&#x20; 8                 where o.order\_id=p.order\_id

&#x20; 9             )

&#x20;10  order by c.customer\_id;





CUSTOMER\_ID NAME                                                                                                   ORDER\_ID

\----------- ---------------------------------------------------------------------------------------------------- ----------

&#x20;        11 Nikhil                                                                                                       11

&#x20;        12 Anita                                                                                                        12

&#x20;        13 Ramesh                                                                                                       13

&#x20;        14 Sita                                                                                                         14

&#x20;        15 Karthik                                                                                                      15

&#x20;        16 Lavanya                                                                                                      16

&#x20;        17 Harsha                                                                                                       17

&#x20;        18 Teja                                                                                                         18

&#x20;        19 Deepa                                                                                                        19

&#x20;        20 Varun                                                                                                        20

&#x20;        21 Manoj                                                                                                        21

&#x20;        22 Priya                                                                                                        22

&#x20;        23 Suresh                                                                                                       23

&#x20;        24 Neha                                                                                                         24

&#x20;        25 Gopi                                                                                                         25

&#x20;        26 Roshika                                                                                                      26

&#x20;        27 Ananya                                                                                                       27

&#x20;        28 Vikram                                                                                                       28



18 rows selected.





**SQL> -- 6. Retrieve orders that include both Mouse and Keyboard.**



SQL> select c.name, o.order\_id

&#x20; 2  from Customers c, Orders o

&#x20; 3  where c.customer\_id = o.customer\_id

&#x20; 4    and exists (

&#x20; 5          select 1

&#x20; 6          from Order\_items oi, Products p

&#x20; 7          where oi.order\_id = o.order\_id

&#x20; 8            and oi.product\_id = p.product\_id

&#x20; 9            and p.product\_name = 'Mouse'

&#x20;10        )

&#x20;11    and exists (

&#x20;12          select 1

&#x20;13          from Order\_items oi, Products p

&#x20;14          where oi.order\_id = o.order\_id

&#x20;15            and oi.product\_id = p.product\_id

&#x20;16            and p.product\_name = 'Keyboard'

&#x20;17        )

&#x20;18  order by c.name;



NAME                                                                                                   ORDER\_ID

\---------------------------------------------------------------------------------------------------- ----------

Asha                                                                                                          1

Priyanka                                                                                                     30

Roshika                                                                                                      26

Teja                                                                                                         18



**SQL> -- 7. For each customer, list the total amount ordered compared with their total payments.**



SQL> select c.customer\_id,

&#x20; 2         c.name,

&#x20; 3         (select sum(oi.quantity \* p.price)

&#x20; 4             from Order\_items oi, Products p , Orders o

&#x20; 5             where oi.product\_id=p.product\_id

&#x20; 6             and o.order\_id=oi.order\_id

&#x20; 7             and o.customer\_id=c.customer\_id ) as total\_ordered\_amt,

&#x20; 8         (select sum(pay.amount)

&#x20; 9             from Orders o, Payments pay

&#x20;10             where pay.order\_id=o.order\_id

&#x20;11             and o.customer\_id=c.customer\_id ) as total\_payment\_amt

&#x20;12  from Customers c

&#x20;13  order by c.customer\_id;



CUSTOMER\_ID NAME                                                                                                 TOTAL\_ORDERED\_AMT TOTAL\_PAYMENT\_AMT

\----------- ---------------------------------------------------------------------------------------------------- ----------------- -----------------

&#x20;         1 Asha                                                                                                              2500              2500

&#x20;         2 Ravi                                                                                                             60000             60000

&#x20;         3 Kiran                                                                                                             4500              3000

&#x20;         4 Meena                                                                                                             3000              3000

&#x20;         5 Rahul                                                                                                             1500              1500

&#x20;         6 Divya                                                                                                             2400              2400

&#x20;         7 Arjun                                                                                                            20000             20000

&#x20;         8 Sneha                                                                                                             7500              7500

&#x20;         9 Vikram                                                                                                            3500              3500

&#x20;        10 Pooja                                                                                                            51500             51500

&#x20;        11 Nikhil                                                                                                           10000

&#x20;        12 Anita                                                                                                            24000

&#x20;        13 Ramesh                                                                                                            4000

&#x20;        14 Sita                                                                                                             46000

&#x20;        15 Karthik

&#x20;        16 Lavanya                                                                                                          36500

&#x20;        17 Harsha                                                                                                           34000

&#x20;        18 Teja                                                                                                              6000

&#x20;        19 Deepa                                                                                                            71500

&#x20;        20 Varun                                                                                                           162000

&#x20;        21 Manoj                                                                                                             8000

&#x20;        22 Priya                                                                                                              600

&#x20;        23 Suresh                                                                                                            1200

&#x20;        24 Neha                                                                                                             20000

&#x20;        25 Gopi                                                                                                             16000

&#x20;        26 Roshika                                                                                                         267000

&#x20;        27 Ananya                                                                                                           86100

&#x20;        28 Vikram                                                                                                           46500

&#x20;        30 Priyanka                                                                                                          2000



29 rows selected.



**SQL> -- 8. List orders where the payment amount is less than the order value.**



SQL> select o.order\_id,

&#x20; 2         c.customer\_id,

&#x20; 3         c.name,

&#x20; 4         (select sum(oi.quantity \* p.price)

&#x20; 5             from Order\_items oi , Products p

&#x20; 6             where oi.product\_id = p.product\_id

&#x20; 7             and o.order\_id = oi.order\_id

&#x20; 8         ) as total\_ordered\_amt

&#x20; 9        ,(select sum(pay.amount)

&#x20;10             from Payments pay

&#x20;11             where pay.order\_id=o.order\_id) as total\_payment\_amt

&#x20;12  from Customers c , Orders o

&#x20;13  where c.customer\_id = o.customer\_id

&#x20;14             and (select sum(pay.amount)

&#x20;15                     from Payments pay

&#x20;16                     where pay.order\_id = o.order\_id

&#x20;17                 ) <

&#x20;18                 (select sum(oi.quantity\* p.price)

&#x20;19                     from Order\_items oi, Products p

&#x20;20                     where oi.order\_id=o.order\_id

&#x20;21                     and p.product\_id=oi.product\_id

&#x20;22                 );



&#x20; ORDER\_ID CUSTOMER\_ID NAME                                                                                                 TOTAL\_ORDERED\_AMT TOTAL\_PAYMENT\_AMT

\---------- ----------- ---------------------------------------------------------------------------------------------------- ----------------- -----------------

&#x20;        3           3 Kiran                                                                                                             4500              3000



**SQL> -- 9. Identify products that appear in orders but have zero inventory.**



SQL> select distinct p.product\_id,

&#x20; 2                  p.product\_name

&#x20; 3  from Products p,Order\_items oi, Inventory i

&#x20; 4  where oi.product\_id = p.product\_id

&#x20; 5  and i.product\_id = p.product\_id

&#x20; 6  and i.quantity\_on\_hand = 0

&#x20; 7  order by p.product\_id;



PRODUCT\_ID PRODUCT\_NAME

\---------- ----------------------------------------------------------------------------------------------------

&#x20;        5 Headphones

&#x20;        9 Speaker

&#x20;       13 Printer



**SQL> -- 10. Find all orders where every product in the order has quantity\_on\_hand < 20.**



SQL> select oi.order\_id,

&#x20; 2        i.product\_id,

&#x20; 3     max(i.quantity\_on\_hand)

&#x20; 4  from Order\_items oi , Inventory i

&#x20; 5  where oi.product\_id=i.product\_id

&#x20; 6  group by oi.order\_id , i.product\_id

&#x20; 7  having max(i.quantity\_on\_hand)<20

&#x20; 8  order by oi.order\_id;



&#x20; ORDER\_ID PRODUCT\_ID MAX(I.QUANTITY\_ON\_HAND)

\---------- ---------- -----------------------

&#x20;        1          2                       5

&#x20;        2          3                      15

&#x20;        2          4                       2

&#x20;        3          5                       0

&#x20;        4          2                       5

&#x20;        6          7                      10

&#x20;        7          8                       8

&#x20;        8          9                       0

&#x20;        9         10                      12

&#x20;       10          2                       5

&#x20;       10          4                       2

&#x20;       11          3                      15

&#x20;       12         11                       6

&#x20;       14         13                       0

&#x20;       16         11                       6

&#x20;       16         15                       7

&#x20;       17         13                       0

&#x20;       18          2                       5

&#x20;       18         15                       7

&#x20;       19          2                       5

&#x20;       19          3                      15

&#x20;       20          4                       2

&#x20;       20          5                       0

&#x20;       21          5                       0

&#x20;       23          7                      10

&#x20;       24          8                       8

&#x20;       25          9                       0

&#x20;       25         10                      12

&#x20;       26          2                       5

&#x20;       26          3                      15

&#x20;       26          4                       2

&#x20;       27          5                       0

&#x20;       27          7                      10

&#x20;       27          8                       8

&#x20;       28          9                       0

&#x20;       28         10                      12

&#x20;       28         11                       6

&#x20;       30          2                       5



38 rows selected.





**SQL> -- 11. List customers who ordered a product before its inventory was last updated.**



SQL> select c.customer\_id,

&#x20; 2     c.name,

&#x20; 3     oi.product\_id,

&#x20; 4     o.order\_date,

&#x20; 5     i.last\_updated

&#x20; 6  from customers c, orders o, order\_items oi, inventory i

&#x20; 7  where c.customer\_id = o.customer\_id

&#x20; 8  and o.order\_id = oi.order\_id

&#x20; 9  and oi.product\_id = i.product\_id

&#x20;10  and o.order\_date < i.last\_updated

&#x20;11  order by c.customer\_id , o.order\_id;



CUSTOMER\_ID NAME                                                                                                 PRODUCT\_ID ORDER\_DAT LAST\_UPDA

\----------- ---------------------------------------------------------------------------------------------------- ---------- --------- ---------

&#x20;         1 Asha                                                                                                          1 28-FEB-26 10-MAR-26

&#x20;         2 Ravi                                                                                                          3 02-MAR-26 15-MAR-26

&#x20;         2 Ravi                                                                                                          4 02-MAR-26 05-MAR-26

&#x20;         3 Kiran                                                                                                         1 04-MAR-26 10-MAR-26

&#x20;         5 Rahul                                                                                                         6 08-MAR-26 19-MAR-26

&#x20;         6 Divya                                                                                                         7 10-MAR-26 17-MAR-26



6 rows selected.



**SQL> --12. Retrieve orders containing products from at least 3 different categories.**



SQL> select oi.order\_id,

&#x20; 2  count(distinct oi.product\_id) as num\_of\_categories

&#x20; 3  from order\_items oi

&#x20; 4  group by oi.order\_id

&#x20; 5  having count(distinct oi.product\_id)>=3

&#x20; 6  order by oi.order\_id;



&#x20; ORDER\_ID NUM\_OF\_CATEGORIES

\---------- -----------------

&#x20;       16                 3

&#x20;       17                 3

&#x20;       18                 3

&#x20;       26                 4

&#x20;       27                 4

&#x20;       28                 4



6 rows selected.



**SQL> -- 13. Find customers who placed orders in each month of a given year.**



SQL> select c.name,

&#x20; 2  c.customer\_id

&#x20; 3  from orders o , customers c

&#x20; 4  where o.customer\_id = c.customer\_id

&#x20; 5  and extract(year from o.order\_date)=2026

&#x20; 6  group by c.customer\_id,c.name

&#x20; 7  having count(distinct extract(month from o.order\_date)) = 12

&#x20; 8  order by c.customer\_id;



no rows selected

**SQL> -- 14. List orders where the highest-priced item is the only item ordered.**



SQL> select oi.order\_id

&#x20; 2  from order\_items oi

&#x20; 3  join products p on oi.product\_id = p.product\_id

&#x20; 4  group by oi.order\_id

&#x20; 5  having count(\*) = 1

&#x20; 6  and max(p.price) = (select max(price) from products);



&#x20; ORDER\_ID

\----------

&#x20;       31

**SQL> --15. Retrieve top 3 customers with the most distinct products ordered.**



SQL> select customer\_id, name, distinct\_products

&#x20; 2  from (

&#x20; 3      select c.customer\_id, c.name,

&#x20; 4             count(distinct oi.product\_id) as distinct\_products,

&#x20; 5             dense\_rank() over (order by count(distinct oi.product\_id) desc) as rnk

&#x20; 6      from customers c

&#x20; 7      join orders o on c.customer\_id = o.customer\_id

&#x20; 8      join order\_items oi on o.order\_id = oi.order\_id

&#x20; 9      group by c.customer\_id, c.name

&#x20;10  )

&#x20;11  where rnk <= 3;



CUSTOMER\_ID NAME                                                                                                 DISTINCT\_PRODUCTS

\----------- ---------------------------------------------------------------------------------------------------- -----------------

&#x20;        26 Roshika                                                                                                              4

&#x20;        27 Ananya                                                                                                               4

&#x20;        28 Vikram                                                                                                               4

&#x20;        17 Harsha                                                                                                               3

&#x20;        18 Teja                                                                                                                 3

&#x20;        16 Lavanya                                                                                                              3

&#x20;         1 Asha                                                                                                                 2

&#x20;         2 Ravi                                                                                                                 2

&#x20;        14 Sita                                                                                                                 2

&#x20;        30 Priyanka                                                                                                             2

&#x20;        19 Deepa                                                                                                                2

&#x20;        20 Varun                                                                                                                2

&#x20;         3 Kiran                                                                                                                2

&#x20;        25 Gopi                                                                                                                 2

&#x20;        10 Pooja                                                                                                                2



15 rows selected.

**SQL> --16. Identify payments made after 48 hours from order\_date.**



SQL> select p.payment\_id, p.order\_id, o.order\_date, p.payment\_date

&#x20; 2  from payments p

&#x20; 3  join orders o on p.order\_id = o.order\_id

&#x20; 4  where (p.payment\_date - o.order\_date) > 2;



PAYMENT\_ID   ORDER\_ID ORDER\_DAT PAYMENT\_D

\---------- ---------- --------- ---------

&#x20;      201        101 01-MAR-26 04-MAR-26



**SQL> --17. Find duplicate product purchases within the same order.**



SQL> select order\_id, product\_id, count(\*) as duplicate\_count

&#x20; 2  from order\_items

&#x20; 3  group by order\_id, product\_id

&#x20; 4  having count(\*) > 1;



&#x20; ORDER\_ID PRODUCT\_ID DUPLICATE\_COUNT

\---------- ---------- ---------------

&#x20;       19          3               2

&#x20;       20          4               2



**SQL>  -- 18. List products ordered consistently in quantities>1**



SQL> select p.product\_id,

&#x20; 2     p.product\_name

&#x20; 3  from Products p, Order\_items oi

&#x20; 4  where p.product\_id = oi.product\_id

&#x20; 5  group by p.product\_id,p.product\_name

&#x20; 6  having min(oi.quantity)>1

&#x20; 7  order by p.product\_id;



PRODUCT\_ID PRODUCT\_NAME

\---------- ----------------------------------------------------------------------------------------------------

&#x20;        5 Headphones

&#x20;        6 USB Cable

&#x20;        9 Speaker

&#x20;       11 SSD

&#x20;       13 Printer

&#x20;       14 Router





**SQL> -- 19. Find inventory not used in orders for over 90 days.**





SQL> SELECT i.inventory\_id,

&#x20; 2         p.product\_name

&#x20; 3  FROM products p

&#x20; 4  JOIN inventory i ON p.product\_id = i.product\_id

&#x20; 5  WHERE i.last\_updated < (

&#x20; 6      SELECT MAX(o.order\_date)

&#x20; 7      FROM orders o

&#x20; 8  ) - INTERVAL '90' DAY;



INVENTORY\_ID PRODUCT\_NAME

\------------ ----------------------------------------------------------------------------------------------------

&#x20;         16 Test Product 16





**SQL> -- 20. Identify orders where payment\_method is NULL**



SQL> insert into customers values(31,'padma',7833409812,'padma@gmail.com')

&#x20; 2  ;



1 row created.



SQL> insert into orders values (31,31,to\_date('15-APR-26','DD-MON-RR'));



1 row created.



SQL> insert into payments values(11,31,30000,NULL,to\_date('16-APR-26','DD-MON-RR'));



1 row created.



SQL> select o.order\_id,p.payment\_id

&#x20; 2  from orders o , payments p

&#x20; 3  where o.order\_id=p.order\_id

&#x20; 4  and p.payment\_method is null;



&#x20; ORDER\_ID PAYMENT\_ID

\---------- ----------

&#x20;       31         11



**SQL> --21. Using a CTE, compute total stock value for every product.**



SQL> with stock\_value as (

&#x20; 2      select p.product\_id,

&#x20; 3             p.product\_name,

&#x20; 4             p.price,

&#x20; 5             i.quantity\_on\_hand,

&#x20; 6             (p.price \* i.quantity\_on\_hand) as total\_value

&#x20; 7      from products p

&#x20; 8      join inventory i

&#x20; 9      on p.product\_id = i.product\_id

&#x20;10  )

&#x20;11  select \*

&#x20;12  from stock\_value;



PRODUCT\_ID PRODUCT\_NAME                                                                                              PRICE QUANTITY\_ON\_HAND TOTAL\_VALUE

\---------- ---------------------------------------------------------------------------------------------------- ---------- ---------------- -----------

&#x20;        1 Mouse                                                                                                       500               50       25000

&#x20;        2 Keyboard                                                                                                   1500                5        7500

&#x20;        3 Monitor                                                                                                   10000               15      150000

&#x20;        4 Laptop                                                                                                    50000                2      100000

&#x20;        5 Headphones                                                                                                 2000                0           0

&#x20;        6 USB Cable                                                                                                   300              100       30000

&#x20;        7 Charger                                                                                                    1200               10       12000

&#x20;        8 Tablet                                                                                                    20000                8      160000

&#x20;        9 Speaker                                                                                                    2500                0           0

&#x20;       10 Webcam                                                                                                     3500               12       42000

&#x20;       11 SSD                                                                                                        6000                6       36000

&#x20;       12 RAM                                                                                                        4000               20       80000

&#x20;       13 Printer                                                                                                    8000                0           0

&#x20;       14 Router                                                                                                     3000               30       90000

&#x20;       15 Power Bank                                                                                                 2500                7       17500



15 rows selected.



**SQL> --22. Build a CTE to calculate monthly sales and find above-average months.**



SQL> with monthly\_sales as (

&#x20; 2      select to\_char(o.order\_date,'yyyy-mm') as month,

&#x20; 3             sum(p.price \* oi.quantity) as total\_sales

&#x20; 4      from orders o

&#x20; 5      join order\_items oi on o.order\_id = oi.order\_id

&#x20; 6      join products p on oi.product\_id = p.product\_id

&#x20; 7      group by to\_char(o.order\_date,'yyyy-mm')

&#x20; 8  )

&#x20; 9  select \*

&#x20;10  from monthly\_sales

&#x20;11  where total\_sales > (select avg(total\_sales) from monthly\_sales);



MONTH   TOTAL\_SALES

\------- -----------

2026-03      993300



**SQL> --23. Generate calendar dates using recursive CTE and join with orders.**



SQL> with calendar\_dates as (

&#x20; 2      select date '2025-01-01' + level - 1 dt

&#x20; 3      from dual

&#x20; 4      connect by level <= 365

&#x20; 5  )

&#x20; 6  select c.dt, o.order\_id

&#x20; 7  from calendar\_dates c

&#x20; 8  left join orders o

&#x20; 9  on c.dt = o.order\_date;



DT          ORDER\_ID

\--------- ----------

27-MAY-25

17-SEP-25

20-MAY-25

27-AUG-25

17-MAY-25

20-APR-25

09-JUN-25

08-APR-25

12-MAR-25

10-DEC-25

09-MAY-25

28-JUN-25

19-MAR-25

03-JAN-25

02-AUG-25

05-MAR-25

23-DEC-25

08-NOV-25

20-JUL-25

26-FEB-25

06-APR-25

14-FEB-25

18-DEC-25

01-JUL-25

28-APR-25



**SQL> --24. Categorize products into price tiers using CTE.**



SQL> WITH product\_tiers AS (

&#x20; 2      SELECT product\_id,

&#x20; 3             product\_name,

&#x20; 4             price,

&#x20; 5             CASE

&#x20; 6                 WHEN price < 5000 THEN 'Low Price'

&#x20; 7                 WHEN price BETWEEN 5000 AND 30000 THEN 'Medium Price'

&#x20; 8                 ELSE 'High Price'

&#x20; 9             END AS price\_category

&#x20;10      FROM products

&#x20;11  )

&#x20;12  SELECT \*

&#x20;13  FROM product\_tiers;



PRODUCT\_ID PRODUCT\_NAME                                                                                              PRICE PRICE\_CATEGO

\---------- ---------------------------------------------------------------------------------------------------- ---------- ------------

&#x20;        1 Mouse                                                                                                       500 Low Price

&#x20;        2 Keyboard                                                                                                   1500 Low Price

&#x20;        3 Monitor                                                                                                   10000 Medium Price

&#x20;        4 Laptop                                                                                                    50000 High Price

&#x20;        5 Headphones                                                                                                 2000 Low Price

&#x20;        6 USB Cable                                                                                                   300 Low Price

&#x20;        7 Charger                                                                                                    1200 Low Price

&#x20;        8 Tablet                                                                                                    20000 Medium Price

&#x20;        9 Speaker                                                                                                    2500 Low Price

&#x20;       10 Webcam                                                                                                     3500 Low Price

&#x20;       11 SSD                                                                                                        6000 Medium Price

&#x20;       12 RAM                                                                                                        4000 Low Price

&#x20;       13 Printer                                                                                                    8000 Medium Price

&#x20;       14 Router                                                                                                     3000 Low Price

&#x20;       15 Power Bank                                                                                                 2500 Low Price



15 rows selected.



**SQL> --25. Using CTEs, create a customer purchase summary.**



SQL> WITH purchase\_summary AS (

&#x20; 2      SELECT c.customer\_id,

&#x20; 3             c.name,

&#x20; 4             COUNT(o.order\_id) AS total\_orders,

&#x20; 5             SUM(p.price \* oi.quantity) AS total\_spent

&#x20; 6      FROM customers c

&#x20; 7      JOIN orders o ON c.customer\_id = o.customer\_id

&#x20; 8      JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20; 9      JOIN products p ON oi.product\_id = p.product\_id

&#x20;10      GROUP BY c.customer\_id, c.name

&#x20;11  )

&#x20;12  SELECT \*

&#x20;13  FROM purchase\_summary;



CUSTOMER\_ID NAME                                                                                                 TOTAL\_ORDERS TOTAL\_SPENT

\----------- ---------------------------------------------------------------------------------------------------- ------------ -----------

&#x20;        26 Roshika                                                                                                         4      267000

&#x20;         1 Asha                                                                                                            2        2500

&#x20;        10 Pooja                                                                                                           2       51500

&#x20;         5 Rahul                                                                                                           1        1500

&#x20;        28 Vikram                                                                                                          4       46500

&#x20;        25 Gopi                                                                                                            2       16000

&#x20;         3 Kiran                                                                                                           2        4500

&#x20;        20 Varun                                                                                                           3      162000

&#x20;        19 Deepa                                                                                                           3       71500

&#x20;         7 Arjun                                                                                                           1       20000

&#x20;         8 Sneha                                                                                                           1        7500

&#x20;         4 Meena                                                                                                           1        3000

&#x20;        24 Neha                                                                                                            1       20000

&#x20;         9 Vikram                                                                                                          1        3500

&#x20;        30 Priyanka                                                                                                        2        2000

&#x20;        17 Harsha                                                                                                          3       34000

&#x20;        27 Ananya                                                                                                          4       86100

17 rows selected.



**SQL> --26. Identify top 1% customers by spending using CTE.**



SQL> WITH spending AS (

&#x20; 2      SELECT c.customer\_id,

&#x20; 3             c.name,

&#x20; 4             SUM(p.price \* oi.quantity) AS total\_spent

&#x20; 5      FROM customers c

&#x20; 6      JOIN orders o ON c.customer\_id = o.customer\_id

&#x20; 7      JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20; 8      JOIN products p ON oi.product\_id = p.product\_id

&#x20; 9      GROUP BY c.customer\_id, c.name

&#x20;10  )

&#x20;11  SELECT \*

&#x20;12  FROM (

&#x20;13      SELECT s.\*,

&#x20;14             PERCENT\_RANK() OVER (ORDER BY total\_spent DESC) AS pr

&#x20;15      FROM spending s

&#x20;16  )

&#x20;17  WHERE pr <= 0.01;



CUSTOMER\_ID NAME                                                                                                 TOTAL\_SPENT         PR

\----------- ---------------------------------------------------------------------------------------------------- ----------- ----------

&#x20;        26 Roshika                                                                                                   267000          0



**SQL> --27. Find products with declining monthly order quantities.**



SQL> WITH monthly\_orders AS (

&#x20; 2      SELECT product\_id,

&#x20; 3             TO\_CHAR(o.order\_date,'YYYY-MM') AS month,

&#x20; 4             SUM(quantity) qty,

&#x20; 5             LAG(SUM(quantity)) OVER (

&#x20; 6                 PARTITION BY product\_id

&#x20; 7                 ORDER BY TO\_CHAR(o.order\_date,'YYYY-MM')

&#x20; 8             ) AS prev\_qty

&#x20; 9      FROM order\_items oi

&#x20;10      JOIN orders o ON oi.order\_id = o.order\_id

&#x20;11      GROUP BY product\_id, TO\_CHAR(o.order\_date,'YYYY-MM')

&#x20;12  )

&#x20;13  SELECT \*

&#x20;14  FROM monthly\_orders

&#x20;15  WHERE qty < prev\_qty;



PRODUCT\_ID MONTH          QTY   PREV\_QTY

\---------- ------- ---------- ----------

&#x20;        1 2026-04          1         13

&#x20;        2 2026-04          1          9

&#x20;        4                  1         10





**SQL> --28. Find inventory gaps using CTE to compare ordered vs on-hand quantities.**



SQL> WITH ordered\_qty AS (

&#x20; 2      SELECT product\_id,

&#x20; 3             SUM(quantity) AS total\_ordered

&#x20; 4      FROM order\_items

&#x20; 5      GROUP BY product\_id

&#x20; 6  )

&#x20; 7  SELECT p.product\_name,

&#x20; 8         i.quantity\_on\_hand,

&#x20; 9         o.total\_ordered

&#x20;10  FROM ordered\_qty o

&#x20;11  JOIN inventory i ON o.product\_id = i.product\_id

&#x20;12  JOIN products p ON p.product\_id = o.product\_id

&#x20;13  WHERE o.total\_ordered > i.quantity\_on\_hand;



PRODUCT\_NAME                                                                                         QUANTITY\_ON\_HAND TOTAL\_ORDERED

\---------------------------------------------------------------------------------------------------- ---------------- -------------

Keyboard                                                                                                            5            11

Laptop                                                                                                              2            11

Headphones                                                                                                          0            14

Speaker                                                                                                             0            10

SSD                                                                                                                 6            10

Printer                                                                                                             0             7



6 rows selected.



**SQL> --29. Compute running total of payments per customer using CTE.**



SQL> WITH customer\_payments AS (

&#x20; 2      SELECT c.customer\_id,

&#x20; 3             c.name,

&#x20; 4             p.payment\_date,

&#x20; 5             p.amount

&#x20; 6      FROM customers c

&#x20; 7      JOIN orders o ON c.customer\_id = o.customer\_id

&#x20; 8      JOIN payments p ON o.order\_id = p.order\_id

&#x20; 9  )

&#x20;10  SELECT customer\_id,

&#x20;11         name,

&#x20;12         payment\_date,

&#x20;13         amount,

&#x20;14         SUM(amount) OVER

&#x20;15         (PARTITION BY customer\_id ORDER BY payment\_date) AS running\_total

&#x20;16  FROM customer\_payments;



CUSTOMER\_ID NAME                                                                                                 PAYMENT\_D     AMOUNT RUNNING\_TOTAL

\----------- ---------------------------------------------------------------------------------------------------- --------- ---------- -------------

&#x20;         1 Asha                                                                                                 01-MAR-26       2500          2500

&#x20;         1 Asha                                                                                                 04-MAR-26                     2500

&#x20;         2 Ravi                                                                                                 03-MAR-26      60000         60000

&#x20;         3 Kiran                                                                                                05-MAR-26       3000          3000

&#x20;         4 Meena                                                                                                07-MAR-26       3000          3000

&#x20;         5 Rahul                                                                                                09-MAR-26       1500          1500

&#x20;         6 Divya                                                                                                11-MAR-26       2400          2400

&#x20;         7 Arjun                                                                                                12-MAR-26      20000         20000

&#x20;         8 Sneha                                                                                                13-MAR-26       7500          7500

&#x20;         9 Vikram                                                                                               14-MAR-26       3500          3500

&#x20;        10 Pooja                                                                                                15-MAR-26      51500         51500



11 rows selected.



**SQL> --30. Identify products that go out of stock after orders.**



SQL> WITH total\_orders AS (

&#x20; 2      SELECT product\_id,

&#x20; 3             SUM(quantity) AS ordered\_qty

&#x20; 4      FROM order\_items

&#x20; 5      GROUP BY product\_id

&#x20; 6  )

&#x20; 7  SELECT p.product\_name,

&#x20; 8         i.quantity\_on\_hand,

&#x20; 9         t.ordered\_qty

&#x20;10  FROM total\_orders t

&#x20;11  JOIN inventory i ON t.product\_id = i.product\_id

&#x20;12  JOIN products p ON p.product\_id = t.product\_id

&#x20;13  WHERE i.quantity\_on\_hand - t.ordered\_qty <= 0;



PRODUCT\_NAME                                                                                         QUANTITY\_ON\_HAND ORDERED\_QTY

\---------------------------------------------------------------------------------------------------- ---------------- -----------

Keyboard                                                                                                            5          11

Laptop                                                                                                              2          11

Headphones                                                                                                          0          14

Speaker                                                                                                             0          10

SSD                                                                                                                 6          10

Printer                                                                                                             0           7



6 rows selected.



**SQL> --31. Flag orders without payments using CTE.**



SQL> WITH unpaid\_orders AS (

&#x20; 2      SELECT o.order\_id, o.customer\_id, o.order\_date

&#x20; 3      FROM orders o

&#x20; 4      LEFT JOIN payments p ON o.order\_id = p.order\_id

&#x20; 5      WHERE p.order\_id IS NULL

&#x20; 6  )

&#x20; 7  SELECT \* FROM unpaid\_orders;



&#x20; ORDER\_ID CUSTOMER\_ID ORDER\_DAT

\---------- ----------- ---------

&#x20;       11          11 15-MAR-26

&#x20;       12          12 16-MAR-26

&#x20;       13          13 17-MAR-26

&#x20;       14          14 18-MAR-26

&#x20;       15          15 19-MAR-26

&#x20;       16          16 21-MAR-26

&#x20;       17          17 22-MAR-26

&#x20;       18          18 23-MAR-26

&#x20;       19          19 24-MAR-26

&#x20;       20          20 25-MAR-26

&#x20;       21          21 26-MAR-26

&#x20;       22          22 27-MAR-26

&#x20;       23          23 28-MAR-26

&#x20;       24          24 29-MAR-26

&#x20;       25          25 30-MAR-26

&#x20;       26          26 31-MAR-26

&#x20;       27          27 31-MAR-26

&#x20;       28          28 31-MAR-26

&#x20;       30          30 02-APR-26



20 rows selected.



**SQL> --32. Compute average fulfillment time using CTE.**



SQL> WITH fulfillment\_time AS (

&#x20; 2      SELECT o.order\_id,

&#x20; 3             (p.payment\_date - o.order\_date) AS fulfillment\_days

&#x20; 4      FROM orders o

&#x20; 5      JOIN payments p ON o.order\_id = p.order\_id

&#x20; 6  )

&#x20; 7  SELECT AVG(fulfillment\_days) AS avg\_fulfillment\_time

&#x20; 8  FROM fulfillment\_time;



AVG\_FULFILLMENT\_TIME

\--------------------

&#x20;         1.18285038



**SQL> -- 34. Bucket products into 5 equal price groups via CTE.**

SQL>

SQL> WITH price\_buckets AS (

&#x20; 2      SELECT product\_id,

&#x20; 3             product\_name,

&#x20; 4             price,

&#x20; 5             NTILE(5) OVER (ORDER BY price) AS price\_group

&#x20; 6      FROM products

&#x20; 7  )

&#x20; 8  SELECT \* FROM price\_buckets;



PRODUCT\_ID PRODUCT\_NAME                                                                                              PRICE PRICE\_GROUP

\---------- ---------------------------------------------------------------------------------------------------- ---------- -----------

&#x20;        6 USB Cable                                                                                                   300           1

&#x20;        1 Mouse                                                                                                       500           1

&#x20;        7 Charger                                                                                                    1200           1

&#x20;        2 Keyboard                                                                                                   1500           2

&#x20;        5 Headphones                                                                                                 2000           2

&#x20;       15 Power Bank                                                                                                 2500           2

&#x20;        9 Speaker                                                                                                    2500           3

&#x20;       14 Router                                                                                                     3000           3

&#x20;       10 Webcam                                                                                                     3500           3

&#x20;       12 RAM                                                                                                        4000           4

&#x20;       11 SSD                                                                                                        6000           4

&#x20;       13 Printer                                                                                                    8000           4

&#x20;        3 Monitor                                                                                                   10000           5

&#x20;        8 Tablet                                                                                                    20000           5

&#x20;        4 Laptop                                                                                                    50000           5



15 rows selected.



**SQL> -- 35. Find orders that exceed twice the customer’s average order size.**

SQL>

SQL> WITH order\_totals AS (

&#x20; 2      SELECT o.order\_id,

&#x20; 3             o.customer\_id,

&#x20; 4             SUM(oi.quantity \* p.price) AS order\_value

&#x20; 5      FROM orders o

&#x20; 6      JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20; 7      JOIN products p ON oi.product\_id = p.product\_id

&#x20; 8      GROUP BY o.order\_id, o.customer\_id

&#x20; 9  ),

&#x20;10  customer\_avg AS (

&#x20;11      SELECT customer\_id,

&#x20;12             AVG(order\_value) AS avg\_order\_value

&#x20;13      FROM order\_totals

&#x20;14      GROUP BY customer\_id

&#x20;15  )

&#x20;16  SELECT ot.order\_id, ot.customer\_id, ot.order\_value

&#x20;17  FROM order\_totals ot

&#x20;18  JOIN customer\_avg ca ON ot.customer\_id = ca.customer\_id

&#x20;19  WHERE ot.order\_value > 2 \* ca.avg\_order\_value;



no rows selected



**SQL> -- 36. Bucket products into 10 price bands and count orders.**

SQL>

SQL> WITH price\_bands AS (

&#x20; 2      SELECT product\_id,

&#x20; 3             price,

&#x20; 4             NTILE(10) OVER (ORDER BY price) AS price\_band

&#x20; 5      FROM products

&#x20; 6  )

&#x20; 7  SELECT pb.price\_band,

&#x20; 8         COUNT(oi.order\_id) AS total\_orders

&#x20; 9  FROM price\_bands pb

&#x20;10  JOIN order\_items oi ON pb.product\_id = oi.product\_id

&#x20;11  GROUP BY pb.price\_band

&#x20;12  ORDER BY pb.price\_band;



PRICE\_BAND TOTAL\_ORDERS

\---------- ------------

&#x20;        1            9

&#x20;        2           10

&#x20;        3            6

&#x20;        4            5

&#x20;        5            6

&#x20;        6            3

&#x20;        7            2

&#x20;        8            5

&#x20;        9            3

&#x20;       10            6



10 rows selected.



**SQL> -- 37. Group customers into spending buckets.**

SQL>

SQL> WITH customer\_spending AS (

&#x20; 2      SELECT c.customer\_id,

&#x20; 3             c.name,

&#x20; 4             SUM(oi.quantity \* p.price) AS total\_spent

&#x20; 5      FROM customers c

&#x20; 6      JOIN orders o ON c.customer\_id = o.customer\_id

&#x20; 7      JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20; 8      JOIN products p ON oi.product\_id = p.product\_id

&#x20; 9      GROUP BY c.customer\_id, c.name

&#x20;10  )

&#x20;11  SELECT customer\_id,

&#x20;12         name,

&#x20;13         total\_spent,

&#x20;14         NTILE(5) OVER (ORDER BY total\_spent) AS spending\_bucket

&#x20;15  FROM customer\_spending;



CUSTOMER\_ID NAME                                                                                                 TOTAL\_SPENT SPENDING\_BUCKET

\----------- ---------------------------------------------------------------------------------------------------- ----------- ---------------

&#x20;        22 Priya                                                                                                        600               1

&#x20;        23 Suresh                                                                                                      1200               1

&#x20;         5 Rahul                                                                                                       1500               1

&#x20;        30 Priyanka                                                                                                    2000               1

&#x20;         6 Divya                                                                                                       2400               1

&#x20;         1 Asha                                                                                                        2500               1

&#x20;         4 Meena                                                                                                       3000               2

&#x20;         9 Vikram                                                                                                      3500               2

&#x20;        13 Ramesh                                                                                                      4000               2

&#x20;         3 Kiran                                                                                                       4500               2

&#x20;        18 Teja                                                                                                        6000               2

&#x20;         8 Sneha                                                                                                       7500               2

&#x20;        21 Manoj                                                                                                       8000               3

&#x20;        11 Nikhil                                                                                                     10000               3

&#x20;        25 Gopi                                                                                                       16000               3

&#x20;        24 Neha                                                                                                       20000               3

&#x20;         7 Arjun                                                                                                      20000               3

&#x20;        12 Anita                                                                                                      24000               3

&#x20;        17 Harsha                                                                                                     34000               4

&#x20;        16 Lavanya                                                                                                    36500               4

&#x20;        14 Sita                                                                                                       46000               4

&#x20;        28 Vikram                                                                                                     46500               4

&#x20;        10 Pooja                                                                                                      51500               4

&#x20;         2 Ravi                                                                                                       60000               5

&#x20;        19 Deepa                                                                                                      71500               5

&#x20;        27 Ananya                                                                                                     86100               5

&#x20;        20 Varun                                                                                                     162000               5

&#x20;        26 Roshika                                                                                                   267000               5



28 rows selected.



**SQL> -- 38. Bucket orders into size categories.**

SQL>

SQL> WITH order\_sizes AS (

&#x20; 2      SELECT o.order\_id,

&#x20; 3             SUM(oi.quantity \* p.price) AS order\_value

&#x20; 4      FROM orders o

&#x20; 5      JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20; 6      JOIN products p ON oi.product\_id = p.product\_id

&#x20; 7      GROUP BY o.order\_id

&#x20; 8  )

&#x20; 9  SELECT order\_id,

&#x20;10         order\_value,

&#x20;11         CASE

&#x20;12             WHEN order\_value < 5000 THEN 'SMALL'

&#x20;13             WHEN order\_value BETWEEN 5000 AND 20000 THEN 'MEDIUM'

&#x20;14             ELSE 'LARGE'

&#x20;15         END AS order\_size\_category

&#x20;16  FROM order\_sizes;



&#x20; ORDER\_ID ORDER\_VALUE ORDER\_

\---------- ----------- ------

&#x20;        1        2500 SMALL

&#x20;       30        2000 SMALL

&#x20;       22         600 SMALL

&#x20;       25       16000 MEDIUM

&#x20;       11       10000 MEDIUM

&#x20;        6        2400 SMALL

&#x20;       28       46500 LARGE

&#x20;       13        4000 SMALL

&#x20;       26      267000 LARGE

&#x20;        2       60000 LARGE

&#x20;       31       50000 LARGE

&#x20;       20      162000 LARGE

&#x20;       21        8000 MEDIUM

&#x20;       14       46000 LARGE

&#x20;        4        3000 SMALL

&#x20;        5        1500 SMALL

&#x20;       24       20000 MEDIUM

&#x20;       17       34000 LARGE

&#x20;       23        1200 SMALL

&#x20;        8        7500 MEDIUM

&#x20;       18        6000 MEDIUM

&#x20;        3        4500 SMALL

&#x20;       27       86100 LARGE

&#x20;        7       20000 MEDIUM

&#x20;       19       71500 LARGE

&#x20;       10       51500 LARGE

&#x20;        9        3500 SMALL

&#x20;       16       36500 LARGE

&#x20;       12       24000 LARGE



29 rows selected.



**SQL> -- 39. Categorize payments based on amount buckets.**

SQL>

SQL> SELECT payment\_id,

&#x20; 2         order\_id,

&#x20; 3         amount,

&#x20; 4         CASE

&#x20; 5             WHEN amount < 5000 THEN 'SMALL PAYMENT'

&#x20; 6             WHEN amount BETWEEN 5000 AND 20000 THEN 'MEDIUM PAYMENT'

&#x20; 7             ELSE 'LARGE PAYMENT'

&#x20; 8         END AS payment\_category

&#x20; 9  FROM payments;



PAYMENT\_ID   ORDER\_ID     AMOUNT PAYMENT\_CATEGO

\---------- ---------- ---------- --------------

&#x20;        1          1       2500 SMALL PAYMENT

&#x20;        2          2      60000 LARGE PAYMENT

&#x20;        3          3       3000 SMALL PAYMENT

&#x20;        4          4       3000 SMALL PAYMENT

&#x20;        5          5       1500 SMALL PAYMENT

&#x20;        6          6       2400 SMALL PAYMENT

&#x20;        7          7      20000 MEDIUM PAYMENT

&#x20;        8          8       7500 MEDIUM PAYMENT

&#x20;        9          9       3500 SMALL PAYMENT

&#x20;       10         10      51500 LARGE PAYMENT

&#x20;      201        101            LARGE PAYMENT



11 rows selected.



**SQL> -- 40. Bucket products by inventory age.**



SQL> WITH inventory\_age AS (

&#x20; 2      SELECT i.product\_id,

&#x20; 3             p.product\_name,

&#x20; 4             (SYSDATE - i.last\_updated) AS age\_days

&#x20; 5      FROM inventory i

&#x20; 6      JOIN products p ON i.product\_id = p.product\_id

&#x20; 7  )

&#x20; 8  SELECT product\_id,

&#x20; 9         product\_name,

&#x20;10         age\_days,

&#x20;11         CASE

&#x20;12             WHEN age\_days < 30 THEN 'FRESH STOCK'

&#x20;13             WHEN age\_days BETWEEN 30 AND 90 THEN 'AGING STOCK'

&#x20;14             ELSE 'OLD STOCK'

&#x20;15         END AS inventory\_category

&#x20;16  FROM inventory\_age;



PRODUCT\_ID PRODUCT\_NAME                                                                                           AGE\_DAYS INVENTORY\_C

\---------- ---------------------------------------------------------------------------------------------------- ---------- -----------

&#x20;        1 Mouse                                                                                                19.2152083 FRESH STOCK

&#x20;        2 Keyboard                                                                                             29.2152083 FRESH STOCK

&#x20;        3 Monitor                                                                                              14.2152083 FRESH STOCK

&#x20;        4 Laptop                                                                                               24.2152083 FRESH STOCK

&#x20;        5 Headphones                                                                                           39.2152083 AGING STOCK

&#x20;        6 USB Cable                                                                                            10.2152083 FRESH STOCK

&#x20;        7 Charger                                                                                              12.2152083 FRESH STOCK

&#x20;        8 Tablet                                                                                               34.2152083 AGING STOCK

&#x20;        9 Speaker                                                                                              49.2152083 AGING STOCK

&#x20;       10 Webcam                                                                                               16.2152083 FRESH STOCK

&#x20;       11 SSD                                                                                                  18.2152083 FRESH STOCK

&#x20;       12 RAM                                                                                                  20.2152083 FRESH STOCK

&#x20;       13 Printer                                                                                              59.2152083 AGING STOCK

&#x20;       14 Router                                                                                               22.2152083 FRESH STOCK

&#x20;       15 Power Bank                                                                                           27.2151968 FRESH STOCK



15 rows selected.



**SQL> -- 41. Create weekly sales buckets.**



SQL> SELECT

&#x20; 2      TRUNC(o.order\_date, 'IW') AS week\_start,

&#x20; 3      COUNT(DISTINCT o.order\_id) AS orders\_in\_week,

&#x20; 4      SUM(p.price \* oi.quantity) AS total\_sales,

&#x20; 5      CASE

&#x20; 6          WHEN SUM(p.price \* oi.quantity) > 50000 THEN 'High'

&#x20; 7          WHEN SUM(p.price \* oi.quantity) > 20000 THEN 'Medium'

&#x20; 8          WHEN SUM(p.price \* oi.quantity) > 5000 THEN 'Low'

&#x20; 9          ELSE 'Very Low'

&#x20;10      END AS sales\_bucket

&#x20;11  FROM orders o

&#x20;12  JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20;13  JOIN products p ON oi.product\_id = p.product\_id

&#x20;14  GROUP BY TRUNC(o.order\_date, 'IW')

&#x20;15  ORDER BY week\_start;



WEEK\_STAR ORDERS\_IN\_WEEK TOTAL\_SALES SALES\_BU

\--------- -------------- ----------- --------

23-FEB-26              1        2500 Very Low

02-MAR-26              4       69000 High

09-MAR-26              6       94900 High

16-MAR-26              5      144500 High

23-MAR-26              7      269300 High

30-MAR-26              5      417600 High

&#x20;                      1       50000 Medium



7 rows selected.



**SQL> -- 42. Bucket customers by order-value distribution.**



SQL> WITH customer\_totals AS (

&#x20; 2      SELECT c.customer\_id,

&#x20; 3             c.name,

&#x20; 4             NVL(SUM(p.price \* oi.quantity), 0) AS total\_spent

&#x20; 5      FROM customers c

&#x20; 6      LEFT JOIN orders o ON c.customer\_id = o.customer\_id

&#x20; 7      LEFT JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20; 8      LEFT JOIN products p ON oi.product\_id = p.product\_id

&#x20; 9      GROUP BY c.customer\_id, c.name

&#x20;10  )

&#x20;11  SELECT name,

&#x20;12         total\_spent,

&#x20;13         NTILE(5) OVER (ORDER BY total\_spent DESC) AS spending\_bucket

&#x20;14  FROM customer\_totals;



NAME                                                                                                 TOTAL\_SPENT SPENDING\_BUCKET

\---------------------------------------------------------------------------------------------------- ----------- ---------------

Roshika                                                                                                   267000               1

Varun                                                                                                     162000               1

Ananya                                                                                                     86100               1

Deepa                                                                                                      71500               1

Ravi                                                                                                       60000               1

Pooja                                                                                                      51500               1

Vikram                                                                                                     46500               2

Sita                                                                                                       46000               2

Lavanya                                                                                                    36500               2

Harsha                                                                                                     34000               2

Anita                                                                                                      24000               2

Arjun                                                                                                      20000               2

Neha                                                                                                       20000               3

Gopi                                                                                                       16000               3

Nikhil                                                                                                     10000               3

Manoj                                                                                                       8000               3

Sneha                                                                                                       7500               3

Teja                                                                                                        6000               3

Kiran                                                                                                       4500               4

Ramesh                                                                                                      4000               4

Vikram                                                                                                      3500               4

Meena                                                                                                       3000               4

Asha                                                                                                        2500               4

Divya                                                                                                       2400               4

Priyanka                                                                                                    2000               5

Rahul                                                                                                       1500               5

Suresh                                                                                                      1200               5

Priya                                                                                                        600               5

Karthik                                                                                                        0               5



29 rows selected.



**SQL> -- 43. Categorize products by sales frequency.**

SQL>

SQL> WITH product\_sales AS (

&#x20; 2      SELECT p.product\_id,

&#x20; 3             p.product\_name,

&#x20; 4             COUNT(oi.order\_item\_id) AS total\_orders

&#x20; 5      FROM products p

&#x20; 6      LEFT JOIN order\_items oi ON p.product\_id = oi.product\_id

&#x20; 7      GROUP BY p.product\_id, p.product\_name

&#x20; 8  )

&#x20; 9  SELECT product\_name,

&#x20;10         total\_orders,

&#x20;11         CASE

&#x20;12             WHEN total\_orders = 0 THEN 'Never Sold'

&#x20;13             WHEN total\_orders >= 5 THEN 'High Demand'

&#x20;14             WHEN total\_orders >= 2 THEN 'Medium Demand'

&#x20;15             ELSE 'Low Demand'

&#x20;16         END AS sales\_category

&#x20;17  FROM product\_sales;



PRODUCT\_NAME                                                                                         TOTAL\_ORDERS SALES\_CATEGOR

\---------------------------------------------------------------------------------------------------- ------------ -------------

Laptop                                                                                                          6 High Demand

Printer                                                                                                         2 Medium Demand

RAM                                                                                                             3 Medium Demand

SSD                                                                                                             3 Medium Demand

Keyboard                                                                                                        7 High Demand

Tablet                                                                                                          3 Medium Demand

Monitor                                                                                                         5 High Demand

USB Cable                                                                                                       3 Medium Demand

Webcam                                                                                                          3 Medium Demand

Router                                                                                                          2 Medium Demand

Mouse                                                                                                           6 High Demand

Headphones                                                                                                      4 Medium Demand

Charger                                                                                                         3 Medium Demand

Speaker                                                                                                         3 Medium Demand

Power Bank                                                                                                      2 Medium Demand



15 rows selected.



**SQL> -- 44. Bucket customers by average order gap.**

SQL>

SQL> WITH order\_gaps AS (

&#x20; 2      SELECT o.customer\_id,

&#x20; 3             o.order\_date,

&#x20; 4             o.order\_date - LAG(o.order\_date) OVER (

&#x20; 5                 PARTITION BY o.customer\_id ORDER BY o.order\_date

&#x20; 6             ) AS gap\_days

&#x20; 7      FROM orders o

&#x20; 8  ),

&#x20; 9  avg\_gaps AS (

&#x20;10      SELECT customer\_id,

&#x20;11             AVG(gap\_days) AS avg\_gap

&#x20;12      FROM order\_gaps

&#x20;13      GROUP BY customer\_id

&#x20;14  )

&#x20;15  SELECT customer\_id,

&#x20;16         avg\_gap,

&#x20;17         CASE

&#x20;18             WHEN avg\_gap <= 30 THEN 'Frequent'

&#x20;19             WHEN avg\_gap <= 90 THEN 'Regular'

&#x20;20             ELSE 'Rare'

&#x20;21         END AS gap\_bucket

&#x20;22  FROM avg\_gaps;



CUSTOMER\_ID    AVG\_GAP GAP\_BUCK

\----------- ---------- --------

&#x20;         1 .141388889 Frequent

&#x20;         2            Rare

&#x20;         3            Rare

&#x20;         4            Rare

&#x20;         5            Rare

&#x20;         6            Rare

&#x20;         7            Rare

&#x20;         8            Rare

&#x20;         9            Rare

&#x20;        10            Rare

&#x20;        11            Rare

&#x20;        12            Rare

&#x20;        13            Rare

&#x20;        14            Rare

&#x20;        15            Rare

&#x20;        16            Rare

&#x20;        17            Rare

&#x20;        18            Rare

&#x20;        19            Rare

&#x20;        20            Rare

&#x20;        21            Rare

&#x20;        22            Rare

&#x20;        23            Rare

&#x20;        24            Rare

&#x20;        25            Rare

&#x20;        26            Rare

&#x20;        27            Rare

&#x20;        28            Rare

&#x20;        30            Rare

&#x20;                      Rare



30 rows selected.



**SQL> -- 45. Rank customers by total ordered amount; return top 5 per email domain.**







**SQL> -- 46. Compute running total of sales per customer.**

SQL>

SQL> SELECT c.customer\_id,

&#x20; 2         o.order\_id,

&#x20; 3         o.order\_date,

&#x20; 4         SUM(p.price \* oi.quantity) AS order\_value,

&#x20; 5         SUM(SUM(p.price \* oi.quantity)) OVER (

&#x20; 6             PARTITION BY c.customer\_id

&#x20; 7             ORDER BY o.order\_date

&#x20; 8         ) AS running\_total

&#x20; 9  FROM customers c

&#x20;10  JOIN orders o ON c.customer\_id = o.customer\_id

&#x20;11  JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20;12  JOIN products p ON oi.product\_id = p.product\_id

&#x20;13  GROUP BY c.customer\_id, o.order\_id, o.order\_date

&#x20;14  ORDER BY c.customer\_id, o.order\_date;



CUSTOMER\_ID   ORDER\_ID ORDER\_DAT ORDER\_VALUE RUNNING\_TOTAL

\----------- ---------- --------- ----------- -------------

&#x20;         1          1 28-FEB-26        2500          2500

&#x20;         2          2 02-MAR-26       60000         60000

&#x20;         3          3 04-MAR-26        4500          4500

&#x20;         4          4 06-MAR-26        3000          3000

&#x20;         5          5 08-MAR-26        1500          1500

&#x20;         6          6 10-MAR-26        2400          2400

&#x20;         7          7 11-MAR-26       20000         20000

&#x20;         8          8 12-MAR-26        7500          7500

&#x20;         9          9 13-MAR-26        3500          3500

&#x20;        10         10 14-MAR-26       51500         51500

&#x20;        11         11 15-MAR-26       10000         10000

&#x20;        12         12 16-MAR-26       24000         24000

&#x20;        13         13 17-MAR-26        4000          4000

&#x20;        14         14 18-MAR-26       46000         46000

&#x20;        16         16 21-MAR-26       36500         36500

&#x20;        17         17 22-MAR-26       34000         34000

&#x20;        18         18 23-MAR-26        6000          6000

&#x20;        19         19 24-MAR-26       71500         71500

&#x20;        20         20 25-MAR-26      162000        162000

&#x20;        21         21 26-MAR-26        8000          8000

&#x20;        22         22 27-MAR-26         600           600

&#x20;        23         23 28-MAR-26        1200          1200

&#x20;        24         24 29-MAR-26       20000         20000

&#x20;        25         25 30-MAR-26       16000         16000

&#x20;        26         26 31-MAR-26      267000        267000

&#x20;        27         27 31-MAR-26       86100         86100

&#x20;        28         28 31-MAR-26       46500         46500

&#x20;        30         30 02-APR-26        2000          2000



28 rows selected.



**SQL> -- 47. Find first and last orders using window functions.**

SQL>

SQL> SELECT customer\_id,

&#x20; 2         FIRST\_VALUE(order\_id) OVER (

&#x20; 3             PARTITION BY customer\_id ORDER BY order\_date

&#x20; 4         ) AS first\_order,

&#x20; 5         FIRST\_VALUE(order\_id) OVER (

&#x20; 6             PARTITION BY customer\_id ORDER BY order\_date DESC

&#x20; 7         ) AS last\_order

&#x20; 8  FROM orders;



CUSTOMER\_ID FIRST\_ORDER LAST\_ORDER

\----------- ----------- ----------

&#x20;         1           1        101

&#x20;         1           1        101

&#x20;         2           2          2

&#x20;         3           3          3

&#x20;         4           4          4

&#x20;         5           5          5

&#x20;         6           6          6

&#x20;         7           7          7

&#x20;         8           8          8

&#x20;         9           9          9

&#x20;        10          10         10

&#x20;        11          11         11

&#x20;        12          12         12

&#x20;        13          13         13

&#x20;        14          14         14

&#x20;        15          15         15

&#x20;        16          16         16

&#x20;        17          17         17

&#x20;        18          18         18

&#x20;        19          19         19

&#x20;        20          20         20

&#x20;        21          21         21

&#x20;        22          22         22

&#x20;        23          23         23

&#x20;        24          24         24

&#x20;        25          25         25

&#x20;        26          26         26

&#x20;        27          27         27

&#x20;        28          28         28

&#x20;        30          30         30

&#x20;                    31         31



31 rows selected.



**SQL> -- 48. Calculate days between customer orders using LAG.**

SQL>

SQL> SELECT customer\_id,

&#x20; 2         order\_id,

&#x20; 3         order\_date,

&#x20; 4         order\_date - LAG(order\_date) OVER (

&#x20; 5             PARTITION BY customer\_id ORDER BY order\_date

&#x20; 6         ) AS days\_gap

&#x20; 7  FROM orders;



CUSTOMER\_ID   ORDER\_ID ORDER\_DAT   DAYS\_GAP

\----------- ---------- --------- ----------

&#x20;         1          1 28-FEB-26

&#x20;         1        101 01-MAR-26 .141388889

&#x20;         2          2 02-MAR-26

&#x20;         3          3 04-MAR-26

&#x20;         4          4 06-MAR-26

&#x20;         5          5 08-MAR-26

&#x20;         6          6 10-MAR-26

&#x20;         7          7 11-MAR-26

&#x20;         8          8 12-MAR-26

&#x20;         9          9 13-MAR-26

&#x20;        10         10 14-MAR-26

&#x20;        11         11 15-MAR-26

&#x20;        12         12 16-MAR-26

&#x20;        13         13 17-MAR-26

&#x20;        14         14 18-MAR-26

&#x20;        15         15 19-MAR-26

&#x20;        16         16 21-MAR-26

&#x20;        17         17 22-MAR-26

&#x20;        18         18 23-MAR-26

&#x20;        19         19 24-MAR-26

&#x20;        20         20 25-MAR-26

&#x20;        21         21 26-MAR-26

&#x20;        22         22 27-MAR-26

&#x20;        23         23 28-MAR-26

&#x20;        24         24 29-MAR-26

&#x20;        25         25 30-MAR-26

&#x20;        26         26 31-MAR-26

&#x20;        27         27 31-MAR-26

&#x20;        28         28 31-MAR-26

&#x20;        30         30 02-APR-26

&#x20;                   31



31 rows selected.



**SQL> -- 49. Identify top-selling product per month using RANK.**

SQL>

SQL> WITH monthly\_sales AS (

&#x20; 2      SELECT TO\_CHAR(o.order\_date, 'YYYY-MM') AS month,

&#x20; 3             p.product\_name,

&#x20; 4             SUM(oi.quantity) AS total\_qty,

&#x20; 5             RANK() OVER (

&#x20; 6                 PARTITION BY TO\_CHAR(o.order\_date, 'YYYY-MM')

&#x20; 7                 ORDER BY SUM(oi.quantity) DESC

&#x20; 8             ) AS rnk

&#x20; 9      FROM orders o

&#x20;10      JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20;11      JOIN products p ON oi.product\_id = p.product\_id

&#x20;12      GROUP BY TO\_CHAR(o.order\_date, 'YYYY-MM'), p.product\_name

&#x20;13  )

&#x20;14  SELECT month, product\_name, total\_qty

&#x20;15  FROM monthly\_sales

&#x20;16  WHERE rnk = 1;



MONTH   PRODUCT\_NAME                                                                                          TOTAL\_QTY

\------- ---------------------------------------------------------------------------------------------------- ----------

2026-02 Mouse                                                                                                         2

2026-03 Headphones                                                                                                   14

2026-04 Keyboard                                                                                                      1

2026-04 Mouse                                                                                                         1

&#x20;       Laptop                                                                                                        1



**SQL> -- 50. Compute moving average of product quantities.**

SQL>

SQL> SELECT product\_id,

&#x20; 2         quantity,

&#x20; 3         AVG(quantity) OVER (

&#x20; 4             PARTITION BY product\_id

&#x20; 5             ORDER BY order\_item\_id

&#x20; 6             ROWS BETWEEN 2 PRECEDING AND CURRENT ROW

&#x20; 7         ) AS moving\_avg

&#x20; 8  FROM order\_items;



PRODUCT\_ID   QUANTITY MOVING\_AVG

\---------- ---------- ----------

&#x20;        1          2          2

&#x20;        1          1        1.5

&#x20;        1          6          3

&#x20;        1          4 3.66666667

&#x20;        1          2          4

&#x20;        1          1 2.33333333

&#x20;        2          1          1

&#x20;        2          2        1.5

&#x20;        2          1 1.33333333

&#x20;        2          1 1.33333333

&#x20;        2          1          1

&#x20;        2          4          2

&#x20;        2          1          2

&#x20;        3          1          1

&#x20;        3          1          1

&#x20;        3          2 1.33333333

&#x20;        3          5 2.66666667

&#x20;        3          1 2.66666667

&#x20;        4          1          1

&#x20;        4          1          1

&#x20;        4          1          1

&#x20;        4          2 1.33333333

&#x20;        4          5 2.66666667

&#x20;        4          1 2.66666667

&#x20;        5          2          2

&#x20;        5          4          3

&#x20;        5          6          4

&#x20;        5          2          4

&#x20;        6          5          5

&#x20;        6          2        3.5

&#x20;        6          3 3.33333333

&#x20;        7          2          2

&#x20;        7          1        1.5

&#x20;        7          1 1.33333333

&#x20;        8          1          1

&#x20;        8          1          1

&#x20;        8          4          2

&#x20;        9          3          3

&#x20;        9          5          4

&#x20;        9          2 3.33333333

&#x20;       10          1          1

&#x20;       10          1          1

&#x20;       10          1          1

&#x20;       11          4          4

&#x20;       11          3        3.5

&#x20;       11          3 3.33333333

&#x20;       12          1          1

&#x20;       12          4        2.5

&#x20;       12          5 3.33333333

&#x20;       13          5          5

&#x20;       13          2        3.5

&#x20;       14          2          2

&#x20;       14          5        3.5

&#x20;       15          1          1

&#x20;       15          1          1



55 rows selected.



**SQL> --51. Detect customers who stopped purchasing for 60 days via LEAD.**



SQL> -- 51. Detect customers who stopped purchasing for 60 days via LEAD.

SQL>

SQL> SELECT customer\_id,

&#x20; 2         order\_date,

&#x20; 3         next\_order\_date,

&#x20; 4         gap\_days

&#x20; 5  FROM (

&#x20; 6      SELECT customer\_id,

&#x20; 7             order\_date,

&#x20; 8             LEAD(order\_date) OVER (PARTITION BY customer\_id ORDER BY order\_date) AS next\_order\_date,

&#x20; 9             LEAD(order\_date) OVER (PARTITION BY customer\_id ORDER BY order\_date) - order\_date AS gap\_days

&#x20;10      FROM orders

&#x20;11  )

&#x20;12  WHERE gap\_days > 60;



no rows selected



**SQL> --52. Calculate percentage contribution of each item to order total.**



SQL> SELECT oi.order\_id,

&#x20; 2         oi.product\_id,

&#x20; 3         (oi.quantity \* p.price) AS item\_value,

&#x20; 4         SUM(oi.quantity \* p.price) OVER (PARTITION BY oi.order\_id) AS order\_total,

&#x20; 5         ROUND(

&#x20; 6             (oi.quantity \* p.price) /

&#x20; 7             SUM(oi.quantity \* p.price) OVER (PARTITION BY oi.order\_id) \* 100, 2

&#x20; 8         ) AS percentage\_contribution

&#x20; 9  FROM order\_items oi

&#x20;10  JOIN products p ON oi.product\_id = p.product\_id;



&#x20; ORDER\_ID PRODUCT\_ID ITEM\_VALUE ORDER\_TOTAL PERCENTAGE\_CONTRIBUTION

\---------- ---------- ---------- ----------- -----------------------

&#x20;        1          1       1000        2500                      40

&#x20;        1          2       1500        2500                      60

&#x20;        2          4      50000       60000                   83.33

&#x20;        2          3      10000       60000                   16.67

&#x20;        3          5       4000        4500                   88.89

&#x20;        3          1        500        4500                   11.11

&#x20;        4          2       3000        3000                     100

&#x20;        5          6       1500        1500                     100

&#x20;        6          7       2400        2400                     100

&#x20;        7          8      20000       20000                     100

&#x20;        8          9       7500        7500                     100

&#x20;        9         10       3500        3500                     100

&#x20;       10          2       1500       51500                    2.91

&#x20;       10          4      50000       51500                   97.09

&#x20;       11          3      10000       10000                     100

&#x20;       12         11      24000       24000                     100

&#x20;       13         12       4000        4000                     100

&#x20;       14         13      40000       46000                   86.96

&#x20;       14         14       6000       46000                   13.04

&#x20;       16         15       2500       36500                    6.85

&#x20;       16         12      16000       36500                   43.84

&#x20;       16         11      18000       36500                   49.32

&#x20;       17         13      16000       34000                   47.06

&#x20;       17          1       3000       34000                    8.82

&#x20;       17         14      15000       34000                   44.12

&#x20;       18          1       2000        6000                   33.33

&#x20;       18         15       2500        6000                   41.67

&#x20;       18          2       1500        6000                      25

&#x20;       19          3      20000       71500                   27.97

&#x20;       19          3      50000       71500                   69.93

&#x20;       19          2       1500       71500                     2.1

&#x20;       20          5      12000      162000                    7.41

&#x20;       20          4     100000      162000                   61.73

&#x20;       20          4      50000      162000                   30.86

&#x20;       21          5       8000        8000                     100

&#x20;       22          6        600         600                     100

&#x20;       23          7       1200        1200                     100

&#x20;       24          8      20000       20000                     100

&#x20;       25         10       3500       16000                   21.88

&#x20;       25          9      12500       16000                   78.13

&#x20;       26          2       6000      267000                    2.25

&#x20;       26          4     250000      267000                   93.63

&#x20;       26          1       1000      267000                     .37

&#x20;       26          3      10000      267000                    3.75

&#x20;       27          6        900       86100                    1.05

&#x20;       27          5       4000       86100                    4.65

&#x20;       27          8      80000       86100                   92.92

&#x20;       27          7       1200       86100                    1.39

&#x20;       28         10       3500       46500                    7.53

&#x20;       28          9       5000       46500                   10.75

&#x20;       28         12      20000       46500                   43.01

&#x20;       28         11      18000       46500                   38.71

&#x20;       30          2       1500        2000                      75

&#x20;       30          1        500        2000                      25

&#x20;       31          4      50000       50000                     100



55 rows selected.



**SQL> --53. Dense-rank products by total quantity sold.**



SQL> SELECT product\_id,

&#x20; 2         total\_quantity,

&#x20; 3         DENSE\_RANK() OVER (ORDER BY total\_quantity DESC) AS rank\_position

&#x20; 4  FROM (

&#x20; 5      SELECT product\_id,

&#x20; 6             SUM(quantity) AS total\_quantity

&#x20; 7      FROM order\_items

&#x20; 8      GROUP BY product\_id

&#x20; 9  );



PRODUCT\_ID TOTAL\_QUANTITY RANK\_POSITION

\---------- -------------- -------------

&#x20;        1             16             1

&#x20;        5             14             2

&#x20;        2             11             3

&#x20;        4             11             3

&#x20;       11             10             4

&#x20;        6             10             4

&#x20;       12             10             4

&#x20;        9             10             4

&#x20;        3             10             4

&#x20;       13              7             5

&#x20;       14              7             5

&#x20;        8              6             6

&#x20;        7              4             7

&#x20;       10              3             8

&#x20;       15              2             9



15 rows selected.



**SQL> --54. Compute cumulative payments per order and find unpaid ones.**



SQL> SELECT o.order\_id

&#x20; 2  FROM orders o

&#x20; 3  LEFT JOIN payments p ON o.order\_id = p.order\_id

&#x20; 4  WHERE p.order\_id IS NULL;



&#x20; ORDER\_ID

\----------

&#x20;       26

&#x20;       15

&#x20;       21

&#x20;       18

&#x20;       30

&#x20;       28

&#x20;       23

&#x20;       25

&#x20;       24

&#x20;       31

&#x20;       12

&#x20;       19

&#x20;       17

&#x20;       14

&#x20;       16

&#x20;       13

&#x20;       22

&#x20;       20

&#x20;       11

&#x20;       27



20 rows selected.



**SQL> --55. Identify second most purchased product per customer using window functions.**



SQL> SELECT customer\_id,

&#x20; 2         product\_id,

&#x20; 3         total\_quantity

&#x20; 4  FROM (

&#x20; 5      SELECT o.customer\_id,

&#x20; 6             oi.product\_id,

&#x20; 7             SUM(oi.quantity) AS total\_quantity,

&#x20; 8             ROW\_NUMBER() OVER (

&#x20; 9                 PARTITION BY o.customer\_id

&#x20;10                 ORDER BY SUM(oi.quantity) DESC

&#x20;11             ) AS rnk

&#x20;12      FROM orders o

&#x20;13      JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20;14      GROUP BY o.customer\_id, oi.product\_id

&#x20;15  )

&#x20;16  WHERE rnk = 2;



CUSTOMER\_ID PRODUCT\_ID TOTAL\_QUANTITY

\----------- ---------- --------------

&#x20;         1          2              1

&#x20;         2          3              1

&#x20;         3          1              1

&#x20;        10          2              1

&#x20;        14         14              2

&#x20;        16         11              3

&#x20;        17         14              5

&#x20;        18         15              1

&#x20;        19          2              1

&#x20;        20          4              3

&#x20;        25         10              1

&#x20;        26          2              4

&#x20;        27          6              3

&#x20;        28         11              3

&#x20;        30          1              1



15 rows selected.



**SQL> --56. Rank customers per product by quantity purchased.**



SQL> SELECT product\_id,

&#x20; 2         customer\_id,

&#x20; 3         total\_quantity,

&#x20; 4         RANK() OVER (

&#x20; 5             PARTITION BY product\_id

&#x20; 6             ORDER BY total\_quantity DESC

&#x20; 7         ) AS rank\_position

&#x20; 8  FROM (

&#x20; 9      SELECT o.customer\_id,

&#x20;10             oi.product\_id,

&#x20;11             SUM(oi.quantity) AS total\_quantity

&#x20;12      FROM orders o

&#x20;13      JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20;14      GROUP BY o.customer\_id, oi.product\_id

&#x20;15  );



PRODUCT\_ID CUSTOMER\_ID TOTAL\_QUANTITY RANK\_POSITION

\---------- ----------- -------------- -------------

&#x20;        1          17              6             1

&#x20;        1          18              4             2

&#x20;        1          26              2             3

&#x20;        1           1              2             3

&#x20;        1          30              1             5

&#x20;        1           3              1             5

&#x20;        2          26              4             1

&#x20;        2           4              2             2

&#x20;        2          10              1             3

&#x20;        2          18              1             3

&#x20;        2          19              1             3

&#x20;        2          30              1             3

&#x20;        2           1              1             3



**SQL> --57. Find orders above the 90th percentile in value.**



SQL> WITH order\_values AS (

&#x20; 2      SELECT oi.order\_id,

&#x20; 3             SUM(oi.quantity \* p.price) AS order\_value

&#x20; 4      FROM order\_items oi

&#x20; 5      JOIN products p ON oi.product\_id = p.product\_id

&#x20; 6      GROUP BY oi.order\_id

&#x20; 7  )

&#x20; 8  SELECT order\_id, order\_value

&#x20; 9  FROM order\_values

&#x20;10  WHERE order\_value >

&#x20;11  (

&#x20;12      SELECT PERCENTILE\_CONT(0.9) WITHIN GROUP (ORDER BY order\_value)

&#x20;13      FROM order\_values

&#x20;14  );



&#x20; ORDER\_ID ORDER\_VALUE

\---------- -----------

&#x20;       26      267000

&#x20;       20      162000

&#x20;       27       86100



**SQL> --58. Split customers into quartiles quartiles based on spending.**



SQL> WITH customer\_spending AS (

&#x20; 2      SELECT o.customer\_id,

&#x20; 3             SUM(oi.quantity \* p.price) AS total\_spending

&#x20; 4      FROM orders o

&#x20; 5      JOIN order\_items oi ON o.order\_id = oi.order\_id

&#x20; 6      JOIN products p ON oi.product\_id = p.product\_id

&#x20; 7      GROUP BY o.customer\_id

&#x20; 8  )

&#x20; 9  SELECT customer\_id,

&#x20;10         total\_spending,

&#x20;11         NTILE(4) OVER (ORDER BY total\_spending DESC) AS spending\_quartile

&#x20;12  FROM customer\_spending;



CUSTOMER\_ID TOTAL\_SPENDING SPENDING\_QUARTILE

\----------- -------------- -----------------

&#x20;        26         267000                 1

&#x20;        20         162000                 1

&#x20;        27          86100                 1

&#x20;        19          71500                 1

&#x20;         2          60000                 1

&#x20;        10          51500                 1

&#x20;                    50000                 1

&#x20;        28          46500                 1

&#x20;        14          46000                 2

&#x20;        16          36500                 2

&#x20;        17          34000                 2

&#x20;        12          24000                 2

&#x20;        24          20000                 2

&#x20;         7          20000                 2

&#x20;        25          16000                 2

&#x20;        11          10000                 3

&#x20;        21           8000                 3

&#x20;         8           7500                 3

&#x20;        18           6000                 3

&#x20;         3           4500                 3

&#x20;        13           4000                 3

&#x20;         9           3500                 3

&#x20;         4           3000                 4

&#x20;         1           2500                 4

&#x20;         6           2400                 4

&#x20;        30           2000                 4

&#x20;         5           1500                 4

&#x20;        23           1200                 4

&#x20;        22            600                 4



29 rows selected.





