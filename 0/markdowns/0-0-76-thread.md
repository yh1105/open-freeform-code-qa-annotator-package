
# Post \#51363855 [Link](https://stackoverflow.com/questions/51363855/)

## How to configure axios to use SSL certificate?

**Vote**: 101 (60/702) **Views**: 275423 (24/702) 

**Internal ID** \#0-0-76

Created at 2018-07-16 14:12:56

Tags: `node.js` `ssl` `ssl-certificate` `axios`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm trying to make a request with axios to an api endpoint and I'm getting the following error: `Error: unable to verify the first certificate`

It seems the https module, which axios uses, is unable to verify the SSL certificate used on the server.

When visiting the server with my browser, the certificate is valid and I can see/download it. I can also make requests to the api on my browser through https.

I can work around it by turning off verification. This code works.

```
const result = await axios.post(
    `https://${url}/login`,
    body,
    {
      httpsAgent: new https.Agent({
        rejectUnauthorized: false
      })
    }
  )
```


Problem is, this doesn't verify the SSL certificate and therefore opens up security holes.

How can I configure axios to trust the certificate and correctly verify it?


----------
        
## Answer \#0

**Accepted** Vote: 119

Created at 2018-12-02 23:42:53

------------

Old question but chiming in for those who land here. No expert. Please consult with your local security gurus and what not.
Axios is an http(s) client and http clients usually participate in TLS anonymously. In other words, the server accepts their connection without identifying who is trying to connect. This is different then say, Mutual TLS where both the server and client verify each other before completing the handshake.
The internet is a scary place and we want to protect our clients from connecting to spoofed public endpoints. We do this by ensuring our clients identify the server before sending any private data.
```
// DO NOT DO THIS IF SHARING PRIVATE DATA WITH SERVICE
const httpsAgent = new https.Agent({ rejectUnauthorized: false });
```

This is often posted (and more egregiously upvoted) as the answer on StackOverflow regarding https client connection failures in any language. And what's worse is that it usually works, unblocks the dev and they move on their merry way. However, while they certainly get in the door, whose door is it? Since they opted out of verifying the server's identity, their poor client has no way of knowing if the connection they just made to the company's intranet has bad actors listening on the line.
If the service has a public SSL cert, the `https.Agent` usually does not need to be configured further because your operating system provides a common set of publicly trusted CA certs. This is usually the same set of CA certs your browser is configured to use and is why a default axios client can hit [https://google.com](https://google.com) with little fuss.
If the service has a private SSL cert (self signed for testing purposes or one signed by your company's private CA to protect their internal secrets), the https agent must be configured to trust the private CA used to sign the server cert:
```
const httpsAgent = new https.Agent({ ca: MY_CA_BUNDLE });
```

where `MY_CA_BUNDLE` is an array of CA certs with both the server cert for the endpoint you want to hit and that cert's complete cert chain in `.pem` format. You must include all certs in the chain up to the trust root.

---



### Where are these options documented?


> HTTPS is the HTTP protocol over TLS/SSL. In Node.js this is implemented as a separate module.
Therefore options passed to the `https.Agent` are a merge of the options passed to [tls.connect()](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback) and [tls.createSecureContext()](https://nodejs.org/api/tls.html#tls_tls_createsecurecontext_options).


------------
    
    
## Answer \#1

 Vote: 38

Created at 2018-08-28 20:00:10

------------

Create a custom agent with SSL certificate:

```
const httpsAgent = new https.Agent({
  rejectUnauthorized: false, // (NOTE: this will disable client verification)
  cert: fs.readFileSync("./usercert.pem"),
  key: fs.readFileSync("./key.pem"),
  passphrase: "YYY"
})

axios.get(url, { httpsAgent })

// or

const instance = axios.create({ httpsAgent })
```


From [https://github.com/axios/axios/issues/284](https://github.com/axios/axios/issues/284)


------------
    
    
## Answer \#2

 Vote: 22

Created at 2020-09-29 22:59:08

------------

For me, when my application is running in development mode, I have disabled `rejectUnauthorized` directly in `axios.defaults.options`. This works very well. be careful and use this only in developer mode.
```
import https from 'https'
import axios from 'axios'
import config from '~/config'

/**
 * Axios default settings
 */
axios.defaults.baseURL = config.apiURL

/**
 * Disable only in development mode
 */
if (process.env.NODE_ENV === 'development') {
  const httpsAgent = new https.Agent({
    rejectUnauthorized: false,
  })
  axios.defaults.httpsAgent = httpsAgent
  // eslint-disable-next-line no-console
  console.log(process.env.NODE_ENV, `RejectUnauthorized is disabled.`)
}
```



------------
    
    
## Answer \#3

 Vote: 11

Created at 2019-07-13 04:47:24

------------

These configuration worked for me (In a Mutual Authentication scenario).

```
const httpsAgent = new https.Agent({
  ca: fs.readFileSync("./resource/bundle.crt"),        
  cert: fs.readFileSync("./resrouce/thirdparty.crt"),
  key: fs.readFileSync("./resource/key.pem"), 
})
```


Note: bundle.crt was prepared from provided certificates (root,intermediate,end entry certificate). Unfortunately no clear documentation found in this regards.


------------
    
    
## Answer \#4

 Vote: 7

Created at 2021-10-09 19:43:18

------------

This is very dirty, but at the top of your script, just put:
`process.env['NODE_TLS_REJECT_UNAUTHORIZED'] = '0';`
This basically tells node to not check SSL certificates, which is very convenient when you get self signed certificates rejected in development.
Please don't use this in production.


------------
    
    
## Answer \#5

 Vote: 2

Created at 2021-05-24 11:24:21

------------

This what worked for me , using axios with nodejs + express
```
exports.test_ssl = async (req,res) => { 
   
let cert_file = fs.readFileSync("./ssl/my_self_signed_certificate.crt")
let ca_file = fs.readFileSync("./ssl/my_self_signed_certificate_ca.crt")
const agent = new https.Agent({
    requestCert: true,
    rejectUnauthorized: true,
    cert: cert_file,
    ca: ca_file
});
const options = {
    url: `https://51.195.45.154/test`,  // <---this is  a fake ip do not bother
    method: "POST",
    httpsAgent : agent,
    headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/txt;charset=UTF-8'
    },
    data: {}
};


console.log(cert_file.toString())

axios(options)
.then(response => {
    payload = response.data ;
    return res.status(200).send({"status":1});
}).catch(err => {
    console.log(err);
    return false
});

}
```



------------
    
    
## Answer \#6

 Vote: 1

Created at 2021-09-17 09:52:52

------------

This worked for me:
```
import axios from 'axios'
import https from 'https'

const headers = {};

const httpsAgent = new https.Agent({
  ca: fs.readFileSync('./certs/cert.pem'),
  cert: fs.readFileSync('./certs/cert.pem'),
})

const data = await axios.get(url, { httpsAgent, headers })
```



------------
    
    
## Answer \#7

 Vote: 1

Created at 2021-09-27 05:38:07

------------

```
const https = require('https');
const axios = require('axios')

const CA = "-----BEGIN CERTIFICATE-----$$$$$-----END CERTIFICATE-----"
const url = "bla"
const httpsAgent = new https.Agent({
  ca: CA
});

const response = await axios.get(url, { httpsAgent });
```

This is what work for me.


------------
    
    