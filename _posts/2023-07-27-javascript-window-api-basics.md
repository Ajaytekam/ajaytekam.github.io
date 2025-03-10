---   
title: Javascript Window.Open() method basics
author: ajaytekam   
date: 2023-07-27 16:00:00 +0530   
img_path: /assets/posts/20230727/windowapi/   
categories: [JavaScript, WebDev, Frontend]    
tags: [JavaScript, WebDev, Frontend]  
image:
  path: window.png 
---    

The `window.open()` method open a new brower window like a new tab or popup depending on the parameter values. Syntax :  

```js  
window.open(URL, NAME, SPECS, REPLACE)
```  

Where :  

* **URL** : Specifies the URL of the page to open. If no URL is specified, a new window/tab with "about:blank" is opened.   
* **NAME** : Specifies the target attribute or the name of the window. There are also other supported values such as '\_blank' (open window on new tab), '\_parent' (load window on parent frame), '\_self' (replace the new window with current page), '\_top' (replaces any framesets that may be loaded).    
* **SPECS** : A comma-saparated list of items with no whitespaces. It is optional field. Also note that to open a popup window set the width and height property.   
* **REPLACE** : Specifies whether the URL creates a new entry or replaces the current entry in the history list. It is optional field.  

**Examples and Use Cases :**   

Open a Popup Window :  

main.html  

```html  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebPage</title>
    <script>
      function OpenPopup() {
        var WinName = "Popup";
        var NewWin = window.open('page.html', WinName, 'width=400,height=20');
      }
    </script>
</head>
<body>
    <h2>Click here to open a new Window</h2>    
    <input type="button" value="Open Popup Window"onclick="OpenPopup()">
</body>
</html>
```  

Popup.html  

```html  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebPage</title>
</head>
<body>
    <h2>This is newly created test window.</h2>    
</body>
</html>
```   

![](sc9.png)  


To Open a new window on Browser tab use :  

```js  
<script>
  function OpenWindow() {
    var WinName = "NewWindow";
    var NewWin = window.open('page.html', WinName, '');
  }
</script>
```  

or you can also use 

```js  
var NewWin = window.open('page.html', '_blank')
``` 

**Accessing/Modifying New Window/Popup :**  

We can access the content of new window with `window.document`. For example creating a Popup window 

```js  
function CreatePopup() {
    let newWin = window.open('/', 'Popup', 'width=300,height=20');
    newWin.document.write('<h2>This is Popup Window');
}
```  

Windows may freely access content of each other only if they come from the same origin.   


**Accessing/Modifying Main Window from new Window/Popup :**  

By using `window.opener` reference a popup/new window can access/modify the content of Main Window. For example the code for modifier window will look like this :   

index.html : 

```html  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebPage</title>
    <script>
      function OpenPopup() {
        var WinName = "Popup";
        var NewWin = window.open('popup.html', WinName, 'width=400,height=20');
      }
    </script>
</head>
<body>
    <h2>Click here to open a new Window</h2>    
    <input type="button" value="Open Popup Window"onclick="OpenPopup()">
</body>
</html>
```  

popup.html : 

```html  
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebPage</title>
    <script>
       window.opener.document.body.innerHTML = '<html><body><h1>Hello World by Popup Window</h1></body></html>';
    </script>
</head>
<body>
    <h2>This is newly created test window.</h2>    
</body>
</html>
```   

**Closing the Popup/Window :**  

By using `window.close()` we can close the window/popup.

