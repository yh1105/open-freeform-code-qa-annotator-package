
# Post \#52074153 [Link](https://stackoverflow.com/questions/52074153/)

## Cannot convert list to array: ValueError: only one element tensors can be converted to Python scalars

**Vote**: 36 (170/702) **Views**: 135170 (61/702) 

**Internal ID** \#1-3-231

Created at 2018-08-29 09:33:50

Tags: `python` `numpy` `pytorch` `numpy-ndarray`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm currently working with the PyTorch framework and trying to understand foreign code. I got an indices issue and wanted to print the shape of a list.
The only way of doing so (as far as Google tells me) is to convert the list into a numpy array and then getting the shape with numpy.ndarray.shape().
But trying to convert my list into an array, I got a `ValueError: only one element tensors can be converted to Python scalars`.
My List is a converted PyTorch Tensor (`list(pytorchTensor)`) and looks somewhat like this:
```
[
tensor([[-0.2781, -0.2567, -0.2353,  ..., -0.9640, -0.9855, -1.0069],  
        [-0.2781, -0.2567, -0.2353,  ..., -1.0069, -1.0283, -1.0927],  
        [-0.2567, -0.2567, -0.2138,  ..., -1.0712, -1.1141, -1.1784],  
        ...,  
        [-0.6640, -0.6425, -0.6211,  ..., -1.0712, -1.1141, -1.0927],  
        [-0.6640, -0.6425, -0.5997,  ..., -0.9426, -0.9640, -0.9640],  
        [-0.6640, -0.6425, -0.5997,  ..., -0.9640, -0.9426, -0.9426]]),

tensor([[-0.0769, -0.0980, -0.0769,  ..., -0.9388, -0.9598, -0.9808],  
        [-0.0559, -0.0769, -0.0980,  ..., -0.9598, -1.0018, -1.0228],    
        [-0.0559, -0.0769, -0.0769,  ..., -1.0228, -1.0439, -1.0859],  
        ...,  
        [-0.4973, -0.4973, -0.4973,  ..., -1.0018, -1.0439, -1.0228],  
        [-0.4973, -0.4973, -0.4973,  ..., -0.8757, -0.9177, -0.9177],  
        [-0.4973, -0.4973, -0.4973,  ..., -0.9177, -0.8967, -0.8967]]),

tensor([[-0.1313, -0.1313, -0.1100,  ..., -0.8115, -0.8328, -0.8753],  
        [-0.1313, -0.1525, -0.1313,  ..., -0.8541, -0.8966, -0.9391],  
        [-0.1100, -0.1313, -0.1100,  ..., -0.9391, -0.9816, -1.0666],  
        ...,  
        [-0.4502, -0.4714, -0.4502,  ..., -0.8966, -0.8966, -0.8966],  
        [-0.4502, -0.4714, -0.4502,  ..., -0.8115, -0.8115, -0.7903],  
        [-0.4502, -0.4714, -0.4502,  ..., -0.8115, -0.7690, -0.7690]]),
]
```

Is there a way of getting the shape of that list without converting it into a numpy array?


----------
        
## Answer \#0

**Accepted** Vote: 32

Created at 2018-08-29 10:10:11

------------

It seems like you have a list of tensors. For each tensor you can see its [size()](https://pytorch.org/docs/stable/generated/torch.Tensor.size.html#torch.Tensor.size) (no need to convert to list/numpy). If you insist, you can convert a tensor to numpy array using [numpy()](https://pytorch.org/docs/stable/generated/torch.Tensor.numpy.html#torch.Tensor.numpy):
Return a list of tensor shapes:
```
>> [t.size() for t in my_list_of_tensors]
```

Returns a list of numpy arrays:
```
>> [t.numpy() for t in my_list_of_tensors]
```

In terms of performance, it is always best to avoid casting of tensors into numpy arrays, as it may incur sync of device/host memory. If you only need to check the `shape` of a tensor, use `size()` function.


------------
    
    
## Answer \#1

 Vote: 5

Created at 2018-10-31 22:53:59

------------

The simplest way to convert pytorch tensor to numpy array is:

```
nparray = tensor.numpy()
```


Also, for size and shape:

```
tensor_size = tensor.size()
tensor_shape = tensor.shape()
tensor_size
>>> (1080)
tensor_shape
>>> (32, 3, 128, 128)
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-06-11 17:50:55

------------

A real-world example, would require to handle [torch no grad issue](https://stackoverflow.com/questions/55466298/pytorch-cant-call-numpy-on-variable-that-requires-grad-use-var-detach-num):
```
with torch.no_grad():
    probs = [t.numpy() for t in my_tensors]
```

or
```
probs = [t.detach().numpy() for t in my_tensors]
```



------------
    
    