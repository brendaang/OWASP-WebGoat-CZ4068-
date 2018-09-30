# SQL Injection (mitigation)
## Immutable Queries
- best defence against SQL Injection
- either do not have data that could get interpreted
- or they treat the data as a single entity that is bound to a column without interpretation

### Static Queries
```
select * from products;
select * from users where user = "'" + session.getAttribute("UserID") + "'";
```

### Parameterized Queries
```
String query = "SELECT * FROM users WHERE last_name = ?";
PreparedStatement statement = connection.prepareStatement(query);
statement.setString(1, accountName);
ResultSet results = statement.executeQuery();
```

### Stored Procedures
Only if stored procedure does not generate dynamic SQL.

#### Safe Stored Procedures (MSSQL Server)
```
CREATE PROCEDURE ListCustomers(@Country nvarchar(30))
AS
SELECT City, COUNT(*)
FROM Customers
WHERE Country LIKE @Country GROUP BY City


EXEC ListCustomers;
```

#### Injectable Stored Procedures (MSSQL Server)
```
CREATE PROEDURE getUser(@lastName nvarchar(25))
AS
declare @sql nvarchar(255)
set @sql = 'select * from users where
            LastName = + @LastName + '
exec sp_executesql @sql
```
## SQL Injection (mitigation) Exercise
- Turn on ZAP and turn on break point.
- Click the sort by option on any of the columns on WebGoat

See the response as below:

![alt text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/7.%20sql%20mitigation%20default%20response.PNG?token=ANjmpri2x7EhrsJczd1zWAojl3puuvgzks5bubIZwA%3D%3D)

Edit the response as below:

![alt text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/8.%20sql%20mitigation%20changed%20response.PNG?token=ANjmpokQcCE5QPFbZY49rr1UqpzY0dmsks5bubI6wA%3D%3D)

Submit and step to the next request or response, see that the table has been changed on WebGoat:

![alt text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/9%20effect%20on%20table.PNG?token=ANjmpuHvz6oPEwHvebP8h3Ld7S42Lqhoks5bubLBwA%3D%3D)

Input the IP address of the Hostname that you have changed.
