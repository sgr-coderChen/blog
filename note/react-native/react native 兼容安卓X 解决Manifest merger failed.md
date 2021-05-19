### 报错信息

```javascript
* What went wrong:
Execution failed for task ':app:processDebugManifest'.
> Manifest merger failed : Attribute application@appComponentFactory value=(androidx.core.app.CoreComponentFactory) from [androidx.core:core:1.0.1] AndroidManifest.xml:22:18-86
```

### 解决办法

**android/gradle.properties添加**

```javascript
android.useAndroidX=true

android.enableJetifier=true

```

### 产生的原因

[参考链接](https://www.jianshu.com/p/41de8689615d)

