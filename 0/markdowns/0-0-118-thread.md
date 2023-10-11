
# Post \#51118396 [Link](https://stackoverflow.com/questions/51118396/)

## Uncaught SyntaxError: Unexpected end of JSON input at JSON.parse (<anonymous>)

**Vote**: 31 (190/702) **Views**: 378090 (12/702) 

**Internal ID** \#0-0-118

Created at 2018-06-30 20:50:55

Tags: `javascript` `json` `parsing` `stringify` `oracle-jet`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I need some help with this error:
> Uncaught SyntaxError: Unexpected end of JSON input
at JSON.parse ()
at Object.success (dashboard.js:22)
at fire (jquery-3.3.1.js:3268)
at Object.fireWith [as resolveWith] (jquery-3.3.1.js:3398)
at done (jquery-3.3.1.js:9305)
at XMLHttpRequest. (jquery-3.3.1.js:9548)
I try to transform a string to a json object using  that cause that error.
I am using oracleJet and this is my code:
```
function DashboardViewModel() {
  
    var self = this;
    self.lineTypeValue = ko.observable('curved');
    var scatterSeries = []; 
    
    $.getJSON( "http://localhost:8080/points", function (data) {
               console.info(data);
               var ch = '{"name":"graphe1","items":'+JSON.stringify(data.results[1])+ '}';
               console.info(ch);
               console.info(JSON.parse(scatterSeries));
               scatterSeries.push(JSON.parse(ch));
               
        });
    
    
    /* chart data */
    
    this.scatterSeriesValue = ko.observableArray(scatterSeries);
    
    self.lineTypeOptions = [
        {id: 'straight', label: 'straight'},
        {id: 'curved', label: 'curved'},
        {id: 'stepped', label: 'stepped'},
        {id: 'segmented', label: 'segmented'},
        {id: 'none', label: 'none'}
    ];
  
  
}
```

