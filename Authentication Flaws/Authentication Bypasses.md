# Authentication Bypasses
- Can happen in many ways.
- usually take advantage of some flaw in the configuration or logic.
- Tampering to achieve the right conditions.

## Hidden Inputs
- simplest form is a reliance on hidden input that is in the web page/DOM.

## Removing Parameters
- if the attacker does not know the correct value of a parameter
- can choose to remove the parameter from the submission and see what happens

## Forced Browsing
- if an area of a site not protected properly by configuration
- this area can be accessed by guessing/brute-forcing

## Exercises
Catch the request using burp:

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/14%20authentication%20bypass%20request.PNG?token=ANjmprljvYmorh2kGiSrq7_khoFgopB7ks5bzHdFwA%3D%3D)

Change content to:
```
secQuestion00=&secQuestion01=&jsEnabled=1&verifyMethod=SEC_QUESTIONS&userId=12309746
```
Note the extra zero in the parameter name of secQuestion0 and secQuestion1.

Results:

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/15%20authentication%20bypassed.PNG?token=ANjmpsODO-kkRrPnRQkxjUBwK7JfUgTTks5bzHgpwA%3D%3D)
