# car_rentals

-- creating a database and using it

create database db3;

use db3;

-- creating table vehicle

create table vehicle(
vehicleID int primary key,
make varchar(255),
model varchar(255),
year int,
dailyRate int,
available bool,
passengerCapacity int,
engineCapacity int);
 
describe vehicle;

-- modifying the dailyrate to decimal

alter table vehicle 
modify column dailyRate decimal(10,2);

-- inserting values into vehicle

insert into vehicle values
(1, 'Toyota', 'Camry', 2022, 50.00, 1, 4, 1450),
(2, 'Honda', 'Civic', 2023, 45.00, 1, 7, 1500),
(3, 'Ford', 'Focus', 2022, 48.00, 0, 4, 1400),
(4, 'Nissan', 'Altima', 2023, 52.00, 1, 7, 1200),
(5, 'Chevrolet', 'Malibu', 2022, 47.00, 1, 4, 1800),
(6, 'Hyundai', 'Sonata', 2023, 49.00, 0, 7, 1400),
(7, 'BMW', '3 Series', 2023, 60.00, 1, 7, 2499),
(8, 'Mercedes', 'C-Class', 2022, 58.00, 1, 8, 2599),
(9, 'Audi', 'A4', 2022, 55.00, 0, 4, 2500),
(10, 'Lexus', 'ES', 2023, 54.00,1, 4, 2500);

select * from vehicle;

 -- creating table customer

create table customer(
customerID int primary key,
firstName varchar(255),
lastName varchar(255),
email varchar(255),
phonenumber int);


desc customer;

-- modifying the column datatype
alter table customer
modify column phonenumber varchar(255);

-- inserting values into  customer 


insert into customer values
(1, 'John', 'Doe', 'johndoe@example.com', '555-555-5555'),
(2, 'Jane', 'Smith', 'janesmith@example.com', '555-123-4567'),
(3, 'Robert', 'Johnson', 'robert@example.com', '555-789-1234'),
(4, 'Sarah', 'Brown', 'sarah@example.com', '555-456-7890'),
(5, 'David', 'Lee', 'david@example.com', '555-987-6543'),
(6, 'Laura', 'Hall', 'laura@example.com', '555-234-5678'),
(7, 'Michael', 'Davis', 'michael@example.com', '555-876-5432'),
(8, 'Emma', 'Wilson', 'emma@example.com', '555-432-1098'),
(9, 'William', 'Taylor', 'william@example.com', '555-321-6547'),
(10, 'Olivia', 'Adams', 'olivia@example.com', '555-765-4321');

select * from customer;


-- creating table lease

create table lease(
leaseID int primary key,
vehicleID int,
customerID int,
startDate date,
endDate date,
type varchar(255),
foreign key(vehicleID) references vehicle(vehicleID) on delete cascade,
foreign key(customerID) references customer(customerID) on delete cascade );

desc lease;

-- inserting values into lease

insert into lease values
(1, 1, 1, '2023-01-01', '2023-01-05', 'Daily'),
(2, 2, 2, '2023-02-15', '2023-02-28', 'Monthly'),
(3, 3, 3, '2023-03-10', '2023-03-15', 'Daily'),
(4, 4, 4, '2023-04-20', '2023-04-30', 'Monthly'),
(5, 5, 5, '2023-05-05', '2023-05-10', 'Daily'),
(6, 4, 3, '2023-06-15', '2023-06-30', 'Monthly'),
(7, 7, 7, '2023-07-01', '2023-07-10', 'Daily'),
(8, 8, 8, '2023-08-12', '2023-08-15', 'Monthly'),
(9, 3, 3, '2023-09-07', '2023-09-10', 'Daily'),
(10, 10, 10, '2023-10-10', '2023-10-31', 'Monthly');

select * from lease;


-- creating table payment

create table payment(
paymentID int primary key,
leaseID int,
paymentDate date,
amount decimal(10,2),
foreign key(leaseID) references lease(leaseID) on delete cascade);

desc payment;

-- inserting values into payment

insert into payment values
(1, 1, '2023-01-03', 200.00),
(2, 2, '2023-02-20', 1000.00),
(3, 3, '2023-03-12', 75.00),
(4, 4, '2023-04-25', 900.00),
(5, 5, '2023-05-07', 60.00),
(6, 6, '2023-06-18', 1200.00),
(7, 7, '2023-07-03', 40.00),
(8, 8, '2023-08-14', 1100.00),
(9, 9, '2023-09-09', 80.00),
(10, 10, '2023-10-25', 1500.00);


select * from payment;


-- TASKS --

-- 1. Update the daily rate for a Mercedes car to 68.
set sql_safe_updates=0;
update vehicle set dailyRate=68.00 where make='Mercedes';
select * from vehicle;

-- 2. Delete a specific customer and all associated leases and payments.

delete from customer where customerID=8;

select * from customer;
select * from lease;
select * from payment;

-- 3. Rename the "paymentDate" column in the Payment table to "transactionDate".

alter table payment
rename column paymentDate to transactionDate;

select * from payment;

-- 4. Find a specific customer by email.

select * from customer where email='robert@example.com';

-- 5. Get active leases for a specific customer.

select * from lease where endDate >= current_timestamp() and customerID=7;

-- 6. Find all payments made by a customer with a specific phone number.

select * from payment where leaseID in (select leaseID from lease where customerID in (select customerID from customer where phonenumber='555-456-7890'));

-- 7. Calculate the average daily rate of all available cars.

select avg(dailyRate) from vehicle where available=1;

-- 8. Find the car with the highest daily rate.

select * from vehicle order by dailyRate desc limit 1;

-- 9.Retrieve all cars leased by a specific customer.

select * from vehicle v join lease l on v.vehicleID=l.vehicleID where l.customerID=3;

-- 10. Find the details of the most recent lease.

select * from lease order by startDate desc limit 1;

-- 11. List all payments made in the year 2023.

select * from payment where year(transactionDate)=2023;

-- 12. Retrieve customers who have not made any payments.

select * from customer where customerID not in(select customerID from lease where leaseID in (select leaseID from payment));

-- 13. Retrieve Car Details and Their Total Payments.

select v.*,sum(p.amount) as totalPayment from vehicle v join lease l on v.vehicleID=l.vehicleID 
join payment p on p.leaseID=l.leaseID
group by v.vehicleID;

-- 14. Calculate Total Payments for Each Customer.

select c.*,sum(p.amount) as totalPayment from customer c join lease l on c.customerID=l.customerID 
join payment p on p.leaseID=l.leaseID
group by c.customerID;

-- 15. List Car Details for Each Lease.

select * from vehicle v join lease l on v.vehicleID=l.vehicleID;

-- 16. Retrieve Details of Active Leases with Customer and Car Information.

select * from customer c join lease l on l.customerID=c.customerID
join vehicle v on l.vehicleID=v.vehicleID
 where endDate >= current_timestamp();
 
 -- 17. Find the Customer Who Has Spent the Most on Leases.
 
 select c.*,sum(p.amount) as totalPayment from customer c join lease l on c.customerID=l.customerID 
join payment p on p.leaseID=l.leaseID
group by c.customerID
order by totalPayment desc limit 1;

-- 18. List All Cars with Their Current Lease Information.

select * from vehicle v join lease l on v.vehicleID=l.vehicleID
where startDate>=current_timestamp();

changed on march 22
