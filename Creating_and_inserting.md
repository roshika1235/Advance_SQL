CREATION OF TABLES



SQL> create table Customers(customer\_id number constraint pk\_cust\_id primary key , name varchar2(100), email varchar2(100) , phone varchar2(20));



Table created.



SQL> create table Products(product\_id number constraint pk\_prod\_id primary key , product\_name varchar2(100) , price number(10,2));



Table created.



SQL> create table Inventory(inventory\_id number constraint pk\_inv\_id primary key , product\_id number, quantity\_on\_hand number , last\_updated date , constraint fk\_inv\_prodid foreign key (product\_id) references Products(product\_id));



Table created.



SQL> create table Orders(order\_id number constraint pk\_ord\_id primary key , customer\_id number ,order\_date date , constraint fk\_cust\_id foreign key (customer\_id) references Customers(customer\_id));



Table created.



SQL> create table Order\_items(order\_item\_id number constraint pf\_oit\_id primary key , order\_id number , product\_id number , quantity number , constraint fk\_or\_id foreign key (order\_id) references Orders(order\_id) , constraint fk\_orit\_pid foreign key (product\_id) references Products(product\_id));



Table created.



SQL> create table Payments(payment\_id number constraint pk\_payid primary key , order\_id number , amount number(10,2) , payment\_method varchar2(50) , payment\_date date , constraint fk\_pay\_orid foreign key (order\_id) references Orders(order\_id));



Table created.



SQL> alter table Order\_items rename constraint pf\_oit\_id to pk\_oit\_id;



Table altered.



SQL> spool off;

&#x20;

INSERTION



SQL> insert into Customers values (1,'Asha','asha@gmail.com','9000000001');



1 row created.



SQL> insert into Customers values (2,'Ravi','ravi@gmail.com','9000000002');



1 row created.



SQL> insert into Customers values (3,'Kiran','kiran@gmail.com','9000000003');



1 row created.



SQL> insert into Customers values (4,'Meena','meena@gmail.com','9000000004');



1 row created.



SQL> insert into Customers values (5,'Rahul','rahul@gmail.com','9000000005');



1 row created.



SQL> insert into Customers values (6,'Divya','divya@gmail.com','9000000006');



1 row created.



SQL> insert into Customers values (7,'Arjun','arjun@gmail.com','9000000007');



1 row created.



SQL> insert into Customers values (8,'Sneha','sneha@gmail.com','9000000008');



1 row created.



SQL> insert into Customers values (9,'Vikram','vikram@gmail.com','9000000009');



1 row created.



SQL> insert into Customers values (10,'Pooja','pooja@gmail.com','9000000010');



1 row created.



SQL> insert into Customers values (11,'Nikhil','nikhil@gmail.com','9000000011');



1 row created.



SQL> insert into Customers values (12,'Anita','anita@gmail.com','9000000012');



1 row created.



SQL> insert into Customers values (13,'Ramesh','ramesh@gmail.com','9000000013');



1 row created.



SQL> insert into Customers values (14,'Sita','sita@gmail.com','9000000014');



1 row created.



SQL> insert into Customers values (15,'Karthik','karthik@gmail.com','9000000015');



1 row created.



SQL> insert into Customers values (16,'Lavanya','lavanya@gmail.com','9000000016');



1 row created.



SQL> insert into Customers values (17,'Harsha','harsha@gmail.com','9000000017');



1 row created.



SQL> insert into Customers values (18,'Teja','teja@gmail.com','9000000018');



1 row created.



SQL> insert into Customers values (19,'Deepa','deepa@gmail.com','9000000019');



1 row created.



SQL> insert into Customers values (20,'Varun','varun@gmail.com','9000000020');



1 row created.



SQL> insert into Customers values (21,'Manoj','manoj@gmail.com','9000000021');



1 row created.



SQL> insert into Customers values (22,'Priya','priya@gmail.com','9000000022');



1 row created.



SQL> insert into Customers values (23,'Suresh','suresh@gmail.com','9000000023');



1 row created.



SQL> insert into Customers values (24,'Neha','neha@gmail.com','9000000024');



1 row created.



SQL> insert into Customers values (25,'Gopi','gopi@gmail.com','9000000025');



1 row created.



SQL> insert into Products values (1,'Mouse',500);



1 row created.



SQL> insert into Products values (2,'Keyboard',1500);



1 row created.



SQL> insert into Products values (3,'Monitor',10000);



1 row created.



SQL> insert into Products values (4,'Laptop',50000);



1 row created.



SQL> insert into Products values (5,'Headphones',2000);



1 row created.



SQL> insert into Products values (6,'USB Cable',300);



1 row created.



SQL> insert into Products values (7,'Charger',1200);



1 row created.



SQL> insert into Products values (8,'Tablet',20000);



1 row created.



SQL> insert into Products values (9,'Speaker',2500);



1 row created.



SQL> insert into Products values (10,'Webcam',3500);



1 row created.



SQL> insert into Products values (11,'SSD',6000);



1 row created.



SQL> insert into Products values (12,'RAM',4000);



1 row created.



SQL> insert into Products values (13,'Printer',8000);



1 row created.



SQL> insert into Products values (14,'Router',3000);



1 row created.



SQL> insert into Products values (15,'Power Bank',2500);



