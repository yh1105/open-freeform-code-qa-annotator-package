
# Post \#64019647 [Link](https://stackoverflow.com/questions/64019647/)

## Why is an empty else-if statement bad style, and how should I rewrite it?

**Vote**: 72 (90/702) **Views**: 11746 (399/702) 

**Internal ID** \#2-5-340

Created at 2020-09-23 01:13:31

Tags: `java` `if-statement`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

The program that automatically grades my code is docking me "style-points" for an else-if that doesn't execute any code. It says that it may cause an error, but I don't think it could.
I'm not sure how to change it so that it still works but doesn't break the rule. Why is doing this bad form? I think any other way I write it will be harder for a reader to understand. How should it be written instead?
```
if (! seesWater(LEFT))
{
    turn(LEFT);
}
else if (! seesWater(AHEAD));
else if (! seesWater(RIGHT))
{
    turn(RIGHT);
}
else
{
    turn180();
}
```

The reason the else-if is there but does nothing is because of the priority in which I want the code to act:
`if (! seesWater(AHEAD))`, then I don't want the rest of the conditions to run at all because they don't matter.


----------
        
## Answer \#0

**Accepted** Vote: 143

Created at 2020-09-23 01:41:56

------------

Who says it's "bad style"?
The relevant question to ask is, is this clearer than alternatives?  In your specific case, I'd say it is.  The code clearly expresses a choice between 4 options, of which one is "do nothing".
The only change I'd make is to replace that rather insignificant semicolon by an empty pair of braces, possibly with a comment to make it clear it's not a mistake.
```
if (! seesWater(LEFT)) {
    turn(LEFT);
}
else if (! seesWater(AHEAD)) {
    // nothing required
}
else if (! seesWater(RIGHT)) {
    turn(RIGHT);
}
else {
    turn180();
}
```

This is not endorsing 'empty clauses' as a generally-acceptable style; merely that cases should be argued on their merits, not on the basis of some Rule That Must Be Obeyed.  It is a matter of developing good taste, and the judgement of taste is for humans, not mindless automata.


------------
    
    
## Answer \#1

 Vote: 45

Created at 2020-09-23 02:18:10

------------

If this is the logic you want, there is nothing wrong with your code. In terms of code styling wise, I agree with the other users. Something like this would be clearer in my opinion:
```
if (! seesWater(LEFT))
{
    turn(LEFT);
}
else if (! seesWater(AHEAD))
{
    //Do nothing
}
else if (! seesWater(RIGHT))
{
    turn(RIGHT);
}
else
{
    turn180();
}
```

