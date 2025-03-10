---   
title: Javascript PostMessage() API Basics for Web Development
author: ajaytekam   
date: 2023-07-27 16:00:00 +0530   
img_path: /assets/posts/20230727/postmessage/   
categories: [JavaScript, WebDev, Frontend]    
tags: [JavaScript, WebDev, Frontend]  
image:
  path: postmessage.png 
---    

The window.postMessage() method allows to send cross-domain data messages between two browser windows or a current window and an inner frame safly, which otherwise restricted to the same domain, protocol or same portnumber. Or as the defition on [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage)  

> The window.postMessage() method safely enables cross-origin communication between Window objects; e.g., between a page and a pop-up that it spawned, or between a page and an iframe embedded within it.  


```   
                               Window.postMessage()
                                 ---------------- 
    http://domain-A.com =======> | Message Data | =======> http://domain-B.com
                                 ---------------- 
```  

**Syntax :**  

At the sender end :  

```js   
targetWindow.postMessage(message, targetOrigin, [transfer]);
```  

Where :  

* `targetWindow` : A reference to the window that will receive the message. (by using method like window.open(), window.frames)  
* `message` : Data to be sent to the other window.  
* `targetOrigin` : It is URI of origin/sender window of message.  
* `transfer` : This is optional, Transferable objects like instances of ImageBitmap, ArrayBuffer, MessagePort; it also becomes unusable on the sender side after transfer. 

At the receiving end/dispatched event :   

```js    
window.addEventListener("message", (event) => {
  if (event.origin !== "")
    return;

  // ...
}, false);
```  

Some important properties :  

* `event.origin` : Contains the targetOrigin value.   
* `event.data` : Contains data/string/objects dispatched by postMessage().   

**First Example : with `window.open()`**  

`mainPage.html` :  

```html  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MainPage</title>
    <script>
        var childWin;
        const childName = "Popup";

        function OpenWindow() {
            childWin = window.open('popup.html', childName, 'height=150px,width=250px');
        }

        function sendMessage() {
            let msg = {Name:"John", Age:"25", Gender:"Male"};
            tOrigin = "http://localhost:8080"; // targteOrigin
            childWin.postMessage(msg, tOrigin);
        }
    </script>
</head>
<body> 
    <input type='button' value='Open Popup Window' onclick='OpenWindow()'>
    <br/><br/>
    <input type='button' value='Send Message' onclick='sendMessage()'>
</body>
</html>
```  

`popup.html` :  

```html  
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>WebPage</title>
  <script>

    window.addEventListener("message", (event) => {
        // verify the targetOrigin
        if (event.origin !== "http://localhost:8080") 
            return;
        // Processing messages 
        document.getElementById("Msg1").innerText = event.data.Name;
        document.getElementById("Msg2").innerText = event.data.Age;
        document.getElementById("Msg3").innerText = event.data.Gender;
    }, false);

    function closeWindow() {
        try {window.close();} catch(e){console.log(e)}
        try {self.close();} catch(e){console.log(e)}
    }
  </script>
</head>
<body>
    Name : <span id='Msg1'></span><br/> 
    Age : <span id='Msg2'></span><br/> 
    Sex : <span id='Msg3'></span> 
    </br></br>  
    <input type='button' value='Close Window' onclick='closeWindow()'>
</body>
</html>
```  

Output :  

![](sc1.png)  

**Second Example :**  

`mainPage.html` :  

```html  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MainPage</title>
</head>
<body> 
    <br/>
    <input type='button' value='Send Message' onclick='sendMessage()'>
    <br/>
    <h3>IFrame window :</h3>
    <iframe src='window.html' id='iframe1' width='200px' height='80px'></iframe>

    <script>
        var iframeWin = document.getElementById("iframe1").contentWindow;
        function sendMessage() {
            let msg = {Name:"John", Age:"25", Gender:"Male"};
            tOrigin = "http://localhost:8080"; // targteOrigin
            iframeWin.postMessage(msg, tOrigin);
        }
    </script>

</body>
</html>
```  

At above code we accessed the iframe properties by `iframe.contentWindow` property. Similarly to access the document inside iframe we can use `iframe.contentWindow.document` or `iframe.contentDocument`.   

`window.html` : 

```html 
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <script>

    window.addEventListener("message", (event) => {
        // verify the targetOrigin
        if (event.origin !== "http://localhost:8080") 
            return;
        // Processing messages 
        document.getElementById("Msg1").innerText = event.data.Name;
        document.getElementById("Msg2").innerText = event.data.Age;
        document.getElementById("Msg3").innerText = event.data.Gender;
    }, false);

  </script>
</head>
<body>
    Name : <span id='Msg1'></span><br/> 
    Age : <span id='Msg2'></span><br/> 
    Sex : <span id='Msg3'></span> 
</body>
</html>
```  

Output :  

![](sc2.png)   
