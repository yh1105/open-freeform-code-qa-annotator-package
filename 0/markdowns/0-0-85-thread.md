
# Post \#70227076 [Link](https://stackoverflow.com/questions/70227076/)

## How can I set a primary color in Chakra UI theme?

**Vote**: 2 (631/702) **Views**: 5756 (505/702) 

**Internal ID** \#0-0-85

Created at 2021-12-04 15:43:16

Tags: `reactjs` `themes` `chakra-ui`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I want to set the primary and secondary color in my theme.js/theme.ts. Is there any way to do that?
I am used to work with Material UI components in my React projects.
It is possible to set a palette with 'primary' and 'secondary' there.
I mean something like this:
```
export const theme = createTheme({
  palette: {
    primary: {
      main: "#7bb9e8"
    },
    secondary: {
      main: "#eb7f7f"
    }
  }
})
```



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-12-30 17:23:26

------------

As soon as you have defined all color's shades, you can use your custom color as a component's colorSheme:
```
const theme = extendTheme({
   colors: {
     primary: {
      main: "#7bb9e8",
      50: "#e3f2fd",
      100: "#bbdefb",
      200: "#90caf9",
      300: "#64b5f6",
      400: "#42a5f5",
      500: "#2196f3",
      600: "#1e88e5",
      700: "#1976d2",
      800: "#1565c0",
      900: "#0d47a1"
    }
  }
});
```

And your component :
```
<Button colorScheme="primary">Button</Button>
```

[Demo](https://codesandbox.io/s/colors-forked-k9u13?file=/src/App.js:141-187)


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-03-27 13:52:53

------------

According to this section: [https://chakra-ui.com/docs/styled-system/theming/customize-theme#using-theme-extensions](https://chakra-ui.com/docs/styled-system/theming/customize-theme#using-theme-extensions)
You don't have to pass `color='primary'` everywhere.
Just create custom theme and then set primary as default color.
This example shows how to set Cyan as default color for every component.
```
const theme = extendTheme(
  {
    colors: {
      primary: baseTheme.colors.cyan
    },
    breakpoints,
  },
  withDefaultColorScheme({
    colorScheme: 'primary'
  }),
);
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-12-04 17:19:10

------------

You can  [customize the theme](https://chakra-ui.com/docs/theming/customize-theme) in any possible way :
```
import { extendTheme } from "@chakra-ui/react";

const theme = extendTheme({
  colors: {
    primary: {
      main: "#7bb9e8"
    },
    secondary: {
      main: "#eb7f7f"
    }
  }
});
```

Then pass this theme to `ChakraProvider`
```
//index.js

    <ChakraProvider theme={theme}>
      <App />
    </ChakraProvider>
```

And you will be able to use your own colors accessing the theme via useTheme hook:
```
import { useTheme } from "@chakra-ui/react";
...
  const theme = useTheme();
...
<Button bg={theme.colors.primary.main}>Button</Button>
```

or override a component styles globally [inside the component object](https://chakra-ui.com/docs/theming/customize-theme#customizing-single-components)
[Demo](https://codesandbox.io/s/colors-mov1q?file=/src/App.js:144-171)


------------
    
    
## Answer \#3

 Vote: -1

Created at 2022-05-21 09:14:19

------------

You can set two variants of primary colors with `default` and `_dark` property. It will be used depending on actual color mode (light/dark theme).
```
import { extendTheme } from '@chakra-ui/react'

const theme = extendTheme({
  initialColorMode: 'light',
  semanticTokens: {
    colors: {
      text: {
        default: '#16161D',
        _dark: '#ade3b8',
      },
      primary: {
        default: '#FF0080',
        _dark: '#fbec8f',
      },
})

export default theme
```



------------
    
    