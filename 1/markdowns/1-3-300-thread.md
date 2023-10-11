
# Post \#54760850 [Link](https://stackoverflow.com/questions/54760850/)

## Replace all newline characters using python

**Vote**: 2 (631/702) **Views**: 48914 (167/702) 

**Internal ID** \#1-3-300

Created at 2019-02-19 07:30:54

Tags: `python` `python-3.x`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am trying to read a pdf using python and the content has many newline (crlf) characters. I tried removing them using below code:

```
from tika import parser

filename = 'myfile.pdf'
raw = parser.from_file(filename)
content = raw['content']
content = content.replace("\r\n", "")
print(content)
```


But the output remains unchanged. I tried using double backslashes also which didn't fix the issue. can someone please advise?


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2019-02-20 01:44:08

------------

I don't have access to your pdf file, so I processed one on my system.  I also don't know if you need to remove all new lines or just double new lines.  The code below remove double new lines, which makes the output more readable.

Please let me know if this works for your current needs.

```
from tika import parser

filename = 'myfile.pdf'

# Parse the PDF
parsedPDF = parser.from_file(filename)

# Extract the text content from the parsed PDF
pdf = parsedPDF["content"]

# Convert double newlines into single newlines
pdf = pdf.replace('\n\n', '\n')

#####################################
# Do something with the PDF
#####################################
print (pdf)
```



------------
    
    
## Answer \#1

 Vote: 10

Created at 2019-02-19 07:34:26

------------

```
content = content.replace("\\r\\n", "")
```


You need to double escape them.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-03-24 01:34:45

------------

If you are having issues with different forms of line break, try the `str.splitlines()` function and then re-join the result using the string you're after. Like this:
```
content = "".join(l for l in content.splitlines() if l)
```

Then, you just have to change the value within the quotes to what you need to join on.
This will allow you to detect all of the line boundaries found [here](https://docs.python.org/3/library/stdtypes.html#str.splitlines).
Be aware though that `str.splitlines()` returns a list not an iterator. So, for large strings, this will blow out your memory usage.
In those cases, you are better off using the file stream or `io.StringIO` and read line by line.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-03-19 03:41:37

------------

```
print(open('myfile.txt').read().replace('\n', ''))
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-08-08 16:47:58

------------

When you write something like `t.replace("\r\n", "")` python will look for a carriage-return followed by a new-line.
Python will not replace carriage returns by themselves or replace new-line characters by themselves.
Consider the following:
```
t = "abc abracadabra abc"
t.replace("abc", "x")
```

- Will `t.replace("abc", "x")` replace every occurrence of the letter `a` with the letter `x`? - Will `t.replace("abc", "x")` replace every occurrence of the letter `b` with the letter `x`? - Will `t.replace("abc", "x")` replace every occurrence of the letter `c` with the letter `x`? 
What will `t.replace("abc", "x")` do?
> `t.replace("abc", "x")` will replace the entire string `"abc"` with the letter `"x"`
Consider the following:
```
test_input = "\r\nAPPLE\rORANGE\nKIWI\n\rPOMEGRANATE\r\nCHERRY\r\nSTRAWBERRY"

t = test_input
for _ in range(0, 3):
    t = t.replace("\r\n", "")
    print(repr(t))

result2 = "".join(test_input.split("\r\n"))
print(repr(result2))
```

The output sent to the console is as follows:
```
'APPLE\rORANGE\nKIWI\n\rPOMEGRANATECHERRYSTRAWBERRY'
'APPLE\rORANGE\nKIWI\n\rPOMEGRANATECHERRYSTRAWBERRY'
'APPLE\rORANGE\nKIWI\n\rPOMEGRANATECHERRYSTRAWBERRY'
'APPLE\rORANGE\nKIWI\n\rPOMEGRANATECHERRYSTRAWBERRY'
```

Note that:
- `str.replace()`- `str.replace()`
If you want to delete all new-line and carriage returns, something like the following will get the job done:
```
in_string = "\r\n-APPLE-\r-ORANGE-\n-KIWI-\n\r-POMEGRANATE-\r\n-CHERRY-\r\n-STRAWBERRY-"

out_string = "".join(filter(lambda ch: ch not in "\n\r", in_string))

print(repr(out_string))
# prints -APPLE--ORANGE--KIWI--POMEGRANATE--CHERRY--STRAWBERRY-
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-11-19 15:32:54

------------

You can also just use
```
text = '''
As she said these words her foot slipped, and in another moment, splash! she
was up to her chin in salt water. Her first idea was that she had somehow
fallen into the sea, “and in that case I can go back by railway,”
she said to herself.”'''

text = ' '.join(text.splitlines())

print(text)
# As she said these words her foot slipped, and in another moment, splash! she was up to her chin in salt water. Her first idea was that she had somehow fallen into the sea, “and in that case I can go back by railway,” she said to herself.”
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-12-29 15:58:39

------------

```
#write a file 
enter code here
write_File=open("sample.txt","w")
write_File.write("line1\nline2\nline3\nline4\nline5\nline6\n")
write_File.close()

#open a file without new line of the characters
open_file=open("sample.txt","r")
open_new_File=open_file.read()
replace_string=open_new_File.replace("\n",." ")
print(replace_string,end=" ")
open_file.close()
```


# OUTPUT


```
line1 line2 line3 line4 line5 line6
```



------------
    
    