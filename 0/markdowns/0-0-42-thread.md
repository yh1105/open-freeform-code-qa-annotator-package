
# Post \#66654037 [Link](https://stackoverflow.com/questions/66654037/)

## mongo-connect error with mongo-connect(session)

**Vote**: 17 (290/702) **Views**: 16994 (334/702) 

**Internal ID** \#0-0-42

Created at 2021-03-16 11:11:10

Tags: `node.js` `express` `session` `mongo-collection`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

After searching around about it, I can't find any solution or mistake in my `code` about this error. I've got my `app.js` files inside my node JS application with the `mongo-connect` declaration :
```
const MongoStore = require('connect-mongo')(session)
```

And I've got this error :
> TypeError: Class constructor MongoStore cannot be invoked without
'new'
at Object. (/Users/souhailmohamed/project/devops/story website/app.js:11:20)
at Module._compile (internal/modules/cjs/loader.js:1063:30)
at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
at Module.load (internal/modules/cjs/loader.js:928:32)
at Function.Module._load (internal/modules/cjs/loader.js:769:14)
at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)
at internal/main/run_main_module.js:17:47
There is my `app.use` code beloww :
```
app.use(session({
    secret: 'story book',
    resave: false,
    saveUninitialized: false,
    store: new MongoStore({ mongooseConnection: mongoose.connection })
}))
```

I understand pretty well about the
```
const MongoStore = require('connect-mongo')(session)
```

but I didn't understand the error. But it's from a tutorial from youtube by traversy media [LinK](https://www.youtube.com/watch?v=SBvmnHTQIPY&t=4202s&ab_channel=TraversyMedia)


----------
        
## Answer \#0

**Accepted** Vote: 20

Created at 2021-05-21 06:39:38

------------

[connect-mongo](https://www.npmjs.com/package/connect-mongo) v4 introduces new signature (compared to v3).
Here is the official [migration guide](https://github.com/jdesboeufs/connect-mongo/blob/HEAD/MIGRATION_V4.md#v4-migration-guide).

---


Also, here is a quick diff of what I changed in my specific project (using [mongoose](https://www.npmjs.com/package/mongoose) and [dotenv](https://www.npmjs.com/package/dotenv)) to upgrade from `connect-mongo` v3 to v4:
[](https://www.diffchecker.com/6Vuly2qx)


------------
    
    
## Answer \#1

 Vote: 12

Created at 2021-04-10 23:07:15

------------

I had the same problem in my code and solved it using this:
```
const MongoDbStore = require('connect-mongo');
```

Remove (session) from the require statement and update the app.use() like this:
```
// Session Config
app.use(
    session({
        secret: 'story book',
        resave: false,
        saveUninitialized: false,
        store: MongoDbStore.create({
            mongoUrl: YourDatabaseURL
        })
    })
);
```



------------
    
    
## Answer \#2

 Vote: 5

Created at 2021-04-08 14:14:44

------------

I was following the same so I discovered an npm pakage [connect-mongodb-session](https://www.npmjs.com/package/connect-mongodb-session).
Install it and use the same code of session as Brad uses in his video
```
const MongoDBStore = require('connect-mongodb-session')(session);

//sessions
app.use(session({
    secret: 'keyboard cat',
    resave: false,
    saveUnitialized: false,
    store: new MongoDBStore({
        mongooseConnection: mongoose.connection
    })
    //cookie: { secure: true }
}))

const store = new MongoStore({
    uri: 'your mongo uri' ,
    collection:'mySessions'
})

//express middleware
app.use(session({
    secret: 'keyboard cat',
    resave: false,
    saveUninitialized: false,
    store: store

// Don't create a session until something is stored 
// cookie: { secure: true } - this wont work without https
}))
```

It solved my relogging issue also. If you close the tab and renter to localhost you get yourself logged in already


------------
    
    
## Answer \#3

 Vote: 2

Created at 2021-03-16 13:00:10

------------

Ok so i've found a way to resolve it, i don't about the reason, on which version of express to use but i removed the (session) after mongo-connect and change the app.use to this :
```
app.use(session({
    secret: 'story book',
    resave: false,
    saveUninitialized: false,
    store: MongoStore.create({ mongoUrl: 'mongodb+srv://<id+ password>@cluster0.cq7f2.mongodb.net/DBname?retryWrites=true&w=majority' })
}))
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2021-05-26 18:30:31

------------

Certain rule are changed for the syntax in "connect-mongo" document [here](https://github.com/jdesboeufs/connect-mongo#express-or-connect-integration)
`const MongoStore = require("connect-mongo");`
```
secret: 'keyboard cat',
  resave: false,
  saveUninitialized: false,
  store: MongoStore.create({
    mongoUrl: process.env.MONGO_URI  //(URI FROM.env file)
  })
  //stroed it in database
}));
```



------------
    
    
## Answer \#5

 Vote: 2

Created at 2021-08-14 17:15:55

------------

In your `app.js` make the following changes:
```
let MongoStore = require('connect-mongo');

app.use(
    session({
        secret: 'keyboard cat',
        resave: false,
        saveUninitialized: false,
        store: MongoStore.create({
            mongoUrl: 'your mongo url'
        })
    });
);
```

References:
- [https://www.npmjs.com/package/connect-mongo](https://www.npmjs.com/package/connect-mongo)


------------
    
    
## Answer \#6

 Vote: 2

Created at 2022-07-01 18:43:08

------------

It seems that 'connect-mongo' is obsolete and the newer version includes 
```
const MongoDBStore = require('connect-mongodb-session')(session);
```

Use the above code instead and it should work without any errors.


: install the 'connect-mongodb-session' package using 


------------
    
    
## Answer \#7

 Vote: 1

Created at 2021-05-02 08:16:53

------------

I have same problem. Here is the  solution:
```
var session = require('express-session');
var MongoStore = require('connect-mongo');
var mongoose = require('mongoose');
mongoose.Promise = global.Promise;


app.use(session({
  secret : 'mysecretkey',
  resave : true,
  saveUninitialized : true,
  store :MongoStore.create({ mongoUrl: DBUrl })
}));
```




------------
    
    
## Answer \#8

 Vote: 1

Created at 2021-08-14 11:21:29

------------

```
app.use(session({
  store: MongoStore.create({ mongoUrl: 'mongodb://localhost/test-app' })
}));

// Advanced usage
app.use(session({
  store: MongoStore.create({
    mongoUrl: 'mongodb://user12345:foobar@localhost/test-app?authSource=admin&w=1',
    mongoOptions: advancedOptions // See below for details
  })
}));
```



------------
    
    
## Answer \#9

 Vote: 0

Created at 2021-04-25 00:16:05

------------

Here is the solution:
```
const session = require('express-session');
const MongoStore = require('connect-mongo');
const mongoose = require('mongoose');
```

```
app.use(session({
    secret: 'story book',
    resave: false,
    saveUninitialized: false,
    store: new MongoStore({
      mongoUrl: mongoose.connection._connectionString,
      mongoOptions: {}
    })
}))
```



------------
    
    
## Answer \#10

 Vote: 0

Created at 2021-11-24 20:28:49

------------

Just use the another npm package connect-mongodb-session and it should work
[https://www.npmjs.com/package/connect-mongodb-session](https://www.npmjs.com/package/connect-mongodb-session)
`const MongoDBStore = require('connect-mongodb-session')(session);`


------------
    
    