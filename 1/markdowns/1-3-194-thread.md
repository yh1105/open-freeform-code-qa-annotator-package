
# Post \#55508303 [Link](https://stackoverflow.com/questions/55508303/)

## How to write a list of list into excel using python?

**Vote**: 14 (336/702) **Views**: 106308 (78/702) 

**Internal ID** \#1-3-194

Created at 2019-04-04 05:12:16

Tags: `python` `excel` `python-3.x`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

How to write a list of list into excel using python 3?

```
new_list = [["first", "second"], ["third", "fourth"]]
with open("file_name.csv", 'w') as f:
    fc = csv.writer(f, lineterminator='\n')
fc.writerows(new_list)
```

I can write this list into `csv` file but How canI want to write in excel file?


----------
        
## Answer \#0

**Accepted** Vote: 19

Created at 2019-04-04 10:28:42

------------

Here is one way to do it using [XlsxWriter](https://xlsxwriter.readthedocs.io/index.html):

```
import xlsxwriter

new_list = [['first', 'second'], ['third', 'four'], [1, 2, 3, 4, 5, 6]]

with xlsxwriter.Workbook('test.xlsx') as workbook:
    worksheet = workbook.add_worksheet()

    for row_num, data in enumerate(new_list):
        worksheet.write_row(row_num, 0, data)
```


Output:

[](https://i.stack.imgur.com/MRJl1.png)


------------
    
    
## Answer \#1

 Vote: 34

Created at 2019-04-04 08:32:28

------------

You can use the [pandas](https://pandas.pydata.org/) library.
```
import pandas as pd

new_list = [["first", "second"], ["third", "four"], ["five", "six"]]
df = pd.DataFrame(new_list)
writer = pd.ExcelWriter('test.xlsx', engine='xlsxwriter')
df.to_excel(writer, sheet_name='welcome', index=False)
writer.save()
```

Related documentation:
- [pandas.ExcelWriter](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.ExcelWriter.html)- [pandas.DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)- [pandas.DataFrame.to_excel](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_excel.html)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2019-04-04 05:21:44

------------

I think you should use [pandas](https://pandas.pydata.org/) library to write and read data in this library the function [pandas.DataFrame.to_excel(..)](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_excel.html#pandas.DataFrame.to_excel) will make you able to directly write to excel files for all this you may need to define [pandas.DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame) for this work here is a [tutorial](https://www.datacamp.com/community/tutorials/pandas-tutorial-dataframe-python) on pandas-dataframe by dataCamp.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-04-25 18:50:49

------------

You can also do this with openpyxl library.
```
from openpyxl import Workbook

new_list = [["first", "second"], ["third", "fourth"]]

wb = Workbook() # creates a workbook object.
ws = wb.active # creates a worksheet object.

for row in new_list:
    ws.append(row) # adds values to cells, each list is a new row.

    
wb.save('File_Name.xlsx') # save to excel file.
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2019-04-04 05:54:09

------------

```
import pyexcel

# Get the data
new_list = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Save the array to a file
pyexcel.save_as(array=new_list, dest_file_name="array_data.xls")


# Retrieve the records of the file
# records = pyexcel.get_records(file_name="test.xls")

# Get an array from the data
# my_array = pyexcel.get_array(file_name="test.xls")

# Get your data in a dictionary of 2D arrays
# 2d_array_dictionary = pyexcel.get_book_dict(file_name="test.xls")

# The data
# 2d_array_dictionary = {'Sheet 1': [
                               ['ID', 'AGE', 'SCORE']
                               [1, 22, 5],
                               [2, 15, 6],
                               [3, 28, 9]
                              ],
                   'Sheet 2': [
                                ['X', 'Y', 'Z'],
                                [1, 2, 3],
                                [4, 5, 6]
                                [7, 8, 9]
                              ],
                   'Sheet 3': [
                                ['M', 'N', 'O', 'P'],
                                [10, 11, 12, 13],
                                [14, 15, 16, 17]
                                [18, 19, 20, 21]
                               ]}

  # Save the data to a file                        
  # pyexcel.save_book_as(bookdict=2d_array_dictionary, dest_file_name="2d_array_data.xls")
```



------------
    
    
## Answer \#5

 Vote: -3

Created at 2020-07-09 06:05:26

------------

I think the most convenient way is to use Series in Pandas.
```
import pandas as pd

new_list =['whatever']
pd.Series(new_list)
new_list.to_excel('aFileName.xlsx')
```



------------
    
    