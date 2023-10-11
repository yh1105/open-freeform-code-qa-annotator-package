
# Post \#61101919 [Link](https://stackoverflow.com/questions/61101919/)

## How can I add an element to a PyTorch tensor along a certain dimension?

**Vote**: 7 (449/702) **Views**: 30943 (228/702) 

**Internal ID** \#1-3-217

Created at 2020-04-08 13:31:27

Tags: `python` `pytorch` `tensor`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a tensor `inps`, which has a size of `[64, 161, 1]` and I have some new data `d` which has a size of `[64, 161]`. How can I add `d` to `inps` such that the new size is `[64, 161, 2]`?


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2020-04-08 14:23:04

------------

There is a cleaner way by using `.unsqueeze()` and `torch.cat()`, which makes direct use of the PyTorch interface:

```
import torch

# create two sample vectors
inps = torch.randn([64, 161, 1])
d = torch.randn([64, 161])

# bring d into the same format, and then concatenate tensors
new_inps = torch.cat((inps, d.unsqueeze(2)), dim=-1)
print(new_inps.shape)  # [64, 161, 2]
```


Essentially, unsqueezing the second dimension already brings the two tensors into the same shape; you just have to be careful to unsqueeze along the right dimension.
Similarly, the [concatenation](https://pytorch.org/docs/stable/torch.html#torch.cat) is unfortunately named differently from the otherwise similarly named NumPy function, but behave the same. Note that instead of letting `torch.cat` figure out the dimension by providing `dim=-1`, you can also explicitly provide the dimension to concatenate along, in this case by replacing it with `dim=2`.

Keep in mind the [difference between concatenation and stacking](https://stackoverflow.com/questions/54307225/whats-the-difference-between-torch-stack-and-torch-cat-functions), which is helpful for similar problems with tensor dimensions.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2020-04-08 13:57:53

------------

You have to first reshape `d` so that it has a third dimension along which concatenation becomes possible. After it has a third dimension and the two tensors have the same number of dimensions, then you can use torch.cat((inps, d),2) to stack them.

```
old_shape = tuple(d.shape)
new_shape = old_shape + (1,)
inps_new = torch.cat( (inps, d.view( new_shape ), 2)
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-03-09 14:59:43

------------

Alternatively, you can achieve this by squeezing the larger tensor and stacking:
```
inps = torch.randn([64, 161, 1])
d = torch.randn([64, 161])

res = torch.stack((inps.squeeze(), d), dim=-1)

res.shape
>>> [64, 161, 2]
```



------------
    
    