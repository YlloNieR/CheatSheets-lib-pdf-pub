# node-red
Home automation system on Node-RED with reusable nodes, flows and subflows

## install Node-RED
```bash
apt-get install nodered
```

```bash
node-red-start
node-red-stop
node-red-restart
node-red-log 
```

## autostart enable/disable
```bash
systemctl status nodered.service
```
```bash
sudo systemctl enable nodered.service
```
```bash
sudo systemctl disable nodered.service
```


---
## network config
### Default config
```bash
127.0.0.1:1880
```
### if you want to change the default config
```bash
nano /etc/hostname
```
from @raspberry to @hostnameThatYouWant

```bash
usermod -l newUserName oldUserName
```
from pi to usernameYouWant

```bash
usermod -aG sudo usernameYouWant
```
from user to superuser

```bash
sudo adduser usernameYouWant video
```
adds usernameYouWant to videogroup

```bash
nano /etc/hosts
127.0.0.1   localhost
...
...
192.168.0.10    ipThatYouWant
```
on pi IP you get access from another client of same network


---
## How to install addons e.g. Dashboard
```Burger Menu > Manage Palette > install > Search for "Dashboard"> Select "node-red-dashboard" > install```


---
## icons
| Option          | Command             | size  |
| --------------- |:-------------------:| -----:|
| Material Design | delete              | ----- |
| Material Design | update              | ----- |
| Font Awesome    | fa-thermometer-full | fa-lg |
| Font Awesome    | fa-address-book     | fa-2x |
| Font Awesome    | fa-address-book     | fa-5x |


---
## parser
```js
parseInt(str)
num.toString(2) // 2 chars after comma
```


---
## edit text with html / js
```js
<font style="color:Black;font-size:18px">Black &#9632;</font>
```


---
## get json components
```json
var compareValue = msg.payload["oem-number"];
var compareValue = msg.payload.oemNumber;
```


---
## get unix timestamp
```js
getTimestamp = Math.round((new Date()).getTime() / 1000);
```


---
## layout editor

dashboard > choose tab > choose layout


---
## js splitter & parser sample
```js
splitter = msg.payload.split('\n');
msg.payload = [];

var i;
for (i=0; i<splitter.length-1; i++){
    time = JSON.parse(splitter[i]);
    msg.payload[i] = time;
}

return msg;
```
---


## using material icons
```html
<font i class="material-icons thumb_up" color = green></i></font>
```


---
## store data
### context 
```js
var count=context.get('count') || 0;
count += 1;
msg.payload=count;
context.set('count',count);
return msg;
```
### object
```js
var local=context.get('data') || {};
if (local.count===undefined)
{
    local.count=0;
}
local.count += 1;
msg.payload=local.count;
context.set('data',local);
return msg;
```
### flow (stays alive after deploy)
```js
var flowCount=flow.get('flowCount') || 0;
flowCount += 1;
msg.payload=flowCount;
flow.set('flowCount',flowCount);
return msg;
```


---
## clear form
```js
msg.payload = {}
msg.payload.vorname = '';
msg.payload.nachname = '';
msg.payload.plz = '';
msg.payload.stadt = '';

msg.topic = {};
return msg;
```


---
## delete certain element from array
```js
var data = msg.payload
var removed = data.splice(3,1); // only the third element will be deleted
msg.payload = data
```


---
## how to find out if array is empty
```js
if(arr.length > 0){
    msg.payload=true;
    return msg
} else {    
    msg.payload=true;
    return msg
}
```


---
## filter table
```js
[
    {
        "id": "8b572b42.8ea348",
        "type": "ui_table",
        "z": "aa4b639a.37b5c",
        "group": "a380aca8.0e2e8",
        "name": "table_db_stock_component",
        "order": 1,
        "width": 30,
        "height": 6,
        "columns": [
            {
                "headerFilter":"input",
                "field": "oem-number",
                "title": "oem-number",
                "width": "",
                "align": "left",
                "formatter": "plaintext",
                "formatterParams": {
                    "target": "_blank"
                }
            },
```


