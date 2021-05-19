# 1.使用官方的默认tabBar（静态化）

**这里搭配了StackNavigator，BottomTabNavigator内嵌在Stack.Screen中**



App中的为所有的StackNavigator路由  HomeTabIndex为BottomTabNavigator路由
```javascript
class App extends React.Component {
  render() {
    return (
      <View style={{flex: 1}}>
        <NavigationContainer >
          <Stack.Navigator 
            initialRouteName='Loading'
            headerMode='none'>
  
            <Stack.Screen name="Loading" component={ROUTE.Loading} options={{...StackOption}} />
            {/* HomeTabIndex为BottomTabNavigator路由  内嵌在StackNavigator路由中 */}
            <Stack.Screen name="HomeTabIndex" component={HomeTabIndex} options={{...StackOption}} />
            <Stack.Screen name="Collect" component={ROUTE.Collect} options={{...StackOption}} />
          </Stack.Navigator>
        </NavigationContainer>
        <WebSocket />
      </View>
      
    )
  }
}
```


BottomTabNavigator路由
```javascript
class HomeTabIndex extends React.Component {

  render() {
      return (
        <Tab.Navigator>

          <Tab.Screen name="HomeIndex" 
            component={ROUTE.HomeIndex} 
            options={{
              tabBarIcon: ({focused}) => (
                  <Image
                      source={focused ? require('./assets/image/common/home_red.png') : 
                                        require('./assets/image/common/home.png')}
                      style={[{width: 26, height: 26}, ]}/>
              ),
              tabBarLabel: '首页'
            }}
          />
          <Tab.Screen name="NewsIndex" component={ROUTE.NewsIndex} 
            options={{
              tabBarIcon: ({focused}) => (
                  <Image
                      source={focused ? require('./assets/image/common/news_red.png') : 
                                        require('./assets/image/common/news.png')}
                      style={[{width: 26, height: 26}, ]}/>
              ),
              tabBarLabel: '新闻'
            }}
          />
          
        </Tab.Navigator>
    )
  }
  
  
}
```

# 2.使用自定义tabBar（动态化）

#### 自定义tabBar的思路：
1.自定义tabBar会被传入 state, descriptors, navigation
state中包含了Tab.Navigator中的所有注册的BottomTab路由
通过state.routes.map渲染所有注册的Tab.Screen

**TabMenu 为自定义的tabBar**

1.第一种为官方的自定义tabBar示例（渲染所有注册的Tab.Screen）
```javascript
function MyTabBar({ state, descriptors, navigation }) {
  return (
    <View style={{ flexDirection: 'row' }}>
      {state.routes.map((route, index) => {
        const { options } = descriptors[route.key];
        const label =
          options.tabBarLabel !== undefined
            ? options.tabBarLabel
            : options.title !== undefined
            ? options.title
            : route.name;

        const isFocused = state.index === index;

        const onPress = () => {
          const event = navigation.emit({
            type: 'tabPress',
            target: route.key,
          });

          if (!isFocused && !event.defaultPrevented) {
            navigation.navigate(route.name);
          }
        };

        const onLongPress = () => {
          navigation.emit({
            type: 'tabLongPress',
            target: route.key,
          });
        };

        return (
          <TouchableOpacity
            accessibilityRole="button"
            accessibilityStates={isFocused ? ['selected'] : []}
            accessibilityLabel={options.tabBarAccessibilityLabel}
            testID={options.tabBarTestID}
            onPress={onPress}
            onLongPress={onLongPress}
            style={{ flex: 1 }}
          >
            <Text style={{ color: isFocused ? '#673ab7' : '#222' }}>
              {label}
            </Text>
          </TouchableOpacity>
        );
      })}
    </View>
  );
}
const Tab = createBottomTabNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator tabBar={props => <MyTabBar {...props} />}>
        <Tab.Screen name="Home" component={HomeScreen} />
        <Tab.Screen name="Settings" component={SettingsScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}
```

2.第二种为自定义tabBar（渲染所有注册的Tab.Screen）

