
# Post \#68605529 [Link](https://stackoverflow.com/questions/68605529/)

## Where to store API key in ReactJS?

**Vote**: 4 (532/702) **Views**: 2848 (576/702) 

**Internal ID** \#0-0-117

Created at 2021-07-31 20:12:44

Tags: `javascript` `reactjs`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I quick question!
I have a webapp that only fetches data from an API so i dont have i backend part. My question is where do you keep your API key? Accordingly to React docs you should not store API keys in the app so how do you manage this when you have an webapp that consumes an API and you have to use the API key in your get requests?


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-07-31 21:58:00

------------

Let's do a bit of explanation, so you connect the dots and design it more robustly. There are two (three) places where to store it in the end:

1. frontend (your React application)
2. backend (your server)
3. third-party service


 use non-frontend solution + rate limiting to a registered user and have the registration step secured properly (+ captcha).

---



1. Frontend


Storing anything on the frontend side is mostly a bad idea, unless you're completely sure you can allow such data to be exposed - constants, names, icons, maybe some URLs so you don't have it hardcoded in the JS files.
Your "compiled" ReactJS (or any other framework) when built is just a slightly mangled (minified/transpiled/etc etc) JavaScript, but for that to work the client has to retrieve it and have it executed in the browser. Therefore before the ReactJS application even starts, there are 1+ requests downloading the JavaScript code and other parts of the application depending on the framework.
Those you can see in the network monitoring tab in any modern browser or simply use Wireshark (unless encrypted, then it's a little bit annoying) or a local proxy if using a less sane browser.
After retrieval you can simply try + or any online deminifier/deobfuscator if you don't know how to do it yourself and you can retrieve the key.

### Implications when retrieved


- - - - `while (true) { callYourApi() }`
Depending on the API key and how serious you intend to approach this problem, you might utilize the `.env` file for development purposes only. Though you should never ever store an API key in the frontend unless you explicitly have to store it in there (e.g. maps) because it's mostly a very stupid idea and allows anyone to misuse it.

1. Backend


Your server, if properly configured and secured, will store the key anywhere which isn't accessible by simply path traversing (if in a file) or scraping (if you attempt to retrieve the key to execute on the frontend part).
Therefore the most sane and secure way would be to retrieve the data (of any service) by having either a custom API or a scheduled script collecting the data, which when your frontend gets called will be able to retrieve as pre-rendered or already fetched, thus no key needed for that case.
However! There's a trick to that. If you design your custom API as `/api/<key>=123` or `/api/<param>` and you use that parameter for the original API to filter on frontend, the attacker couldn't care less for the API key because you've already created an API for free and made it public and unsecure.
So `GET /yourapi/<my data>` and API key for free without even needing to have one displayed.
How to do it safely? Two simple approaches:

### pre-rendering data to HTML


You then fetch with frontend and just display - but this one can be scraped, it's just a bit annoying if more complex, but that's it. Server-side rendering sounds nice, but doesn't really work for this case. It should be mostly used to make the frontend fast or to template the views, but never for security purposes as the silver bullet solution (because it doesn't work that way).

### rate limiting + CORS + account management


with rate limiting you make sure that a user (preferably you have that API called only after a user is logged in) can call that API only e.g. 10 times within 1 hour and with CORS you make sure it's callable only by your frontend.
It's not a silver bullet either, anybody with a little bit of brain can simply scrape your API locally thus go around CORS, but the rate limit will still hit hard, if you forbid registering more than 1 user from a single IP or if you require a phone number for verification. And add some annoying captcha, so it's problematic to automate for some people.
Still it can be attacked and misused, but it's  unless you allow the same phone number (or any other ID less comfortable to get / requiring effort to get) to be used multiple times, so it'll make the most incompetent people go away... and the remaining ones, well, they'd play with your website anyway, so have a proper security assessment / harden your server if you maintain it alone.

1. Third-party


It's like 2., but you don't maintain the "low-level" server part, because the third-party is then managing it for you, you just need to specify conditions under which it'll be called. This applies to [Firebase](https://firebase.google.com/) or [Supabase](https://supabase.io/) which kind of behaves like a separate backend, but can have multiple modules (for FB, [1](https://stackoverflow.com/q/50741936/5994041), [2](https://stackoverflow.com/q/24830079/5994041)).
Thus you'd use [Firebase functions](https://firebase.google.com/docs/functions/) (or other alternatives), where you'd have your key e.g. even hardcoded and the client (browser) wouldn't have any access to that, add a limit, cors, perhaps some user registration limit and you're kind of done.

---


Note: Any domain, IP, region, phone number restrictions , so do not rely on them. It's just a mean to require effort when misusing your website for something different than you intended.
- `curl http(s)://yourweb/path -H "Host: spoofed-domain"`- - 


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-07-31 20:14:44

------------

It's more of a recommendation for them to keep your API keys server-side, and let your web app communicate with your server. Otherwise malicious users could easily steal your API key and use it for whatever.
If you think it isn't much of a security risk if your key gets (scratch that, ) compromised, that's fine then, you can just keep it in your webapp. Really depends on your use case and what that API key is for.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-07-31 22:29:20

------------

The only way to do this without exposing your API keys in your client app is to create a backend and serve the client app from the backend app as stated by Kelvin Schoofs and Peter Badida answers above (or use a third party service such as AWS Credential Vault). I suggest you use Node Express library for a backend as this will handle a lot of the boiler plate code for you. There are plenty of tutorials online for this.
Using a dotenv file as suggested by a few other users will only hide your API code from version sharing tools like Git (because you can ignore the dotenv in gitignore). It is very important that you understand the process of dotenv with a react app. Any user who opens the Dev console in their browser can view your exposed API keys in the static HTML.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-07-31 21:29:07

------------

Create a dotenv file and store all secret and API keys. Make sure to use  before every variable.
DOCS: [https://create-react-app.dev/docs/adding-custom-environment-variables/](https://create-react-app.dev/docs/adding-custom-environment-variables/)
dotenv package: [https://www.npmjs.com/package/dotenv](https://www.npmjs.com/package/dotenv)


------------
    
    