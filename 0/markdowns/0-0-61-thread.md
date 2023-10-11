
# Post \#59145165 [Link](https://stackoverflow.com/questions/59145165/)

## Change root background color with Material-UI theme

**Vote**: 65 (99/702) **Views**: 88794 (94/702) 

**Internal ID** \#0-0-61

Created at 2019-12-02 19:02:11

Tags: `reactjs` `material-ui`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm trying something very simple: building two themes for a website using Material-UI themes:
A `light` theme and `dark` one, but it does not work well: the theme is on every Material-UI react element, but the root element on the html document keeps having the same default white background.
Of course it can be changed by attacking the body with pure .css:
```
body {
  background-color: #222;
}
```

But I was looking to change it dynamically with React, I though this would work, but it does not:
```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import { ThemeProvider } from '@material-ui/styles';
import { MuiThemeProvider, createMuiTheme } from '@material-ui/core/styles';

const themeLight = createMuiTheme({
  palette: {
    background: {
      default: "#e4f0e2"
    }
  },
});

const themeDark = createMuiTheme({
  palette: {
    background: {
      default: "#222222",
    }
  },
});

ReactDOM.render(
  <MuiThemeProvider theme = { themeDark }>
    <App />
  </MuiThemeProvider>, document.getElementById('root'));
```

and I'm lost here, there is no way to make this with Material-UI theme?


----------
        
## Answer \#0

**Accepted** Vote: 123

Created at 2019-12-02 19:57:06

------------