```javascript
function MyTabBar({ state, descriptors, navigation }) {
  return (
    <View style={{ flexDirection: 'row' }}>
      {state.routes.map((route, index) => {
        const isFocused = state.index === index;
        const onPress = () => {
          const event = navigation.emit({
            type: 'tabPress',
            target: route.key,
          });

          if (!isFocused && !event.defaultPrevented) {
            navigation.navigate(route.name);
          }
        };
		
        // 这里自定义每个tab的样式
        return (
          <TouchableOpacity
            onPress={onPress}
            style={{ flex: 1 }}
          >
            <Text style={{ color: isFocused ? '#673ab7' : '#222' }}>
              {label}
            </Text>
          </TouchableOpacity>
        );
      })}
    </View>
  );
}
```

3.接口返回菜单 异步渲染tabBar

**坑1：这里需要注意 如果不使用state.routes 需要注意顺序问题 不能使用const isFocused = state.index === index,一旦使用了，则会受到Tab.Screen的注册顺序问题，state.index为当前的路由在Tab.Navigator总路由的索引所以这里自定义一个currentIndex来判断是否选择（isFocused）**

**坑2：当使用了currentIndex，因为为内部变量 所以初始路由需要由接口提供，再传入到自定义tabBar去初始化currentIndex**


TabMenu为自定义tabBar
```javascript
//  如果接口路由名字不一致可用于替换名称
const MENUS_PARSE = {
    "/home-index":"HomeIndex",
    "/news-index":"NewsIndex",
    "/performance-index":"PerformanceIndex",
    "/message-index":"MessageIndex",
    "/my-index":"MyIndex",
};

class TabMenu extends Component {

    constructor(props) {
        super(props);
        this.state = {
            menusData: [],
            currentIndex: 0
        }
    }
    
    componentDidMount() {
        console.log('props', this.props)
    }


    onPress = (item,index) => {
        this.setState({currentIndex:index},()=>{
            this.props.navigation.navigate(MENUS_PARSE[item.url]);//替换组件名称
        })
    };
    
	// this.props.user.menus为接口返回的菜单数据（这里放在了redux中）
    render() {
        const {currentIndex} = this.state
        return (
            <View style={styles.tabBox}>

                {this.props.user && this.props.user.menus.map((item, index) => {
                    return (
                        <TouchableOpacity onPress={()=>this.onPress(item,index)} style={styles.tabItem} key={index}>
                            <Image resizeMode='contain' source={currentIndex === index 
                                ? {uri: item.icon_selected} 
                                : {uri: item.icon_unselected} }
                                style={[{width: 26, height: 26}, ]}
                            />
                            <Text style={{ color: currentIndex === index ? '#c73420' : '#222',marginTop: 6 }}>
                                {item.name}
                            </Text>
                        </TouchableOpacity>
                    )
                })}
                
            </View>
        );
    }
}

const mapStateToProps = (state) => {
    return {
        user: state.user
    }
}



export default connect(mapStateToProps)(TabMenu)
```


将自定义tabBar嵌入Tab.Navigator

```javascript
class HomeTabIndex extends React.Component {

  componentDidMount() {
      console.log('user', this.props.user)
  }

  render() {
      const initialRouteName = 'HomeIndex' // 初始路由为接口提供,这边暂时写死,再传入自定义的TabMenu中 去初始化currentIndex！！！！！
      return (
          <Tab.Navigator initialRouteName={initialRouteName} tabBar={props => <TabMenu {...props} />}>
              <Tab.Screen name="MyIndex" component={ROUTE.MyIndex} />
              <Tab.Screen name="HomeIndex" component={ROUTE.HomeIndex} />
              <Tab.Screen name="NewsIndex" component={ROUTE.NewsIndex} />
              <Tab.Screen name="MessageIndex" component={ROUTE.MessageIndex} />
              <Tab.Screen name="PerformanceIndex" component={ROUTE.PerformanceIndex} />
          </Tab.Navigator>
      )
  }
}

const mapStateToPropsTab = (state) => {
  return {
      user: state.user
  }
}

connect(mapStateToPropsTab)(HomeTabIndex)
```