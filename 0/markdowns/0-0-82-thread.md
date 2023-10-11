
# Post \#61969722 [Link](https://stackoverflow.com/questions/61969722/)

## How to enable `ignoreUndefinedProperties` in node js

**Vote**: 46 (133/702) **Views**: 18535 (314/702) 

**Internal ID** \#0-0-82

Created at 2020-05-23 09:04:48

Tags: `javascript` `node.js` `express` `google-cloud-firestore`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am developing a REST api for my application in Nodejs and Express. But each time i try to send a request I get an undefined error. Please how can i enable 'ignoreundefinedproperties'


----------
        
## Answer \#0

**Accepted** Vote: 99

Created at 2020-05-24 10:50:06

------------

once you import the firebase-admin sdk (in Typescript) like this 

```
import * as firestore from "firebase-admin";
```


then you just need to set the setting like this :

```
const db = firestore.firestore();
db.settings({ ignoreUndefinedProperties: true })
```



------------
    
    
## Answer \#1

 Vote: 25

Created at 2021-04-06 21:23:32

------------

If you are getting errors for calling this more than once, I recommend putting `admin.firestore().settings({ignoreUndefinedProperties:true});` in the same place you have `admin.initializeApp();`
Here's how I did it in my Typescript project
initialize.ts:
```
import * as admin from 'firebase-admin';
import 'firebase-functions';


admin.initializeApp();
admin.firestore().settings({ignoreUndefinedProperties:true});
```

Top of index.ts:
```
import './initialize.ts'
```



------------
    
    
## Answer \#2

 Vote: 11

Created at 2022-01-04 21:38:25

------------

For anyone using the v9 API:
```
import { getFirestore, connectFirestoreEmulator, initializeFirestore } from 'firebase/firestore'; // Firebase v9+

// Must be called before getFirestore()
initializeFirestore(app, {
  ignoreUndefinedProperties: true
});
const firestore = getFirestore(app);
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-05-25 18:54:55

------------

If you're facing error () even after trying other answers, restart your IDE/VSCode and terminal. This is what worked for me.


------------
    
    