The Json that I get from  look like this:
```
{ "results":[  
  [  
     {  
        "b":"0.110547334",
        "cost":"0.000000",
        "w":"1.998889"
     }
  ],
  [  
     {  
        "x":0,
        "y":0
     },
     {  
        "x":1,
        "y":2
     },
     {  
        "x":2,
        "y":4
     },
     {  
        "x":3,
        "y":6
     },
     {  
        "x":4,
        "y":8
     },
     {  
        "x":5,
        "y":10
     },
     {  
        "x":6,
        "y":12
     },
     {  
        "x":7,
        "y":14
     },
     {  
        "x":8,
        "y":16
     },
     {  
        "x":9,
        "y":18
     },
     {  
        "x":10,
        "y":20
     },
     {  
        "x":11,
        "y":22
     },
     {  
        "x":12,
        "y":24
     },
     {  
        "x":13,
        "y":26
     },
     {  
        "x":14,
        "y":28
     },
     {  
        "x":15,
        "y":30
     },
     {  
        "x":16,
        "y":32
     },
     {  
        "x":17,
        "y":34
     },
     {  
        "x":18,
        "y":36
     },
     {  
        "x":19,
        "y":38
     },
     {  
        "x":20,
        "y":40
     },
     {  
        "x":21,
        "y":42
     },
     {  
        "x":22,
        "y":44
     },
     {  
        "x":23,
        "y":46
     },
     {  
        "x":24,
        "y":48
     },
     {  
        "x":25,
        "y":50
     },
     {  
        "x":26,
        "y":52
     },
     {  
        "x":27,
        "y":54
     },
     {  
        "x":28,
        "y":56
     },
     {  
        "x":29,
        "y":58
     },
     {  
        "x":30,
        "y":60
     },
     {  
        "x":31,
        "y":62
     },
     {  
        "x":32,
        "y":64
     },
     {  
        "x":33,
        "y":66
     },
     {  
        "x":34,
        "y":68
     },
     {  
        "x":35,
        "y":70
     },
     {  
        "x":36,
        "y":72
     },
     {  
        "x":37,
        "y":74
     },
     {  
        "x":38,
        "y":76
     },
     {  
        "x":39,
        "y":78
     },
     {  
        "x":40,
        "y":80
     },
     {  
        "x":41,
        "y":82
     },
     {  
        "x":42,
        "y":84
     },
     {  
        "x":43,
        "y":86
     },
     {  
        "x":44,
        "y":88
     },
     {  
        "x":45,
        "y":90
     },
     {  
        "x":46,
        "y":92
     },
     {  
        "x":47,
        "y":94
     },
     {  
        "x":48,
        "y":96
     },
     {  
        "x":49,
        "y":98
     },
     {  
        "x":50,
        "y":100
     },
     {  
        "x":51,
        "y":102
     },
     {  
        "x":52,
        "y":104
     },
     {  
        "x":53,
        "y":106
     },
     {  
        "x":54,
        "y":108
     },
     {  
        "x":55,
        "y":110
     },
     {  
        "x":56,
        "y":112
     },
     {  
        "x":57,
        "y":114
     },
     {  
        "x":58,
        "y":116
     },
     {  
        "x":59,
        "y":118
     },
     {  
        "x":60,
        "y":120
     },
     {  
        "x":61,
        "y":122
     },
     {  
        "x":62,
        "y":124
     },
     {  
        "x":63,
        "y":126
     },
     {  
        "x":64,
        "y":128
     },
     {  
        "x":65,
        "y":130
     },
     {  
        "x":66,
        "y":132
     },
     {  
        "x":67,
        "y":134
     },
     {  
        "x":68,
        "y":136
     },
     {  
        "x":69,
        "y":138
     },
     {  
        "x":70,
        "y":140
     },
     {  
        "x":71,
        "y":142
     },
     {  
        "x":72,
        "y":144
     },
     {  
        "x":73,
        "y":146
     },
     {  
        "x":74,
        "y":148
     },
     {  
        "x":75,
        "y":150
     },
     {  
        "x":76,
        "y":152
     },
     {  
        "x":77,
        "y":154
     },
     {  
        "x":78,
        "y":156
     },
     {  
        "x":79,
        "y":158
     },
     {  
        "x":80,
        "y":160
     },
     {  
        "x":81,
        "y":162
     },
     {  
        "x":82,
        "y":164
     },
     {  
        "x":83,
        "y":166
     },
     {  
        "x":84,
        "y":168
     },
     {  
        "x":85,
        "y":170
     },
     {  
        "x":86,
        "y":172
     },
     {  
        "x":87,
        "y":174
     },
     {  
        "x":88,
        "y":176
     },
     {  
        "x":89,
        "y":178
     },
     {  
        "x":90,
        "y":180
     },
     {  
        "x":91,
        "y":182
     },
     {  
        "x":92,
        "y":184
     },
     {  
        "x":93,
        "y":186
     },
     {  
        "x":94,
        "y":188
     },
     {  
        "x":95,
        "y":190
     },
     {  
        "x":96,
        "y":192
     },
     {  
        "x":97,
        "y":194
     },
     {  
        "x":98,
        "y":196
     },
     {  
        "x":99,
        "y":198
     }
  ]]}
```

and what I want the variable  to hold is a table like this one:
```
[  {  
  name:"graphe1",
  items:[  
     {  
        x:8,
        y:2
     },
     {  
        x:15,
        y:15
     },
     {  
        x:25,
        y:26
     },
     {  
        x:33,
        y:22
     },
     {  
        x:36,
        y:40
     }
  ]},]
```

