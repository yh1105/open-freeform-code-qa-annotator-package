
# Post \#63027020 [Link](https://stackoverflow.com/questions/63027020/)

## Problem with pip install mariadb - mariadb_config not found

**Vote**: 15 (318/702) **Views**: 25066 (262/702) 

**Internal ID** \#1-3-251

Created at 2020-07-22 04:42:48

Tags: `mysql` `python-3.x` `pip` `raspberry-pi` `mariadb`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have been trying to run `pip3 install mariadb` on my raspberry pi running ubuntu 18.04 and I was unsuccessful.
I have tried installing following packages as suggested in other answers:
```
sudo apt-get install mariadb-server
sudo apt-get install libmariadbclient-dev
sudo apt-get install libmysqlclient-dev

pip3 install mysqlclient
pip3 install mysql-connector-python-rf
```

However, I'm still running intto the problem as shown:
```
ubuntu@ubuntu:~$ pip3 install mariadb
Collecting mariadb
  Using cached https://files.pythonhosted.org/packages/8f/c9/7050899dc1066409a17e1147d3afe1b078e582afdb755c6d3cb9c9a5c3ab/mariadb-1.0.0.tar.gz
    Complete output from command python setup.py egg_info:
    /bin/sh: 1: mariadb_config: not found
    Traceback (most recent call last):
      File "<string>", line 1, in <module>
      File "/tmp/pip-build-2gdw_t_r/mariadb/setup.py", line 26, in <module>
        cfg = get_config(options)
      File "/tmp/pip-build-2gdw_t_r/mariadb/mariadb_posix.py", line 49, in get_config
        cc_version = mariadb_config(config_prg, "cc_version")
      File "/tmp/pip-build-2gdw_t_r/mariadb/mariadb_posix.py", line 27, in mariadb_config
        "mariadb_config not found.\nPlease make sure, that MariaDB Connector/C is installed on your system, edit the configuration file 'site.cfg' and set the 'mariadb_config'\noption, which should point to the mariadb_config utility.")
    OSError: mariadb_config not found.
    Please make sure, that MariaDB Connector/C is installed on your system, edit the configuration file 'site.cfg' and set the 'mariadb_config'
    option, which should point to the mariadb_config utility.
    
    ----------------------------------------
Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-2gdw_t_r/mariadb/
```

I do have `/etc/mysql/my.cnf` file.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2020-07-22 12:20:09

------------

It looks like that you didn't install MariaDB Connector/C properly, on Ubuntu mariadb_config should be found in /usr/bin.
Please also note, that MariaDB Connector/Python requires MariaDB Connector/C 3.1.5 or newer, afaik Ubuntu 18.04 provides 3.0.3 - so simplest way would be to download the Connector/C sources and build it manually.


------------
    
    
## Answer \#1

 Vote: 53

Created at 2020-08-09 12:51:57

------------

You probably need to install MariaDB database development files: [https://packages.debian.org/unstable/libmariadb-dev](https://packages.debian.org/unstable/libmariadb-dev). You need this package for your python connector to work properly in linux. You need to follow this steps:

1. sudo apt-get update -y
2. sudo apt-get install -y libmariadb-dev
3. pip3 install mariadb


Here you are first updating the list of packages that need an upgrade for your system. Then you are installing the previously mentioned dev library. The last step is to install mariadb with pip3 which should now work as expected.


------------
    
    
## Answer \#2

 Vote: 5

Created at 2022-01-31 15:50:46

------------

On a Mac you need to first install `mariadb-connector-c` which can be done using `brew`:
```
brew install mariadb-connector-c
```

Then you can install `mysqlclient`:
```
pip3 install mysqlclient
```



------------
    
    