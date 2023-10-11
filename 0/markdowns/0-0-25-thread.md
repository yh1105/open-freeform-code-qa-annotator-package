
# Post \#61127168 [Link](https://stackoverflow.com/questions/61127168/)

## React Navigation v5: How to get route params of the parent navigator inside the child screen

**Vote**: 3 (575/702) **Views**: 10695 (409/702) 

**Internal ID** \#0-0-25

Created at 2020-04-09 17:49:32

Tags: `javascript` `reactjs` `react-native` `react-navigation` `react-navigation-v5`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

So I have nested navigators
Main BottomTab.Navigator
- - - - - - - - 
The problem is when I navigate from Profile View Screen to Following Navigator, I pass some parameters to the parent Following Navigator and I want all of those params in the children tabs Screens (Pages/Groups).
But the route of the children tab screens do not get the parameters which are passed to the parent navigator (Following Tab Navigator)
Is there a way to do that?
Here's my code:

```
const ProfileStack = () => (
  <Stack.Navigator
    initialRouteName='profileView'
  >
    <Stack.Screen
      name='profileView'
      component={ProfileScreen}
      options={{
        headerMode: 'screen',
        headerShown: false,
      }}
    />

    <Stack.Screen
      name='followers'
      component={FollowersScreen}
      options={{
        headerTitle: 'Followers',
      }}
    />
    <Stack.Screen
      name='following'
      component={FollowingTabs}
      options={{
        headerTitle: 'Following',
      }}
    />
 </Stack.Navigator>
```


```
const Tabs = createMaterialTopTabNavigator();
export const FollowingTabs = () => (
  <Tabs.Navigator
    initialRouteName='page'
    lazy
    swipeEnabled
  >
    <Tabs.Screen
      component={PageScreen}
      name='page'
      options={{ tabBarLabel: '2 Pages' }}
    />
    <Tabs.Screen
      component={GroupScreen}
      name='groups'
      options={{ tabBarLabel: '3 Groups' }}
    />
  </Tabs.Navigator>
);
```

From the profileView Screen I'm trying to navigate to the following Tabs Screen and need to pass some parameters as follows.
```
const onPressHandler = () => {
    navigation.navigate('following', **{ isPublicProfile, firstName }**);  // These parameters are passed to the route of the following Tabs Navigator
  };
```

And when I try to read these parameters in children tabs (Pages/Groups) these are undefined
```
const PageScreen = ({ route }) => {
  const { isPublicProfile, firstName } = route.params; // undefined?? Can't read parent's params
...
```

Any help would be appreciated.
Edit: I found this open Issue on GitHub ([https://github.com/react-navigation/rfcs/issues/43](https://github.com/react-navigation/rfcs/issues/43)) Is this not possible yet?


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2020-04-10 11:36:02

------------

So I ended up use `React.Context` as recommended by official React Navigation [documentation](https://reactnavigation.org/docs/hello-react-navigation/#passing-additional-props). Please Follow the official documentation for more information.

> 1- Use React context and wrap the navigator with a context provider to pass data to the screens (recommended).

Here's my solution:

```
const DEFAULT_CONTEXT = {
  isPublicProfile: false,
  ...
};

const FollowingTabNavigatorContext = createContext(DEFAULT_CONTEXT);
```


In Parent Following Tab Navigator

```
const Tabs = createMaterialTopTabNavigator();

export const FollowingTabs = ({ route }) => {
  const { isPublicProfile } = route.params;
  return (
    <FollowingTabNavigatorContext.Provider value={{ isPublicProfile }}>
      <Tabs.Navigator
        initialRouteName='pages'
        lazy
        swipeEnabled
      >
        <Tabs.Screen
          component={PageScreen}
          name='pages'
          options={{ tabBarLabel: '2 Pages' }}
        />
        <Tabs.Screen
          component={GroupScreen}
          name='groups'
          options={{ tabBarLabel: '3 Groups' }}
        />
      </Tabs.Navigator>
    </FollowingTabNavigatorContext.Provider>
  );
};

FollowingTabs.propTypes = propTypes;
FollowingTabs.defaultProps = defaultProps;
```


And Finally In my the child Tab Screens:

```
export const GroupScreen = () => {
  const { isPublicProfile } = useContext(FollowingTabNavigatorContext);
  ...
}
```



------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-08-04 12:25:00

------------

You can easily use initialParams in tab navigator and pass the props.
Check this solution.
```
<Stack.Screen name="Settings" >
    { (props) => (
      <Tab.Navigator>
        <Tab.Screen name="Tab1" component={ Tab1 } 
          initialParams={ props.route.params }  // <- pass params from root navigator
        />
        <Tab.Screen name="Tab2" component={ Tab2 } 
          initialParams={ props.route.params } 
        />
      </Tab.Navigator>
      )}
  </Stack.Screen>
```

Reference
[https://github.com/react-navigation/rfcs/issues/43#issuecomment-610706264](https://github.com/react-navigation/rfcs/issues/43#issuecomment-610706264)


------------
    
    
## Answer \#2

 Vote: 3

Created at 2020-06-25 07:11:01

------------

So Here's an alternate hacky solution without using context ;p
BUT Beware this render callback solution comes with a cost. Read [here](https://reactnavigation.org/docs/hello-react-navigation/#passing-additional-props)
> Note: By default, React Navigation applies optimizations to screen components to prevent unnecessary renders. Using a render callback removes those optimizations. So if you use a render callback, you'll need to ensure that you use `React.memo` or `React.PureComponent` for your screen components to avoid performance issues.
```
const Tabs = createMaterialTopTabNavigator();

export const FollowingTabs = ({ route }) => {
  const { isPublicProfile } = route.params;
  return (
      <Tabs.Navigator
        initialRouteName='pages'
        lazy
        swipeEnabled
      >
        <Tabs.Screen
          name='pages'
          options={{ tabBarLabel: '2 Pages' }}
        >
          {() => <PageScreen isPublicProfile={isPublicProfile} />}
        </Tabs.Screen>
        <Tabs.Screen
          name='groups'
          options={{ tabBarLabel: '3 Groups' }}
        >
         {() => <GroupScreen isPublicProfile={isPublicProfile} />}
        </Tabs.Screen>
      </Tabs.Navigator>
  );
};

FollowingTabs.propTypes = propTypes;
FollowingTabs.defaultProps = defaultProps;
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-05-24 16:42:54

------------


If anyone is still wanting a non-context solution, I believe you can use the `initialParams` prop in the children to do what you're looking for.
Assume route has some params passed to it.
```
export const FollowingTabs = ({route}) => (
  <Tabs.Navigator
    initialRouteName='page'
    lazy
    swipeEnabled
  >
    <Tabs.Screen
      component={PageScreen}
      name='page'
      options={{ tabBarLabel: '2 Pages' }}
      initialParams={route.params // Then you can grab these params using the usual route.params in the PageScreen component} 
    />
    <Tabs.Screen
      component={GroupScreen}
      name='groups'
      options={{ tabBarLabel: '3 Groups' }}
    />
  </Tabs.Navigator>
);
```



------------
    
    