
# Post \#64314141 [Link](https://stackoverflow.com/questions/64314141/)

## psycopg2 - ImportError: DLL load failed while importing _psycopg: The operating system cannot run %1

**Vote**: 12 (356/702) **Views**: 15682 (347/702) 

**Internal ID** \#1-3-174

Created at 2020-10-12 08:22:01

Tags: `python` `anaconda` `conda` `psycopg2`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I installed psycopg2 using conda on Windows 10.
[https://anaconda.org/anaconda/psycopg2](https://anaconda.org/anaconda/psycopg2)
I did it in a clean new conda environment (named wr).
I then tried to run this sample app but I am getting this error (see below).
I have no idea what I might be doing wrong because it was all straightforward and I did it in a clean way.
Any ideas how to solve this?
```
import psycopg2
try:
    connection = psycopg2.connect(user = "***",
                                  password = "***",
                                  host = "***",
                                  port = "5432",
                                  database = "***")


    cursor = connection.cursor()
    # Print PostgreSQL Connection properties
    print ( connection.get_dsn_parameters(),"\n")

    # Print PostgreSQL version
    cursor.execute("SELECT version();")
    record = cursor.fetchone()
    print("You are connected to - ", record,"\n")

except (Exception, psycopg2.Error) as error :
    print ("Error while connecting to PostgreSQL", error)
finally:
    #closing database connection.
        if(connection):
            cursor.close()
            connection.close()
            print("PostgreSQL connection is closed")
```

Error in VS code:
```
PS C:\Work\WRR\git\tools\JTunnelTestApp>  cd 'c:\Work\WRR\git\tools\JTunnelTestApp'; & 'C:\Programs\Anaconda3\envs\wr\python.exe' 'c:\Users\petrop01\.vscode\extensions\ms-python.python-2020.9.114305\pythonFiles\lib\python\debugpy\launcher' '56143' '--' 'c:\Work\WRR\git\tools\JTunnelTestApp\main.py'
Traceback (most recent call last):
  File "c:\Work\WRR\git\tools\JTunnelTestApp\main.py", line 1, in <module>
    import psycopg2
  File "C:\Programs\Anaconda3\envs\wr\lib\site-packages\psycopg2\__init__.py", line 51, in <module>
    from psycopg2._psycopg import (                     # noqa
ImportError: DLL load failed while importing _psycopg: The operating system cannot run %1.
PS C:\Work\WRR\git\tools\JTunnelTestApp>
```

: Seems they had a bug open for this 2 years ago and they just closed it, ignoring it completely.
[https://github.com/psycopg/psycopg2/issues/734](https://github.com/psycopg/psycopg2/issues/734)


----------
        
## Answer \#0

**Accepted** Vote: 13

Created at 2020-10-12 08:28:29

------------

you can use `psycopg2-binary` library instead of `psycopg2`. after installation the usage is the same.


------------
    
    
## Answer \#1

 Vote: 8

Created at 2021-10-05 13:53:56

------------

For Windows when using Anaconda I have found that installing from the VS Code/Windows terminal just doesn't work for all cases. Instead install from the Anaconda terminal. I have no idea why this is the case, but it has been the fix on multiple computers.

1. Open Anaconda navigator
2. Environments
3. Select the environment you want to install psycopg2/psycopg2-binary to and Open Terminal
4. Uninstall any pervious installs pip uninstall psycopg2 pip uninstall psycopg2-binary
5. Install again pip install psycopg2 pip install psycopg2-binary


Now it should work.
Particularly found this useful to get standalone scripts that make use of Django ORM  to work with Postgresql. Django was working fine, but without this fix the standalone scripts don't. Very strange.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-03-15 08:45:25

------------

for me updating to  to  worked in python 


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-08-24 15:38:32

------------

I found the solution in [this reddit post](https://www.reddit.com/r/PostgreSQL/comments/p87f0v/python_39_and_importerror_dll_load_failed_while/), all credit to u/brianckeegan
If you're using conda to manage psycopg2 for Python 3.9+, the wheels point to an old version (v2.8.6) which causes this error. If you install via pip, you'll get a more up-to-date version (v2.9.1) that supports Python 3.9. Until the conda wheels are updated:
```
conda remove psycopg2
pip install psycopg2
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2023-02-19 13:49:21

------------

For me, updating `psycopg2` to  works in Python 


------------
    
    