1 row created.



SQL> insert into Inventory values (1,1,50,sysdate-10);



1 row created.



SQL> insert into Inventory values (2,2,5,sysdate-20);



1 row created.



SQL> insert into Inventory values (3,3,15,sysdate-5);



1 row created.



SQL> insert into Inventory values (4,4,2,sysdate-15);



1 row created.



SQL> insert into Inventory values (5,5,0,sysdate-30);



1 row created.



SQL> insert into Inventory values (6,6,100,sysdate-1);



1 row created.



SQL> insert into Inventory values (7,7,10,sysdate-3);



1 row created.



SQL> insert into Inventory values (8,8,8,sysdate-25);



1 row created.



SQL> insert into Inventory values (9,9,0,sysdate-40);



1 row created.



SQL> insert into Inventory values (10,10,12,sysdate-7);



1 row created.



SQL> insert into Inventory values (11,11,6,sysdate-9);



1 row created.



SQL> insert into Inventory values (12,12,20,sysdate-11);



1 row created.



SQL> insert into Inventory values (13,13,0,sysdate-50);



1 row created.



SQL> insert into Inventory values (14,14,30,sysdate-13);



1 row created.



SQL> insert into Inventory values (15,15,7,sysdate-18);



1 row created.



SQL> insert into Orders values (1,1,sysdate-20);



1 row created.



SQL> insert into Orders values (2,2,sysdate-18);



1 row created.



SQL> insert into Orders values (3,3,sysdate-16);



1 row created.



SQL> insert into Orders values (4,4,sysdate-14);



1 row created.



SQL> insert into Orders values (5,5,sysdate-12);



1 row created.



SQL> insert into Orders values (6,6,sysdate-10);



1 row created.



SQL> insert into Orders values (7,7,sysdate-9);



1 row created.



SQL> insert into Orders values (8,8,sysdate-8);



1 row created.



SQL> insert into Orders values (9,9,sysdate-7);



1 row created.



SQL> insert into Orders values (10,10,sysdate-6);



1 row created.



SQL> insert into Orders values (11,11,sysdate-5);



1 row created.



SQL> insert into Orders values (12,12,sysdate-4);



1 row created.



SQL> insert into Orders values (13,13,sysdate-3);



1 row created.



SQL> insert into Orders values (14,14,sysdate-2);



1 row created.



SQL> insert into Orders values (15,15,sysdate-1);



1 row created.



SQL> insert into Order\_items values (1,1,1,2);



1 row created.



SQL> insert into Order\_items values (2,1,2,1);



1 row created.



SQL> insert into Order\_items values (3,2,3,1);



1 row created.



SQL> insert into Order\_items values (4,2,4,1);



1 row created.



SQL> insert into Order\_items values (5,3,5,2);



1 row created.



SQL> insert into Order\_items values (6,3,1,1);



1 row created.



SQL> insert into Order\_items values (7,4,2,2);



1 row created.



SQL> insert into Order\_items values (8,5,6,5);



1 row created.



SQL> insert into Order\_items values (9,6,7,2);



1 row created.



SQL> insert into Order\_items values (10,7,8,1);



1 row created.



SQL> insert into Order\_items values (11,8,9,3);



1 row created.



SQL> insert into Order\_items values (12,9,10,1);



1 row created.



SQL> insert into Order\_items values (13,10,4,1);



1 row created.



SQL> insert into Order\_items values (14,10,2,1);



1 row created.



SQL> insert into Order\_items values (15,11,3,1);



1 row created.



SQL> insert into Payments values (1,1,2500,'UPI',sysdate-19);



1 row created.



SQL> insert into Payments values (2,2,60000,'CARD',sysdate-17);



1 row created.



SQL> insert into Payments values (3,3,3000,'CASH',sysdate-15);



1 row created.



SQL> insert into Payments values (4,4,3000,'UPI',sysdate-13);



1 row created.



SQL> insert into Payments values (5,5,1500,'CARD',sysdate-11);



1 row created.



SQL> insert into Payments values (6,6,2400,'UPI',sysdate-9);



1 row created.



SQL> insert into Payments values (7,7,20000,'CARD',sysdate-8);



1 row created.



SQL> insert into Payments values (8,8,7500,'UPI',sysdate-7);



1 row created.



SQL> insert into Payments values (9,9,3500,'UPI',sysdate-6);



1 row created.



SQL> insert into Payments values (10,10,51500,'CARD',sysdate-5);



1 row created.



SQL> spool off;











**\*\* INSERTING FEW MORE RECORDS \*\***



SQL> insert into Orders values (16,16,sysdate);



1 row created.



SQL> insert into Orders values (17,17,sysdate+1);



1 row created.



SQL> insert into Orders values (18,18,sysdate+2);



1 row created.



SQL> insert into Orders values (19,19,sysdate+3);



1 row created.



SQL> insert into Orders values (20,20,sysdate+4);



1 row created.



SQL> insert into Orders values (21,21,sysdate+5);



1 row created.



SQL> insert into Orders values (22,22,sysdate+6);



1 row created.



SQL> insert into Orders values (23,23,sysdate+7);



1 row created.



SQL> insert into Orders values (24,24,sysdate+8);



1 row created.



SQL> insert into Orders values (25,25,sysdate+9);



1 row created.





