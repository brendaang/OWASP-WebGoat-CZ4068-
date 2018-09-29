# SQL Injection

A SQL injection attack consists of insertion or "injection" of an malicious data via the SQL query input from the client to the application.

A successful SQL injection exploit can:
- Read and modify sensitive data from the database

- Execute administration operations on the database

  -  Shutdown auditing or the DBMS

  - Truncate tables and logs

  - Add users

- Recover the content of a given file present on the DBMS file system

- Issue commands to the operating system

Consequences of SQL Injection
SQL injection attacks allow attackers to
- Spoof identity

- Tamper with existing data

- Cause repudiation issues such as voiding transactions or changing balances

- Allow the complete disclosure of all data on the system

- Destroy the data or make it otherwise unavailable

- Become administrator of the database server

SQL Injection is more common in PHP, Classic ASP, Cold Fusion and older languages
- Languages that do not provide parameterized query support

- Parameterized queries have been added to newer versions

- Early adopters of web technology (i.e. Old Code)

## SQL String Injection
Code used:
```
Smith' or '1'='1
```
![alt text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/1%20string%20sql%20injection.png?token=ANjmphShORVH9YNbXzOVl2qLOor_DTeUks5buFZtwA%3D%3D)

## SQL Numeric injection
Code used:
```
1234567 or 1=1
```

![alt text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/2%20numeric%20sql%20injection.png?token=ANjmpvTzCV9BFjfQNI43A-cANBDiq51Nks5buFbWwA%3D%3D)
