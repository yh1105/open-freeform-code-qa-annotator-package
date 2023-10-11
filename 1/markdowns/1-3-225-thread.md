
# Post \#60982439 [Link](https://stackoverflow.com/questions/60982439/)

## Pig latin string conversion in python

**Vote**: 2 (631/702) **Views**: 34130 (210/702) 

**Internal ID** \#1-3-225

Created at 2020-04-02 00:28:14

Tags: `python` `function` `for-loop`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm trying to create a function that turns text into pig Latin: simple text transformation that modifies each word moving the first character to the end and appending "ay" to the end. But all I get is an empty list. Any tips?
```
def pig_latin(text):
  say = ""
  words = text.split()
  for word in words:
    endString = str(word[1]).upper()+str(word[2:])
    them = endString, str(word[0:1]).lower(), 'ay'
    word = ''.join(them)
    return word

print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay ythonpay siay unfay"
```



----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2020-04-02 00:43:41

------------

```
def pig_latin(text):
  words = text.split()
  pigged_text = []

  for word in words:
    word = word[1:] + word[0] + 'ay'
    pigged_text.append(word)

  return ' '.join(pigged_text)

print(pig_latin("hello how are you"))
```


Outputs: `ellohay owhay reaay ouyay`


------------
    
    
## Answer \#1

 Vote: 1

Created at 2020-07-30 11:14:41

------------


```
def pig_latin(text):
  say = []

  # Separate the text into words

  words = text.split()
  for word in words:

    # Create the pig latin word and add it to the list

    word = word[1:] + word[0] + "ay"
    say.append(word)

    # Turn the list back into a phrase

  return " ".join(say)

print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"

print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay ythonpay siay unfay"
```




------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-08-25 06:51:49

------------

Simply use a list comprehension for compactness and simplicity:
`’ ‘.join([word[1:] + word[0] + ‘ay’ for word in text.split(‘ ‘)])`


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-02-04 19:17:43

------------

```
def pig_latin(text):
  say = []
  # Separate the text into words
  words = text.split(" ")
  for word in words:
    # Create the pig latin word and add it to the list
    say.append(word[1:]+word[0]+'ay')
    # Turn the list back into a phrase
  return " ".join(x for x in say)
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-04-02 00:34:45

------------

Works for me

```
def pig_latin(sentence):
    output = []
    for word in sentence.split(" "):
        word = word[1:] + word[0] + "ay"
        output.append(word)
    return " ".join(output)
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-04-02 00:34:58

------------

Just use a list comprehension on each word then join it back together.

edit:  this solution have issues with multiple spaces, like when people put two spaces after a `colon: !`

```
def dowork(sentence):

    def pigword(word):
        return "".join([word[1:], word[0], "ay"])

    return " ".join([pigword(word) for word in sentence.split()])


dataexp = [
    ("hello how are you","ellohay owhay reaay ouyay"),
    ("programming in python is fun","rogrammingpay niay ythonpay siay unfay")
    ]

for inp, exp in dataexp:
    got = dowork(inp)
    msg = "exp :%s:  for %s \n      got :%s:" % (exp, inp, got)
    if exp == got:
        print("good! %s" % msg)
    else:
        print("bad ! %s" % msg)
```



### output



```
good! exp :ellohay owhay reaay ouyay:  for hello how are you
      got :ellohay owhay reaay ouyay:
good! exp :rogrammingpay niay ythonpay siay unfay:  for programming in python is fun
      got :rogrammingpay niay ythonpay siay unfay:
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2020-07-24 21:36:45

------------

Here is a solution that worked in my case:
```
def pig_latin(text):
      # Separate the text into words
        words = text.split()
        #creating a new empty list called pig
        pig=[]
        # creating a for loop to alter every word in words
        for word in words:
            
            """here we are assigning the altered word to a new variable pigged_word. The we can remove first word alone from the original word usin
                indexing and add/concat the letter at zeroth index (1st letter) back to the
                    word and add/concat new string 'ay' at end """
            
            pigged_word=word[1:]+word[0]+'ay'
            #append the altered words to the list
            # use append instead of insert since you can add word to end of list
            pig.append(pigged_word)
            #now convert it back to 
        return (' '.join(pig))
            
    print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"
    print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay ythonpay siay unfay"
