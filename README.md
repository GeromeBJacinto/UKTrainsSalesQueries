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
![image](https://github.com/user-attachments/assets/26c6c864-bf45-4fa9-ae0d-cf0d242cf9aa)


# Validating PowerBI values via SQL queries
**1. How much is the total ticket sales of the National Railways?**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/b56830ff-1f1a-4b32-b791-49636d574b77)

- SQL
```
SELECT SUM(Price) as TotalTicketSales
	FROM railway;
```
![image](https://github.com/user-attachments/assets/f62c1386-a28c-4e8d-bdfe-538b02bdb79d)

**2. How much is the total refunds given of the National Railways?**
- PowerBI:

![image](https://github.com/user-attachments/assets/3084bef9-2ff8-4fbd-9fc9-bd6787fe8aca)

- SQL
```
SELECT SUM(Price) as TotalRefundsGiven
	FROM railway
  WHERE RefundRequest = "Yes";
```
![image](https://github.com/user-attachments/assets/a8dfa081-dcf8-4486-b4ef-47354d343a53)

    - Note that only price were RefundRequest = "Yes" was retrieved and summarized by this query.

**3. How much is the total net revenue of the National Railways?**
- PowerBI:

![image](https://github.com/user-attachments/assets/d3bfe2b4-76d3-4b58-9b38-6bc51649b077)

- SQL
```
SELECT sum(price) - SUM(CASE WHEN RefundRequest = "Yes" then price end) as NetRevenue
  FROM railway;
```
![image](https://github.com/user-attachments/assets/3ad66e6f-efa6-454c-93ca-dfa5dd2e0f08)

    - We can also get the same results by getting the sum of all price were RefundRequest = "No".

**4. How much is the total sales per month?**
- PowerBI:
  
![image](https://github.com/user-attachments/assets/a70db094-c6ae-4a39-9888-941c12858ffd)

- SQL:

```
SELECT MONTHNAME(DateofJourney) as months, SUM(price) as sales
	FROM railway
    GROUP BY months;
```

![image](https://github.com/user-attachments/assets/1a598b06-e7f0-43e3-8e3c-0b9cf3a134d2)
