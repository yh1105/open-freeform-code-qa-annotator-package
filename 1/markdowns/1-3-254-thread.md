
# Post \#63945330 [Link](https://stackoverflow.com/questions/63945330/)

## Plotly: How to add text labels to a histogram?

**Vote**: 5 (499/702) **Views**: 11810 (397/702) 

**Internal ID** \#1-3-254

Created at 2020-09-17 19:52:37

Tags: `python` `plotly` `plotly-express`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Is there a way how to display the counted value of the histogram aggregate in the Plotly.Express histogram?
`px.histogram(pd.DataFrame({"A":[1,1,1,2,2,3,3,3,4,4,4,5]}),x="A")`
[](https://i.stack.imgur.com/mML9n.png)
If I would use regular histogram, I can specify `text` parameter which direct to the column which contain the value to display.
`px.bar(pd.DataFrame({"val":[1,2,3,4,5], "height": [3,2,3,3,1]}), x="val", y="height", text="height")`
[](https://i.stack.imgur.com/Pqvhj.png)
But with histograms, this value is calculated and it's not even part of the `fig.to_dict()`. Is there a way to add the text labels into histogram?
Using the answers below, I've summarized the finding to an article - [https://towardsdatascience.com/histograms-with-plotly-express-complete-guide-d483656c5ad7](https://towardsdatascience.com/histograms-with-plotly-express-complete-guide-d483656c5ad7)


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2020-09-18 21:32:37

------------

As far as I know, plotly histograms do not have a text attribute. It also turns out that it's complicated if at all possible to retrieve the applied x and y values and just throw them into appropriate annotations. Your best option seems to be to take care of the binning using [numpy.histogram](https://numpy.org/doc/stable/reference/generated/numpy.histogram.html) and the set up your figure using `go.Bar`. The code snippet below will produce the following plot:
[](https://i.stack.imgur.com/pmHje.png)

### Complete code:


```
import numpy as np
import plotly.express as px
import plotly.graph_objects as go

# sample data
df = px.data.tips()

# create bins
bins = [0, 10, 20, 30, 40, 50]
counts, bins = np.histogram(df.total_bill, bins=bins)
#bins2 = 0.5 * (bins1[:-1] + bins2[1:])

fig = go.Figure(go.Bar(x=bins, y=counts))
fig.data[0].text = counts
fig.update_traces(textposition='inside', textfont_size=8)
fig.update_layout(bargap=0)


fig.update_traces(marker_color='blue', marker_line_color='blue',
                  marker_line_width=1, opacity=0.4)

fig.show()
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2022-02-18 13:46:58

------------

The `text_auto` parameter set to `True` will do what you want.
Taking your example code, this is what i get :
```
fig = px.histogram(pd.DataFrame({"A":[1,1,1,2,2,3,3,3,4,4,4,5]}),x="A", 
text_auto=True)
fig.show()
```

Being a new member i cannot embed the screenshot yet, but here is a link.
[Histogram](https://i.stack.imgur.com/FF8ip.png)
A bit late but hope this will help.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-09-28 21:46:50

------------

I had his same problem this morning while trying to plot a histogram of TDD percentages. Using plotly, I wanted to normalize (histnorm: 'percent') so I could see percentages of my monthly TDD values instead of the counts. I found this solution by simply doing a 
First, I printed the histogram to the console and saw this output...
```
Figure({
'data': [{'alignmentgroup': 'True',
          'bingroup': 'x',
          'histnorm': 'percent',
          'hovertemplate': 'Total Demand Distortion TDD %=%{x}<br>count=%{y}<extra></extra>',
          'legendgroup': '',
          'marker': {'color': '#636efa'},
          'name': '',
          'offsetgroup': '',
          'orientation': 'v',
          'showlegend': False,
          'type': 'histogram',
          'x': array([0.67, 0.68, 0.68, ..., 2.41, 2.48, 2.01]),
          'xaxis': 'x',
          'yaxis': 'y'}],
'layout': {'barmode': 'relative',
           'legend': {'tracegroupgap': 0},
           'template': '...',
           'title': {'text': 'Percent Histogram of TDD%'},
           'xaxis': {'anchor': 'y', 'domain': [0.0, 1.0], 'title': {'text': 'Total Demand Distortion TDD %'}},
           'yaxis': {'anchor': 'x', 'domain': [0.0, 1.0], 'title': {'text': 'count'}, 'type': 'log'}}
```

Now I can clearly see that to change this, I do a
```
tdd_hist.layout.yaxis.title.text = 'Percent'
```

And it works!


------------
    
    