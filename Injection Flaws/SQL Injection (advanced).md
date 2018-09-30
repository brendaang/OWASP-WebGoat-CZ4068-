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

## Blind SQL Injection
- Asks the database true or false questions
- Determines the answer based on the applications response
- Often used when the web application is configured to show generic error messages
- BUT has not mitigated the code that is vulnerable to SQL injection.

### Difference
- Normal SQL Injection:
  - error messages from the database are displayed
  - gives enough information to find out how the query is working
- Blind SQL Injection
  - application does not reflect the information directly on the webpage
  - so in the case where nothing is displayed
  - have to start asking the database true or false questions.
  - therefore blind SQL injection much more difficult to exploit.

## Blind SQL Injection Exercise
Steps taken:
- Use Burp to catch the request at the register webpage
- Details:
  - Username: user
  - Email: test@test.com
  - Password: 123
  - Retype Password: 123

Once the request comes into Burp Proxy, change the username_reg field
```
user'AND substring(database_version(),1,1) = 2; --
```

The rest of the fields can be left as is.

![alt text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/4%20sqli%20burp%20request.PNG?token=ANjmpvbRGAnOFhcP3uWPspFu6D5QhviRks5buWiiwA%3D%3D)

![alt text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/5%20sqli%20burp%20response.PNG?token=ANjmppN9tBXpVH662iJEkwHHsMhvFp1Nks5buWl2wA%3D%3D)

Note: This part is weird. The response says my lesson is complete but I haven't logged  in as Tom, and all I did was as the database a boolean question. I have emailed Prof Shar, and he hasn't replied me. Feel free to try this part and let me know if you found something. Gonna retry this after Prof Shar gets back to me.
~ Brenda
