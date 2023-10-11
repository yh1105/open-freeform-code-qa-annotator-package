
# Post \#65341400 [Link](https://stackoverflow.com/questions/65341400/)

## How to Connect Heroku Redis TLS Node?

**Vote**: 3 (575/702) **Views**: 5546 (509/702) 

**Internal ID** \#0-0-35

Created at 2020-12-17 13:11:01

Tags: `node.js` `heroku` `heroku-redis`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I dont seem to be able to connect to Heroku Redis using TLS on Node.
These docs arent really much help: [https://devcenter.heroku.com/articles/securing-heroku-redis](https://devcenter.heroku.com/articles/securing-heroku-redis)
Does anyone have a working example? Should I be using REDIS_URL or REDIS_TLS_URL?
Im using node_redis v3


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2021-01-04 13:42:19

------------

I found the Redis 6 add-on by Heroku generated an `Error: self signed certificate in certificate chain` error when when connecting to REDIS_URL without any parameters with ioredis on Node. You can avoid this error by passing in TLS options with `rejectUnauthorized` set to `false`.
The `rejectUnauthorized` of `false` allows for self-signed certificates, which would be an issue if concerned about MITM attacks. See [TLS options](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback) for more background.
This is working for me with the latest `ioredis` package with rediss:// and redis:// URL's...
```
const REDIS_URL = process.env.REDIS_URL;
  const redis_uri = url.parse(REDIS_URL);
  const redisOptions = REDIS_URL.includes("rediss://")
    ? {
        port: Number(redis_uri.port),
        host: redis_uri.hostname,
        password: redis_uri.auth.split(":")[1],
        db: 0,
        tls: {
          rejectUnauthorized: false,
        },
      }
    : REDIS_URL;
  const redis = new Redis(redisOptions);
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-08-18 13:41:28

------------

Here's my approach. It's easier to pass URL and TLS options separately.
```
const redisUrl = process.env.REDIS_TLS_URL ? process.env.REDIS_TLS_URL : process.env.REDIS_URL;
const redisDefaults = {
  tls: {
  // Heroku uses self-signed certificate, which will cause error in connection, unless check is disabled
  rejectUnauthorized: false,
  },
};
const defaultClient = redis.createClient(redisUrl, redisDefaults);
```

If you have test env running with hobby version, the TLS is URL is set in REDIS_TLS_URL, while production normally runs with premium and the env is REDIS_URL. So, to be compatible with the both, I first look for REDIS_TLS_URL and after that REDIS_URL to support both test and prod env.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-04-07 11:53:27

------------

For devs using `node-redis`, you'll need to set TLS to true when initializing your client.
```
redis.createClient({
        url: REDIS_URL,
        socket: {
          tls: true,
          rejectUnauthorized: false,
        },
      }
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2020-12-17 15:10:36

------------

I don't know why you can't connect to this Redis Add-on unfortunately.
In the event you want to test on another Add-On, I have developped a Redis Add-On that is in the "alpha" phrase (free) on Heroku. I'll be able to provide you some support if you can't connect to it.
If you are interested, give me your Heroku email in private and I'll send you an invitation :)


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-04-13 18:31:14

------------

For people using Bull, this implementation worked for me. Thanks @Tom McLellan.
```
const Queue = require('bull');
const redisUrlParse = require('redis-url-parse');

const REDIS_URL = process.env.REDIS_URL || 'redis://127.0.0.1:6379';
const redisUrlParsed = redisUrlParse(REDIS_URL);
const { host, port, password } = redisUrlParsed;
const bullOptions = REDIS_URL.includes('rediss://')
  ? {
      redis: {
        port: Number(port),
        host,
        password,
        tls: {
          rejectUnauthorized: false,
        },
      },
    }
  : REDIS_URL;

const workQueue = new Queue('work', bullOptions);
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-11-22 12:33:10

------------

This worked for me using `node-redis` v3.0.0
```
const opts = config.REDIS_URL.includes('rediss://') ? {
  url: config.REDIS_URL,
  tls: {
    rejectUnauthorized: false
  }
} : config.REDIS_URL;

const client = redis.createClient(opts);
```

Use `tls` not `socket`. Thanks to [this](https://github.com/redis/node-redis/issues/1767#issuecomment-1184091501).


------------
    
    