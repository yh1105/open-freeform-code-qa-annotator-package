
# Post \#69601082 [Link](https://stackoverflow.com/questions/69601082/)

## React Native - Change button color on focus

**Vote**: 6 (473/702) **Views**: 2856 (575/702) 

**Internal ID** \#0-0-104

Created at 2021-10-17 03:21:25

Tags: `android` `react-native`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm using the `Button` component from React Native and I need to change the color of the button when the button is focused with a TV remote (Amazon Fire Stick). I don't see any focus listeners available for the button. Is there anyway to achieve this functionality? Here is my code:
```
<Button disabled={props.loading} styles={styles.button} title="1" onPress={() => {props.addNumber(1)}}/>
```

I looked at the `TouchableHighlight` component but it won't work because I need the color to change when the user has focused on the button before the button is clicked. By default, the opacity change is so small that it's hard to tell the button is currently focused.


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-10-25 23:58:39

------------

from react-native [docs](https://reactnative.dev/docs/building-for-tv#build-changes) :
> When running on Android TV the Android framework will automatically
apply a directional navigation scheme based on the relative position
of focusable elements in your views. The Touchable mixin has code
added to detect focus changes and use existing methods to style the
components properly and initiate the proper actions when the view is
selected using the TV remote, so TouchableWithoutFeedback,
TouchableHighlight, TouchableOpacity, and TouchableNativeFeedback will
work as expected. In particular:```
onFocus will be executed when the touchable view goes into focus
onBlur will be executed when the touchable view goes out of focus
onPress will be executed when the touchable view is actually selected by pressing the "select" button on the TV remote.
```

so adding onfocus on TouchableOpacity should work for you the reason it is not you need some changes in AndroidManifest.xml to activate android tv mode
```
<!-- Add custom banner image to display as Android TV launcher icon -->
 <application
  ...
  android:banner="@drawable/tv_banner"
  >
    ...
    <intent-filter>
      ...
      <!-- Needed to properly create a launch intent when running on Android TV -->
      <category android:name="android.intent.category.LEANBACK_LAUNCHER"/>
    </intent-filter>
    ...
  </application>
```

also, you can use tvParallaxProperties and hasTVPreferredFocus to show the initial focus but it has limited effects
```
<TouchableHighlight
      hasTVPreferredFocus
      tvParallaxProperties={{ magnification: 1.2 }}
    >
      <Text>Button</Text>
    </TouchableHighlight>
```

[https://reactnative.dev/docs/touchableopacity#tvparallaxproperties-android](https://reactnative.dev/docs/touchableopacity#tvparallaxproperties-android)


------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-10-26 04:30:31

------------

you can have `onFocus` prop in `TouchableOpacity` or `TouchableHighlight`, but in order to work it on `TV` you need to import it from the specific package as mentioned [here](https://github.com/facebook/react-native/issues/31391#issuecomment-921619273). as mentioned there `react-native``react-native-tvos``react-native`.
[read this](https://reactnative.dev/blog/2020/03/26/version-0.62#moving-apple-tv-to-react-native-tvos)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-10-19 18:04:11

------------

Replace it for:
```
state = {
        buttonStyle: styles.button,
        [...]
      }
      [...]

      <TouchableWithoutFeedback disabled={props.loading} styles={this.state.buttonStyle} 
      onPress={() => {props.addNumber(1)}} 
      onFocus={() => {this.setState({buttonStyle: styles.focusedButton})}} >
          <Text>1</Text>
      </TouchableWithoutFeedback >
```

If you have all buttons in the same component, you will need to use the state value as array or object and define the key or index to access to the specific one.
[https://reactnative.dev/docs/touchablewithoutfeedback#onfocus](https://reactnative.dev/docs/touchablewithoutfeedback#onfocus)


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-10-26 04:59:42

------------

You can use the `Pressable` component, here's how you can do it:
```
<Pressable
  onPress={onPressFunction}
  style={({ focused }) => ({ backgroundColor: focused ? 'red' : 'blue' })}
>
  <Text>I'm pressable!</Text>
</Pressable>
```

You can read more about it [in the docs](https://reactnative.dev/docs/pressable)


------------
    
    