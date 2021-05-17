# 思路
- 获取dom对象 
- 调用源码中的getRootState方法

**App.js**

```javascript
import NavigationService from './util/common/NavigationService'
```


```javascript
<NavigationContainer ref={navigatorRef => {
    NavigationService.setTopLevelNavigator(navigatorRef);
    }}>
    <Stack.Navigator 
    	headerMode='none'>

    <Stack.Screen name="HomeTabIndex" component={HomeTabIndex} options={{...StackOption}} />
    <Stack.Screen name="WebView" component={ROUTE.WebViews} options={{...StackOption}} />
    <Stack.Screen name="Collect" component={ROUTE.Collect} options={{...StackOption}} />
    <Stack.Screen name="LoginIndex" component={ROUTE.LoginIndex} options={{...StackOption}} />
    <Stack.Screen name="Lock" component={ROUTE.Lock} options={{...StackOption}} />
    <Stack.Screen name="PhoneBooksIndex" component={ROUTE.PhoneBooksIndex} options={{...StackOption}} />
    <Stack.Screen name="PhoneBooksList" component={ROUTE.PhoneBooksList} options={{...StackOption}} />
    <Stack.Screen name="MessageSingleChatting" component={ROUTE.MessageSingleChatting} options={{...StackOption}} />

    </Stack.Navigator>
</NavigationContainer>
```

**NavigationService.js**

```javascript
import { CommonActions } from '@react-navigation/native';

let _navigator;

function setTopLevelNavigator(navigatorRef) {
    _navigator = navigatorRef;
}

function getNavigator() {
    return _navigator
}

export default {
    setTopLevelNavigator,
    getNavigator
};

```

**获取路由栈**

```javascript
let routes = NavigationService.getNavigator().getRootState()
```

**源码**
```javascript
export declare type NavigationContainerRef = NavigationHelpers<ParamListBase> & EventConsumer<NavigationContainerEventMap> & {
    /**
     * Reset the navigation state of the root navigator to the provided state.
     *
     * @param state Navigation state object.
     */
    resetRoot(state?: PartialState<NavigationState> | NavigationState): void;
    /**
     * Get the rehydrated navigation state of the navigation tree.
     */
    getRootState(): NavigationState;
    /**
     * Get the currently focused navigation route.
     */
    getCurrentRoute(): Route<string> | undefined;
    /**
     * Get the currently focused route's options.
     */
    getCurrentOptions(): object | undefined;
};
```