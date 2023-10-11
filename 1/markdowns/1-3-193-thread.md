
# Post \#65318382 [Link](https://stackoverflow.com/questions/65318382/)

## Expected browser binary location, but unable to find binary in default location, no 'moz:firefoxOptions.binary' capability provided using GeckoDriver

**Vote**: 34 (179/702) **Views**: 78437 (107/702) 

**Internal ID** \#1-3-193

Created at 2020-12-16 06:35:45

Tags: `python` `selenium` `selenium-webdriver` `firefox` `geckodriver`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

```
from selenium import webdriver;
browser= webdriver.Firefox();
browser.get('http://www.seleniumhq.org');
```

When I try to run this code, it gives me an `error message`:
```
Expected browser binary location, but unable to find binary in default location, no 'moz:firefoxOptions.binary' capability provided, and no binary flag set on the command line.
```

Any thoughts-highly appreciated!


----------
        
## Answer \#0

**Accepted** Vote: 60

Created at 2020-12-16 11:24:59

------------

This error message...
```
Expected browser binary location, but unable to find binary in default location, no 'moz:firefoxOptions.binary' capability provided, and no binary flag set on the command line.
```

...implies that the [GeckoDriver](https://stackoverflow.com/questions/45329528/which-firefox-browser-versions-supported-for-given-geckodriver-version/45331403#45331403) was unable to find the  binary at the default location. Additionally you haven't passed the `moz:firefoxOptions.binary` capability.

---



## Solution


Possibly within your system [firefox](/questions/tagged/firefox) is installed in a custom location and these cases you need to pass the  of the  binary through the `moz:firefoxOptions.binary` capability as follows:
```
from selenium import webdriver
from selenium.webdriver.firefox.options import Options

options = Options()
options.binary_location = r'C:\Program Files\Mozilla Firefox\firefox.exe'
driver = webdriver.Firefox(executable_path=r'C:\WebDrivers\geckodriver.exe', options=options)
driver.get('http://google.com/')
```


---



## References


You can find a couple of relevant detailed discussion in:
- [SessionNotCreatedException: Message: Expected browser binary location, but unable to find binary in default location, no 'moz:firefoxOptions.binary'](https://stackoverflow.com/questions/64908154/sessionnotcreatedexception-message-expected-browser-binary-location-but-unabl/64908883#64908883)- [InvalidArgumentException: Message: binary is not a Firefox executable error using GeckoDriver Firefox Selenium and Python](https://stackoverflow.com/questions/64034859/invalidargumentexception-message-binary-is-not-a-firefox-executable-error-usin/64035303#64035303)- [Expected browser binary location, but unable to find binary in default location, no 'moz:firefoxOptions.binary' capability provided](https://stackoverflow.com/questions/66000401/expected-browser-binary-location-but-unable-to-find-binary-in-default-location/66000618#66000618)


------------
    
    
## Answer \#1

 Vote: 23

Created at 2021-05-07 15:52:22

------------

Firefox was not installed on my system at all. That's why this error came up.


------------
    
    
## Answer \#2

 Vote: 8

Created at 2021-07-18 04:32:30

------------

same issue here:
- - `Mac`- `Firefox`- `geckodriver``PATH`- `Firefox`- [firefox official site](https://www.mozilla.org/zh-CN/firefox/mac/)


------------
    
    
## Answer \#3

 Vote: 3

Created at 2021-11-11 12:04:09

------------

Before this ensure that path variable has include for geckodriver [click here to download driver](https://github.com/mozilla/geckodriver/releases) and run below python script.
```
from selenium import webdriver
from selenium.webdriver.firefox.options import Options

options = Options()
options.binary_location = r'C:\Program Files\Mozilla Firefox\firefox.exe'
driver = webdriver.Firefox(options=options)
driver.get('http://google.com/')
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2021-04-21 09:28:31

------------

I have uninstalled firefox and installed it again which resolved my issue.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-12-16 06:42:28

------------

You should download appropriate web driver from [https://github.com/mozilla/geckodriver/releases](https://github.com/mozilla/geckodriver/releases) and put it into folder where your py file is. Also you can put it anywhere as long as the location of the file it is in your system path.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2020-12-16 07:34:32

------------

Selenium uses a  (a specific one for each web browser) in order to communicate with the  (Firefox in your case).
To use Firefox, you have to:

1. Download its web driver from https://github.com/mozilla/geckodriver/releases
2. Put the web driver in a specific location in the file system (same folder as the python script for example)
3. Add the web driver location path when initializing in the python code.


So the final code would look like this:
```
from selenium import webdriver

browser = webdriver.Firefox('./geckodriver')

browser.get('https://www.python.org/')
```

Note: Sometimes a  of the web driver isn't compatible with an .


------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-05-04 10:34:50

------------

I have encountered the same problem (Windows, Firefox v99, Selenium 4.1.4, geckodriver 0.31.0), the path to exe file and the driver initialisation were set correctly, solved the issue by changing the win32 by win64 version of geckodriver


------------
    
    
## Answer \#8

 Vote: 0

Created at 2023-01-30 15:40:45

------------

as a side note for selenium/firefox (but with C#, not Python), this issue is quite relevant now in the sense that firefox location looks to be stored in windows in a new regedit location. Indeed geckodriver is looking in regedit location documented here:
```
HKEY_LOCAL_MACHINE\SOFTWARE WOW6432Node\Mozilla\Mozilla Firefox\[VERSION]\Main\PathToExe


HKEY_LOCAL_MACHINE\SOFTWARE\Mozilla\Mozilla Firefox\[VERSION]\Main\PathToExe
```

Source:
[https://developer.mozilla.org/en-US/docs/Web/WebDriver/Capabilities/firefoxOptions](https://developer.mozilla.org/en-US/docs/Web/WebDriver/Capabilities/firefoxOptions)
when on my machine it is there:
```
HKEY_LOCAL_MACHINE\SOFTWARE\Mozilla\Mozilla Firefox 109.0\bin
```

With the version number stored here:
```
HKEY_LOCAL_MACHINE\SOFTWARE\mozilla.org\Mozilla
```

and I set the selenium driver with C# Api with ():
```
var options = new FirefoxOptions();
...
options.BrowserExecutableLocation = @"C:\Program Files\Mozilla Firefox\firefox.exe";
Driver = new FirefoxDriver(options);
```

Regards


------------
    
    
## Answer \#9

 Vote: -1

Created at 2020-12-16 06:42:21

------------

You need to download geckodriver.
[https://github.com/mozilla/geckodriver/releases](https://github.com/mozilla/geckodriver/releases)
```
from selenium import webdriver;

browser= webdriver.Firefox('./geckodriver');
browser.get('http://www.seleniumhq.org');
```



------------
    
    