---
## date time
### sample 1
```js
unixstamp=msg.topic.Geburtstag.birthday;


dateObject = new Date(unixstamp);
//humanDateFormat = dateObject.toLocaleString(); // dd/MM/YYYY, HH:MM:SS AM
var date = dateObject.toLocaleString('de-DE',{timeZoneName: "short"});
var year = dateObject.getFullYear();
var month = dateObject.getMonth()+1;
var day = dateObject.getDate();

msg.topic.Geburtstag.birthday = day+'.'+month+'.'+year;

msg.payload = [msg.topic];
msg.topic = {};

return msg;
```
### sample 2
```js
unix=msg.payload;

msg.payload={};

dateObject = new Date(unix);
timeObject = new Date(unix);
var date = dateObject.toLocaleString('de-DE',{timeZoneName: "short"});
var year = dateObject.getFullYear();
var month = dateObject.getMonth()+1;
var day = dateObject.getDate();
var hours = ('0' + timeObject.getHours()).slice(-2);
var minutes = ('0' + timeObject.getMinutes()).slice(-2);
msg.payload.date=day+'.'+month+'.'+year;
msg.payload.time=hours+':'+minutes;

return msg;
```
### sample 3
```js
unix=msg.payload;

msg.payload={};

dateObject = new Date(unix*1000);
timeObject = new Date(unix*1000);
var date = dateObject.toLocaleString('de-DE',{timeZoneName: "short"});
var year = dateObject.getFullYear();
var month = ('0' + (dateObject.getMonth()+1)).slice(-2);
var day = dateObject.getDate();
var hours = ('0' + timeObject.getHours()).slice(-2);
var minutes = ('0' + timeObject.getMinutes()).slice(-2);
msg.payload.date=year+' '+month+' '+day;
msg.payload.time=hours+'-'+minutes;
msg.payload.datetimestr = msg.payload.date+" "+msg.payload.time+" "+"Backup.txt";
msg.payload=msg.payload.datetimestr
return msg;
```


---
## form table data
```js
tmp = msg;
msg = {};
msg.payload = [{},{},{}];

for (var i=0 ; i < tmp.details.length; i++){
    msg.payload[i].place = i+1;
    msg.payload[i].name = tmp.details[i].className;
    msg.payload[i].probability = ((tmp.details[i].probability)*100).toString().substring(0,5)+" %";
}

return msg;
```


---
### form json
```js
// Setze ID
var counter=1;
var h;
for (h=0; h<msg.payload.length; h++){
    msg.payload[h].id = counter;
    counter=counter+1;
}
// ----------------------

// Korrigiere Hostname Zeit Bug
var i;
for (i=0; i<msg.payload.length; i++){
    msg.payload[i].time = msg.payload[i].hostname;
    delete msg.payload[i].hostname;
}
// ----------------------

// form date
var j;
var formatDate = function(datum) {
    var date = new Date(datum);
    return date.getDate() + '.' + (date.getMonth() + 1) + '.' + date.getFullYear();
};

for (j=0; j<msg.payload.length; j++){
    msg.payload[j].timestamp = formatDate(new Date(msg.payload[j].timestamp))
}
// ----------------------

// form Tag / Device
var k;
for (k=0; k<msg.payload.length; k++){
    var split = msg.payload[k].tag.split(',')
    msg.payload[k].devicename = split[0];
    msg.payload[k].devicemac = split[1];
    msg.payload[k].deviceversion = split[2];
    delete msg.payload[k].tag;
}
// ----------------------

// formatiere Device MAC
var l;
var formatMac = function(mac) {
    var macSplit = mac.split('');
    return macSplit[0]+macSplit[1]+':'+macSplit[2]+macSplit[3]+':'+macSplit[4]+macSplit[5]+':'+macSplit[6]+macSplit[7]+':'+macSplit[8]+macSplit[9]+':'+macSplit[10]+macSplit[11];
    };
for (l=0; l<msg.payload.length; l++){
    msg.payload[l].devicemac = formatMac(msg.payload[l].devicemac);
}    
// ----------------------

msg.log = msg.payload;
delete msg.payload

return msg;
```


