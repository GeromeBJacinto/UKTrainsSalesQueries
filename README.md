# PowerBI Railway Challenge: Validating Visuals with SQL queries

## Overview of the Project
This project aims to showcase the skills of the author in creating SQL queries by validating a visualization report from National Railway Company, which is his another project.
The visualization report is primarily concerned on the Sales Performance for the National Railway Company.
Since the author is not yet an expert on PowerBI, he opted to use SQL to countercheck the values being produced by manipulations he performed in PowerBI.

## Data sources
The data source for this project is the dataset that can be downloaded from the Maven Rail Challenge of Maven Analytics. [https://mavenanalytics.io/challenges/maven-rail-challenge/08941141-d23f-4cc9-93a3-4c25ed06e1c3](url)

## Description of Data
There are 31,653 rows of data (not included: 1 row header) for 18 columns in our dataset. 

The columns are:
1. TransactionID - Unique identifier for an individual train ticket purchase
2. Date of Purchase - Date the ticket was purchased
3. Time of Purchase - Time the ticket was purchased
4. Purchase Type - Whether the ticket was purchased online or directly at a train station
5. Payment Method - Payment method used to purchase the ticket (Contactles, Credit Card, or Debit Card)
6. Railcard - Payment method used to purchase the ticket (Contactles, Credit Card, or Debit Card)
7. Ticket Class - Seat class for the ticket (Standard or First)
8. Ticket Type - When you bought or can use the ticket. Advance tickets are 1/2 off and must be purchased at least a day prior to departure. Off-Peak tickets are 1/4 off and must be used outside of peak hours (weekdays between 6-8am and 4-6pm). Anytime tickets are full price and can be bought and used at any time during the day.
9. Price - Final cost of the ticket
10. Departure Station - Station to board the train
11. Arrival Destination - Date the train departed
12. Date of Journey - Date the train departed
13. Departure Time - Time the train departed
14. Arrival Time - Time the train was scheduled to arrive at its destination (can be on the day after departure)
15. Actual Arrival Time - Time the train was scheduled to arrive at its destination (can be on the day after departure)
16. Journey Status - Time the train was scheduled to arrive at its destination (can be on the day after departure)
17. Reason for Delay - Reason for the delay or cancellation
18. Refund Request - Reason for the delay or cancellation


## Exploratory Data Analysis

The main focus of the visualization report is the Sales Performance of National Railway Company.
Specifically, we are interested in answering the following questions.

- How much is the total ticket sales of the National Railways?
- How much is the total refunds given of the National Railways?
- How much is the total Net Revenue of the National Railways?
- How much is the total sales per month? per month and ticket type? per month and railcard?
- What is the Top 5 Route in terms of Net Revenue?
- What is the Bottom 5 Route in terms of Net Revenue?
- What is the Top 5 Route in terms of Refunds Given?
- How much refunds are given per Journey Status?
- How much refunds are given per Minutes Delayed?
- How much is the sales, refunds given, Net Revenue, and average Net Revenue per trip by Departure Station and Arrival Destination?



## Actual Visualization from PowerBI
![image](https://github.com/user-attachments/assets/dac9d888-5480-42f4-9594-1c92dd79e895)


# Validating PowerBI values via SQL queries
**1. How much is the total ticket sales of the National Railways?**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/b56830ff-1f1a-4b32-b791-49636d574b77)

- SQL
```
SELECT CONCAT("$",SUM(Price)) AS Sales
	FROM railway;
```
![image](https://github.com/user-attachments/assets/f7fe1323-41b9-4241-93f2-d2e8d970e71d)

**2. How much is the total refunds given of the National Railways?**
- PowerBI:

![image](https://github.com/user-attachments/assets/3084bef9-2ff8-4fbd-9fc9-bd6787fe8aca)

- SQL
```
SELECT CONCAT("$",SUM(Price)) as RefundsGiven
	FROM railway
    WHERE RefundRequest = "Yes";
```
![image](https://github.com/user-attachments/assets/7a7abd08-0508-4b23-bd80-5fad99b4f2bd)

    - Note that only price were RefundRequest = "Yes" was retrieved and summarized by this query.

**3. How much is the total net revenue of the National Railways?**
- PowerBI:

![image](https://github.com/user-attachments/assets/d3bfe2b4-76d3-4b58-9b38-6bc51649b077)

- SQL
```
SELECT CONCAT("$",SUM(price) - SUM(CASE WHEN RefundRequest = "Yes" THEN price END)) as NetRevenue
    FROM railway;
```
![image](https://github.com/user-attachments/assets/d7b1b596-ba29-4a99-b097-172b426a5b1b)

    - We can also get the same results by getting the sum of all price were RefundRequest = "No".

**4. How much is the total sales per month?per month and ticket type? per month and railcard?**

- **Total Sales Per Month**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/a70db094-c6ae-4a39-9888-941c12858ffd)

- SQL:

```
SELECT MONTHNAME(DateofJourney) as Months, CONCAT("$",SUM(price)) as Sales
	FROM railway
    GROUP BY months;
```

![image](https://github.com/user-attachments/assets/0142eb0c-8a94-46a2-ac5f-d2569ab9c38b)

- **Total Sales Per Month and Ticket Type**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/6fbc7fbf-0b42-4634-b103-ec3d50a03252)

- SQL:

```
SELECT monthname(DateofJourney) as Months, TicketType, CONCAT("$",SUM(price)) as Sales
	FROM railway
	GROUP BY months, TicketType;
```

![image](https://github.com/user-attachments/assets/12723557-ab99-4f4c-bd7f-2c8655424dd0)

- **Total Sales Per Month and Railcard**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/1da4c65f-050c-48c8-92f0-d35e1ad9cfc3)

- SQL:

```
SELECT MONTHNAME(DateofJourney) as Months, Railcard, CONCAT("$",SUM(price)) as Sales
	FROM railway
    GROUP BY months, Railcard;
```

![image](https://github.com/user-attachments/assets/a75a8348-3bf0-499a-8837-f715749ab2a7)

**5. What is the Top 5 Route in terms of Net Revenue?**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/b1e53078-d2cc-4435-981a-7efc499816ff)

- SQL:

```
SELECT CONCAT(DepartureStation, " - ", ArrivalDestination) as Route, 
COALESCE(CONCAT("$",SUM(CASE WHEN RefundRequest = "No" THEN price END)),"$0") as NetRevenue
	FROM railway
    GROUP BY Route
    ORDER BY SUM(CASE WHEN RefundRequest = "No" THEN price END) DESC
    LIMIT 5;
```

![image](https://github.com/user-attachments/assets/9653573c-24e1-4161-8f5c-510acd0921e4)

**6.What is the Bottom 5 Route in terms of Net Revenue?**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/d953aab6-f42b-4395-9002-2a61ea5c2190)

- SQL:

```
SELECT CONCAT(DepartureStation, " - ", ArrivalDestination) as Route, 
 COALESCE(CONCAT("$",SUM(CASE WHEN RefundRequest = "No" THEN price END)),"$0") as NetRevenue
	FROM railway
    GROUP BY Route
    ORDER BY SUM(CASE WHEN RefundRequest = "No" THEN price END) 
    LIMIT 5;
```

![image](https://github.com/user-attachments/assets/045f1e13-b88d-439e-861a-b6444865096c)

**7.What is the Top 5 Route in terms of Refunds Given?**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/65dc9f03-0851-422e-880d-387b5f4c3475)

- SQL:

```
SELECT CONCAT(DepartureStation, " - ", ArrivalDestination) as Route, 
COALESCE(CONCAT("$",SUM(CASE WHEN RefundRequest = "Yes" THEN price END)),"$0") as RefundsGiven
	FROM railway
    GROUP BY Route
    ORDER BY SUM(CASE WHEN RefundRequest = "Yes" THEN price END) DESC 
    LIMIT 5;
```

![image](https://github.com/user-attachments/assets/494c01a3-1a0c-48a0-a41f-42368ace9e8a)



**8.How much refunds are given per Journey Status?**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/a8d84d50-6aea-4cc8-b747-ead1d19e8631)

- SQL:

```
SELECT JourneyStatus, CONCAT("$",SUM(price)) as Refunds
	FROM railway
     WHERE RefundRequest = "Yes"
     GROUP BY JourneyStatus;
```

![image](https://github.com/user-attachments/assets/132f417b-83b1-458a-ba7a-e6b97500ed11)

**9.How much refunds are given per Minutes Delayed?**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/38789208-223b-4bd4-be6a-4cdd99a33cca)

- SQL:

```
SELECT 
CASE WHEN TIMESTAMPDIFF(MINUTE,ArrivalTime,ActualArrivalTime) = 0  then "0" 
WHEN TIMESTAMPDIFF(MINUTE,ArrivalTime,ActualArrivalTime) between 1 and 4 then "01-04"
WHEN TIMESTAMPDIFF(MINUTE,ArrivalTime,ActualArrivalTime) between 5 and 15 then "05-15"
WHEN TIMESTAMPDIFF(MINUTE,ArrivalTime,ActualArrivalTime) between 16 and 30 then "16-30"
WHEN TIMESTAMPDIFF(MINUTE,ArrivalTime,ActualArrivalTime) between 31 and 59 then "31-60"
WHEN TIMESTAMPDIFF(MINUTE,ArrivalTime,ActualArrivalTime) between 60 and 1000 then "60 and above"
END AS MinutesDelayed, 
CONCAT("$",SUM(price)) AS TotalSales, 
CONCAT("$",SUM(CASE WHEN RefundRequest LIKE "%Yes%" THEN price ELSE 0 END)) as RefundsGiven
FROM railway
WHERE JourneyStatus = "Delayed"
GROUP BY MinutesDelayed
ORDER BY MinutesDelayed;
```

    - Note that we used TIMESTAMPDIFF to get the difference between the ArrivalTime and ActualArrivalTime.
    - This allows us to get their difference in minutes, hence the delay.
    
![image](https://github.com/user-attachments/assets/a97eb3af-e7ca-4e39-88e8-05aede47a92c)


**10. How much is the sales, refunds given, Net Revenue, and average Net Revenue per trip by Departure Station and Arrival Destination?**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/240d9759-4823-4dbd-bde3-8a5bfed6dc18)

- SQL:

```
CREATE TABLE trips AS
SELECT CONCAT(DepartureStation, " - ", ArrivalDestination) AS Route,
	COUNT(DISTINCT  DateofJourney,DepartureTime,ArrivalTime,ActualArrivalTime,DepartureStation,ArrivalDestination,JourneyStatus) AS Count
    FROM railway
    GROUP BY route
    ORDER BY DepartureStation, ArrivalDestination;
```

    - Create TRIPS table to get count of trips per route.
    - Note that we will get the count per route upon unique combination of DateofJourney, DepartureTime, ArrivalTime, ActualArrivalTime, DepartureStation, ArrivalDestination, and JourneyStatus

```
CREATE TABLE initial AS
SELECT CONCAT(DepartureStation, " - ", ArrivalDestination) AS Route,
	SUM(price) AS Sales, SUM(CASE WHEN RefundRequest = "Yes" THEN price ELSE 0 END) AS Refund, 
    sum(CASE WHEN RefundRequest = "No" THEN price ELSE 0 END) AS Revenue
    FROM railway
    GROUP BY route
    ORDER BY DepartureStation, ArrivalDestination;
```

    - Compute Sales, Refund,Revenue per Route on a different table.

```
SELECT a.route, CONCAT("$",a.Sales) as Sales, CONCAT("$",a.Refund) as Refunds, CONCAT("$",a.Revenue) as NetRevenue, 
CONCAT("$",a.Revenue/b.Count) AS AverageRevenuePerTrip
FROM initial as a
LEFT JOIN trips as b
ON a.route = b.route;
```

 - Join the tables. Divide the revenue by the trips count to get Average Revenue per Trip.

![image](https://github.com/user-attachments/assets/22d39efc-4dd1-4d66-b8da-ac5692af76ea)
