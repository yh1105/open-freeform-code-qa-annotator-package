
# Post \#62810567 [Link](https://stackoverflow.com/questions/62810567/)

## Difference between Pressable and TouchableOpacity

**Vote**: 41 (153/702) **Views**: 24532 (266/702) 

**Internal ID** \#0-0-4

Created at 2020-07-09 08:27:08

Tags: `javascript` `reactjs` `react-native`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

With the update `react native` to version `0.63`, new components have appeared. Can someone more experienced explain how the `Pressable` differs from the `TouchableOpacity` and when it is better to use them.


----------
        
## Answer \#0

**Accepted** Vote: 46

Created at 2020-07-09 08:36:22

------------

Pressable was a new introduction to RN 0.63, prior to that,Touchable Opacity was the most common used Component to wrap a  component or simliar components.
Both their basic functionalities are same, to make a text/image clickable and user interactive.
But with `Pressable` you get to access a lot new props like :
`HitRect`, which is such a cool feature, acc to docs :
> Fingers are not the most precise instruments, and it is common for
users to accidentally activate the wrong element or miss the
activation area. To help, `Pressable` has an optional `HitRect` you can
use to define how far a touch can register away from the the wrapped
element. Presses can start anywhere within a `HitRect`.
This is clearly a better alternative to what we used for `hitslop` , here its more precise and you define the area.And it doesnt interfere with the child/other components Z-index too.
So basically you get all the feature of a button, touchableOpacity with cool new props. Do check out thier docs : [rn-pressable](https://reactnative.dev/docs/pressable)
: Also as other comments in this thread suggests, Pressable still doesnt have an   animation attached with the onPress Event
Hopeit helps.feel free for doubts


------------
    
    
## Answer \#1

 Vote: 29

Created at 2021-08-30 01:48:04

------------

Just wanted to add a note about one drawback of `Pressable` and a workaround.
The drawback is `Pressable` has no automatic feedback like its TouchableOpacity counterpart.
However, you can add your own custom feedback with Pressable's `style` prop, which comes with a pressed state identifier:
```
<Pressable
  style={({ pressed }) => [
    { opacity: pressed ? 0.5 : 1.0 }
  ]}
  onPress={() => console.log('Pressed')}
>
 <View><Text>Press Me</Text></View>
</Pressable>
```



------------
    
    
## Answer \#2

 Vote: 14

Created at 2020-07-09 11:30:53

------------

[](https://i.stack.imgur.com/Exbsz.png) Here is a picture to clear your doubt.
How it works
On an element wrapped by
> Pressable
:
onPressIn is called when a press is activated.
onPressOut is called when the press gesture is deactivated.
After pressing onPressIn, one of two things will happen:

1. The person will remove their finger, triggering onPressOut followed by onPress.
2. If the person leaves their finger longer than 370 milliseconds before removing it, onLongPress is triggered. (onPressOut will still fire when they remove their finger.)


Please refer to [documentation](https://reactnative.dev/docs/next/pressable) for more details.


------------
    
    