what I get in the console about the string  is this:
```
{"name":"graphe1","items":[{"x":0,"y":0},{"x":1,"y":2},{"x":2,"y":4},{"x":3,"y":6},{"x":4,"y":8},{"x":5,"y":10},{"x":6,"y":12},{"x":7,"y":14},{"x":8,"y":16},{"x":9,"y":18},{"x":10,"y":20},{"x":11,"y":22},{"x":12,"y":24},{"x":13,"y":26},{"x":14,"y":28},{"x":15,"y":30},{"x":16,"y":32},{"x":17,"y":34},{"x":18,"y":36},{"x":19,"y":38},{"x":20,"y":40},{"x":21,"y":42},{"x":22,"y":44},{"x":23,"y":46},{"x":24,"y":48},{"x":25,"y":50},{"x":26,"y":52},{"x":27,"y":54},{"x":28,"y":56},{"x":29,"y":58},{"x":30,"y":60},{"x":31,"y":62},{"x":32,"y":64},{"x":33,"y":66},{"x":34,"y":68},{"x":35,"y":70},{"x":36,"y":72},{"x":37,"y":74},{"x":38,"y":76},{"x":39,"y":78},{"x":40,"y":80},{"x":41,"y":82},{"x":42,"y":84},{"x":43,"y":86},{"x":44,"y":88},{"x":45,"y":90},{"x":46,"y":92},{"x":47,"y":94},{"x":48,"y":96},{"x":49,"y":98},{"x":50,"y":100},{"x":51,"y":102},{"x":52,"y":104},{"x":53,"y":106},{"x":54,"y":108},{"x":55,"y":110},{"x":56,"y":112},{"x":57,"y":114},{"x":58,"y":116},{"x":59,"y":118},{"x":60,"y":120},{"x":61,"y":122},{"x":62,"y":124},{"x":63,"y":126},{"x":64,"y":128},{"x":65,"y":130},{"x":66,"y":132},{"x":67,"y":134},{"x":68,"y":136},{"x":69,"y":138},{"x":70,"y":140},{"x":71,"y":142},{"x":72,"y":144},{"x":73,"y":146},{"x":74,"y":148},{"x":75,"y":150},{"x":76,"y":152},{"x":77,"y":154},{"x":78,"y":156},{"x":79,"y":158},{"x":80,"y":160},{"x":81,"y":162},{"x":82,"y":164},{"x":83,"y":166},{"x":84,"y":168},{"x":85,"y":170},{"x":86,"y":172},{"x":87,"y":174},{"x":88,"y":176},{"x":89,"y":178},{"x":90,"y":180},{"x":91,"y":182},{"x":92,"y":184},{"x":93,"y":186},{"x":94,"y":188},{"x":95,"y":190},{"x":96,"y":192},{"x":97,"y":194},{"x":98,"y":196},{"x":99,"y":198}]}
```

