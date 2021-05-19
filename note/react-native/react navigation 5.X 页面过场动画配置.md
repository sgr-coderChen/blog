### 官网链接
[过场动画配置官方API链接](https://reactnavigation.org/docs/stack-navigator/#cardstyleinterpolators)

### 配置示例
1.通过Stack.Screen逐个配置

```javascript
import { createStackNavigator, CardStyleInterpolators } from '@react-navigation/stack';

// ...

const StackOption = {
  headerStyle: {
    backgroundColor: '#c73420',
  },
  headerTitleAlign: 'center',
  headerTintColor: '#fff',
  headerTitleStyle: {
    fontWeight: 'bold',
  },
  cardStyleInterpolator: CardStyleInterpolators.forHorizontalIOS //这里配置其他的过场动画
}

<Stack.Screen
  name="Profile"
  component={Profile}
  options={{
    ...StackOption
  }}
/>;
```
2.通过Stack.Navigator统一配置
[参考链接](https://blog.csdn.net/qq_41672008/article/details/104236567?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.channel_param)

```javascript
<NavigationContainer ref={navigatorRef => {
	NavigationService.setTopLevelNavigator(navigatorRef);
}}>
    <Stack.Navigator 
    	screenOptions={{
    		// 添加这一行会实现安卓下页面的左右切换，默认是从下到上
    		cardStyleInterpolator: CardStyleInterpolators.forHorizontalIOS
    	}}
    	initialRouteName='Loading'
    	headerMode='none'
    >

    	<Stack.Screen name="Loading" component={ROUTE.Loading} options={{...StackOption}} />
    	<Stack.Screen name="HomeTabIndex" component={HomeTabIndex} options={{...StackOption}} />
    	<Stack.Screen name="PortraitDemo" component={ROUTE.PortraitDemo} options={{...StackOption}} />

    </Stack.Navigator>
    
</NavigationContainer>
```