﻿
#JSON Parser Date Extensions
####Date parsing extensions for the JavaScript JSON parser to provide real JavaScript dates from JSON.parse()####

This small library provides a few JavaScript extensions for automatically 
parsing JSON date strings to real JavaScript dates, either on individual date
values or in nested date values in object trees. You can either manually
apply the date parsing or replace the JSON parser for the current global 
scope to force *all* JSON operations to parse dates automatically.

This library provides:

* **JSON.dateParser()**<br/>
  Extension that can be used with JSON.parse() 
  to parse dates with explicit calls to JSON.parse.

* **JSON.parseWithDates()**<br/>
  Function to provide a wrapper function
  that behaves like JSON.parse() but parses dates.

* **JSON.useDateParser()**<br/> 
  Globally replace JSON.parse() with
  JSON.parseWithDates to affect all JSON.parse() operations within
  the current page/scope. Affects all JSON operations including 
  framework JSON parsing such as jQuery.getJSON() etc.

* **JSON.dateStringToDate()**<br/> 
  Safely converts JSON ISO and MSAJAX
  dates, raw ISO and MSAJAX string values and dates to JavaScript
  dates. This function is a simple helper to guarantee you get a 
  date value.

##Usage##

**JSON.parseWithDate**<br/>
Manual JSON parsing with automatic date conversion:

```javascript
var date = new Date();
var json = JSON.stringify(date);

var date2 = JSON.parseWithDate(json);
console.log(date2);   // date: Wed Jan 01 2014 13:28:56 GMT-1000 (Hawaiian Standard Time) 
```

Likewise you can apply that to complex objects that contain dates:

```javascript
var obj = {
    id: "141923asd1",
    name: "rick",
    entered: new Date(),
    updated: new Date()
};
var json = JSON.stringify(obj);

var obj2 = JSON.parseWithDate(json);

equal(!obj2.entered.getTime, false, "Date should be a date object");
equal(obj2.entered.toString(), obj.entered.toString(), "Dates should be equal");
```


**JSON.useDateParser**<br/>
useDateParser() can globally replace the JSON.parse() function with the
JSON.parseWithDate() function, which results in automatically converting dates
for all JSON operations on the global scope. This allows automatic conversions
for all subsequent JSON.parse() calls including those inside of frameworks.

```javascript
// enable global JSON date parsing
JSON.useDateParser();
       
var date = new Date();
var json = JSON.stringify(date);

// using just plain JSON.parse() should decode dates
var date2 = JSON.parse(json);
console.log(date2);

equal(!date2.getTime, false, "Date should be a date object");
equal(date2.toString(), date.toString(), "Dates should be equal");

// optionally replace original parser
JSON.useDateParser(false);
```

The following example demonstrates using $.getJSON() with automatic
date conversion:

```javascript
// enable global JSON date parsing
JSON.useDateParser();    

$.getJSON("JsonWithDate.txt")
    .done(function(data) {
        console.log("jquery result.entered: " + data.entered +
            "  result.updated: " + data.updated);

        equal(!data.entered.getTime, false, "Entered should be a date");            
    })
    .success(function () {        
        // Optionally replace original parser
        JSON.useDateParser(false);
    });
```

**JSON.dateParser**<br />
dateParser is the JSON parse extension that is used to filter dates from
date strings. You can use this filter directly with JSON.parse() although
I'd recommend you use JSON.parseWithDate() instead.

```javascript
var obj = {
    id: "141923asd1",
    name: "rick",
    entered: new Date(),
    updated: new Date()
};
var json = JSON.stringify(obj);

var obj2 = JSON.parse(json, JSON.dateParser);

console.log(obj2.entered,obj2.updated);
```

**JSON.dateStringToDate**<br />
dateStringToDate reliably provides JavaScript dates from JSON dates strings,
plain strings in ISO or MS AJAX formats or dates. Useful when you are not
converting JSON dates automatically and you need to be sure you always get
consistent date values in code.

```javascript
var date = new Date();
var json = JSON.stringify(date);

var date2 = JSON.dateStringToDate(json);
console.log(date2);  // Wed Jan 01 2014 13:28:56 GMT-1000 (Hawaiian Standard Time) 
```