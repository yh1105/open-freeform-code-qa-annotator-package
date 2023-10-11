
# Post \#66935418 [Link](https://stackoverflow.com/questions/66935418/)

## Disable Jedi linting for Python in Visual Studio Code

**Vote**: 6 (473/702) **Views**: 4838 (526/702) 

**Internal ID** \#1-3-236

Created at 2021-04-03 20:05:02

Tags: `python` `visual-studio-code` `jedi`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have set my linter for Python to Pylint, but I still get error messages from Jedi. I even went to settings.json and added the line `"python.linting.jediEnabled": false`, but the line, though present, is greyed out. What more can I do?


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2021-04-12 16:41:28

------------

Go to `settings.json` and add the line `"python.languageServer": "None"`. Then restart VSCode.


------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-08-18 11:08:46

------------

The accepted answer removes all linting and intellisense-like features, rather than removing jedi per-se.
Note that [since this pull request](https://github.com/microsoft/vscode-python/pull/12226) Jedi is used as the default fallback if Pylance is not available, so errors from jedi may be arising suddenly because of a problem with Pylance.
For those of us who still want linting and intellisense who were perfectly happy with pylance and pylint, the docs [here](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance) say to set that as your language server like:
```
"python.languageServer": "Pylance"
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-04-22 14:56:21

------------

I had this same exact issue, and it was all of a sudden. It was working just fine when I had used it earlier. A couple of hours later when I had opened the same python project files I had used earlier, I got those builtins errors and Jedi syntax errors as I was typing my code (I couldn't even finish typing my code blocks and I'd still have this issue). Plus, I had this yellow lightbulb beside my code which has never been there before. I had even tried uninstalling and reinstalling everything but no luck. Then I finally found Sourya's answer which had helped me. After setting  "python.languageServer": "None" , it all works fine.


------------
    
    