# Commonly used Tableau functions

- ABS(number):  
Returns absolute value of a number. e.g., ABS(-42) = 42

- AVG(column):  
Returns average of all the numeric value (Note: Null values are ignored in the calculation).

- CASE Expression
WHEN value1 THEN return1
WHEN value2 THEN return2...
ELSE default
return END:  
Can be used to create a custom mapping. e.g., CASE [Region] WHEN ’West’ THEN 1 WHEN ’East’ THEN 2 ELSE 3 END

- CEILING(number):  
Rounds a number to the nearest integer of equal or greater value.

- CONTAINS(string, substring):  
Check if the string contains the specified substring. e.g., CONTAINS("Sub-Category","Cat") = True

- COUNT(expression):  
Returns the number of items in a group. Null values are not counted.

- COUNTD(expression):  
Returns the number of distinct items in a group. Null values are not counted. Let’s say you have a column with
products "Car", "Bike", "Space ships" each repeated 10 times individually. The COUNTD function will return a value of 3.

- DATE(expression):  
Returns a date given a number, string, or date expression.  
e.g., DATE("April 15, 2004") = April 15, 2004; DATE("4/15/2004"); DATE(2006-06-15 14:52) = 2006-06-15

- DATEADD(date_part, interval, date):  
Useful when you need to offset dates.  
e.g., DATEADD(’month’, 3, 2004-04-15) = 2004-07-15 12:00:00 AM; DATEADD(’days’, 42, [Order Date]) = Adds 42 days to all values in the order date column.

- DATEDIFF(date_part, date1, date2, [start_of_week]):  
Useful to calculate difference between two dates.  
e.g., DATEDIFF(’month’, 2013-09-22, 2013-12-24)= 3; DATEDIFF(’year’, 2013-11-11, 2011-11-11)= 2

- DATEPART(date_part, date, [start_of_week]):  
Useful to recover just the part of the date that you’re interested in.  
e.g., DATEPART(’year’, 2004-04-15) = 2004; DATEPART(’month’, 2004-04-15) = 4

- DATETRUNC(date_part, date, [start_of_week]):  
Useful to truncate dates.  
e.g., DATETRUNC(’quarter’, 2004-08-15) = 2004-07-01 12:00:00 AM; DATETRUNC(’month’, 2004-04-15) = 2004-04-01 12:00:00 AM

- DAY(date):  
Returns the day of the given date as an integer.

- FIRST( ):  
Returns the number of rows from the current row to the first row in the partition.

- IF test1 THEN value1
ELSEIF test2 THEN value2
ELSEIF test3 THEN value3
ELSE value4
END:  
Typical If-Else can be used similar to CASE-WHEN statements to create custom mappings and groupings.  
e.g., IF [Age] < 21 THEN ’Under 21’ ELSEIF [Age] <= 32 THEN ’21-32’ ELSEIF [Age] <= 64 THEN ’33-64’ ELSE ’65+’ END

- IIF(test, then, else, [unknown]):  
Use the IIF function to perform logical tests and return appropriate values. The first argument, test, must be a boolean:
either a boolean field in the data source, or the result of a logical expression using operators (or a logical comparison
of AND, OR, or NOT).  
e.g., IIF(7>5, ’seven is greater than five’, ’seven is less than five’); IIF([Cost]>[Budget Cost], ’Over Budget’, ’Under Budget’)

- INDEX():  
Returns the index of the current row in the partition, without any sorting with regard to value. The first row index starts at 1.

- LEFT(string, number) || RIGHT(string, number):  
Truncate a string to a given number of characters starting from the left or right, respectively.  
e.g., LEFT("Shankar", 4) = "Shan"; RIGHT("Shankar",3) = "kar"

- LOOKUP(expression, [offset]):  
Returns the value of the expression in a target row, specified as a relative offset from the current row.

- LOWER(string) || UPPER(string):  
Convert uniformly to lowercase or uppercase respectively.

- MAX(a, b) || MIN(a, b):  
Returns the maximum / minimum of a and b respectively.

- NOW( ):  
Returns the current date and time.

- ROUND(number, [decimals]):  
Rounds numbers to a specified number of digits.

- RUNNING_AVG(expression) || RUNNING_SUM(expression) ):  
Returns the running average/sum of the given expression, from the first row in the partition to the current row.

- ZN(expression):  
Returns the expression if it is not null, otherwise returns zero. Use this function to use zero values instead of null values.  
e.g., ZN([Profit]) = [Profit]
