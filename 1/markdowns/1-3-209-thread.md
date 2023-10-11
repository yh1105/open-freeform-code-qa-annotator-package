
# Post \#67481900 [Link](https://stackoverflow.com/questions/67481900/)

## How to add a mean line to a seaborn stripplot or swarmplot

**Vote**: 7 (449/702) **Views**: 4948 (523/702) 

**Internal ID** \#1-3-209

Created at 2021-05-11 06:57:29

Tags: `python` `matplotlib` `seaborn` `boxplot` `swarmplot`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a rather simple strip plot with vertical data.
```
planets = sns.load_dataset("planets")
sns.stripplot(x="method", y="distance", data=planets, size=4, color=".7")
plt.xticks(rotation=45, ha="right")
plt.show()
```

I want to plot the mean of each x-element (`method`) as a small horizontal bar similar to what you get with:
```
sns.boxplot(
    x="method",
    y="distance",
    data=planets,
    whis=[50, 50],
    showfliers=False,
    showbox=False,
    showcaps=False
)
```

But without the vertical lines (with `whis=[50,50]` just spots) for the first / third quartile and showing mean instead of median. Maybe there is a more elegant solution not involving a Boxplot.
Thanks in advance.


----------
        
## Answer \#0

**Accepted** Vote: 10

Created at 2021-05-18 03:17:05

------------

- [matplotlib.pyplot.boxplot](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.boxplot.html)- `showmeans=True`- `meanline=True`- `meanprops={'color': 'k', 'ls': '-', 'lw': 2}`- [matplotlib.lines.Line2D](https://matplotlib.org/stable/api/_as_gen/matplotlib.lines.Line2D.html)- `medianprops={'visible': False}`- `whiskerprops={'visible': False}`- `zorder=10`- `matplotlib v3.4.2``seaborn v0.11.1`
```
import seaborn as sns
import matplotlib.pyplot as plt

# load the dataset
planets = sns.load_dataset("planets")

p = sns.stripplot(x="method", y="distance", data=planets, size=4, color=".7")
plt.xticks(rotation=45, ha="right")
p.set(yscale='log')

# plot the mean line
sns.boxplot(showmeans=True,
            meanline=True,
            meanprops={'color': 'k', 'ls': '-', 'lw': 2},
            medianprops={'visible': False},
            whiskerprops={'visible': False},
            zorder=10,
            x="method",
            y="distance",
            data=planets,
            showfliers=False,
            showbox=False,
            showcaps=False,
            ax=p)
plt.show()
```

[](https://i.stack.imgur.com/xhXVY.png)
- `seaborn.swarmplot`
[](https://i.stack.imgur.com/4R7cg.png)


------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-05-18 03:43:34

------------

Here's a solution using `ax.hlines` with find the mean using `groupby` and list comprehension:
```
import seaborn as sns
import matplotlib.pyplot as plt

# load the dataset
planets = sns.load_dataset("planets")

p = sns.stripplot(x="method", y="distance", data=planets, size=4, color=".7", zorder=1)
plt.xticks(rotation=45, ha="right")
p.set(yscale='log');

df_mean = planets.groupby('method', sort=False)['distance'].mean()
_ = [p.hlines(y, i-.25, i+.25, zorder=2) for i, y in df_mean.reset_index()['distance'].items()]
```

Output:
[](https://i.stack.imgur.com/J5d2I.png)


------------
    
    
## Answer \#2

 Vote: 5

Created at 2021-05-19 14:23:12

------------

Here's another hack that is similar to the boxplot idea but requires less overriding: draw a `pointplot` but with a confidence interval of width 0, and activate the errorbar "caps" to get a horizontal line with a parametrizable width:
```
planets = sns.load_dataset("planets")
spec = dict(x="method", y="distance", data=planets)
sns.stripplot(**spec, size=4, color=".7")
sns.pointplot(**spec, join=False, ci=0, capsize=.7, scale=0)
plt.xticks(rotation=45, ha="right")
```

[](https://i.stack.imgur.com/gZtma.png)
One downside that is evident here is that bootstrapping gets skipped for groups with a single observation, so you don't get a mean line there. This may or may not be a problem in an actual application.
Another trick would be to do the groupby yourself and then draw a scatterplot with a very wide vertical line marker:
```
planets = sns.load_dataset("planets")
variables = dict(x="method", y="distance")
sns.stripplot(data=planets, **variables, size=4, color=".7")
sns.scatterplot(
    data=planets.groupby("method")["distance"].mean().reset_index(),
    **variables, marker="|", s=2, linewidth=25
)
plt.xticks(rotation=45, ha="right")
```

[](https://i.stack.imgur.com/KFB7Q.png)


------------
    
    