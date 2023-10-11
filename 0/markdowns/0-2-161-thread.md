
# Post \#66199371 [Link](https://stackoverflow.com/questions/66199371/)

## How to change primary color for Prime NG

**Vote**: 4 (532/702) **Views**: 8395 (451/702) 

**Internal ID** \#0-2-161

Created at 2021-02-14 19:39:16

Tags: `css` `angular` `primeng`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

How can I change default primary color for primeNG (saga-blue theme) ? changing --primary-color doesn't help, because in node_modules/..../theme.css elements are styled using the main color hex and not ' --primary-color '.
I also can't override styling for all elements that use that primary color because they are too many + they have different shades of --primary-color on hover and on focus, how can I handle this ?


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2021-02-15 07:43:22

------------

If you want to change the primary color to green, orange, or purple, you can change your theme to one of those (saga-green, saga-orange...)
If you want more customization, you can use [their theme designer](https://www.primefaces.org/designer/primeng).


------------
    
    
## Answer \#1

 Vote: 5

Created at 2022-03-25 13:14:21

------------

I used part of [Ε Г И І И О](https://stackoverflow.com/users/687190/%ce%95-%d0%93-%d0%98-%d0%86-%d0%98-%d0%9e) solution, not sure if it could break something in future yet it's working fine.
My solution:

1. (same) Make a copy of the base theme.css from primeng (Find it from node_modules/primeng/resources/themes/{your_theme}/theme.css)
2. (same) Change all colors, etc as per your liking. (Hint: use find -> replace)
3. Save changed themes.css to any folder, like src/app/styles/theme.css (Hint: you can upgrade to scss and use color variables for reuseable and more readable code)
4. Inside angular.json under "styles" array replace imported node_modules/primeng/resources/themes/{your_theme}/theme.css to your path, like src/app/styles/theme.css
5. Enjoy, no cpx or node-modules hand changes required, so you can faster move code on another computer!




------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-09-21 17:43:24

------------

Here's a hacky workaround.

1. Make a copy of the base theme.css from primeng (Find it from node_modules/primeng/resources/themes/{your_theme}/theme.css)
2. Change all colors, etc as per your liking. (Hint: use find -> replace)
3. Save this file in an assets folder. (You can keep it outside of your src folder)
4. Install cpx (https://www.npmjs.com/package/cpx). It's a file copier. npm install cpx --save-dev
5. Add the following scripts to your package.json. "copyCss": "cpx \"assets/theme.css\"\"node_modules/primeng/resources/themes/{your_theme}/\"" "ngBuild": "npm run copyCss && ng build"
6. Use npm run ngBuild to build your app. (Use any other build switches as desired)
7. Voila! Enjoy your customized styles :)




------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-05-03 15:34:33

------------

There is another hard way of doing this,
Like everyone else suggested, copy any theme.css to a file, after that follow below step to convert to scss,
 - Using below link, extract all the css colors, not having the part of css variables.
[http://www.css-color-extractor.com/](http://www.css-color-extractor.com/)
Now, from step-1, we have list of colors to be replaced with css variable
 - Use any tool to convert css to SCSS,
for example - [https://codebeautify.org/css-to-scss-converter](https://codebeautify.org/css-to-scss-converter)
Copy the , to a new theme.scss file.
-> define a css variable/ scss variable for each colors in step 1,
Do a  with all css variable / scss variable for every color found in step-1 (if does not exist).


------------
    
    