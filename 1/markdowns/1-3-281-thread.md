
# Post \#71231719 [Link](https://stackoverflow.com/questions/71231719/)

## I am trying to compare the letters in two words however duplicate letters is causing the program to not work as intended [Python]

**Vote**: 1 (672/702) **Views**: 1364 (632/702) 

**Internal ID** \#1-3-281

Created at 2022-02-23 05:21:18

Tags: `python` `wordle-game`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am trying to build a wordle solver as a self project however I got stuck in a small part of my code. The method that checks for each letter in both the choice word and the guess work and color codes them is working perfectly unless there is a word with a duplicate letter (eg: the word SHARE would work correctly however the word GUESS would only color code GUES). I tried using counter but how exactly would I exactly input the letter at the right place in the list?
```
def wordcolor(self, choice, guess):
    guesscolor = dict()
    choiceword = []
    guesslist = []
    guessdict = dict()
    choicedict = dict()
    for a in choice:
        choiceword.append(a)
    for b in guess:
        guesslist.append(b)

    for idx, value in enumerate(choiceword):
        choicedict[idx] = value

    for idx, value in enumerate(guesslist):
        guessdict[idx] = value

    guesscolor[0] = guesslist[0]
    guesscolor[1] = guesslist[1]
    guesscolor[2] = guesslist[2]
    guesscolor[3] = guesslist[3]
    guesscolor[4] = guesslist[4]

    counter = 0

    lengthofword = len(guesslist)

    totalsame = 0

    sameword = 0

    countsguess = Counter(guessh)
    duplicatesguess = [c for c in countsguess if countsguess[c] > 1]
    countschoice = Counter(choice)
    duplicateschoices = [c for c in countschoice if countschoice[c] > 1]

    for a in duplicateschoices:
        if a == None:
            duplicateschoices.append("None")
    for a in duplicatesguess:
        if a == None:
            duplicatesguess.append("None")

    while (counter < lengthofword):
        if guessdict[counter] in choicedict.values():

            totalsame += 1

            if choiceword[counter] == guesslist[counter]:
                print(f"The word at index {counter} is present in both strings in same place")
                sameword += 1
                a = guessdict[counter]
                guesscolor[a] = "green"



            else:
                a = guessdict[counter]
                guesscolor[a] = "orange"
            counter += 1


        else:
            a = guessdict[counter]
            guesscolor[a] = "red"
            counter += 1

    print(guesscolor)

    print("There are " + str(totalsame) + " duplicate characters." + str(sameword) + " of them are in the same place")


Game(0).wordcolor(choice, guessh)
```

Example output:
```
Computer choice word: eject 
Human Guess: ejjet (I haven't made the code to check guesses against dictionary) 
The word at index 0 is present in both strings in same place
The word at index 1 is present in both strings in same place
The word at index 4 is present in both strings in same place
{0: 'e', 1: 'j', 2: 'j', 3: 'e', 4: 't', 'e': 'orange', 'j': 'orange', 't': 'green'} (Ignores duplicate letters) 
There are 5 duplicate characters. 3 of them are in the same place
Incorrect Guess
```


```
Computer choice word: rotas 
Human Guess: share  
0: 's', 1: 'h', 2: 'a', 3: 'r', 4: 'e', 's': 'orange', 'h': 'red', 'a': 'orange', 'r': 'orange', 'e': 'red'}
There are 3 duplicate characters.0 of them are in the same place
Incorrect Guess
```



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-02-23 06:43:02

------------