```

Kindly try to understand the question and solve. Splitting the say variable won't work , ideally you'll have to split the text into pieces and even then you don't have to split with `" "`


------------
    
    
## Answer \#7

 Vote: 0

Created at 2020-07-30 11:50:16

------------

Based on Herman Singh's answer, but omitting the anti-pattern (might be a bit strong to call it that) of creating an empty list and appending to it in a for-loop.
```
def pig_latin(text):
    words = [
        word[1:] + word[0] + "ay"
        for word in text.split()
    ]
    return " ".join(words)


print(pig_latin("hello how are you"))  # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun"))  # Should be "rogrammingpay n
```

Which can be reduced to a single line, but I think it doesn't aid readability.
```
def pig_latin(text):
    return " ".join([word[1:] + word[0] + "ay" for word in text.split()])


print(pig_latin("hello how are you"))  # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun"))  # Should be "rogrammingpay siay unfay
```



------------
    
    
## Answer \#8

 Vote: 0

Created at 2020-08-16 18:01:15

------------

```
def pig_latin(text):
  # Separate the text into words
  words = text.split()
  newWord = []
  for word in words:
    # Create the pig latin word and add it to the list
    n = ""
    for char in range(len(word)-1, -1, -1):
      if(char == 0) :
        n+= (word[char]+"ay")
      n += word[char]
    newWord.append(n)    
    # Turn the list back into a phrase
  return " ".join(newWord)
    
print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay ythonpay siay unfay"
```



------------
    
    
## Answer \#9

 Vote: 0

Created at 2020-09-04 21:24:05

------------

def pig_latin(text):
```
say = ""
  pig_list= []
  # Separate the text into words
  words = text.split(' ')
  for word in words:
    # Create the pig latin word and add it to the list
    word = word[1:] + word[0] + "ay"
    # print(word)
    pig_list.append(word)
    # Turn the list back into a phrase
    say = ' '.join(pig_list)
  return say
```



------------
    
    
## Answer \#10

 Vote: 0

Created at 2020-09-18 13:16:08

------------

Solution without `.join` and `.append`:
```
def pig_latin(text):
  say = ""
  words = text.split()
  for word in words:
    pig_word = word[1:] + word[0] + "ay "
    say += pig_word
  return say
        
print(pig_latin("hello how are you")) # "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun")) # "rogrammingpay niay ythonpay siay unfay"
```



------------
    
    
## Answer \#11

 Vote: 0

Created at 2021-02-17 07:00:19

------------

```
def pig_latin(text):
  say = ""
  # Separate the text into words
  words = text.split()
  for word in words:
    # Create the pig latin word and add it to the list
    say += word[1:] + word[0] + "ay "
    # Turn the list back into a phrase
  return say.rstrip(" ")

print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay ythonpay siay unfay"
```



------------
    
    
## Answer \#12

 Vote: 0

Created at 2021-04-21 17:36:12

------------

```
def pig_latin(text):
    say = ""
    str=[]
  # Separate the text into words
    words = text.split()
   for word in words:
     # Create the pig latin word and add it to the list
      say=word[1:]+word[:1]+"ay"
      str.append(say)
      # Turn the list back into a phrase
      joined=" ".join(str)
      return joined
    
print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay 
ythonpay siay unfay"
```



------------
    
    
## Answer \#13

 Vote: 0

Created at 2021-04-24 12:43:04

------------

```
def pig_latin(text):
  say = ""
  # Separate the text into words
  words = text.split()
  for word in words:
    # Create the pig latin word and add it to the list
    say = say + word[1:] +word[0] +"ay "
    # Turn the list back into a phrase
  return "".join(say)

