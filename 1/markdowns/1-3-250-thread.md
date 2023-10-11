
# Post \#66348333 [Link](https://stackoverflow.com/questions/66348333/)

## Speed up reading multiple pickle files

**Vote**: 17 (290/702) **Views**: 3852 (548/702) 

**Internal ID** \#1-3-250

Created at 2021-02-24 09:46:41

Tags: `python` `python-multiprocessing` `python-multithreading`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a lot of pickle files. Currently I read them in a loop but it takes a lot of time. I would like to speed it up but don't have any idea how to do that.
Multiprocessing wouldn't work because in order to transfer data from a child subprocess to the main process data need to be serialized (pickled) and deserialized.
Using threading wouldn't help either because of GIL.
I think that the solution would be some library written in C that takes a list of files to read and then runs multiple threads (without GIL). Is there something like this around?

Answering your questions:
- - `pandas.Series`- - - - - 


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2021-03-01 03:02:45

------------

> I think that the solution would be some library written in C that
takes a list of files to read and then runs multiple threads (without
GIL). 
 `pickle` is apparently good enough for enough people that there are no major alternate implementations fully compatible with the pickle protocol. As of sometime in python 3, `cPickle` was merged with `pickle`, and neither release the GIL anyway which is why threading won't help you (search for `Py_BEGIN_ALLOW_THREADS` in [_pickle.c](https://github.com/python/cpython/blob/master/Modules/_pickle.c) and you will find nothing).
If your data can be re-structured into a simpler data format like csv, or a binary format like `numpy`'s npy, there will be less cpu overhead when reading your data. Pickle is built for flexibility first rather than speed or compactness first. One possible exception to the rule of more complex less speed is the HDF5 format using `h5py`, which can be fairly complex, and I have used to max out the bandwidth of a sata ssd.
Finally you mention you have many many pickle files, and that itself is probably causing no small amount of overhead. Each time you open a new file, there's some overhead involved from the operating system. Conveniently you can combine pickle files by simply appending them together. Then you can call `Unpickler.load()` until you reach the end of the file. Here's a quick example of combining two pickle files together using `shutil`
```
import pickle, shutil, os

#some dummy data
d1 = {'a': 1, 'b': 2, 1: 'a', 2: 'b'}
d2 = {'c': 3, 'd': 4, 3: 'c', 4: 'd'}

#create two pickles
with open('test1.pickle', 'wb') as f:
    pickle.Pickler(f).dump(d1)
with open('test2.pickle', 'wb') as f:
    pickle.Pickler(f).dump(d2)
    
#combine list of pickle files
with open('test3.pickle', 'wb') as dst:
    for pickle_file in ['test1.pickle', 'test2.pickle']:
        with open(pickle_file, 'rb') as src:
            shutil.copyfileobj(src, dst)
            
#unpack the data
with open('test3.pickle', 'rb') as f:
    p = pickle.Unpickler(f)
    while True:
        try:
            print(p.load())
        except EOFError:
            break
        
#cleanup
os.remove('test1.pickle')
os.remove('test2.pickle')
os.remove('test3.pickle')
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-02-28 18:14:18

------------

I agree with what has been noted in the comments, namely that due to the constraint of python itself (chiefly, the GIL lock, as you noted) and there may simply be no faster loading the information beyond what you are doing now. Or, if there is a way, it may be both highly technical and, in the end, only gives you a modest increase in speed.
That said, depending on the datatypes you have, it may be faster to use [quickle](https://jcristharif.com/quickle/index.html) or [pyrobuf](https://github.com/appnexus/pyrobuf).


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-02-27 09:35:52

------------

I think you should try and use mmap(memory mapped files) that is similar to `open()` but way faster.
Note: If your each file is big in size then use mmap otherwise If the files are small in size use regular methods.
I have written a sample that you can try.
```
import mmap
from time import perf_counter as pf
def load_files(filelist):
    start = pf() # for rough time calculations
    for filename in filelist:
        with open(filename, mode="r", encoding="utf8") as file_obj:
            with mmap.mmap(file_obj.fileno(), length=0, access=mmap.ACCESS_READ) as mmap_file_obj:
                data = pickle.load(mmap_file_obj)
                print(data)
    print(f'Operation took {pf()-start} sec(s)')
```

Here `mmap.ACCESS_READ` is the mode to open the file in binary. The `file_obj` returned by `open` is just used to get the `file descriptor` which is used to open the stream to the file via `mmap` as a memory mapped file.
As you can see below in the documentation of python `open` returns the `file descriptor` or `fd` for short. So we don't have to do anything with the `file_obj` operation wise. We just need its `fileno()` method to get its file descriptor. Also we are not closing the `file_obj` before the `mmap_file_obj`. Please take a proper look. We are closing the the `mmap` block first.
As you said in your comment.
```
open (file, flags[, mode])
Open the file file and set various flags according to flags and possibly its mode according to mode. 
The default mode is 0777 (octal), and the current umask value is first masked out. 
Return the file descriptor for the newly opened file.
```

Give it a try and see how much impact does it do on your operation
You can read more about mmap [here](https://realpython.com/python-mmap/). And about file descriptor [here](https://en.m.wikipedia.org/wiki/File_descriptor)


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-03-01 02:07:43

------------

You can try multiprocessing:
```
import os,pickle
pickle_list=os.listdir("pickles")

output_dict=dict.fromkeys(pickle_list, '')

def pickle_process_func(picklename):
    with open("pickles/"+picklename, 'rb') as file:
        dapickle=pickle.load(file)

    #if you need previus files output wait for it
    while(!output_dict[pickle_list[pickle_list.index(picklename)-1]]):
        continue

    #thandosomesh
    print("loaded")
    output_dict[picklename]=custom_func_i_dunno(dapickle)
    

from multiprocessing import Pool

with Pool(processes=10) as pool:
     pool.map(pickle_process_func, pickle_list)
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-03-03 18:03:58

------------

Consider using HDF5 via [h5py](https://github.com/h5py/h5py) instead of `pickle`. The performance is generally much better than `pickle` with numerical data in `Pandas` and `numpy` data structures and [it supports most common data types](https://docs.h5py.org/en/stable/faq.html#what-datatypes-are-supported) and compression.


------------
    
    