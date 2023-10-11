
# Post \#68698823 [Link](https://stackoverflow.com/questions/68698823/)

## Why is a doubly-linked-list used in a music player with “next” and “previous” buttons while using list is simpler?

**Vote**: 2 (631/702) **Views**: 2707 (583/702) 

**Internal ID** \#1-3-274

Created at 2021-08-08 07:55:25

Tags: `python` `algorithm` `data-structures` `linked-list`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am learning about linked lists on Codecademy and there is an instruction saying that
> Before moving on, take a moment to think about doubly-linked lists.
What do you think are some possible real-life uses?
with some uses
- - - 

---


Would it be simpler to use a list?
What are the benefits of using a linked list to perform these tasks?
For instance, using a list for a music player next and previous buttons
```
counter = 0
playlist = ['song', 'song2', 'song3', 'song4']
current_song = playlist[counter]
next_song = playlist[min(counter+1, len(playlist)-1]
```



----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2021-08-08 09:36:33

------------

On an algorithmic point of view, there are different  types:
- static arraysIt is the most simple possible container with a static (maximum) size and direct access (through a numeric index)- dynamic arraysYou still have direct access with a numeric index, but the size can arbitrary grow (limited by available memory). Python  actually fall here. The downside is that they can require a full reallocation and copy when they reach the allocated size. Removing elements is also a costly operation- singly linked listsadding and removing elements at the head side is easy, as is inserting a new element (or removing one) after an already found other one. You can only scan them in one direction, and finding an element knowing its position is rather lengthy (no direct access). It has an overhead of one index per node- doubly linked listswhen compared to singly linked lists, you can scan them in both direction and inserting (or removing) before an element is easy. No direct access either and the overhead is two index per node
Dynamic arrays are the  in most languages, and are the standard Python lists. With few additions and removal they offer both ease of use and correct performances. But other containers do have use cases. For example a fifo queue could be easily implemented as a singly linked list.
I agree on one point: music player with prev and next button  could be implemented as an array (what a Python list is). But an undo/redo functionality with a limited depth is an excellent use case for a doubly linked list:
- - 


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-08-08 08:37:26

------------

Linked list consumes limited memory, where doubly linked list possible to allocate more memory if you have more data. Linked list will keep refernence of next element, where doubly linked list will keep reference of previous and next element.
[](https://i.stack.imgur.com/rgNKi.png)
Since doubly linked list have reference to both side, memory consumption is more but more efficient to access elements (reverse iteration as well (BACK/NEXT))
> Reference means - Address of next / previous element
Undo and Redo - I believe, it simply need a linked list because it requires only less memory and insert and delete operation from one side 
Please refer [Dictionary best data structure for train routes?](https://stackoverflow.com/questions/15534438/dictionary-best-data-structure-for-train-routes) to know about 
The time complexity of removal and insertion is O(1) but searching is O(n)
Please refer to know about time complexity [https://www.oreilly.com/library/view/php-7-data/9781786463890/c5319c42-c462-43a1-b33d-d683f3ef7e35.xhtml](https://www.oreilly.com/library/view/php-7-data/9781786463890/c5319c42-c462-43a1-b33d-d683f3ef7e35.xhtml)
Please refer to know more about linked list in python [Does python have built-in linkedList data structure?](https://stackoverflow.com/questions/14668769/does-python-have-built-in-linkedlist-data-structure)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-08-08 08:21:39

------------

From the Official [Documentation](http://docs.python.org/2/faq/design.html#how-are-lists-implemented)
> Python’s lists are really variable-length arrays, not Lisp-style linked lists. The implementation uses a contiguous array of references to other objects, and keeps a pointer to this array and the array’s length in a list head structure.
This makes indexing a list a[i] an operation whose cost is independent of the size of the list or the value of the index.
When items are appended or inserted, the array of references is resized. Some cleverness is applied to improve the performance of appending items repeatedly; when the array must be grown, some extra space is allocated so the next few times don’t require an actual resize.
So, python lists are nothing but variable length arrays. I dug into [source code](https://hg.python.org/cpython/file/tip/Include/listobject.h#l23) of cpython, and on expanding the macro, the basic structure is defined as:
```
typedef struct { 
    PyObject_VAR_HEAD 
    PyObject **ob_item; 
    Py_ssize_t allocated; 
} PyListObject;
```


• Skip Back/Forward- Because each node in a double linked list has a pointer the previous and next node, it is easy to implement skip forward/backward functionality.
• Play Next Track- The pointer to the next node also makes it quite easy to start the next track when a track is over.
• Append When you add a new track to a playlist, you tack it on to the end. In a linked list, adding a new element is constant time — O(1) operation. Note that as the songs are read in from a data source and added to the play list, this will be done as a sequence of calls to append.
• Beginning/End- Finally, because a linked list has head and tail properties, this provides for an easy way to delineate the beginning and end of a playlist


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-08-08 09:47:40

------------

In my experience programming in many different problem domains, there is  a reason to use a linked list, whether singly or doubly linked.
The theoretical advantage is that a linked list supports O(1) insertion and removal at arbitrary positions in the list. But on today's hardware, you need a pretty large list (thousands to tens of thousands of items)  you need to be doing very frequent insertion and removal operations before this advantage really starts to matter in practice.


------------
    
    