
# Post \#65197025 [Link](https://stackoverflow.com/questions/65197025/)

## How can I create a forest plot?

**Vote**: 5 (499/702) **Views**: 6190 (501/702) 

**Internal ID** \#1-3-172

Created at 2020-12-08 10:18:37

Tags: `python-3.x` `forestplot`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I would like to combine different risk ratios into one forest plot. I would expect the output to be similar to `metamiss` in STATA or `metafor` in R. How can I do this in Python?


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2021-01-17 12:43:12

------------

By using the zEPID package I create a forest plot of different risk ratios.
```
import matplotlib.image as mpimg
import numpy as np
import matplotlib.pyplot as plt
import zepid
from zepid.graphics import EffectMeasurePlot

labs = ["ACA(Isq=41.37% Tausq=0.146 pvalue=0.039 )",
        "ICA0(Isq=25.75% Tausq=0.092 pvalue=0.16 )",
        "ICA1(Isq=60.34% Tausq=0.121 pvalue=0.00 )",
        "ICAb(Isq=25.94% Tausq=0.083 pvalue=0.16 )",
        "ICAw(Isq=74.22% Tausq=0.465 pvalue=0.00 )"]
measure = [2.09,2.24,1.79,2.71,1.97]
lower = [1.49,1.63,1.33,2.00,1.25]
upper = [2.92,3.07,2.42,3.66,3.11]
p = EffectMeasurePlot(label=labs, effect_measure=measure, lcl=lower, ucl=upper)
p.labels(effectmeasure='RR')
p.colors(pointshape="D")
ax=p.plot(figsize=(7,3), t_adjuster=0.09, max_value=4, min_value=0.35 )
plt.title("Random Effect Model(Risk Ratio)",loc="right",x=1, y=1.045)
plt.suptitle("Missing Data Imputation Method",x=-0.1,y=0.98)
ax.set_xlabel("Favours Control      Favours Haloperidol       ", fontsize=10)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['bottom'].set_visible(True)
ax.spines['left'].set_visible(False)
plt.savefig("Missing Data Imputation Method",bbox_inches='tight')
```

[](https://i.stack.imgur.com/dIRMc.png)


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-04-29 15:12:59

------------

The [statsmodels](https://www.statsmodels.org/devel/examples/notebooks/generated/metaanalysis1.html#) library has an API for doing simple meta-analysis and plotting forest plots. It supports DerSimonian-Laird (chi2) and Paule-Mandel (iterated). See the [statsmodel docs](https://www.statsmodels.org/devel/examples/notebooks/generated/metaanalysis1.html#) for more use cases, options and examples.
An example from their docs:
```
import numpy as np
from statsmodels.stats.meta_analysis import combine_effects

# dummy data
mean_effect = np.array([61.00,61.40,62.21,62.30,62.34,62.60,62.70,62.84,65.90])
var_effect = np.array([0.2025,1.2100,0.0900,0.2025,0.3844,0.5625,0.0676,0.0225,1.8225])
idx = ['lab1','lab2','lab3','lab4','lab5','lab6','lab7','lab8','lab9']

# meta-analysis and forest plot
results = combine_effects(mean_effect, var_effect, method_re="chi2", use_t=True, row_names=idx)
print(results.summary_frame())
fig = results.plot_forest()
```

Output:
```
eff    sd_eff     ci_low     ci_upp      w_fe      w_re
lab1               61.000000  0.450000  60.118016  61.881984  0.057436  0.123113
lab2               61.400000  1.100000  59.244040  63.555960  0.009612  0.040314
lab3               62.210000  0.300000  61.622011  62.797989  0.129230  0.159749
lab4               62.300000  0.450000  61.418016  63.181984  0.057436  0.123113
lab5               62.340000  0.620000  61.124822  63.555178  0.030257  0.089810
lab6               62.600000  0.750000  61.130027  64.069973  0.020677  0.071005
lab7               62.700000  0.260000  62.190409  63.209591  0.172052  0.169810
lab8               62.840000  0.150000  62.546005  63.133995  0.516920  0.194471
lab9               65.900000  1.350000  63.254049  68.545951  0.006382  0.028615
fixed effect       62.583397  0.107846  62.334704  62.832090  1.000000       NaN
random effect      62.390139  0.245750  61.823439  62.956838       NaN  1.000000
fixed effect wls   62.583397  0.189889  62.145512  63.021282  1.000000       NaN
random effect wls  62.390139  0.294776  61.710384  63.069893       NaN  1.000000
```

[](https://i.stack.imgur.com/W3npz.png)
I’d also recommend having a read through the docs for the individual methods such as [combine_effects()](https://www.statsmodels.org/devel/generated/statsmodels.stats.meta_analysis.combine_effects.html) which contains additional notes and references regarding the implementation.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-09-20 10:41:46

------------

Since I haven't found a customizable package to create a forest plot, I developed [myforestplot](https://toshiakiasakura.github.io/myforestplot/index.html) for that purpose.
The following is one example of a forest plot using titanic dataset.
```
import pandas as pd
import statsmodels.api as sm
import statsmodels.formula.api as smf
import numpy as np
import matplotlib.pyplot as plt

import myforestplot as mfp

data = (pd.read_csv("titanic.csv")
      [["survived", "pclass", "sex", "age", "embark_town"]]
      .dropna()
      )
ser = data["age"]
data["age"] = (ser
             .mask(ser >= 40, "40 or more")
             .mask(ser < 40, "20_39")
             .mask(ser <20, "0_19")
             )
res = smf.logit("survived ~ sex + age + embark_town", data=data).fit()

order = ["age", "sex", "embark_town"]
cont_cols = []
item_order = {"embark_town": ['Southampton', 'Cherbourg', 'Queenstown'],
              "age": ["0_19", "20_39", "40 or more"]
              }
df = mfp.statsmodels_pretty_result_dataframe(data, res,
                                             order=order,
                                             cont_cols=cont_cols,
                                             item_order=item_order,
                                             fml=".3f",
                                             )
```

`df` is a dataframe for creating a forest plot.
```
plt.rcParams["font.size"] = 8
fp = mfp.SimpleForestPlot(ratio=(8,3), dpi=150, figsize=(5,3), df=df,
                          vertical_align=True)
fp.errorbar(errorbar_kwds=None, log_scale=True)

xticklabels = [0.1, 0.5, 1.0, 2.0]
fp.ax2.set_xlim(np.log([0.1, 1.5]))
fp.ax2.set_xticks(np.log(xticklabels))
fp.ax2.set_xticklabels(xticklabels)
fp.ax2.set_xlabel("OR (log scale)")
fp.ax2.axvline(x=0, ymin=0, ymax=1.0, color="black", alpha=0.5)

fp.ax1.set_xlim([0.35, 1])
fp.embed_cate_strings("category", 0.3, header="Category",
                      text_kwds=dict(fontweight="bold"),
                      header_kwds=dict(fontweight="bold")
                      )
fp.embed_strings("item", 0.36, header="", replace={"age":""})
fp.embed_strings("nobs", 0.60, header="N")
fp.embed_strings("risk_pretty", 0.72, header="OR (95% CI)")
fp.horizontal_variable_separators()
fp.draw_outer_marker(log_scale=True, scale=0.008)
plt.show()
```

and we obtain the figure.
[A forest plot image](https://i.stack.imgur.com/KNB4q.png)


------------
    
    