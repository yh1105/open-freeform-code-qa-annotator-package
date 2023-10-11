
# Post \#64117723 [Link](https://stackoverflow.com/questions/64117723/)

## Puppeteer: Failed to launch the browser process! spawn

**Vote**: 7 (449/702) **Views**: 26687 (249/702) 

**Internal ID** \#0-0-112

Created at 2020-09-29 10:27:08

Tags: `node.js` `express` `web-scraping` `puppeteer`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

When I try to run `node app.js`, I get error:
> the message is Failed to launch the browser process! spawn
/Users/iliebogdanbarbulescu/Downloads/firstProject/node_modules/chromium/lib/chromium/chrome-mac/Chromium.app
> EACCES

I checked  the folder at `/Users/iliebogdanbarbulescu/Downloads/firstProject/node_modules/chromium/lib/chromium/chrome-mac/Chromium.app` and the file is not zipped. It can be run.

If I try to execute without the path,  it works, but
I would like to use either Chrome or Chromium to open a new page.
`const browser = await puppeteer.launch({headless:false'});`
```
const express = require('express');
const puppeteer = require('puppeteer');
const app = express();

(async () => {
  const browser = await puppeteer.launch({headless:false, executablePath:'/Users/iliebogdanbarbulescu/Downloads/firstProject/node_modules/chromium/lib/chromium/chrome-mac/Chromium.app'});
 const page = await browser.newPage();
 await page.goto('https://google.com', {waitUntil: 'networkidle2'});

})().catch((error) =>{
  console.error("the message is " + error.message);
});

app.listen(3000, function (){
    console.log('server started');
})
```

[](https://i.stack.imgur.com/0Sef5.jpg)


----------
        
## Answer \#0

**Accepted** Vote: 18

Created at 2020-10-02 12:48:14

------------

If you navigate to `chrome://version/` page in this exact browser, it will show the `Executable Path` which is the exact string you need to use as `executablePath` puppeteer launch option.
Usually, chrome's path looks like this on MAC:
```
/Applications/Google Chrome.app/Contents/MacOS/Google Chrome
```

Or something like this if chromium is located in your `node_modules` folder:
```
/Users/iliebogdanbarbulescu/Downloads/firstProject/node_modules/chromium/lib/chromium/chrome-mac/Chromium.app/Contents/MacOS/Chromium
```

Now if you compare the string you used for `executablePath`: it differs from the one retrieved with the method mentioned above. `/Contents/MacOS/Chromium`
 the chromium bundled with puppeteer is the version guaranteed to work together with the actual pptr version: if you plan to use other chrome/or chromium-based browsers you might experience unexpected issues.


------------
    
    
## Answer \#1

 Vote: 8

Created at 2020-11-17 16:10:54

------------

Following up on @theDavidBarton:
Chromium which was shipped with Puppeteer did not work, but the Chrome installation on my MacBook did work.
OS: OS-X 10.15.7 (Catalina)
Node version: v14.5.0
Failed code:
```
const browser = await puppeteer.launch({
  headless: true, 
  executablePath: "/users/bert/Project/NodeJS/PuppeteerTest/node_modules/puppeteer/.local-chromium/mac-818858/chrome-mac/Chromium.app/Contents/MacOS/Chromium"
});
```

Successful code:
```
const browser = await puppeteer.launch({
  headless: true, 
  executablePath: "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
});
```

Full code, Just the first example on the Puppeteer website:
```
const puppeteer = require('puppeteer');

(async () => {
    try {
        const browser = await puppeteer.launch({headless: true, executablePath: "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"});
        const page = await browser.newPage();
        await page.goto('https://example.com');
        await page.screenshot({path: 'example.png'});

        await browser.close();
    } catch (err) {
        console.log(err);
    }
})();
```

And, yes, I got the Screenshot !! :-)


------------
    
    
## Answer \#2

 Vote: 5

Created at 2021-04-21 15:44:55

------------

Using location-chrome: [https://www.npmjs.com/package/locate-chrome](https://www.npmjs.com/package/locate-chrome)
```
const locateChrome = require('locate-chrome');
const executablePath = await new Promise(resolve => locateChrome(arg => resolve(arg)));
const browser = await puppeteer.launch({ executablePath });
```



------------
    
    