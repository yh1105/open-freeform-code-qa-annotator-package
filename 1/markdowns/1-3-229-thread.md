
# Post \#71319929 [Link](https://stackoverflow.com/questions/71319929/)

## How to find and replace text in a single cell when using Jupyter extension inside Visual Studio Code

**Vote**: 7 (449/702) **Views**: 1858 (612/702) 

**Internal ID** \#1-3-229

Created at 2022-03-02 08:59:11

Tags: `python` `visual-studio-code` `jupyter-notebook`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

As the title says, how to find and replace text inside a single jupyter cell when using the jupyter extension in Visual Studio Code? I am familiar with ctr+h but that will replace all the occurrences in the entire jupyter notebook file. This is a really important feature for me, as I am using it a lot in jupyter on the browser.


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-03-02 11:40:37

------------

You can select the first occurrence and then use `Ctrl+D`. It will select the next occurence in the cell. Repeat that until you go back to the first ocurrence and then type the new value. It will replace all the values your circled on.
In case you have changed that keyboard shortcut or if it is different you can find the correct one:
[](https://i.stack.imgur.com/adLVj.png)


------------
    
    
## Answer \#1

 Vote: 11

Created at 2022-07-25 12:21:40

------------


For the most updated VSCode, select something you would like to replace, and then `F2`.
[](https://i.stack.imgur.com/u5feS.png)

---



`F3` do the trick. It first shows the "Find" widget for a single cell. Then click the triangle on the left, which will be expanded to a "Replace" widget.
[](https://i.stack.imgur.com/xeuJ5.png)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-11-01 11:37:09

------------

try select a cell and use `Ctrl+g` as I just found out accidentally.


------------
    
    