[CssBaseline](https://github.com/mui-org/material-ui/blob/v4.7.1/packages/material-ui/src/CssBaseline/CssBaseline.js#L26) is the component that controls this aspect. If you aren't using `CssBaseline`, then you are just seeing the default provided by the browser.
Here is a working v4 example (v5 example further down):
```
import React from "react";
import ReactDOM from "react-dom";
import CssBaseline from "@material-ui/core/CssBaseline";
import { MuiThemeProvider, createMuiTheme } from "@material-ui/core/styles";
import Button from "@material-ui/core/Button";

const themeLight = createMuiTheme({
  palette: {
    background: {
      default: "#e4f0e2"
    }
  }
});

const themeDark = createMuiTheme({
  palette: {
    background: {
      default: "#222222"
    },
    text: {
      primary: "#ffffff"
    }
  }
});

const App = () => {
  const [light, setLight] = React.useState(true);
  return (
    <MuiThemeProvider theme={light ? themeLight : themeDark}>
      <CssBaseline />
      <Button onClick={() => setLight(prev => !prev)}>Toggle Theme</Button>
    </MuiThemeProvider>
  );
};

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

[](https://codesandbox.io/s/intelligent-snowflake-6y71c?fontsize=14&hidenavigation=1&theme=dark)

---


Below is a Material-UI v5 example. The only difference from v4 is the name change for `ThemeProvider` (though this name is also available in v4 in addition to `MuiThemeProvider`) and `createTheme` (instead of `createMuiTheme`) and using the new `@mui/material` package name instead of `@material-ui/core`.
```
import React from "react";
import ReactDOM from "react-dom";
import CssBaseline from "@mui/material/CssBaseline";
import { ThemeProvider, createTheme } from "@mui/material/styles";
import Button from "@mui/material/Button";

const themeLight = createTheme({
  palette: {
    background: {
      default: "#e4f0e2"
    }
  }
});

const themeDark = createTheme({
  palette: {
    background: {
      default: "#222222"
    },
    text: {
      primary: "#ffffff"
    }
  }
});

const App = () => {
  const [light, setLight] = React.useState(true);
  return (
    <ThemeProvider theme={light ? themeLight : themeDark}>
      <CssBaseline />
      <Button onClick={() => setLight((prev) => !prev)}>Toggle Theme</Button>
    </ThemeProvider>
  );
};

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

[](https://codesandbox.io/s/theme-body-background-ktqhf?fontsize=14&hidenavigation=1&theme=dark)


------------
    
    
## Answer \#1

 Vote: 7

Created at 2021-09-18 04:07:58

------------

In , you can also use `GlobalStyles` component to add the styles to the `body` element:
```
<GlobalStyles
  styles={{
    body: { backgroundColor: "lightyellow" }
  }}
/>
```

[](https://codesandbox.io/s/59145165-change-root-background-color-with-material-ui-theme-qgv7m?file=/demo.js)


------------
    
    
## Answer \#2

 Vote: 6

Created at 2021-11-09 21:48:33

------------

On top of @NearHuscarl 's answer, importing the GlobalStyles after the CSSBaseLine, will retain the page defaults (like margin: 0, etc.,) still able to customize root-level / global styles. For eg,
```
import { Component } from "react";
import { Button, CssBaseline, GlobalStyles } from "@mui/material";
import { ThemeProvider, createTheme } from "@mui/material/styles";

export class App extends Component {
  render() {
    const theme = createTheme({
      palette: {
        mode: "dark",
        primary: {
          main: "#ff0000",
          contrastText: "#fff",
        },
        secondary: {
          main: green[500],
        },
      },
    });
    return (
      <ThemeProvider theme={theme}>
        <CssBaseline />
        <GlobalStyles
          styles={{
            body: { backgroundColor: "cyan" },
          }}
        />
        <Button color="primary" variant="contained">
          Button
        </Button>
      </ThemeProvider>
    );
  }
}

export default App;
```

(I'm just using class component out of habit )
Full example with nested themes [MUI Theme toggle](https://github.com/MrSrv7/MUI-Theme-Toggle)


------------
    
    
## Answer \#3

 Vote: 3

Created at 2021-06-25 04:25:16

------------

My use case, I only wanted to change `background-color` of `body` from within a React component, not the entire theme. Used a global override.
TL;DR code:
```
// other imports ...
import { makeStyles } from "@material-ui/core/styles";

const useStyles = makeStyles((theme) => ({
  '@global':{
      body:{
        backgroundColor:"#382E7E"
      }
  },
  otherstyles:{
    //  other styles ....
  },

}));


// React component, etc ...
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-03-01 19:21:45

------------


### MUI Version 5


```
import { createTheme } from "@mui/material/styles";

const themeX = createMuiTheme({
  palette: {
    mode: "dark",
  }
});
```


### MUI Version 4


```
import { createMuiTheme } from '@material-ui/core/styles';

const themeX = createMuiTheme({
   palette: {
     type: "dark",
   }
 });
```

just as simple that, changing the pallets type to dark by default its set to light. This will also help in custom color for other components like typography, icon etc


------------
    
    
## Answer \#5

 Vote: 0

Created at 2019-12-02 19:33:27

------------

ReactDOM doesn't replace the targeted element. I haven't worked with material ui personally. However, if you put the background color you want into your App state as something like 'currentRootColor', then in your App component's render function you could put:

```
render() {
    document.body.style.backgroundColor = this.state.currentRootColor;

    ...the rest of your App render code
}
```


This would set the body's background color and if you change 'this.state.currentRootColor', then your App component would re-render with the new background color.

However if you dont already have a < body > tag in your document you would need to add one.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-07-12 15:05:41

------------

In MUI v5, this seem to work for me. I used it to apply specific styles only to HomePage (overwrite default styles).
```
pages/HomePage.js
```

...
```
import GlobalStyles from '@mui/material/GlobalStyles';
// or
import { GlobalStyles } from '@mui/material';
```

> Note: It is a good practice to hoist the `<GlobalStyles />` to a static
constant, to avoid rerendering. This will ensure that the  tag
generated would not recalculate on each render.
```
const homePageStyles = (
    <GlobalStyles
      styles={{
        body: { backgroundColor: 'cyan' },
        '.MuiTypography-root': {
          color: 'red',
        },
      }}
    />
  );
```

...
```
return (
    <>
      {homePageStyles}
      <MyComponents />
    </>
);
```

....
More:
[https://mui.com/material-ui/customization/how-to-customize/](https://mui.com/material-ui/customization/how-to-customize/)
[https://mui.com/material-ui/api/global-styles/](https://mui.com/material-ui/api/global-styles/)


------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-07-19 19:02:32

------------

All the above answers did not work for me, why?? I don't know.
I covered all of my components with `ScopedCssBaseline` and `mui` will apply the palette style only to the children.
Below is my answer,
```
import React from "react";
import ReactDOM from "react-dom";
import { ScopedCssBaseline, Button } from "@mui/material";
import { ThemeProvider, createTheme } from "@mui/material/styles";

const themeLight = createTheme({
  palette: {
    background: {
      default: "#fff"
    },
    text: {
      default: "#000"
    }
  }
});

const themeDark = createTheme({
  palette: {
    background: {
      default: "#000"
    },
    text: {
      primary: "#fff"
    }
  }
});

const App = () => {
  const [light, setLight] = React.useState(true);

  return (
    <ThemeProvider theme={light ? themeLight : themeDark}>
      <ScopedCssBaseline enableColorScheme>
        <Button onClick={() => setLight((prev) => !prev)}>Toggle Theme</Button>
      </ScopedCssBaseline>
    </ThemeProvider>
  );
};

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

[](https://codesandbox.io/s/theme-body-scoped-background-q1to6u?fontsize=14&hidenavigation=1&theme=dark)


------------
    
    