---
## form msg with icons
```js
var m;
for (m=0; m<msg.log.length; m++){
    msg.log[m].newMsg = msg.log[m].msg 
    if(msg.log[m].severityCode==0){
        msg.log[m].msg = '<center><i class="fa fa-exclamation fa-2x" aria-hidden="true" style="color:#B33A3A"></i></center>';
    }
    if(msg.log[m].severityCode==1){
        msg.log[m].msg = '<center><i class="fa fa-bell fa-2x nr-dashboard-warning" aria-hidden="true"></i></center>';
    }
    if(msg.log[m].severityCode==2){
        msg.log[m].msg = '<center><i class="fa fa-bolt fa-2x" aria-hidden="true" style="color:#B33A3A"></i></center>';
    }
    if(msg.log[m].severityCode==3){
        msg.log[m].msg = '<center><i class="fa fa-ambulance fa-2x" aria-hidden="true" style="color:#B33A3A"></i></center>';
    }
    if(msg.log[m].severityCode==4){
        msg.log[m].msg = '<center><i class="fa fa-exclamation-triangle fa-2x nr-dashboard-warning" aria-hidden="true"></i></center>';
    }
    if(msg.log[m].severityCode==5){
        msg.log[m].msg = '<center><i class="fa fa-lightbulb-o fa-2x nr-dashboard-warning" aria-hidden="true"></i></center>';
    }
    if(msg.log[m].severityCode==6){
        msg.log[m].msg = '<center><i class="fa fa-info-circle fa-2x" aria-hidden="true" style="color:#79BD9A"></i></center>';
    }
    if(msg.log[m].severityCode==7){
        msg.log[m].msg = '<center><i class="fa fa-bug fa-2x" aria-hidden="true" style="color:#644667"></i></center>';
    }
}
// ----------------------

return msg;
```


---
## multiple outputs
```js
data = msg.payload.compatibility;
msg2 = {};
msg2.payload = [];
counter = 1;

for(i=0; i<data.length; i++){
    if(data[i] !== null){
        msg2.payload[i] = {"id":counter,"comp": data[i].toString()};
        counter++;
    }
}

msg1 = {};
msg1.topic = "Does compatible printer of oem:"+ msg.payload.oem + " exist on wiweb Network?";
appearanceInUse = msg.appearanceData.appearanceInUse;
appearanceInUseLastUpdate = msg.appearanceData.appearanceInUseLastUpdate;
if(appearanceInUse == 0){
    appearanceInUse = "No, last check: "+appearanceInUseLastUpdate;
}
if(appearanceInUse == 1){
    appearanceInUse = "Yes, last seen: "+appearanceInUseLastUpdate;
}

msg1.payload = appearanceInUse;

return [msg1,msg2];
```


---
## push to array
```js
const animals = ['pigs', 'goats', 'sheep'];

const count = animals.push('cows');
console.log(count);
// expected output: 4
console.log(animals);
// expected output: Array ["pigs", "goats", "sheep", "cows"]

animals.push('chickens', 'cats', 'dogs');
console.log(animals);
// expected output: Array ["pigs", "goats", "sheep", "cows", "chickens", "cats", "dogs"]
```


---
## exec node (linux shell) & echo string with quotation marks
for e.g. you want to create xml file on raspi by node-red
```xml
<?xml version='1.0' encoding=...
```
msg.payload as follows is {"oem":"asd123","manu":"hp"}

```js
filenameBegin = "./Desktop/xmlLabels/";
filenameMiddle = "";
filenameEnd = ".label";
 
json = msg.payload;
filenameMiddle = json.oem+"-"+json.manu;

filename = filenameBegin+filenameMiddle+filenameEnd;

fileInput = '<?xml version='+"'"+'"'+"'"+"1.0"+"'"+'"'+"'"+' encoding=...'
command = 'echo '+'"'+fileInput+ '"'+' > '

msg.payload=command+filename;
return msg;
```