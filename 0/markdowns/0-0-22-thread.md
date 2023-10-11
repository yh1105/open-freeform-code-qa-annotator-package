
# Post \#60267273 [Link](https://stackoverflow.com/questions/60267273/)

## React Navigation V5 Hide Bottom Tabs

**Vote**: 15 (318/702) **Views**: 26138 (254/702) 

**Internal ID** \#0-0-22

Created at 2020-02-17 17:03:09

Tags: `javascript` `react-native` `react-navigation` `react-native-navigation`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I would like to be able to hide the tabs on a screen using React Native Navigation v5.

I've been reading the documentation but it doesn't seem like they've updated this for v5 and it refers to the < v4 way of doing things.

here is my code:

```
import Home from './components/Home';
import SettingsScreen from './components/Settings';
import * as React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createStackNavigator } from '@react-navigation/stack';

const SettingsStack = createStackNavigator();
const ProfileStack  = createStackNavigator();

function SettingsStackScreen() {
    return (
        <SettingsStack.Navigator>
            <SettingsStack.Screen name="Settings" component={SettingsScreen} />
        </SettingsStack.Navigator>
    )
}

function ProfileStackScreen() {
    return (
        <ProfileStack.Navigator>
            <ProfileStack.Screen name="Home" component={Home} />
        </ProfileStack.Navigator>
    )
}

const Tab = createBottomTabNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator>
        <Tab.Screen name="Home" component={ProfileStackScreen} />
        <Tab.Screen name="Settings" component={SettingsStackScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}
```


Things I have tried:


1. Accessing the options of the function and hiding that way.
2. Passing tabBarVisible as a prop to the Screen.



What I am asking for is, what is the correct way of hiding tabs on screens in React Navigation v5.


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2020-02-17 20:45:46

------------

Let's suppose that you want to hide tabs when you are entering Settings. Just add navigation in your constructor:
```
function SettingsStackScreen({ navigation }) {
    navigation.setOptions({ tabBarVisible: false })
    return (
        <SettingsStack.Navigator>
            <SettingsStack.Screen name="Settings" component={SettingsScreen} />
        </SettingsStack.Navigator>
    )
}
```

This code should work.


------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-11-15 11:21:44

------------

[tabbarvisible-option-is-no-longer-present](https://reactnavigation.org/docs/upgrading-from-5.x/#the-tabbarvisible-option-is-no-longer-present) in react navigation v5 upwards. You can achieve the same behavior by specifying
`tabBarStyle: { display: 'none' }` in options of the screen you want to hide bottom tab
```
export default function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator>
        <Tab.Screen name="Home" component={ProfileStackScreen} />
        <Tab.Screen options={{tabBarStyle:{display:'none'}}} name="Settings" component={SettingsStackScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-06-18 10:17:37

------------

You will have to restructure your navigation by having your Tab Navigator nested in the Stack Navigator. Following the details here [hiding-tabbar-in-screens](https://reactnavigation.org/docs/hiding-tabbar-in-screens)

This way it's still also possible to have a Stack Navigator nested in yourTab Navigator. `SettingsStack`

With this when the user is on the Setting screen and also the Update detail screen, the tab bar is visible but on the Profile screen, the tab bar is not.

```
import Home from './components/Home';
import Settings from './components/Settings';
import UpdateDetails from './components/UpdateDetails';
import Profile from './components/Profile';

import * as React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();
const StackSettings = createStackNavigator();
const Tab = createBottomTabNavigator();

function SettingsStack() {
    return (
        <StackSettings.Navigator>
            <StackSettings.Screen name="Settings" component={Settings} />
            <StackSettings.Screen name="UpdateDetails" component={UpdateDetails} />
        </StackSettings.Navigator>
    )
}

function HomeTabs() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Home" component={Home} />
      <Tab.Screen name="Settings" component={SettingsStack} />
    </Tab.Navigator>
  );
}


export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Home" component={HomeTabs} />
        <Stack.Screen name="Profile" component={Profile} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-04-28 10:07:21

------------

I had this issue and couldn't find solution even in official docs ( the issues in github resulted to broken links) after some trials and research I found a solution for me  

```
<Tab.Navigator tabBarOptions={stackOptions} >
  <Tab.Screen
    name={"market"}
    component={MarketNavigator}
    options={navigation => ({
      // tabBarIcon: ,
      tabBarVisible: navigation.route.state.index > 0 ? false : true
    })}
  />
</Tab.Navigator>
```


Hope it helps someone! 


------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-06-28 13:46:49

------------


# 2022 Answer - How to hide Bottom Tabs in React Navigation V6



Sometimes we may want to hide the tab bar in specific screens in a native stack navigator nested in a tab navigator. Let's say we have 5 screens: `Home`, `Feed`, `Notifications`, `Profile` and `Settings`, and your navigation structure looks like this:
```
function HomeStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Home" component={Home} />
      <Stack.Screen name="Profile" component={Profile} />
      <Stack.Screen name="Settings" component={Settings} />
    </Stack.Navigator>
  );
}

function App() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Home" component={HomeStack} />
      <Tab.Screen name="Feed" component={Feed} />
      <Tab.Screen name="Notifications" component={Notifications} />
    </Tab.Navigator>
  );
}
```

With this structure, when we navigate to the `Profile` or `Settings` screen, the tab bar will still stay visible over those screens.

Now if we want to show the tab bar only on the `Home`, `Feed` and `Notifications` screens, but not on the `Profile` and `Settings` screens, we'll need to change the navigation structure. The way to achieve this is to nest the  as the first route of the stack.
Move your Tabs into a separate function ...
Other routes should be in the main App.js return function...
Make  the  as seen below under the App() return function...