But by having priority of making a left turn over moving ahead (by doing nothing), the movement may end up in circles:
[](https://i.stack.imgur.com/Ba0Q9.png)
If you want the movement to "do nothing" but avoid moving into waters like this:
[](https://i.stack.imgur.com/FRlLr.png)
You may want to change your logic to something like this:
```
if (! seesWater(AHEAD))
{
    //Do nothing. Keep moving
}
else if (! seesWater(LEFT))
{
    turn(LEFT);
}
else if (! seesWater(RIGHT))
{
    turn(RIGHT);
}
else
{
    turn180();
}
```



------------
    
    
## Answer \#2

 Vote: 26

Created at 2020-09-23 01:23:02

------------

You can invert the `! seesWater(AHEAD)` condition. Then you can move the code in its `else` clause to its `if` clause:
```
if (! seesWater(LEFT))
{
    turn(LEFT);
}
else if (seesWater(AHEAD)) 
{
    if (! seesWater(RIGHT))
    {
        turn(RIGHT);
    }
    else
    {
        turn180();
    }
}
```



------------
    
    
## Answer \#3

 Vote: 13

Created at 2020-09-23 01:33:37

------------

I would argue that you can decide to not change the logic of your code at all.  I think there's no reason to avoid an empty `if` or `else if` block if that leads to your code being the most readable and the logic most understandable.  You can often rewrite your code to not include an empty code block and yet have it be just as understandable.  But if not, I say this is fine.
One thing though...I don't like the style of using a `;` to denote an empty code block.  That's really easy to miss and is not normally seen, so it can confuse the reader.  I'd suggest replacing the `;` with an empty set of curly braces.  You could also put a comment inside the empty curlies to make it clear you mean for that code block to be empty, ie `// In this case, we don't want to do anything`.


------------
    
    
## Answer \#4

 Vote: 13

Created at 2020-09-23 16:48:06

------------

For this particular code sample, I don't believe the logic is accurate and does not flow intuitively.  It looks like you want to turn if there is water ahead...but that left turn is in there that makes it confusing and potentially a bug.  Ultimately I see the empty logic as a double negative, 'if there is no water ahead, do nothing', and double negatives are also frowned upon.
```
if (seesWater(AHEAD))
{
    if (! seesWater(LEFT))
    {
        turn(LEFT);
    }
    else if (! seesWater(RIGHT))
    {
        turn(RIGHT);
    }
    else
    {
        turn180();
    }
}
```

This makes the most sense since the additional logic is based on whether there is water ahead.  It also makes it easier if you want to move the actions to another function if there is water ahead.


------------
    
    
## Answer \#5

 Vote: 13

Created at 2020-09-23 21:11:10

------------

I find the negative logic in the `if(!condition)` statements hard to read and mind-twisting, even more so in combination with if-else-else-else. I would prefer a positive logic like `seesLand()` rather than `!seesWater`.
You could modify the turn() function, so that `turn(AHEAD)` does nothing and `turn(BACK)` does the 180 turn instead of needing a separate function for that.
Then you could rewrite this into.
```
List<Direction> directions = new ArrayList(LEFT, FORWARD, RIGHT, BACK);
for (Direction d: directions) {
    if(seesLand(d) {
        turn(d);
        return;
}
```

One upside is that this generalises even more. You could create different movement strategies simply by defining another array where you order the directions as you wish, and feed them into this function to check and turn in the chosen order.


------------
    
    
## Answer \#6

 Vote: 10

Created at 2020-09-23 22:44:30

------------

What you do with something like this is you turn it into a speaking method and preferably split it up.
Option 1:
```
private turnTowardsGround(){
  if(! seesWater(AHEAD){
    return;
  }
  if (! seesWater(LEFT))
  {
     turn(LEFT);
     return;
  }
  if (! seesWater(RIGHT))
  {
    turn(RIGHT);
    return;
  }
  turn180();
}
```

Option 2:
```
private determineMoveDirection(){
  if(! seesWater(AHEAD){
    return AHEAD;
  }
  if (! seesWater(LEFT))
  {
     return LEFT
  }
  if (! seesWater(RIGHT))
  {
    return RIGHT
  }
  return BACK;
}
```

and then use Option 2 like:
```
Direction directionToMove = determineMoveDirection();
turn(directionToMove);
```

Why? Because the method names make the intend of the code clear. And the early return exits allow a quick read where we don't need to read the whole if-else code to make sure nothing else happens once one case applies. Plus in the option 2 case you have a clear separation of concerns (finding out where to move to vs actually turning), which is nice for modularity and in this case also allows one single place to log the final decision where you're going to turn, for example.
Also, preferably the checks would not be negated but there would be a "seesLand" or "seesAccessibleFieldType" method. It's psychologically easier/faster to parse non-negated statements and it makes it more clear what you are actually looking for (i.e. a land tile, a ice tile, ...).


------------
    
    
## Answer \#7

 Vote: 8

Created at 2020-09-24 16:25:38

------------

 Because it is a non-obvious way of 'exiting' the if block. It skips over subsequent else-ifs and more importantly skips the final else, which in a multi-condition block ends up as the 'default' action.
To me there are two major problems here:

1. The if-statement order matters, but it's unclear why this order is chosen
2. The else-statement appears to have logic, which is skipped by do_nothing rather than being a catch-all


The first question I'd ask looking at code like this is why is the do_nothing option `(! seesWater(AHEAD))` not the  thing we check? What makes `! seesWater(LEFT)` the first thing we check? I'd hope to see a comment as to why this order was important as the ifs don't seem to be obviously exclusive (could seeWater in multiple directions).
The second question I'd ask is in what cases we expect to end up in the catch-all `else` statement. At the moment `turn180()` is the 'default', but it doesn't feel like very default-y behaviour to me to go back the way you've come if looking around fails for some reason.


------------
    
    
## Answer \#8

 Vote: 7

Created at 2020-09-24 11:20:28

------------

I'm coming from C, and living with MISRA, but to me an "if" with a semicolon is bad style regardless of whether there is an "else". Reason is that people don't normally put a semicolon there unless it's a typo. Consider this:
```
if (! seesWater(AHEAD));
{
  stayDry();
}
```

Here someone reading the code will think that you only stayDry() if you don't see water ahead -- you have to look hard to see that it will actually be called unconditionally. Much better, if you don't want to do anything in this case, is curly brackets and a comment:
```
if (! seesWater(AHEAD))
{
  /* don't need to do anything here */
}
```

In fact putting the curly brackets around the body of the "if", "else" etc. should  be done, and good on you for using the curly brackets even when the clause is a single turn() call! I sometimes find code like
```
if (! seesWater(AHEAD))
  stayDry();
```

then someone will come along later and add something else:
```
if (! seesWater(AHEAD))
  stayDry();
  doSomethingElse();
```

and of course the Something Else is done whether you see water or not, which was not the second coder's intention. You may think no-one would make a mistake that is so obvious but they do.


------------
    
    
## Answer \#9

 Vote: 5

Created at 2020-09-23 06:51:55

------------

The reason why a style checker reports something as poor style is that the people who implemented the style checker considered it poor style.
You'd have to ask  why, and their answer may, or may not, have merit.
Automated checkers of anything that require human intelligence should be treated with caution. Sometimes they produce useful output, and sometimes they don't. It's very unfortunate if such a checker is assessing the work of a human being, and extremely unfortunate if that assessment has consequences.


------------
    
    
## Answer \#10

 Vote: 2

Created at 2020-09-23 01:16:53

------------

You don't do anything with the 2nd else if statement you should rewrite it like this
```
else if(!seesWater(AHEAD)){return }
```

This is so the program has a conditional operator to return a boolean statement hope this helps -SG


------------
    
    
## Answer \#11

 Vote: 0

Created at 2023-02-24 10:11:32

------------

For me it's a way to avoid forgetting a case later, especially if some of the options are different categories
```
// do nothing if ignoring
if(option=="red"){
    sound = "beep"
}else if(option=="blue"){
    sound = "beep"
}else if(option=="green"){
    sound = "long beep"
}else if(option=="ignore"){
    ; // do nothing
}
```

In that code case ignore is hard to forget however if you didn't have it, the outcome would be the same.
```
// do nothing if ignoring
if(option=="red"){
    sound = "beep"
}else if(option=="blue"){
    sound = "beep"
}else if(option=="green"){
    sound = "long beep"
}
```

then later if you decide that you want a the same beep for all colours so to simplify the code you could have
```
// do nothing if ignoring
sound = "beep"
```

which would work for the colors, but would also set sound to beep if ignoring, which we weren't doing previously. "But the comment", I hear you cry, well in longer code you might miss a comment and forget the ignore case because you might have been only thinking about the category of colours [of course automated testing can help you out]
So if I start from my first version it's harder to miss it, and I already have an if statement for the ignore case so I can rearrange
```
if(option=="ignore"){
    ; // do nothing
}else{
    sound = "beep"
}
```

Or
```
if(option!="ignore"){ sound = "beep" }
```



------------
    
    
## Answer \#12

 Vote: -1

Created at 2020-09-23 02:12:50

------------

Well we don't really know the context of the entire question. We don't know if this is the only code in a method. I'm not sure why you test for LEFT first. I would have thought you test for AHEAD first since I would think you continue in the same direction as the default if it is possible. If you keep going LEFT you will be walking in a circle. So given the lack of requirements it is hard to give a complete answer.
However, I would tend to agree that I don't like the empty if statement. If you want to keep walking AHEAD you need to indicate that somehow.
Other comments:

1. I would rather have a positive method name like noWater(...). Then the if statement becomes a positive test instead of a negative test.
2. Why do you have multiple forms of the method that you invoke: turn(LEFT), turn(RIGHT), and turn180()? Why is turn180() different? I would create a method that would accept a parameter for any direction.


Using the above suggestions you would have something like:
```
if (noWater(FORWARD))
    turn(0);
else if (noWater(LEFT))
    turn(270);
else if (noWater(RIGHT))
    turn(90);
else
    turn(180);
```

With a structure like this you don't have an empty if statement and the movement becomes parameterized (and explicit) so you can have different values as required.


------------
    
    