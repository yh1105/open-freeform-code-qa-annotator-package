
# Post \#69547756 [Link](https://stackoverflow.com/questions/69547756/)

## MUI - makeStyles - Cannot read properties of undefined

**Vote**: 5 (499/702) **Views**: 10112 (414/702) 

**Internal ID** \#0-0-75

Created at 2021-10-12 22:40:05

Tags: `javascript` `reactjs` `material-ui` `jss`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm studying MUI, and in the course, the instructor asks me to style just one component and not the entire theme.
For that, it uses the  function and spreads the `theme.mixins.toolbar`. The problem is when I do this, I have the following error:
```
TypeError: Cannot read properties of undefined (reading 'toolbar')
```

This course is apparently in version 4, and I am using version 5. Despite this, my code appears to follow the changes that the documentations asks for. So what could be causing this error?

```
import "./App.css";
import Header from "./components/ui/Header";
import { ThemeProvider } from "@material-ui/core/styles";
import theme from "./components/ui/Theme";

function App() {
    return (
        <ThemeProvider theme={theme}>
            <Header />
        </ThemeProvider>
    );
}

export default App;
```


```
import { createTheme } from "@material-ui/core/styles";

const arcBlue = "#0B72B9";
const arcOrange = "#FFBA60";

export default createTheme({
    typography: {
        h3: {
            fontWeight: 100,
        },
    },
    palette: {
        common: {
            blue: `${arcBlue}`,
            orange: `${arcOrange}`,
        },
        primary: {
            main: `${arcBlue}`,
        },
        secondary: {
            main: `${arcOrange}`,
        },
    },
});
```


```
import React from "react";
import AppBar from "@mui/material/AppBar";
import Toolbar from "@mui/material/Toolbar";
import useScrollTrigger from "@mui/material/useScrollTrigger";
import Typography from "@mui/material/Typography";
import { makeStyles } from "@material-ui/styles";

function ElevationScroll(props) {
    const { children, window } = props;
    const trigger = useScrollTrigger({
        disableHysteresis: true,
        threshold: 0,
        target: window ? window() : undefined,
    });

    return React.cloneElement(children, {
        elevation: trigger ? 10 : 0,
    });
}

const useStyles = makeStyles((theme) => ({
    toolbarMargin: { ...theme.mixins.toolbar },
}));

function Header() {
    const classes = useStyles();
    return (
        <React.Fragment>
            <ElevationScroll>
                <AppBar color="primary">
                    <Toolbar>
                        <Typography variant="h3" component="h3">
                            Nome de teste
                        </Typography>
                    </Toolbar>
                </AppBar>
            </ElevationScroll>
            <div className={classes.toolBarMargin} />
        </React.Fragment>
    );
}

export default Header;
```



----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-10-13 05:17:24

------------

Since you're using v5, change your `ThemeProvider`, `createTheme` and `makeStyles` import path from:
```
import { ThemeProvider, createTheme, makeStyles } from "@material-ui/core/styles";
```

To:
```
import { ThemeProvider, createTheme } from "@mui/material/styles";
import { makeStyles } from "@mui/styles";
```

`@material-ui/core` is v4 package and `@mui/material` is the v5 equivalent. The API from the 2 versions are not compatible. In v5, `makeStyles` is also moved to a legacy package called `@mui/styles`, if you are using MUI v5 in a new project, you should switch completely to `styled`/`sx` API as [recommended](https://mui.com/styles/basics/#main-content) by the MUI team.

## Related answers


- [Difference between @mui/material/styles and @mui/styles?](https://stackoverflow.com/q/69506133/9449426)- [Cannot use palette colors from MUI theme](https://stackoverflow.com/q/69399031/9449426)- [MUI createTheme is not properly passing theme to MUI components](https://stackoverflow.com/q/69419447/9449426)


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-10-13 04:06:09

------------

I created a project on CodeSandbox and it doesn't seem the problem in code.
I guess you need to check the version of package you installed in `package.json` file.
Here is the link to the CodeSandbox  project and you can see the console.log message on console tab.
[https://codesandbox.io/s/check-makestyle-eq67m?file=/src/components/ui/Header/index.js](https://codesandbox.io/s/check-makestyle-eq67m?file=/src/components/ui/Header/index.js)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-08-23 12:22:02

------------

I faced similar issue with `Can not read properties of undefined, reading refs` thrown by `makeStyles`. In my case it was mui v5 and the issue occurred after upgrading to React 18.
In my case it turned out I had some legacy `makeStyles(()=>createStyles)` wrapped in a manner:
```
const useStyles = (minWidth: number) =>
  makeStyles((theme: Theme) => ...
```

I had to change it to:
```
const useStyles =
  makeStyles((theme: Theme) => ...
```

Perhaps it will be useful for somebody else facing the issue.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-10-14 06:32:38

------------


Install @mui/styles
`npm install @mui/styles`
Change
`import { ThemeProvider, createTheme } from "@material-ui/core/styles";`
To
`import { ThemeProvider, createTheme } from "@mui/material/styles";`
And
`import { makeStyles } from "@material-ui/styles";`
To
`import { makeStyles } from "@mui/styles";`
And
`<div className={classes.toolBarMargin} />`
To
`<div className={classes.toolbarMargin} />`


------------
    
    