
# Post \#64717302 [Link](https://stackoverflow.com/questions/64717302/)

## DeprecationWarning: executable_path has been deprecated selenium python

**Vote**: 164 (29/702) **Views**: 357696 (17/702) 

**Internal ID** \#1-3-309

Created at 2020-11-06 15:22:39

Tags: `python` `selenium`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am using sublime to code python scripts. The following code is for selenium in python to install the driver automatically by using the webdriver_manager package
```
# pip install webdriver-manager
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.common.by import By

driver = webdriver.Chrome(ChromeDriverManager().install())
driver.maximize_window()

#s=Service(path)
#driver=webdriver.Chrome(service=s)
driver.get('https://www.google.com')
driver.find_element(By.NAME, 'q').send_keys('Yasser Khalil')
```

The code works fine but I got a warning like that
```
Demo.py:7: DeprecationWarning: executable_path has been deprecated, please pass in a Service object
  driver = webdriver.Chrome(ChromeDriverManager().install())
```

How to fix such a bug?


----------
        
## Answer \#0

**Accepted** Vote: 222

Created at 2021-11-24 15:52:47

------------

This error message...
```
DeprecationWarning: executable_path has been deprecated, please pass in a Service object
```

...implies that the  `executable_path` will be deprecated in the upcoming releases.
This change is inline with the  [changelog](https://github.com/SeleniumHQ/selenium/blob/d6acda7c0254f9681574bf4078ff2001705bf940/py/CHANGES#L101) which mentions:
> Deprecate all but `Options` and `Service` arguments in driver instantiation. (#9125,#9128)

---



## Solution


With [selenium4](/questions/tagged/selenium4) as the  `executable_path` is deprecated you have to use an instance of the `Service()` class along with `ChromeDriverManager().install()` command as discussed below.

## Pre-requisites


Ensure that:
-  is upgraded to ```
pip3 install -U selenium
```
-  is installed```
pip3 install webdriver-manager
```

> You can find a detailed discussion on installing  in [ModuleNotFoundError: No module named 'webdriver_manager' error even after installing webdrivermanager](https://stackoverflow.com/questions/63421086/modulenotfounderror-no-module-named-webdriver-manager-error-even-after-instal)

## Selenium v4 compatible Code Block


```
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))
driver.get("https://www.google.com")
```

Console Output:
```
[WDM] - ====== WebDriver manager ======
[WDM] - Current google-chrome version is 96.0.4664
[WDM] - Get LATEST driver version for 96.0.4664
[WDM] - Driver [C:\Users\Admin\.wdm\drivers\chromedriver\win32\96.0.4664.45\chromedriver.exe] found in cache
```

> You can find a detailed discussion on installing  in [Selenium ChromeDriver issue using Webdriver Manager for Python](https://stackoverflow.com/questions/69774045/selenium-chromedriver-issue-using-webdriver-manager-for-python/69774322#69774322)

---


Incase you want to pass the `Options()` object you can use:
```
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

options = Options()
options.add_argument("start-maximized")
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=options)
driver.get("https://www.google.com")
```


---



## TL; DR


You can find the relevant Bug Report/Pull Request in:
- [deprecate all but Options and Service arguments in driver instantiation](https://github.com/SeleniumHQ/selenium/issues/9125)- [deprecate all but Options and Service arguments in driver instantiation](https://github.com/SeleniumHQ/selenium/pull/9128)


------------
    
    
## Answer \#1

 Vote: 77

Created at 2021-11-09 03:47:07

------------

This works for me
```
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
ser = Service(r"C:\chromedriver.exe")
op = webdriver.ChromeOptions()
s = webdriver.Chrome(service=ser, options=op)
```

Extending on the accepted answer, the Service class allows to explicitly specify a ChromeDriver executable in the same way as previously using the executable_path parameter. In this way existing code is easily migrated (clearly you need to replace `C:\chromedriver.exe` above by your path).


------------
    
    
## Answer \#2

 Vote: 69

Created at 2020-11-06 17:46:49

------------

I could figure it out
```
# pip install webdriver-manager
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.common.by import By

s=Service(ChromeDriverManager().install())
driver = webdriver.Chrome(service=s)
driver.maximize_window()
driver.get('https://www.google.com')
driver.find_element(By.NAME, 'q').send_keys('Yasser Khalil')
```



------------
    
    
## Answer \#3

 Vote: 51

Created at 2021-11-08 15:07:04

------------

I found this deprecation issue is appearing on Selenium, Pip and Python updates. so simply just change :
before:
```
from selenium import webdriver
chrome_driver_path = 'C:/Users/Morteza/Documents/Dev/chromedriver.exe'
driver = webdriver.Chrome(executable_path=chrome_driver_path)

url = "https://www.google.com"
driver.get(url)
```

after:
```
from selenium import webdriver
from selenium.webdriver.chrome.service import Service

s=Service('C:/Users/Morteza/Documents/Dev/chromedriver.exe')
browser = webdriver.Chrome(service=s)
url='https://www.google.com'
browser.get(url)
```



------------
    
    
## Answer \#4

 Vote: 13

Created at 2022-03-26 14:13:27

------------

All the above answers refer to Chrome, adding the one for 
Install:
```
pip install webdriver-manager
```

Code:
```
from selenium.webdriver.firefox.service import Service
from webdriver_manager.firefox import GeckoDriverManager
driver = webdriver.Firefox(service=Service(executable_path=GeckoDriverManager().install()))
```

Reference: [https://github.com/SergeyPirogov/webdriver_manager/issues/262#issuecomment-955197860](https://github.com/SergeyPirogov/webdriver_manager/issues/262#issuecomment-955197860)


------------
    
    
## Answer \#5

 Vote: 8

Created at 2022-05-06 07:01:25

------------

```
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
service_obj = Service("WebDrivers_path\chromedriver.exe")
driver = webdriver.Chrome(service=service_obj)
driver.get("https://www.google.com")
```



------------
    
    
## Answer \#6

 Vote: 3

Created at 2021-12-19 06:10:28

------------

Simplest option with Chrome auto-installer:
```
from selenium import webdriver    
import chromedriver_autoinstaller
from selenium.webdriver.chrome.service import Service

chromedriver_autoinstaller.install()
driver = webdriver.Chrome(service=Service())
```



------------
    
    
## Answer \#7

 Vote: 3

Created at 2022-06-22 08:55:17

------------

Have a look at the new definition in the Service object [here](https://sourcegraph.com/github.com/SeleniumHQ/selenium@cc00a3cec8ba78a7311a23bd8db29dc33a03f503/-/blob/py/selenium/webdriver/chromium/service.py).
My solution
```
from selenium.webdriver.chrome.service import Service

chrome_executable = Service(executable_path='chromedriver.exe', log_path='NUL')
driver = webdriver.Chrome(service=chrome_executable)
```



------------
    
    
## Answer \#8

 Vote: 1

Created at 2021-09-27 10:27:45

------------

if you are using any IDE like PyCharm install webdriver-manager package of that IDE as how do install for selenium package


------------
    
    
## Answer \#9

 Vote: -1

Created at 2021-10-20 05:51:36

------------

You can create an instance of ChromeOptions, which has convenient methods for setting ChromeDriver-specific capabilities. You can then pass the ChromeOptions object into the ChromeDriver constructor:
```
ChromeOptions options = new ChromeOptions();

options.addExtensions(new File("/path/to/extension.crx"));

ChromeDriver driver = new ChromeDriver(options);
```

Since Selenium version 3.6.0, the ChromeOptions class in Java also implements the Capabilities interface, allowing you to specify other WebDriver capabilities not specific to ChromeDriver.
```
ChromeOptions options = new ChromeOptions();

// Add the WebDriver proxy capability.

Proxy proxy = new Proxy();

proxy.setHttpProxy("myhttpproxy:3337");

options.setCapability("proxy", proxy);

// Add a ChromeDriver-specific capability.

options.addExtensions(new File("/path/to/extension.crx"));

ChromeDriver driver = new ChromeDriver(options);
```



------------
    
    