
# Post \#59188483 [Link](https://stackoverflow.com/questions/59188483/)

## Error: Invalid login: 535-5.7.8 Username and Password not accepted

**Vote**: 47 (131/702) **Views**: 83608 (102/702) 

**Internal ID** \#0-0-12

Created at 2019-12-05 04:55:24

Tags: `javascript` `node.js` `gmail` `smtp-auth`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

The code below is perfect to send emails using node.js code/program. However, still getting error mentioned in the title.

```
var nodemailer = require('nodemailer');

var transporter = nodemailer.createTransport({
  service: 'gmail',
  auth: {
    user: 'haideryaqoobengr@gmail.com',
    pass: '**********'
  }
});

var mailOptions = {
  from: 'haideryaqoobengr@gmail.com',
  to: 'haideryaqoob720@gmail.com',
  subject: 'Sending Email using Node.js',
  text: 'That was easy!'
};

transporter.sendMail(mailOptions, function(error, info){
  if (error) {
    console.log(error);
  } else {
    console.log('Email sent: ' + info.response);
  }
});
```



----------
        
## Answer \#0

**Accepted** Vote: 79

Created at 2019-12-05 11:50:58

------------

Yes, code is perfect. However, you need to allow less secure apps from your google account to send emails. Through this link.
[Allow less secure apps From your Google Account](https://myaccount.google.com/lesssecureapps)


------------
    
    
## Answer \#1

 Vote: 21

Created at 2022-05-17 19:42:32

------------

Google disabled less secure apps now so you need to setup [Login with app password](https://support.google.com/accounts/answer/185833)
In summary you must setup your access with two factor authentication in order to allow the app password
```
transport: {
  host: 'smtp.gmail.com',
  port: 465,
  secure: true,
  auth: {
    user: 'contact@gmail.com',
    pass: 'app password',
  },
},
```



------------
    
    
## Answer \#2

 Vote: 7

Created at 2023-01-16 14:51:35

------------

There is very simple solution for this.
Follow the following steps to send emails from your gmail using node (nodemailer)

1. Step1: Open this link https://myaccount.google.com/security
2. Step2: Enable 2 factor authentication
3. Click on App passwords just below the 2 factor authentication
4. From Select App options select Other and write your app name it could be any name like mycustomapp
5. It will generate you the password copy the password from the popup and use the following code.
6. Use that copied password in the Auth password section my password was this rkancqhzgvmzsdaqyx


```
const nodemailer = require('nodemailer');

  const transporter = nodemailer.createTransport({
   service: 'gmail',
   host: 'smtp.gmail.com',
   port: 465,
   secure: true,
   auth: {
    user: 'niazi@gmail.com',
    pass: 'rkancqhzgvmzsdaqyx',
   },
  });

  const sendEmail = (email, token) => {
   const mailOptions = {
    from: 'niazi@gmail.com',
    to: email,
    subject: 'Email verification',
    html:
  '<p>Please click on the following link to verify your email address:</p>' +
  '<a href="http://localhost:3000/verify/' +
  token +
  '">http://localhost:3000/verify/' +
  token +
    '</a>',
  };

  transporter.sendMail(mailOptions, function (error, info) {
    if (error) {
      console.log('Error in sending email  ' + error);
      return true;
    } else {
     console.log('Email sent: ' + info.response);
     return false;
    }
   });
  };

  module.exports = sendEmail;
```




------------
    
    
## Answer \#3

 Vote: 6

Created at 2022-06-08 11:49:22

------------

For gmail, Less secure app access is no longer available.
[https://support.google.com/accounts/answer/6010255?authuser=1&hl=en&authuser=1&visit_id=637902855221010844-2637605848&p=less-secure-apps&rd=1](https://support.google.com/accounts/answer/6010255?authuser=1&hl=en&authuser=1&visit_id=637902855221010844-2637605848&p=less-secure-apps&rd=1)


------------
    
    
## Answer \#4

 Vote: 2

Created at 2021-10-11 15:34:15

------------

 Go to  Less secure app access . convert icon Allow less secure apps to ON. 

Link : https://myaccount.google.com/lesssecureapps



------------
    
    
## Answer \#5

 Vote: 1

Created at 2022-06-20 12:55:20

------------

[Less secure app](https://support.google.com/accounts/answer/6010255) no longer available: go to [https://myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords) and setup "App password".
App password will work only if you have 2FA setup for your account, read more: [https://support.google.com/accounts/answer/185833?hl=en](https://support.google.com/accounts/answer/185833?hl=en)
It's just different password per every registered app. It's always better to not use the main account password so makes total sense.


------------
    
    
## Answer \#6

 Vote: 1

Created at 2022-07-18 13:33:13

------------

Google disabled less secure apps, to resolve the issue one need to setup "Login with app password" and to allow the app password "setup two factor authentication"
when 2-Step-Verification is on and one get a "password incorrect" error, sign in to your account and go to security, try to use an App Password.
```
transport: {
  host: 'smtp.gmail.com',
  port: 465,
  secure: true,
  auth: {
    user: 'contact@gmail.com',
    pass: 'app password',
  },
},
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-02-16 11:26:09

------------

You could create a password for your application to use that email. Go to manage account, then to security, you will see the add password option. You will need to add the name of your application then click generate.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2022-08-04 13:54:57

------------

1st create app password ( [https://support.google.com/accounts/answer/185833](https://support.google.com/accounts/answer/185833) )...
2nd use that app password in your app


------------
    
    