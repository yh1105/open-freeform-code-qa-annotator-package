
# Post \#67238650 [Link](https://stackoverflow.com/questions/67238650/)

## Problem for display a chart with Chart.js and Angular

**Vote**: 9 (409/702) **Views**: 9765 (419/702) 

**Internal ID** \#0-0-83

Created at 2021-04-24 01:49:25

Tags: `angular` `chart.js`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I want to display a chart using chart.js in angular. In first time I try to implement a example took on the net and I have no problem during the compilation. This is the code in my .ts :
```
import { Component, OnInit } from '@angular/core';
import { Chart } from "chart.js";

@Component({ 
   ...
})

export class MyChartComponent implements OnInit {
    constructor(){
    }

    ngOnInit(): void {
      new Chart("myChart", {
      type: 'bar',
      data: {
        labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
        datasets: [{
          label: '# of Votes',
          data: [12, 19, 3, 5, 2, 3],
          backgroundColor: [
            'rgba(255, 99, 132, 0.2)',
            'rgba(54, 162, 235, 0.2)',
            'rgba(255, 206, 86, 0.2)',
            'rgba(75, 192, 192, 0.2)',
            'rgba(153, 102, 255, 0.2)',
            'rgba(255, 159, 64, 0.2)'
          ],
          borderColor: [
            'rgba(255, 99, 132, 1)',
            'rgba(54, 162, 235, 1)',
            'rgba(255, 206, 86, 1)',
            'rgba(75, 192, 192, 1)',
            'rgba(153, 102, 255, 1)',
            'rgba(255, 159, 64, 1)'
          ],
          borderWidth: 1
        }]
      },
      options: {
        scales: {
          y: {
            beginAtZero: true
          }
        }
      }
    });
    }
}
```

And in my .html I have :
```
<div id="divChart">
  <canvas id="myChart"></canvas>
</div>
```

But when i start the serve, I have this error :
```
ERROR Error: "linear" is not a registered scale.
    _get chart.esm.js:4622
    getScale chart.esm.js:4576
    buildOrUpdateScales chart.esm.js:5224
    each helpers.segment.js:102
    buildOrUpdateScales chart.esm.js:5211
    update chart.esm.js:5336
    Chart chart.esm.js:5096
    ngOnInit my-chart.component.ts:32
    Angular 37
    zUnb main.ts:11
    Webpack 6

ERROR TypeError: area is undefined
    _isPointInArea helpers.segment.js:1266
    getNearestItems chart.esm.js:2465
    nearest chart.esm.js:2548
    getElementsAtEventForMode chart.esm.js:5503
    _handleEvent chart.esm.js:5737
    _eventHandler chart.esm.js:5720
    listener chart.esm.js:5617
    proxy chart.esm.js:3022
    throttled helpers.segment.js:28
    Angular 17
    throttled helpers.segment.js:26
    Angular 14
    addListener chart.esm.js:2905
    createProxyAndListen chart.esm.js:3028
    addEventListener chart.esm.js:3071
    _add chart.esm.js:5605
    bindEvents chart.esm.js:5619
    each helpers.segment.js:102
    bindEvents chart.esm.js:5619
    _initialize chart.esm.js:5129
    Chart chart.esm.js:5094
    ngOnInit my-chart.component.ts:32
    Angular 13
```

and many other errors.
Can you help me please ?


----------
        
## Answer \#0

**Accepted** Vote: 10

Created at 2021-04-24 06:44:53

------------

Chart.js 3 is [tree-shakeable](https://webpack.js.org/guides/tree-shaking/), so it is necessary to import and register the controllers, elements, scales and plugins you are going to use.
> Please take a look at [Bundlers (Webpack, Rollup, etc.)](https://www.chartjs.org/docs/latest/getting-started/integration.html#bundlers-webpack-rollup-etc) from the Chart.js documentation.
In your case, this could be done as follows ():
```
import { Chart, BarElement, BarController, CategoryScale, Decimation, Filler, Legend, Title, Tooltip} from 'chart.js';

export class MyChartComponent implements OnInit {
  
    constructor() {
        Chart.register(BarElement, BarController, CategoryScale, Decimation, Filler, Legend, Title, Tooltip);
    }
    ...
}
```



------------
    
    
## Answer \#1

 Vote: 32

Created at 2021-05-22 16:38:58

------------

```
import { Chart, registerables} from 'chart.js';

Chart.register(...registerables);
```




------------
    
    
## Answer \#2

 Vote: 5

Created at 2021-11-19 13:48:25

------------

I have the same problem, I fixed it this way.
:
```
import Chart from 'chart.js/auto';
```

Instead of this:
```
import { Chart } from 'node_modules/chart.js';
```



------------
    
    
## Answer \#3

 Vote: 3

Created at 2022-09-18 09:18:54

------------

To register it  use the 
```
constructor(){
       Chart.register(...registerables);
    }
```



------------
    
    