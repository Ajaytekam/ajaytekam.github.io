---   
title: How to Debgug Javascript Code
author: ajaytekam   
date: 2023-07-27 16:00:00 +0530   
img_path: /assets/posts/20230727/debug/   
categories: [JavaScript, WebDev, Frontend]    
tags: [JavaScript, WebDev, Frontend]  
image:
  path: debug.png 
---    

First we have to look at how to open and use Develper Tools given on chrome browser. Keyboard short-cuts for various things :  

* `F12` or `Ctrl + Shift + i` to open the developer tools  

Some important tabs are :  

* `console` : With console we can execute javascript code, call html DOM function, read console logs etc. It is very useful and handy.  
* `Sources` :  It shows all the front-end data of loaded page like javascript, html, css codes in the left-side, and on the right side the tabs like Watch (shows current values for any expressions), Breakpoints, scope (shows current variables), Call Stack (shows the nested calls chain), DOM Breakpoints, Event Listener Breakpoints etc. The center pain shows the code from selected file with line number.  

* `Network` :  The Network tab shows all the requests made by the web browser when loading that page. You can also filter requests by the type of request (xhr) or files like css, js, img Font, Doc etc.  

**Using `onsole.log()` :** By using console.log() we can print the javascript value in the console at the time of execution.  

Code :  

```js 
<script>
    var a = 10;
    var b = 20;
    var c = a+b;
    console.log(c);
    var d = a*b; 
    console.log(d);
</script>
```  

![](sc3.png)  

**Using `debugger` keyword :**  The debugger keyword force the browser to pause the execution of code where it encounter the keyword. Code sample :  

```js  
<script>
    var a = 10;
    var b = 20;
    debugger;
    var c = a+b;
    debugger;
    var d = a*b; 
    debugger;
</script>
```  

![](sc4.png)  

As we can see the execution is paused at line 11, now we can go `Console` tab and check the values of variable a and b  

![](sc5.png)  

To go next `debug` point, press `F8` or click on the play button visible on the page.  

![](sc6.png)  

![](sc7.png)  

Or to next line by line, press `F10` then the execution will stop at every line. 

**By Selecting line numbers on Source tab :** This is the simplest way of to setup the breakpoint, steps :  

Code Sample :  

```js  
<script>
    var a = 10;
    var b = 20;
    var c = a+b;
    console.log(c);
    var d = a*b; 
    console.log(d);
</script>
```  

* Load the page, `Open the Developer tools > Sources`  
* Select the lines on which you want to set breakpoints, remember click on the line number, not the code itself.  

![](sc8.gif)  

Now we can pause the execution at the given brakpoints.   