```
function BottomTabs() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Home" component={Home} />
      <Tab.Screen name="Feed" component={Feed} />
      <Tab.Screen name="Notifications" component={Notifications} />
    </Tab.Navigator>
  );
}

function App() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="BottomTabs" component={BottomTabs} />
      <Stack.Screen name="Profile" component={Profile} />
      <Stack.Screen name="Settings" component={Settings} />
    </Stack.Navigator>
  );
}
```

After re-organizing the navigation structure, now if you navigate to the `Profile` or `Settings` screens, the bottom tab bar won't be visible over the screen anymore.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-02-17 21:48:58

------------

You have API reference exactly for this.
Read: [tabBarVisible](https://reactnavigation.org/docs/en/bottom-tab-navigator.html#tabbarbutton)


------------
    
    
## Answer \#6

 Vote: 0

Created at 2020-02-19 15:48:48

------------

The above answer will help you to remove the bottom tabs from the root navigation.If you want to remove bottom tabs from a specific screen like Home Screen or Settings Screen you need to change navigation options dynamically.

For changing navigation options dynamically you will need the concept of:

- - 

 - will dynamically change the navigationOption value i.e. either to hide the bottom Tabs or not. We can choose MobX or Redux to do the same.

 - will help context to know at which screen the user is.

We need to create Context in a separate .js file so that Home.js and Settings.js can access it in all the other screens. 

```
import * as React from 'react';
import { View, Text } from 'react-native'
import { NavigationContainer, useNavigationState, useRoute } from '@react-navigation/native';
const Tab = createBottomTabNavigator();
const Context = React.createContext();

import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createStackNavigator } from '@react-navigation/stack';
import { TouchableOpacity } from 'react-native-gesture-handler';


const SettingsStack = createStackNavigator();
const ProfileStack = createStackNavigator();

function SettingsScreen({ navigation }) {
  return (
    <View>
      <Text>
        Setting
      </Text>
    </View>
  );
}

function Home({ navigation }) {
  const rout = useNavigationState(state => state);
  const { screen, setScreen } = React.useContext(Context);
  setScreen(rout.index);
  return (
    <View>
      <TouchableOpacity
        onPress={() => {
          navigation.navigate("Settings");
        }}
      >
        <Text>
          Home
        </Text>
      </TouchableOpacity>
    </View>
  );
}

function SettingsStackScreen({ navigation }) {
  return (
    <SettingsStack.Navigator>
      <SettingsStack.Screen name="Settings" component={SettingsScreen} />
    </SettingsStack.Navigator>
  )
}

function ProfileStackScreen({ navigation }) {
  const { screen, setScreen } = React.useContext(Context)
  if (screen == 0) {
    navigation.setOptions({ tabBarVisible: true })
  } else {
    navigation.setOptions({ tabBarVisible: false })
  }
  return (
    <ProfileStack.Navigator>
      <ProfileStack.Screen name="Home" component={Home} />
      <ProfileStack.Screen name="Settings" component={SettingsScreen} />
    </ProfileStack.Navigator>
  )
}

function BottomNav({ navigation }) {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Home" component={ProfileStackScreen} />
      <Tab.Screen name="Settings" component={SettingsStackScreen} />
    </Tab.Navigator>
  );
}


export default function App() {
  const [screen, setScreen] = React.useState(0);

  return (
    <Context.Provider value={{ screen, setScreen }}>
      <NavigationContainer>
        <BottomNav />
      </NavigationContainer>
    </Context.Provider>
  );
}
```


Here the screen is a flag that checks the index of the navigation and removes the bottom navigation for all the screen stacked in ProfileStackScreen.


------------
    
    
## Answer \#7

 Vote: 0

Created at 2020-03-21 14:39:12

------------

Use You Looking for Nested Screen Visible then Tab Bar Options Should be hide than Use this Simple Condition in StackNavigator Funtions.

```
function HistoryStack({navigation, route}) {
if (route.state.index === 0) {
 navigation.setOptions({tabBarVisible: true});
 } else {
 navigation.setOptions({tabBarVisible: false});
}
return (
<Historys.Navigator initialRouteName={Routes.History}>
  <Historys.Screen
    name={Routes.History}
    component={History}
    options={{headerShown: false}}
  />
  <Historys.Screen
    name={Routes.HistoryDetails}
    component={HistoryDetails}
    options={{headerShown: false}}
  />
</Historys.Navigator>
  );
}
```



------------
    
    
## Answer \#8

 Vote: 0

Created at 2020-10-21 18:01:22

------------

```
import { createBottomTabNavigator } from "@react-navigation/bottom-tabs"; // version 5.6.1
import { createStackNavigator } from "@react-navigation/stack"; // version 5.6.2
```

From my inspection navigation.routes.state.index will have a value when you navigation/push to a second screen so I create a function
```
const shouldTabBarVisible = (navigation) => {
  try {
    return navigation.route.state.index < 1;
  } catch (e) {
    return true;
  }
};
```

and call it in `BottomTab.Screen` options
```
<BottomTab.Navigator
    initialRouteName='Home'
    tabBarOptions={{
        activeTintColor: "#1F2B64",
        showLabel: false,
    }}
>
    <BottomTab.Screen
        name='Home'
        component={HomeNavigator}
        options={(navigation) => ({
            tabBarIcon: ({ color }) => <TabBarIcon name='home' color={color} />,
            tabBarVisible: shouldTabBarVisible(navigation),
        })}
    />
</BottomTab.Navigator>
```



------------
    
    
## Answer \#9

 Vote: -1

Created at 2020-08-22 19:04:40

------------

just follow as what the documentation suggests: [https://reactnavigation.org/docs/hiding-tabbar-in-screens/](https://reactnavigation.org/docs/hiding-tabbar-in-screens/)


------------
    
    