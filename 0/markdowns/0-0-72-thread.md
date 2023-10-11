
# Post \#57088357 [Link](https://stackoverflow.com/questions/57088357/)

## AWS Lambda - Runtime.ImportModuleError: Error: Cannot find module 'jmespath'

**Vote**: 17 (290/702) **Views**: 24169 (269/702) 

**Internal ID** \#0-0-72

Created at 2019-07-18 06:33:54

Tags: `node.js` `amazon-web-services` `aws-lambda` `serverless-framework`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am working with aws lambda using serverless framework, I changed the runtime from `nodejs8.10` to `nodejs10.x`, then I got an errortrace, 

```
{"errorType":"Runtime.ImportModuleError","errorMessage":"Error: Cannot find module 'jmespath'","stack":["Runtime.ImportModuleError: Error: Cannot find module 'jmespath'","    at _loadUserApp (/var/runtime/UserFunction.js:100:13)","    at Object.module.exports.load (/var/runtime/UserFunction.js:140:17)","    at Object.<anonymous> (/var/runtime/index.js:36:30)","    at Module._compile (internal/modules/cjs/loader.js:701:30)","    at Object.Module._extensions..js (internal/modules/cjs/loader.js:712:10)","    at Module.load (internal/modules/cjs/loader.js:600:32)","    at tryModuleLoad (internal/modules/cjs/loader.js:539:12)","    at Function.Module._load (internal/modules/cjs/loader.js:531:3)","    at Function.Module.runMain (internal/modules/cjs/loader.js:754:12)","    at startup (internal/bootstrap/node.js:283:19)"]}
```


What is the cause of this issue and how can get it fixed?


----------
        
## Answer \#0

**Accepted** Vote: 11

Created at 2020-06-18 08:48:31

------------

I hit the same problem `Error: Cannot find module 'jmespath'` and solved it.
Do you use `aws-sdk` via node_modules? like follows
```
var aws = require('aws-sdk');
```

If so you just remove aws-sdk from node_modules.
remove aws-sdk for yarn
```
yarn remove aws-sdk
```

remove aws-sdk for npm
```
npm uninstall aws-sdk
```

`aws-sdk` has been included to lambda since nodejs10.
see: [https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html)


------------
    
    
## Answer \#1

 Vote: 1

Created at 2020-01-06 18:45:46

------------

Try to put `./` in front of your module name. I changed my name from `require("xxx")` to `require("./xxx")` and it worked again. In my case, the local module file i wanted to add (`xxx.js`) is on the same level as the `index.js` file.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-06-02 15:36:29

------------

I'd also like to add that as a preliminary step before adjusting your path, check and verify your dependencies listed in your package.json(s) files.
I've seen this error "Runtime.ImportModuleError","errorMessage":"Error: Cannot find module 'something' occur in the aws cloudwatch logs. 
It happened because my project has multiple subprojects/subfolders with their own package.json files.  Ensure that module is properly referenced in the subproject's package.json.
In local dev, you might have the dependency cited in your base/global package.json, and thinking it works -- but when you deploy the lambda the npm install that occurs during build does not include the newly required module because its not referenced in the local subproject's package.json.     


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-09-29 13:33:56

------------

In the root serverless project folder
```
npm i --save <npm module name missing>
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-12-16 17:34:23

------------

Well encountered the same issue in our project too.
It was issue in the same of file import.
Our file name `xabc.js` and in imported as `Xabc.js`
VScode was not complaining and was showing proper import


------------
    
    
## Answer \#5

 Vote: 0

Created at 2021-02-20 07:27:13

------------

For me the problem was that this `jmespath` library was actually missing(and some others, too) from uploaded `node_modules` folder.
Verified this by downloading zip(as the source was too big) and did not found there `jmespath` nor `aws-sdk` libraries.
Solved it by installing dependencies with `npm i` and re-uploading lambda with updated node_modules folder.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-07-14 21:47:42

------------

If 'nodejs' folder  is not there in Lambda Layer, create 'nodejs' and any file so that while executing the function, lambda will create zip file with entire node_modules packages inside 'nodejs' folder of Layer. If it is empty it will not execute. I hope it will resolve the issue. In my case it is working fine.


------------
    
    