print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay ythonpay siay unfay"
```



------------
    
    
## Answer \#14

 Vote: 0

Created at 2021-12-20 18:27:38

------------

```
def pig_latin(text):
  say = " "
  # Separate the text into words
  words = text.split()
  for word in words:
    # Create the pig latin word and add it to the   list
    say+=word[1:]+word[0]+str("ay")+ " "
    # Turn the list back into a phrase
  return say
    
print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay ythonpay siay unfay"
```



------------
    
    
## Answer \#15

 Vote: 0

Created at 2022-05-27 20:23:26

------------

```
def pig_latin(text):
  say = ""
  words = text.split()
  for word in words:
    word = str(word[1])+str(word[2:] + str(word[0:1]).lower() + 'ay')
    say = say + word + " "
  return say

print(pig_latin("hello how are you")) 
# Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun"))
 # Should be "rogrammingpay niay ythonpay siay unfay"
```



------------
    
    
## Answer \#16

 Vote: 0

Created at 2022-06-07 02:12:08

------------

```
def pig_latin(text):
    say = "ay"
    # Separate the text into words
    words = text.split()
    newtext = []
    # Create the pig latin word and add it to the list
    for newword in words:
        newlist = newword[1:] + newword[0] + say
        newtext.append(newlist)
    return " ".join(newtext)
```



------------
    
    
## Answer \#17

 Vote: 0

Created at 2022-08-14 18:40:58

------------

```
def pig_latin(text):
  say = ""
  # Separate the text into words
  words = text.split()
  last = words[-1]
  for word in words:
    if word == last:
        say += word[1:]+word[0]+"ay"
    else:
         say += word[1:]+word[0]+"ay "
  return say

print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay ythonpay siay unfay"
```

Will get the output without that extra space at end of sentence.


------------
    
    
## Answer \#18

 Vote: 0

Created at 2022-11-14 14:00:58

------------

This is better work I think so as it is simple to understand
```
def pig_latin(text):
  # Separate the text into words
  words = text.split()
  list1 = []
  for word in words:
    # Create the pig latin word and add it to the list
    pig_latin = word[0] + "ay"
    mod_word = word.replace(word[0],"")
    mod_word2 = mod_word + pig_latin
    list1.append(mod_word2)
    # Turn the list back into a phrase
  return " ".join(list1)
        
print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay ythonpay siay unfay"
```



------------
    
    
## Answer \#19

 Vote: -1

Created at 2020-08-25 06:34:46

------------

```
def pig_latin(text):
  say = ""
  words = text.split()
  for word in words:
    word=word[1:] + word[0] + "ay" + " "
    say +=word
  return say
        
print(pig_latin("hello how are you"))
```



------------
    
    
## Answer \#20

 Vote: -1

Created at 2021-02-12 00:37:28

------------

```
def pig_latin(text):
  say = ""
  # Separate the text into words
  words = text.split()
  for word in words:
    # Create the pig latin word and add it to the list
    say += " {}{}ay".format(word[1:], word[0])
    # Turn the list back into a phrase
  return say
    
print(pig_latin("hello how are you")) # Should be "ellohay owhay reaay ouyay"
print(pig_latin("programming in python is fun")) # Should be "rogrammingpay niay ythonpay siay unfay"
```



------------
    
    
## Answer \#21

 Vote: -1

Created at 2021-12-04 02:55:22

------------

Using the following code:
```
def pig_latin(texts):
    return ' '.join([text.replace(text[0],'') + text[0]+'ay' for text in texts.split()])

print(pig_latin("hello how are you"))
print(pig_latin("programming in python is fun"))
```

will get the desired result:
```
ellohay owhay reaay ouyay
rogrammingpay niay ythonpay siay unfay
```



------------
    
    