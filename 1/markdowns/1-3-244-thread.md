
# Post \#70519979 [Link](https://stackoverflow.com/questions/70519979/)

## printing with RGB background

**Vote**: 2 (631/702) **Views**: 958 (662/702) 

**Internal ID** \#1-3-244

Created at 2021-12-29 13:17:18

Tags: `python` `python-3.x` `printing`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I know its possible to print RGB colored text like so:
```
def colored(r, g, b, text):
    return "\033[38;2;{};{};{}m{} \033[39m".format(r, g, b, text)


text = "Hello, World"
colored_text = colored(132, 204, 247, text)
print(colored_text)
```

But, is there a way to print with RGB colored ?
Cuz, as far as I know, there are a few kinda built-in background colors for printing. But I want to be able to use rgb codes and get the appropriate background color for the printed text.
Is this possible?
Thanks.


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-12-29 14:15:21

------------

According to
[https://chrisyeh96.github.io/2020/03/28/terminal-colors.html#ansi-escape-codes-for-terminal-graphics](https://chrisyeh96.github.io/2020/03/28/terminal-colors.html#ansi-escape-codes-for-terminal-graphics)
the  parameter following the `'\033['` (the
) to be used to define the background color
is `48;2;r;g;b`.
So here is `colored_background` that returns the text parameter with
the given background color:
```
def colored_background(r, g, b, text):
    return f'\033[48;2;{r};{g};{b}m{text}\033[0m'

text = "What a nice red background!"
colored_text = colored_background(255, 0, 0, text)
print(colored_text)
```

You can naturally combine both:
```
def colored(fg_color, bg_color, text):
    r, g, b = fg_color
    result = f'\033[38;2;{r};{g};{b}m{text}'
    r, g, b = bg_color
    result = f'\033[48;2;{r};{g};{b}m{result}\033[0m'
    return result

text = "What a nice blue text with a red background!"
colored_text = colored((0, 0, 255), (255, 0, 0), text)
print(colored_text)
```

Note that here you only need one escape sequence with parameter 0 as this
one resets both the foreground and the background colors to their
default.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-12-29 13:30:55

------------

I suggest using  module, it helps you to style apps running on command line.
[Tech with Tim on YouTube](https://www.youtube.com/watch?v=Db4oc8qc9RU) has a tutorial for that. you'll find all what you need for coloring texts and backgrounds.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-12-29 13:58:12

------------

this will do:
```
"\u001b[48;2;{};{};{}m{} \u001b[0m".format(r, g, b, text)
```

A helpful recourse I found:
[https://www.lihaoyi.com/post/BuildyourownCommandLinewithANSIescapecodes.html#background-colors](https://www.lihaoyi.com/post/BuildyourownCommandLinewithANSIescapecodes.html#background-colors)


------------
    
    