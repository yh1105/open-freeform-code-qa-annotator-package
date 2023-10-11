
# Post \#70486515 [Link](https://stackoverflow.com/questions/70486515/)

## how to prevent iis default not-found page and show a custom reactjs not-found page

**Vote**: 0 (696/702) **Views**: 1112 (652/702) 

**Internal ID** \#0-0-33

Created at 2021-12-26 12:40:32

Tags: `reactjs` `iis` `react-router` `configuration` `react-router-dom`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

so I have a react app which is deployed on IIS, I'm using react router and my routes and the order of them are OK, everything works fine locally in development mode,
my routes.js file returns routes like this :
```
<Router history={history}>
    <Switch>
      {/* there are some other routes that are removed from here */}
      {/* token is the jwt token for user */}
      {!token && <Route path="/Home" component={HomePage} />}

      <Route path="/" exact>
        {!!token ? <Redirect to="/Dashboard" /> : <Redirect to="/Home" />}
      </Route>
      <Route path="*" exact component={FourOFour} />
    </Switch>
  </Router>
```

I have a jsx file for my "not-found page", which returns the component ,
as I said everything including the not-found page shows completely fine in the development mode on my machine. but when I deploy my react app on IIS, my custom not-found page won't show, instead the default 404 page of the IIS is shown, how do I make the ISS stop messing with my routes?
PS: I know it's the servers job to handle the errors such as 404 and it's a default behavior, and I also have tried some suggestions I've found on the web but some of them was ruining everything and some was not clear that what was happening,
so I was hoping to get a clear explanation of the steps for configs of IIS,
any suggestion is appreciated

## Update



first I handled error 404 in my router in client-side, it was not working because iis has default html pages for common errors such as 404, 500, ... and it kinda catches the errors before the request even reaches the client side, so I figured out it's an issue handled by the server not the client side, then I tried different configs in iis, because iis has an "Error Pages" section for each site and in this section you can change the way it treats such errors, for each type of error (actually each status code) it has three options:

1. insert content from static file into the error response


which you can give a path to a static html page and it will show up, but as I said above, my not-found page is written in a jsx file, and it has a looooot of java-script code in it, it's not impossible to put everything in a html page and use this way but it would get my code dirty and I prefer a way that is not getting my code messy, plus I tried this way just to see if it works but it show this error:
"The resource you are looking for has been removed, had its name changed, or is temporarily unavailable."

1. execute a url in this site


it could be the best solution for me and I could set a "/404" path in my routes that redirects to my not-found page, and everything would look nice and clean, but sadly it didn't work, looks like iis was not redirect to my specific url and instead shows a white screen, which is worse than it's default 404 page...

1. response with a 302 redirect


which is not the solution for me because I don't want to redirect to a page outside of my app
I also read [this answer](https://stackoverflow.com/a/36623117/10220474) very carefully but still have no clue what to do


----------
        
## Answer \#0

**Accepted** Vote: 0

Created at 2022-01-12 05:39:31

------------

since I couldn't find a working solution in the answers, I did it this way:
first I chose the second option in `ErrorPages > 404 > Edit Custom Error Page` menu, which is `execute a URL on this site`:
[](https://i.stack.imgur.com/c1p4v.png)
and I gave it the `/` value which is the relative path to the root of my react-app.
In this way, whenever an error occurs iis will call the react-app and give the control to it, for example if the path to the `Dashboard` is /dashboard, and the user has a typo and have /dahboard (missing the s in the middle) or any other problem, the `not found` component will be rendered, and interestingly the URL in the browser will stay the same (for example /dahboard) and won't change to /


------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-12-27 06:38:20

------------

Since you mentioned that everything worked fine locally in development mode, there could be something wrong with the settings in IIS. The following steps are worth trying: (1) Open the IIS manager (2) Identify the app you are working on (3) Open “Error Pages” in the home pane (4) Using Add (if you don’t have the 404 status code) or Edit (if you already have a 404 status code) in Actions pane (5) Enter the 404 underneath “Status Code” (6) for the response action, select “Execute a URL on this site” and enter the URL for the custom error page. NOTE: Please make sure that you are using the relative path.
In addition, the version of IIS could lead to some difference in practice. If you still have any problems, please feel free to contact us, and more details could help us better solving your case.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-12-30 21:35:29

------------

This is how your `web.config` should look like, see below.
The critical portion is the [rewrite rule](https://learn.microsoft.com/en-us/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module#viewing-the-rewrite-rule-in-configuration-file) named `ReactRouter Routes` which essentially delegates the routing to the react router i.e. client side routing by "pointing" to index.html so react router can take over.
You'll notice a `<match url=".*">` which matches the pattern for the request URL using reg ex with the pattern .* so all urls sent by the browser are caught & thrown to the client so to speak.
Make sure you have [URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) installed on IIS.
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="ReactRouter Routes" stopProcessing="true">
          <match url=".*" />
          <conditions logicalGrouping="MatchAll">
            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
            <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
          </conditions>
          <action type="Rewrite" url="/index.html" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-12-26 15:07:05

------------

make NotFound page and Route code first.
```
<Route path="/notfound" component={NotFound} />
```

And then type some redirect code if an error occurs while Main component render.(Main component is just an example)
```
export const Main = () => {
const history = useHistory();

useEffect(() => {
    if (error) {
       history.push("/notfound")
    }
})

render ....
}
```

I hope you find a solution!
Btw, React-Router is now v6 you should know it :)


------------
    
    