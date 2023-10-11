
# Post \#59131764 [Link](https://stackoverflow.com/questions/59131764/)

## Laravel error when sending custom email address using smtp server

**Vote**: 9 (409/702) **Views**: 20674 (299/702) 

**Internal ID** \#2-7-427

Created at 2019-12-02 02:03:56

Tags: `php` `laravel` `email` `smtp`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I have a project that has a module that sends email from request. Im using beautymail package for the email template. I can send an email using a gmail account, but there is this email that I get from my client that has a custome email address in it. Like this xx.xxxxx@propnex.sg, they said that the email is a smtp server. So I tried configuring my `.env` and other configuration files in laravel. But I get this error upon sending `Connection could not be established with host mail.propnex.sg :stream_socket_client(): SSL operation failed with code 1. OpenSSL Error messages: error:1408F10B:SSL routines:ssl3_get_record:wrong version number` Could someone tell me why Im getting this error and what should I do to get rid of this? Thanks a lot

.env config

```
MAIL_DRIVER=smtp
MAIL_HOST=mail.propnex.sg
MAIL_PORT=587
MAIL_USERNAME=xx.xxxxx@propnex.sg
MAIL_PASSWORD=xxxxxxxx
MAIL_ENCRYPTION=ssl
```


Mail.php

```
'from' => [
        'address' => 'xx.xxxxx@propnex.sg',
        'name' => 'Propnex',
    ],

    'reply_to' => ['address' => 'xx.xxxxx@propnex.sg', 'name' => 'Propnex'],

    'encryption' => env('MAIL_ENCRYPTION', 'tls'),


    'username' => env('MAIL_USERNAME'),

    'password' => env('MAIL_PASSWORD'),

    'port' => env('MAIL_PORT', 587),

    'driver' => env('MAIL_DRIVER', 'smtp'),

    'host' => env('MAIL_HOST', 'mail.propnex.sg'),
```



----------
        
## Answer \#0

**Accepted** Vote: 10

Created at 2019-12-02 02:29:17

------------

I just solved it by making a small changes in my .env and mail.php file like this

```
MAIL_ENCRYPTION=""
```


```
'encryption' => env('MAIL_ENCRYPTION', ''),
```


Tell me what are the drawbacks in this unsecured/alternative answer of mine. I want feedbacks. Thanks a lot.


------------
    
    
## Answer \#1

 Vote: 5

Created at 2019-12-02 07:00:36

------------

You're using SSL (MAIL_ENCRYPTION=ssl) so you need to change MAIL_PORT=465 in .env file.


------------
    
    
## Answer \#2

 Vote: 5

Created at 2021-07-25 17:58:25

------------


for TLS use Port : 587
for SSL use Port : 465


------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-06-12 21:00:53

------------

You might have crossed check every bit of line you have. The issue is with mailtrap.io it self. If your configuration is actually like this below.

```
MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=31466861b7bXXX
MAIL_PASSWORD=fe2b6503618XXX
MAIL_ENCRYPTION=null
```


I tested with two separate framework to proof this error. check the SMTP credentials try resetting the credentials.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2019-12-02 06:55:27

------------

```
You are setting mail encryption as tls in mail.php and on the other hand in the env file
you are setting it to ssl. Try setting it the same in both the files.

But i would recommend that you skip the certificate.
You can skip verification of the ssl certificate by using the code below in the mail.php file:

 'stream' => [
        'ssl' => [
            'allow_self_signed' => true,
            'verify_peer' => false,
            'verify_peer_name' => false,
        ],
    ],
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-09-21 15:37:50

------------

in you `.env` file 
change this 
`'encryption' =>ssl`
line into 
`'encryption' =>tls` 
and make sure you have some value for MAIL_FROM_ADDRESS also .
this worked for both localhost & server environments fine.


------------
    
    