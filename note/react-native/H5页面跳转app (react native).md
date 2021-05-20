### H5代码

**建议加一个延迟**

```javascript
jump() {
    setTimeout(() => {
        window.location.href = "demo://ch?id=123" //这里的?后面为自定义的参数 不能带#号
    }, 1000);
},
```

### RN代码   AndroidManifest.xml 文件

```java
<activity
    android:name=".MainActivity"
    android:label="@string/app_name"
    android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
    android:launchMode="singleTask"
    android:windowSoftInputMode="adjustResize">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    <!-- 添加下面这一段代码 注意host,pathPrefix（这个不清楚怎么用）,scheme都是自己自定义的，只要与h5页面调用的一致即可-->
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />

        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <data
            android:host="ch"
            android:scheme="demo" />
    </intent-filter>
</activity>
```

### RN端获取h5传的参数

```javascript
//https://reactnative.cn/docs/linking#1-if-the-app-is-already-open-the-app-is-foregrounded-and-a-linking-event-is-fired  ,getInitialURL 方法
import { View, Text, Linking } from 'react-native'


Linking.getInitialURL().then((url) => {
    //  url即 demo://ch?id=123 
    if (url) {
        //	这里要注意跳转后再点击返回的问题  最好重置一下页面栈
        //  NavigationService.customReset(1, 
        //   [{ name: 'HomeTabIndex' },{ name: 'Collect' }]
        //)
        this.props.navigation.navigate('Collect')
    } else {
        if (user.token) {
            // navigation.navigate('HomeTabIndex')
            navigation.navigate('Lock')
        } else {
            navigation.navigate('LoginIndex')
        }
    }
}).catch(err => console.error('An error occurred', err));


// react navigation 5.x写法

/* 
    自定义页面栈
    index设定初始页面栈的索引
    routesArr  需要自定义的页面栈数组
    [
      { name: 'Home' },
      {
        name: 'Profile',
        params: { user: 'jane' },
      },
    ]
*/

function customReset(index, routesArr) {
    _navigator.dispatch(
        CommonActions.reset({
            index: index,
            routes: routesArr,
        })
    );
}


```

**tips: 微信，钉钉内部打开浏览器 不支持scheme跳转  需要单独用自带浏览器打开**