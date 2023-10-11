
# Post \#69373525 [Link](https://stackoverflow.com/questions/69373525/)

## Running two python file at the same time in visual studio code

**Vote**: 3 (575/702) **Views**: 1933 (610/702) 

**Internal ID** \#1-3-201

Created at 2021-09-29 09:11:26

Tags: `python` `multithreading` `visual-studio-code` `binance` `binance-api-client`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

i have two files, and i want to run both. The first one is basically asking down the prices of stocks and writes them to a database, and the second one using python dash to create a webpage. This webpage is showing the data in real time from the database which is constantly refreshes. I tried to use threading but it does not work, because the two files basically function as two infinite while loop. How should i solve this problem?
The two functions im having problem with are
dataMiningScript= fd.financeData(database,1600)
and
if  == "":
app.run_server(debug=True)
```
app = dash.Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])
database=db.dataBase()
homepage=hp.homepage(database)
homepage.timeUpdateCallback(app)
homepage.gaugeRefreshCallback(app)
dataMiningScript= fd.financeData(database,1600)

app.layout = homepage.layoutMaker()



if __name__ == "__main__":
   app.run_server(debug=True)
```



----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2021-10-01 09:40:08

------------

For some reason i cannot run two python programs at the same time in Visual Studio Code, but i managed to solve this problem with the solution of the first commenter:
I opened terminal and search my .py program. Then i write
python3 xy.py


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-09-29 15:24:03

------------

You can just create two terminals in visual studio code to run both files (see [here](https://code.visualstudio.com/docs/editor/integrated-terminal#_managing-terminals)). Or you can create a simple shell script which starts both programs (start a program in the background with a '&' at the end of the command line)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-01-06 13:06:32

------------

Helpful discussion here: [run multiple python scripts at the same time](https://stackoverflow.com/questions/51969662/run-multiple-python-scripts-at-the-same-time).
Covers the question posted at the title fairly well.
On new window I managed to run the necessary code by typing a command. Run Python File button remains tied too original window.


------------
    
    