Your current code might be working for no-duplicate-letter situations, but it's
not working from a code simplicity perspective. You're doing too much
complicated work.
As is often the case, [smarter data leads to simpler code](https://users.ece.utexas.edu/%7Eadnan/pike.html). To reframe this
algorithm as a data question, notice that there are two logical tests: (1) does
the guessed letter exactly match the word's corresponding letter, and (2) is the
guessed letter in the word. Here are the possible results:
```
LETTER_COLORS = {
    (True, True): 'green',
    (False, True): 'orange',
    (False, False): 'red',
}
```

And the code to use that data:
```
def get_letter_colors(word, guess):
    return {
        g : LETTER_COLORS[g == word[i], g in word]
        for i, g in enumerate(guess)
    }
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-02-23 06:22:50

------------

If I understand your question correctly, you're looking for a way to distinguish superfluous letters in 'guess' (which should be labeled as incorrect and red) from letters in 'guess' which do correspond to letters in 'choice' (which should be either green or orange, depending on whether they are in the correct place).  By 'superfluous', meaning:  the letter in 'guess' also occurs in 'choice', but it occurs more times in 'guess' than in 'choice'.
Here is an example of one way you could possibly resolve this special case, with comments included. I tested it on your example case of 'eject' versus 'ejjet' and it correctly labels the first 'j' as green and the second as red.
```
def compareTwoFiveLetterWords(actual, guess):
    # There are three possible conditions for every letter in the guess word:
    # Correct letter, correct position (green)
    # Correct letter, incorrect position (orange)
    # Incorrect letter (red)
    letterColors = []
    
    actualLetterCounts = {}
    for letter in actual:
        if letter not in actualLetterCounts:
            actualLetterCounts[letter] = 1
        else:
            actualLetterCounts[letter] += 1

    guessLetterCounts = {}        
    for letter in guess:
        if letter not in guessLetterCounts:
            guessLetterCounts[letter] = 1
        else:
            guessLetterCounts[letter] += 1

    for letterIndex in range(0, 5):
        if guess[letterIndex] == actual[letterIndex]:
            letterColors.append((guess[letterIndex], 'green'))
        # Only this one condition can lead to the duplicate problem.
        # So, instead of just checking whether the letter is in the word,
        # we have to check how many of it are in the guess versus how many
        # of it are in the actual word.  If it's in the wrong place and the
        # count is higher in guess than actual, it is a wrong letter:
        # color it red.  Otherwise, it's correct but misplaced:  color it orange.
        elif guess[letterIndex] in actual:
            if guessLetterCounts[guess[letterIndex]] > actualLetterCounts[guess[letterIndex]]:
                letterColors.append((guess[letterIndex], 'red'))
                # Decrement the count once the superfluous letter has been labeled incorrect
                guessLetterCounts[guess[letterIndex]] -= 1
            else:
                letterColors.append((guess[letterIndex], 'orange'))
        else:
            letterColors.append((guess[letterIndex], 'red'))

    return letterColors
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-02-23 06:47:29

------------

Every time you check a letter it is being added to the `guesscolor` dictionary. The problem with this is it will overwrite the value if one already exists. Thus if there is a duplicate letter, only the last duplicate will be saved. I would suggest the following code instead:
```
while counter < lengthofword:

    if guessdict[counter] in choicedict.values():

        totalsame += 1

        if choiceword[counter] == guesslist[counter]:
            print(f"The word at index {counter} is present in both strings in same place")
            sameword += 1

            guesscolor[counter]["color"] = "green"
        else:
            guesscolor[counter]["color"] = "orange"

        counter += 1
    else:
        guesscolor[counter]["color"] = "red"
        counter += 1
```

So instead of adding the letters with the index as the key and also with the letter as the key, a better way is to assign a dictionary as the value to the already existing index keys.
This gives you the output of:
```
{0: {'letter': 'e', 'color': 'green'}, 1: {'letter': 'j', 'color': 'green'}, 2: {'letter': 'j', 'color': 'orange'}, 3: {'letter': 'e', 'color': 'orange'}, 4: {'letter': 't', 'color': 'green'}}
```

Also at the top in order to make this work you need to change it from:
```
for idx, value in enumerate(guesslist): 
     guessdict[idx] = value
    
guesscolor[0] = guesslist[0]
guesscolor[1] = guesslist[1]
guesscolor[2] = guesslist[2]
guesscolor[3] = guesslist[3]
guesscolor[4] = guesslist[4]
```

To this instead:
```
for idx, value in enumerate(guesslist):
     guessdict[idx] = value
     guesscolor[idx] = {"letter": guesslist[idx], "color": ""}
```

Then later if you want to print a list of all the color values, you can do a for loop like this:
```
for idx in guesscolor:
    print(guesscolor[idx]["color"])
```

One final suggestion: Please replace all your dictionary default assignments in the beginning from `dict()` to `{}`. Using the brackets is the pythonic correct way. Also maybe change the variable `guesslist` to `guessword` to make the code more readable.


------------
    
    