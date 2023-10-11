
# Post \#65343377 [Link](https://stackoverflow.com/questions/65343377/)

## Adam optimizer with warmup on PyTorch

**Vote**: 14 (336/702) **Views**: 25056 (263/702) 

**Internal ID** \#1-3-277

Created at 2020-12-17 15:12:56

Tags: `python` `machine-learning` `pytorch`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

In the paper [Attention is all you need](https://papers.nips.cc/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Paper.pdf), under section 5.3, the authors suggested to increase the learning rate linearly and then decrease proportionally to the inverse square root of steps.
[](https://i.stack.imgur.com/GQurA.png)
How do we implement this in PyTorch with [Adam](https://pytorch.org/docs/stable/optim.html#torch.optim.Adam) optimizer? Preferably without additional packages.


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2020-12-17 16:07:59

------------

PyTorch provides  for implementing various methods of adjusting the learning rate during the training process.
Some simple LR-schedulers are are already implemented and can be found here: [https://pytorch.org/docs/stable/optim.html#how-to-adjust-learning-rate](https://pytorch.org/docs/stable/optim.html#how-to-adjust-learning-rate)
In your special case you can - just like the other LR-schedulers do - subclass [_LRScheduler](https://pytorch.org/docs/stable/_modules/torch/optim/lr_scheduler.html) for implementing a variable schedule based on the number of epochs. For a bare-bones method you only need to implement `__init__()` and `get_lr()` methods.
Just note that many of these schedulers expect you to call `.step()` once per epoch. But you can also update it more frequently or even pass a custom argument just like in the cosine-annealing LR-scheduler: [https://pytorch.org/docs/stable/_modules/torch/optim/lr_scheduler.html#CosineAnnealingLR](https://pytorch.org/docs/stable/_modules/torch/optim/lr_scheduler.html#CosineAnnealingLR)


------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-03-18 17:41:03

------------

As suggested in the last comment, we can use the class introduced by [https://nlp.seas.harvard.edu/2018/04/03/attention.html#optimizer](https://nlp.seas.harvard.edu/2018/04/03/attention.html#optimizer). But this answer will give an error unless we define a function to update the state_dict.
So here's the full Scheduler:
```
class NoamOpt:
    "Optim wrapper that implements rate."
    def __init__(self, model_size, warmup, optimizer):
        self.optimizer = optimizer
        self._step = 0
        self.warmup = warmup
        self.model_size = model_size
        self._rate = 0
    
    def state_dict(self):
        """Returns the state of the warmup scheduler as a :class:`dict`.
        It contains an entry for every variable in self.__dict__ which
        is not the optimizer.
        """
        return {key: value for key, value in self.__dict__.items() if key != 'optimizer'}
    
    def load_state_dict(self, state_dict):
        """Loads the warmup scheduler's state.
        Arguments:
            state_dict (dict): warmup scheduler state. Should be an object returned
                from a call to :meth:`state_dict`.
        """
        self.__dict__.update(state_dict) 
        
    def step(self):
        "Update parameters and rate"
        self._step += 1
        rate = self.rate()
        for p in self.optimizer.param_groups:
            p['lr'] = rate
        self._rate = rate
        self.optimizer.step()
        
    def rate(self, step = None):
        "Implement `lrate` above"
        if step is None:
            step = self._step
        return (self.model_size ** (-0.5) *
            min(step ** (-0.5), step * self.warmup ** (-1.5)))
```

Later, to use it inside the training loop:
```
optimizer = NoamOpt(input_opts['d_model'], 500,
            torch.optim.Adam(model.parameters(), lr=0, betas=(0.9, 0.98), eps=1e-9))
```

.
.
.
```
optimizer.step()
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2021-02-15 17:31:57

------------

```
class NoamOpt:
"Optim wrapper that implements rate."
def __init__(self, model_size, factor, warmup, optimizer):
    self.optimizer = optimizer
    self._step = 0
    self.warmup = warmup
    self.factor = factor
    self.model_size = model_size
    self._rate = 0
    
def step(self):
    "Update parameters and rate"
    self._step += 1
    rate = self.rate()
    for p in self.optimizer.param_groups:
        p['lr'] = rate
    self._rate = rate
    self.optimizer.step()
    
def rate(self, step = None):
    "Implement `lrate` above"
    if step is None:
        step = self._step
    return self.factor * \
        (self.model_size ** (-0.5) *
        min(step ** (-0.5), step * self.warmup ** (-1.5)))
    
def get_std_opt(model):
    return NoamOpt(model.src_embed[0].d_model, 2, 4000,torch.optim.Adam(model.parameters(), lr=0, betas=(0.9, 0.98), eps=1e-9))
```

As in: [https://nlp.seas.harvard.edu/2018/04/03/attention.html#optimizer](https://nlp.seas.harvard.edu/2018/04/03/attention.html#optimizer)


------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-12-25 06:21:07

------------

The NoamOpt of cause provides a path to implement the warmup learning rate as in
[https://nlp.seas.harvard.edu/2018/04/03/attention.html#optimizer](https://nlp.seas.harvard.edu/2018/04/03/attention.html#optimizer). However, it is a little bit old and inconvenient. A smarter way to achieve that is to directly use the [lambda learning rate scheduler](https://pytorch.org/docs/stable/generated/torch.optim.lr_scheduler.LambdaLR.html) supported by Pytorch.
That is, you first define a warmup function to adjust the learning rate automatically as:
```
def warmup(current_step: int):
if current_step < args.warmup_steps:  # current_step / warmup_steps * base_lr
    return float(current_step / args.warmup_steps)
else:                                 # (num_training_steps - current_step) / (num_training_steps - warmup_steps) * base_lr
    return max(0.0, float(args.training_steps - current_step) / float(max(1, args.training_steps - args.warmup_steps)))
```

Then you build the learning rate scheduler and use it during the training process:
```
lr_scheduler = torch.optim.lr_scheduler.LambdaLR(optimizer, lr_lambda=warmup)
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2023-01-11 23:05:47

------------

This is built off of Fang Wu's answer but is logarithmic and allows you to use a built-in function as the main scheduler. CosineAnnealingLR can be replaced with any scheduler you want.
```
train_scheduler = CosineAnnealingLR(optimizer, num_epochs, eta_min=max_learning_rate)

def warmup(current_step: int):
    return 1 / (10 ** (float(number_warmup_epochs - current_step)))
warmup_scheduler = LambdaLR(optimizer, lr_lambda=warmup)

scheduler = SequentialLR(optimizer, [warmup_scheduler, train_scheduler], [number_warmup_epochs])
```



------------
    
    