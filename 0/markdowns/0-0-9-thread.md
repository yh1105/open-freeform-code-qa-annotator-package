
# Post \#72336177 [Link](https://stackoverflow.com/questions/72336177/)

## Error: req#logout requires a callback function

**Vote**: 41 (153/702) **Views**: 26343 (251/702) 

**Internal ID** \#0-0-9

Created at 2022-05-22 09:08:45

Tags: `javascript` `express` `passport.js` `google-authentication`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

can't able to find solution of this tried everything, i am just new in using passport.   i have coded this before but that time i have not received any error, but this time dont know why i am getting this, i have also read passport docs and tried every solutions
thanks in advance
```
<%- include('partials/header') %>

<header>
    <nav class="flex align-item-center">
        <div class="div-left">
            <img id="userimg" src="img/user.png" alt="logo">
        </div>
        <div class="div-right flex">
            <div style="font-size: 14px">
                <a href="/logout" role="button"> Logout</a>
            </div>
        </div>
    </nav>
</header>

<%- include('partials/footer') %>
```


---



## js code


```
require('dotenv').config();
const express = require("express");
const bodyParser = require("body-parser");
const ejs = require("ejs");
const mongoose = require("mongoose");
const session = require('express-session');
const passport = require('passport');
const passportLocalMongoose = require('passport-local-mongoose');
const GoogleStrategy = require('passport-google-oauth20').Strategy;
const findOrCreate = require('mongoose-findorcreate');

const app = express();

app.use(express.static("public"));
app.set('view engine', 'ejs');
app.use(bodyParser.urlencoded({
    extended: true
}));

app.use(session({
    secret: process.env.SECRET,
    resave: false,
    saveUninitialized: false
}))

app.use(passport.initialize());
app.use(passport.session());

mongoose.connect("#DB")

const accountsdata = new mongoose.Schema({
    email: String,
    password: String,
    googleId: String,
    facebookId: String,
    combo: String,
    date: String,
    price: String,
    accountemail: String

});

accountsdata.plugin(passportLocalMongoose);
accountsdata.plugin(findOrCreate);

const data = new mongoose.model("amazonprime", accountsdata);

passport.use(data.createStrategy());

passport.serializeUser(function (user, cb) {
    process.nextTick(function () {
        cb(null, { id: user.id, username: user.username });
    });
});

passport.deserializeUser(function (user, cb) {
    process.nextTick(function () {
        return cb(null, user);
    });
});

passport.use(new GoogleStrategy({
    clientID: process.env.CLIENT_ID,
    clientSecret: process.env.CLIENT_SECRET,
    callbackURL: "http://localhost:3000/auth/google/home",
    userProfileURL: "https://www.googleapis.com/oauth2/v3/userinfo"
},
    function (accessToken, refreshToken, profile, cb) {
        console.log(profile)
        data.findOrCreate({ googleId: profile.id }, function (err, user) {
            return cb(err, user);
        });
    }
));

app.get("/", (req, res) => {
    res.render("login");
});

app.get('/auth/google',
    passport.authenticate("google", { scope: ["profile"] })
);

app.get("/auth/google/home",
    passport.authenticate("google", { failureRedirect: "/" }),
    function (req, res) {

        res.redirect("/home");
    });

app.get("/signup", (req, res) => {
    res.render("signup");
});

app.get("/home", function (req, res) {
    if (req.isAuthenticated()) {
        res.render("home");

    } else {
        res.redirect("/");
    }
})

app.get("/logout", (req, res) => {
    req.logout();
    res.redirect("/");
});


app.post("/signup", (req, res) => {

    data.register({ username: req.body.username }, req.body.password, function (err, user) {
        if (err) {
            console.log(err);
            res.redirect("/signup");
        } else {
            passport.authenticate("local")(req, res, function () {
                res.redirect("/home");
            })
        }
    })
})

app.post("/", function (req, res) {

    const user = new data({
        username: req.body.username,
        password: req.body.password
    })
    req.login(user, function (err) {
        if (err) {
            console.log(err);
        } else {
            passport.authenticate("local")(req, res, function () {
                res.redirect("/home")
            })
        }
    })
});

app.get("/logout", (req, res) => {
    req.logout();
    res.redirect("/");
});




app.listen(3000, function () {
    console.log("Server started on port 3000.");
});
```


---



## error


```
Error: req#logout requires a callback function
    at IncomingMessage.req.logout.req.logOut (E:\Web Development\Data Handler\node_modules\passport\lib\http\request.js:65:44)
    at E:\Web Development\Data Handler\app.js:105:9
    at Layer.handle [as handle_request] (E:\Web Development\Data Handler\node_modules\express\lib\router\layer.js:95:5)
    at next (E:\Web Development\Data Handler\node_modules\express\lib\router\route.js:144:13)
    at Route.dispatch (E:\Web Development\Data Handler\node_modules\express\lib\router\route.js:114:3)
    at Layer.handle [as handle_request] (E:\Web Development\Data Handler\node_modules\express\lib\router\layer.js:95:5)
    at E:\Web Development\Data Handler\node_modules\express\lib\router\index.js:284:15
    at Function.process_params (E:\Web Development\Data Handler\node_modules\express\lib\router\index.js:346:12)
    at next (E:\Web Development\Data Handler\node_modules\express\lib\router\index.js:280:10)
    at SessionStrategy.strategy.pass (E:\Web Development\Data Handler\node_modules\passport\lib\middleware\authenticate.js:346:9)
```



