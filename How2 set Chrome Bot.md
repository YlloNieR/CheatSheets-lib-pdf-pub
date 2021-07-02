## Preparation
- install Chrome
- install Addon from Webstore **User-Agent Switcher**

## set User-Agent Switcher
> options > Custom Mode > 
```json
{
  "www.sample.com": [
    "1",
    "2",
    "3",
    "4",
    "5",
    "6",
    "7",
    "8",
    "9",
    "10",
    "11"
  ]
}
```

## create js for chrome dev console
start Chrome + F12

### set counter
```js
localStorage.setItem('counter', 0);
```
start once as follows
```js
function asd(){
var counter = parseInt(localStorage.getItem('counter'));
console.log("stage 1");
document.querySelector("#image_competition-entry-290 > div > div.image_competition-items-item--image.rounded > img").click();
console.log("stage 2");
document.querySelector("#image_competition-form--vote > button").click();
console.log("stage 3");
console.log("userAgent:",navigator.userAgent);
console.log("counter:",counter);
counter++;
localStorage.setItem('counter', counter);
setTimeout(function(){location.reload();},1000);
}
setTimeout(function(){asd();},100);
```

## create VBSkript file for automation
```vbs
siteA = "https://www.sample.com"
Set browobj = CreateObject("Wscript.Shell")
browobj.Run "chrome -incognito -url "&siteA
Set browobj = Nothing

Dim a
Set a = CreateObject("WScript.Shell")
WScript.Sleep 2500
a.SendKeys "{F12}"

Dim myNum
myNum = 20
Do While myNum > 0
myNum = myNum - 1
WScript.Sleep 2000
a.SendKeys "{UP}"
WScript.Sleep 500
a.SendKeys "{ENTER}"
Loop
```

![Screenshot - Chrome Bot.png](.\"Screenshot - Chrome Bot.png")