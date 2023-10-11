
# Post \#51800122 [Link](https://stackoverflow.com/questions/51800122/)

## Using openpyxl to find rows that contain cell with specific value

**Vote**: 15 (318/702) **Views**: 77456 (109/702) 

**Internal ID** \#1-3-184

Created at 2018-08-11 13:27:57

Tags: `python` `excel` `openpyxl`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am completely new to `openpyxl` so, as you can imagine, I am having pretty hard times when I try to make use of it.
I have an Excel report that contains only one sheet (called `Sheet1)`. I'd like to search all cells for those that contain specific string (product name ABC in this case).
Then I would like to copy contents of every cell in the rows that contain cell with `ABC` product name. And assign every cell to a variable.
To give you better idea of what I am trying to achieve I'll give you an example:
[](https://i.stack.imgur.com/aQ9m7.jpg)
So in this case I would only copy cells from rows: 2, 4, 6 (as only they contain `ABC` product).
I have already looked up similar questions and answers to them but I don't understand them (never have used Excel before).


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2018-08-11 13:59:48

------------

is it important for you to use openpyxl to do this? i would suggest using pandas if not.

```
import pandas as pd

    df = pd.read_excel("path_to_excel_file")
    df_abc = df[df["Products"] == "ABC"] # this will only contain 2,4,6 rows
```


then:

```
for row in df_abc.iterrows():
        # do what you want with the row
```



------------
    
    
## Answer \#1

 Vote: 33

Created at 2018-08-11 16:00:10

------------

There's no need to use the pandas for this.

```
from openpyxl import Workbook
import openpyxl

file = "enter_path_to_file_here"
wb = openpyxl.load_workbook(file, read_only=True)
ws = wb.active

for row in ws.iter_rows("E"):
    for cell in row:
        if cell.value == "ABC":
            print(ws.cell(row=cell.row, column=2).value) #change column number for any cell value you want
```



------------
    
    
## Answer \#2

 Vote: 8

Created at 2018-08-11 14:23:58

------------

```
from openpyxl import load_workbook

wb = load_workbook("report.xlsx")
ws = wb.active

for row in ws.rows:
if row[4].value == "ABC":
    for cell in row:
        print(cell.value, end=" ")
    print()
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-08-27 21:35:38

------------

```
wb = load_workbook("report.xlsx")
ws = wb.active

abc_rows=[]

for row in range(2,ws.max_row+1):
    if(ws[row][4]=='ABC'):
       abc_rows.append(row)
```

To access the selected rows separately, further add this code
```
i=1
abc_dict={}
for row in ws.values:
    if(i in abc_rows):
        (a,b,c,d,e,f,g,h,i,j,k,l)=row
         abc_dict[i]=row
    i=i+1
```

, use
>  gives entire second row as tuples and  gives
first cell value. you can parse till  to get each cell
of selected rows seperately.


------------
    
    