----------
        
## Answer \#0

**Accepted** Vote: 95

Created at 2022-05-22 09:43:33

------------

Since version 0.6.0 (which was released only a few days ago by the time of writing this), `req.logout` is asynchronous. This is part of a larger change that averts session fixation attacks.
See the [release announcement](https://medium.com/passportjs/fixing-session-fixation-b2b68619c51d):
> The other major change is that that `req.logout()` is now an asynchronous function, whereas previously it was synchronous. For instance, a logout route that was previously:```
app.post('/logout', function(req, res, next) {
  req.logout();
  res.redirect('/');
});
```
should be modified to:```
app.post('/logout', function(req, res, next) {
  req.logout(function(err) {
    if (err) { return next(err); }
    res.redirect('/');
  });
});
```

Jared Hanson [mentioned](https://github.com/jaredhanson/passport/issues/902#issuecomment-1133897569) that the docs are not up to date yet:
> It is necessary in order to improve the security of how sessions are managed during logout. Upgrading to 0.6.0 will require applications to pass a callback to `req#logout`. I'm still working on updating the docs and examples.


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-05-22 13:00:57

------------

Learner here (too?) -- I had that same problem. I was banging my head against the wall until the instructor mentioned that `req.login()` requires a callback function. For the callback he used an error-handling function that turned out to work well for `req.logout()` too.
Maybe you could try this:
```
app.get("/logout", (req, res) => {
  req.logout(req.user, err => {
    if(err) return next(err);
    res.redirect("/");
  });
});
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-06-16 19:26:33

------------

If you are following Angela Yu web dev bootcamp, then use this,
```
app.get('/logout', function(req, res, next) {
  req.logout(function(err) {
    if (err) { 
      return next(err); 
      }
    res.redirect('/');
  });
});
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-09-04 10:11:07

------------

This works perfectly for me:
```
app.get("/logout", function(req, res, next) {
  req.logout(function(err) {
    if (err) { return next(err); }
    res.redirect("/");
  });
});
```

and you can as well use this:
```
app.post("/logout", function(req, res, next) {
  req.logout(function(err) {
    if (err) { return next(err); }
    res.redirect("/");
  });
});
```

Both works, cheers!!!


------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-11-04 06:12:33

------------

if this is with Angela Yu bootcamp.
use this:
[https://www.passportjs.org/tutorials/password/logout/](https://www.passportjs.org/tutorials/password/logout/)
```
app.get("/logout", function(req,res,next){
  req.logout(function(err){
    if (err)
    {
      return next(err);
    }
    res.redirect("/");
  });
});
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-06-16 11:20:48

------------

```
app.get('/users/logout', (req, res, next) => {
  req.logout(function (err) {
    if (err) {
      return next(err);
    }
    // if you're using express-flash
    req.flash('success_msg', 'session terminated');
    res.redirect('/login');
  });
});
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-08-05 10:28:36

------------

here is my problem that is not working:
```
app.get("/logout", (req, res) => {
  req.logout();
  res.redirect("/");
});
```


```
require("dotenv").config();

const express = require("express");
const bodyParser = require("body-parser");
const ejs = require("ejs");
const mongoose = require("mongoose");
const session = require("express-session");
const passport = require("passport");
const passportLocalMongoose = require("passport-local-mongoose");

const app = express();
app.use(express.static("public"));

app.use(bodyParser.urlencoded({
  extended: true
}));

app.set("view engine", "ejs");

app.use(
  session({
    secret: "our little secret.",
    resave: false,
    saveUninitialized: false,
  })
);

app.use(passport.initialize());

app.use(passport.session());

mongoose.connect("mongodb://127.0.0.1/userDB");

const userSchema = new mongoose.Schema({
  email: String,
  password: String,
});

userSchema.plugin(passportLocalMongoose);

const User = new mongoose.model("User", userSchema);

passport.use(User.createStrategy());

passport.serializeUser(User.serializeUser());
passport.deserializeUser(User.deserializeUser());

app.get("/", (req, res) => {
  res.render("home");
});

app.get("/login", (req, res) => {
  res.render("login");
});

app.get("/register", (req, res) => {
  res.render("register");
});

app.get("/secrets", (req, res) => {
  if (req.isAuthenticated()) {
    res.render("secrets");
  } else {
    res.redirect("/login");
  }
});

app.get("/logout", function(req, res, next) {
  req.logout(function(err) {
    if (err) {
      return next(err);
    }
    res.redirect("/");
  });
});
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-12-30 18:05:53

------------

```
<nav class="grey darken-3">
  <div class="nav-wrapper container">
    <a href="#!" class="brand-logo center">StoryBooks</a>
    <a href="#" data-target="mobile-demo" class="sidenav-trigger show-on-large"><i class="fas fa-bars"></i></a>
    <ul class="sidenav" id="mobile-demo">
      <li><a href="/stories">Public Stories</a></li>
      <li><a href="/dashboard">Dashboard</a></li>
      <li><a href="/auth/logout">Logout</a></li>
    </ul>
  </div>
</nav>
```



------------
    
    