Any help please?!! :( :(


----------
        
## Answer \#0

**Accepted** Vote: 41

Created at 2018-06-30 20:59:11

------------

You are calling:

```
JSON.parse(scatterSeries)
```


But when you defined `scatterSeries`, you said:

```
var scatterSeries = [];
```


When you try to parse it as JSON it is converted to a string (`""`), which is empty, so you reach the end of the string before having any of the possible content of a JSON text.

`scatterSeries` is not JSON. Do not try to parse it as JSON.

`data` is not JSON either (`getJSON` will parse it as JSON automatically).

`ch` is JSON … but shouldn't be. You should just create a plain object in the first place:

```
var ch = {
    "name": "graphe1",
    "items": data.results[1]
};

scatterSeries.push(ch);
```


In short, for what you are doing, you shouldn't have `JSON.parse` anywhere in your code. The only place it should be is in the jQuery library itself.


------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-05-30 18:56:39

------------

I was trying to debug this same error (I was writing code in TypeScript), but no matter what I did, the error didn't go away, it was really persistent.
I tried to look into the solution in many blogs and also this SO thread. I tried everything and nothing worked out.
Finally, I saw that I created the required file named `products.json` in which I gave a single extra newline (meaning, previously the file had 1 empty line, now it had 2 empty lines) and was following [good GitHub practices as mentioned here](https://stackoverflow.com/questions/5813311/whats-the-significance-of-the-no-newline-at-end-of-file-log).
Because of that single extra newline in my `products.json` file, I wasted 2 hours fixing a bug that didn't exist in the first place.
Finally when I deleted the entire `products.json` file and re-ran the entire thing, I didn't get any runtime errors and everything was running without any problems.
What I learned by going through all this?
It's that 


------------
    
    
## Answer \#2

 Vote: 2

Created at 2018-06-30 20:59:22

------------

Remove this line from your code:

```
console.info(JSON.parse(scatterSeries));
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2018-06-30 21:00:03

------------

You define `var scatterSeries = [];`, and then try to parse it as a json string at `console.info(JSON.parse(scatterSeries));` which obviously fails. The variable is converted to an empty string, which causes an "unexpected end of input" error when trying to parse it.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2018-06-30 20:59:54

------------

Issue is with the Json.parse of empty array - scatterSeries , as you doing console log of scatterSeries before pushing ch 

```
var data = { "results":[  
  [  
     {  
        "b":"0.110547334",
        "cost":"0.000000",
        "w":"1.998889"
     }
  ],
  [  
     {  
        "x":0,
        "y":0
     },
     {  
        "x":1,
        "y":2
     },
     {  
        "x":2,
        "y":4
     },
     {  
        "x":3,
        "y":6
     },
     {  
        "x":4,
        "y":8
     },
     {  
        "x":5,
        "y":10
     },
     {  
        "x":6,
        "y":12
     },
     {  
        "x":7,
        "y":14
     },
     {  
        "x":8,
        "y":16
     },
     {  
        "x":9,
        "y":18
     },
     {  
        "x":10,
        "y":20
     },
     {  
        "x":11,
        "y":22
     },
     {  
        "x":12,
        "y":24
     },
     {  
        "x":13,
        "y":26
     },
     {  
        "x":14,
        "y":28
     },
     {  
        "x":15,
        "y":30
     },
     {  
        "x":16,
        "y":32
     },
     {  
        "x":17,
        "y":34
     },
     {  
        "x":18,
        "y":36
     },
     {  
        "x":19,
        "y":38
     },
     {  
        "x":20,
        "y":40
     },
     {  
        "x":21,
        "y":42
     },
     {  
        "x":22,
        "y":44
     },
     {  
        "x":23,
        "y":46
     },
     {  
        "x":24,
        "y":48
     },
     {  
        "x":25,
        "y":50
     },
     {  
        "x":26,
        "y":52
     },
     {  
        "x":27,
        "y":54
     },
     {  
        "x":28,
        "y":56
     },
     {  
        "x":29,
        "y":58
     },
     {  
        "x":30,
        "y":60
     },
     {  
        "x":31,
        "y":62
     },
     {  
        "x":32,
        "y":64
     },
     {  
        "x":33,
        "y":66
     },
     {  
        "x":34,
        "y":68
     },
     {  
        "x":35,
        "y":70
     },
     {  
        "x":36,
        "y":72
     },
     {  
        "x":37,
        "y":74
     },
     {  
        "x":38,
        "y":76
     },
     {  
        "x":39,
        "y":78
     },
     {  
        "x":40,
        "y":80
     },
     {  
        "x":41,
        "y":82
     },
     {  
        "x":42,
        "y":84
     },
     {  
        "x":43,
        "y":86
     },
     {  
        "x":44,
        "y":88
     },
     {  
        "x":45,
        "y":90
     },
     {  
        "x":46,
        "y":92
     },
     {  
        "x":47,
        "y":94
     },
     {  
        "x":48,
        "y":96
     },
     {  
        "x":49,
        "y":98
     },
     {  
        "x":50,
        "y":100
     },
     {  
        "x":51,
        "y":102
     },
     {  
        "x":52,
        "y":104
     },
     {  
        "x":53,
        "y":106
     },
     {  
        "x":54,
        "y":108
     },
     {  
        "x":55,
        "y":110
     },
     {  
        "x":56,
        "y":112
     },
     {  
        "x":57,
        "y":114
     },
     {  
        "x":58,
        "y":116
     },
     {  
        "x":59,
        "y":118
     },
     {  
        "x":60,
        "y":120
     },
     {  
        "x":61,
        "y":122
     },
     {  
        "x":62,
        "y":124
     },
     {  
        "x":63,
        "y":126
     },
     {  
        "x":64,
        "y":128
     },
     {  
        "x":65,
        "y":130
     },
     {  
        "x":66,
        "y":132
     },
     {  
        "x":67,
        "y":134
     },
     {  
        "x":68,
        "y":136
     },
     {  
        "x":69,
        "y":138
     },
     {  
        "x":70,
        "y":140
     },
     {  
        "x":71,
        "y":142
     },
     {  
        "x":72,
        "y":144
     },
     {  
        "x":73,
        "y":146
     },
     {  
        "x":74,
        "y":148
     },
     {  
        "x":75,
        "y":150
     },
     {  
        "x":76,
        "y":152
     },
     {  
        "x":77,
        "y":154
     },
     {  
        "x":78,
        "y":156
     },
     {  
        "x":79,
        "y":158
     },
     {  
        "x":80,
        "y":160
     },
     {  
        "x":81,
        "y":162
     },
     {  
        "x":82,
        "y":164
     },
     {  
        "x":83,
        "y":166
     },
     {  
        "x":84,
        "y":168
     },
     {  
        "x":85,
        "y":170
     },
     {  
        "x":86,
        "y":172
     },
     {  
        "x":87,
        "y":174
     },
     {  
        "x":88,
        "y":176
     },
     {  
        "x":89,
        "y":178
     },
     {  
        "x":90,
        "y":180
     },
     {  
        "x":91,
        "y":182
     },
     {  
        "x":92,
        "y":184
     },
     {  
        "x":93,
        "y":186
     },
     {  
        "x":94,
        "y":188
     },
     {  
        "x":95,
        "y":190
     },
     {  
        "x":96,
        "y":192
     },
     {  
        "x":97,
        "y":194
     },
     {  
        "x":98,
        "y":196
     },
     {  
        "x":99,
        "y":198
     }
  ]]};

var scatterSeries = []; 

var ch = '{"name":"graphe1","items":'+JSON.stringify(data.results[1])+ '}';
               console.info(ch);
               
               scatterSeries.push(JSON.parse(ch));
console.info(scatterSeries);
```



code sample - [https://codepen.io/nagasai/pen/GGzZVB](https://codepen.io/nagasai/pen/GGzZVB)


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-03-21 02:47:11

------------

Encountered this error in the following function when I tried to pull 'nothing' from LS
Quick fix for posterity
```
const getUserFromLocalStorage = () => {
    try {
        return JSON.parse(localStorage.getItem('user') || '');
    } catch (error) {
        return null;
    }
};
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-05-21 08:55:12

------------

In my case, using .NET 6, the problem was that the controller was declared as `void` and did not return any value.
Simply changing the method declaration to `int` and adding `return 0;` solved this issue for me.
```
[HttpPost]
public int MyMethod(string text)
{
    _logger.Log(text);
    return 0;
}
```



------------
    
    
## Answer \#7

 Vote: -1

Created at 2022-03-09 05:21:25

------------

This is because of the data that you are trying to parse is not in a parsable format .
so i suggest to check the data is parsable or not before define the parse.
```
const IsParsable = (data) => {
            try {
                  JSON.parse(data);
                 } catch (e) {
                  return false;
                }
             return true;
    }
        
    var Obj= JSON.stringify({
                "name": "graphe1",
               "items": data.results[1]
     })

    return  IsParsable (Obj) ?  JSON.parse(Obj) : false
```



------------
    
    