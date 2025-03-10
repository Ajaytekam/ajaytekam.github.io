---   
title: Javascript Ajax Basics for Web-Devlopment 
author: ajaytekam   
date: 2023-07-27 15:55:00 +0530   
img_path: /assets/posts/20230727/ajax/   
categories: [JavaScript, WebDev, Frontend]    
tags: [JavaScript, WebDev, Frontend]  
image:
  path: ajax.png 
---    

## Contents 

- [Introduction](#introduction)  
- [XMLHTTPRequest](#xmlhttprequest)
- [Making Request to Server](#making-request-to-server)
- [GET and Post Request to Server](#making-request-to-server)
- [Using a callback function](#using-a-callback-function)

------------------

Introduction
------------

AJAX stands for Asynchronous JavaScript and XML. By using Ajax, a web application can send and retrieve data from a server asynchronously (in the background) without interfering with the display and behaviour of the existing page. It basically allows a web page to change content dynamically without the need to reload the entire page. 

Ajax is basically combination of  built-in `XMLHttpRequest` browser object and HTML DOM with JavaScript. Ajax uses XMLHttpRequest to send request to the server, then server returns a response, which is processed by javascript and according to response front-end webpage gets modified by manipulating html DOM.  


**Steps to Send AJAX query :**    

![](sc1.png)

1. An event occurs in a web page.  

2. An XMLHttpRequest object is created. 

3. The XMLHttpRequest object sends a request to a web server. 

4. The server processes the request. 

5. The server sends a response back to the web page

6. The response is processed withjavascript and according to that, the page is updated by manipulating HTML DOM.    


**Note :** At here one thing to point out that Ajax is not a new technology, or different language, its just existing technologies used in new ways.  


XMLHTTPRequest
--------------  

XMLHttpRequest is a JavaScript object that performs asynchronous interaction with the server and used to exchange data with a web server behind the scenes. All modern browsers (Chrome, Firefox, IE7+, Edge, Safari, Opera) have a built-in XMLHttpRequest object.  

## XMLHttpRequest Methods :    

* **new XMLHttpRequest() :** Creates a new XMLHttpRequest object.  

```js   
var Req = new XMLHttpRequest();
```  

* **open(method, url, async) :** Specifies the request. where `method` denotes the request type (GET/POST), `url` is location of file or data, `async` denotes the behaviour or request true (asynchronous) or false (synchronous). There is also two optional parameters `user` optional user name and `pwd` optional password.   

```js   
Req.open("GET", "http://mysite.com/data.txt", true);  
```   

* **send() :** Sends GET request to the server.   

```js   
Req.send();  
```   

* **send(string) :** 	Sends POST request to the server

```js  
Req.open("POST", "http://mysite.com/userinfo.php", true);  
Req.send("fname=John&lname=snow");  
```  

* **setRequestHeader() :**  Adds a label/value pair to the header to be sent.  

```js  
Req.open("POST", "http://mysite.com/userinfo.php", true);  
Req.setRequestHeader("Content-type", "application/x-www-form-urlencoded"); 
Req.send("fname=John&lname=snow");  
```  

* **getResponseHeader()	:** Returns specific header information.   

```js  
Req.getResponseHeader('Last-Modified');
```  

* **getAllResponseHeaders()	:** Returns header information.

```js  
Req.getAllResponseHeaders();
```  

* **abort()	:** Cancels the current request.  

```js  
Req.abort();
```  

## XMLHttpRequest properties :   

* **onreadystatechange :**  Defines a function to be called when the readyState property changes.   
 

* **readyState :** Holds the status of the XMLHttpRequest. Where 0 means request not initialized, 1 means server connection established, 2 :request received, 3 :processing request, 4 :request finished and response is ready.     


* **status :** Returns the status-number of a request, 200 (OK), 301 (Moved Permanently), 401 (Unauthorized), 404 (Not Found) etc. You can find the full list here [HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).  


* **statusText :** Returns the status-text (e.g. "OK" or "Not Found").    


* **responseText :** Returns the response data as a string.   


* **responseXML :** Returns the response data as XML data.   

Making Request to Server
------------------------  

**Step 1 :** Create XMLHttpRequest object.  

```js  
var req = new XMLHttpRequest();  
```  

**Step 2 :** Defining an Event handling function with `onreadystatechange` property, which will be called everytime when the `readyState` property changes.   

```js   
req.onreadystatechange = function() {
    if(this.readyState == 4 && this.status == 200) {
        var ResponseData = this.responseText;
       // statement  to be execute  
    }
}
```   

When the `req.readyState` is 4 and `req.status` is 200, then we know that the response is ready to be processed. The Response Data from server can be fetched from `req.responseText`.  

**Step 3 :** Define the connection parameter by using `open()` method. This method does not send the request to the webserver but saves its arguments for sending the request later.  

```js  
req.open(GET, "data.txt", true);
```   

The supported methods are POST, GET, HEADER, PUT, DELETE.   

**Step 4 :** Preparing Data Paramters for Submission. However this is optional and not necessary. Some examples are :  

```js  
// simple data  
var paramData = "Param1=Value1&Param2=Value2"  

// In json format for Post request
var paramData = encodeURIComponent(JSON.stringify({'param1':'value1', 'param2':'value2'}));  

// In json format for Get request
var paramData = JSON.stringify({'param1':'value1', 'param2':'value2'});  
```  

**Step 5 :** Submitting Request to the server :    

```js  
req.send();
```  

If there are some paramters to be submitted then :  

```js  
req.send(paramData);  
```

Now a simple AJAX request will look like this :  

```js  
var req = new XMLHttpRequest();  

req.onreadystatechange = function() {
    if(this.readyState == 4 && this.status == 200) {
        var ResponseData = this.responseText;
       // statement  to be execute  
    }
}

req.open(GET, "data.txt", true);

req.send();
```  
 
### Setting-up test lab :  

Use a web server like apache to host a file and page index.html. You can also use php, python one liner server for this.  

```shell 
// for php  
php -S localhost:8080  

// for python2
python -m SimpleHTTPServer 8080

// for python3 
python3 -m http.server 8080 
```

Code for accessing data.txt on server by using AJAX.  

```html  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebPage</title>
</head>
<body>
    <p id="demo"></p>

<script>
    var req = new XMLHttpRequest();

    req.onreadystatechange = function() {
        if(this.readyState == 4 && this.status == 200) {
            var responseData = this.responseText;
            document.getElementById("demo").innerText = responseData;
        }
    }

    req.open("GET", "data.txt", true);
    req.send();
</script>

</body>
</html>
```  

Output :   

![](sc2.png)   


Get and Post Request to the Server
----------------------------------  

**GET AJAX Request :**   

File: server.php   

```php  
<?php

    if($_SERVER["REQUEST_METHOD"] == "GET") {
        $name = $_GET['name'];
        $age = $_GET['age'];
        echo "Hello $name. <br/>";
        echo "You are $age years old."; 
    } elseif($_SERVER["REQUEST_METHOD"] == "POST") {
        $name = $_POST['name'];
        $age = $_POST['age'];
        echo "Hello $name. <br/>";
        echo "You are $age years old.";
    }

?>
```  

File: index.html  

```html  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebPage</title>
</head>
<body>
    <p id="demo"></p>

<script>

    var req = new XMLHttpRequest();  

    req.onreadystatechange = function() {
        if(this.readyState == 4 && this.status == 200) {
            var responseData = this.responseText;
            document.getElementById("demo").innerHTML = responseData;        
        }
    }

    var paramData = "name=ajay&age=25";
    var url = "server.php?" + paramData;
    req.open("GET", url, true);
    req.send(); 

</script>

</body>
</html>
```  

Output :  

```
Hello ajay.
You are 25 years old.
```  

**Post Ajax Request :**  

File: index.html  

```html  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebPage</title>
</head>
<body>
    <p id="demo"></p>

<script>

    var req = new XMLHttpRequest();  

    req.onreadystatechange = function() {
        if(this.readyState == 4 && this.status == 200) {
            var responseData = this.responseText;
            document.getElementById("demo").innerHTML = responseData;        
        }
    }

    var paramData = "name=ajay&age=25";
    req.open("POST", "server.php", true);
    req.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
    req.send(paramData); 

</script>

</body>
</html>
```  

```
Hello ajay.
You are 25 years old.
```  

Using a Callback Function
-------------------------   

A callback function is a function passed as a parameter to another function. If your web-application make more then one AJAX request, then it is good practice to create one function to execute `XMLHttpRequest` object, and one callback function for each AJAX task. 

```js  
loadDoc("server.php", "name=ajay&age=25", myFunc1);
loadDoc("server.php", "name=john&age=30", myFunc2);
loadDoc("server.php", "name=Bruce&age=40", myFunc3);

function loadDoc(url, param, callBackFunc) {
    var req = new XMLHttpRequest();
    req.onreadystatechange = function() {
        if(this.readyState == 4 && this.status == 200) {
            callBackFunc(this);
        }
    }

    var Fullurl = url + "?" + param;
    req.open("GET", Fullurl, true);
    req.send();
}

// callback functions  
function myFunc1(req) {
    document.getElementById("demo1").innerHTML = req.responseText;
}

function myFunc2(req) {
    document.getElementById("demo2").innerHTML = req.responseText;
}

function myFunc3(req) {
    document.getElementById("demo3").innerHTML = req.responseText;
}
```  

Test Example :  

File: server.php   

```php  
<?php

    if($_SERVER["REQUEST_METHOD"] == "GET") {
        $name = $_GET['name'];
        $age = $_GET['age'];
        echo "Hello $name. <br/>";
        echo "You are $age years old."; 
    }

?>
```  

File: index.html

```html  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebPage</title>
</head>
<body>
    <p id="demo1"></p>
    <p id="demo2"></p>
    <p id="demo3"></p>

<script>

loadDoc("server.php", "name=ajay&age=25", myFunc1);
loadDoc("server.php", "name=john&age=30", myFunc2);
loadDoc("server.php", "name=Bruce&age=40", myFunc3);

function loadDoc(url, param, callBackFunc) {
    var req = new XMLHttpRequest();
    req.onreadystatechange = function() {
        if(this.readyState == 4 && this.status == 200) {
            callBackFunc(this);
        }
    }

    var Fullurl = url + "?" + param;
    req.open("GET", Fullurl, true);
    req.send();
}

function myFunc1(req) {
    document.getElementById("demo1").innerHTML = req.responseText;
}

function myFunc2(req) {
    document.getElementById("demo2").innerHTML = req.responseText;
}

function myFunc3(req) {
    document.getElementById("demo3").innerHTML = req.responseText;
}

</script>

</body>
</html>
```  

Output :  

![](sc3.png)  

**Access Across Domains :** For security reasons, modern browsers do not allow access across domains. For example if the data.txt file is hosted on the other domain then we can not access it. For more information about this read [SOP](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy) and [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).  

