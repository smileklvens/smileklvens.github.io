---
layout:     post
title:      开发ReactNative-Android-原生模块
subtitle:   有时访问平台api，但在RN中没有相应的模块，这就需要进一步开发RN原生模块。
date:       2017-10-19
author:     klvens
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - RN
---



## 缘由
有时候App需要访问平台api，但在RN中没有相应的模块，或者需要你复用一些原生代码，这就需要进一步开发RN原生模块。一般用React Native开发App时会用到一些原生模块，比如：在做社会化分享、第三方登录、扫描、通信录，日历等等。

## 开发Android原生模块的主要流程

```
* 编写原生模块的Java代码
* js调用java原生代码与数据交互
* 注册与导出React Native原生模块
```
##  编写原生模块的Java代码；
1.创建一个 ReactNative 项目
`$ react-native init AwesomeProject`
2.用 AndroidStudio 打开 AwesomeProject/android/ 目录下的 Android 项目,新建一个 Android Library,`QQSDK`,
为原生模块添加 ReactNative 依赖，在 QQSDK module 下的 build.gradle 的 dependencies 下添加
`compile "com.facebook.react:react-native:+"`
为 app 添加原生模块依赖，在 app 下的 build.gradle 的 dependencies 下添加`compile project(':QQSDK')` 。 
```
public class QQSDK extends ReactContextBaseJavaModule {
    @Override
    public String getName() {
        return "QQSDK";
    }

//释放资源
    @Override
    public void onCatalystInstanceDestroy() {
        super.onCatalystInstanceDestroy();
        // ...   省略部分代码
    }

      @ReactMethod
    public void shareText(String text,int shareScene, final Promise promise) {
             // ...   省略部分代码
    }
```

继承 `ReactContextBaseJavaModule`，我们重写了`public String getName()`方法，来暴露我们原生模块的名字。并在 `public void shareText(String text,int shareScene, final Promise promise) `上添加了`@ReactMethod`注解来暴露接口，这样以来我们就可以在js文件中来通过shareText调用我们所暴露给React Native的接口了。

数据交互方式分三种：Promise，Callbacks，DeviceEventEmitter，注意：前2个我们只能调用一次，RCTDeviceEventEmitter，它是原生模块和js之间的一个事件发射器
```
//声明：
 public void shareText(String text,int shareScene, final Promise promise) 

//js调用
 QQ. shareText(”text“,
                    shareScene).
                    .then();
```

```
//声明：
 public void shareText(String text,int shareScene, Callback errorCallback,Callback successCallback) 

//js调用
Q. shareText(”text“,
                    shareScene,(error)=>{
    console.log(error);
},(result)=>{
    console.log(result);
})
```

```
private void sendEvent(ReactContext reactContext,String eventName, @Nullable WritableMap params) {
    reactContext.getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
            .emit(eventName, params);
}
```
在上述方法中我们可以向js模块发送任意次数的事件，其中eventName是我们要发送事件的事件名，params是此次事件所携带的数据，接下来呢我们就可以在js模块中监听这个事件了
```
componentDidMount() {
    //注册扫描监听
    DeviceEventEmitter.addListener('onScanningResult',this.onScanningResult);
}
onScanningResult = (e)=> {
    this.setState({
        scanningResult: e.result,
    });
}
```

另外，不要忘记在组件被卸载的时候移除监听：
```
componentWillUnmount(){
    DeviceEventEmitter.removeListener('onScanningResult',this.onScanningResult);//移除扫描监听
}
```

##注册与导出React Native原生模块
* 只有我们注册了刚才创建的原生模块，在js中才能调用，

只需实现`ReactPackage`即可；模版代码：如下
```
public class QQSDKPackage implements ReactPackage {
    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        List<NativeModule> modules = new ArrayList<>();
        modules.add(new QQSDK(reactContext));
        return modules;
    }
    public List<Class<? extends JavaScriptModule>> createJSModules() {
        return Collections.emptyList();
    }
    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }
}
```

* 然后导出一个js模块，以方便我们使用它
创建一个qqindex’.js文件，然后添加如下代码：
```
import {
  NativeModules, 
  NativeEventEmitter
} from 'react-native';

const {QQSDK} =  NativeModules;

export function shareText(text,shareScene) {
	return QQSDK.shareText(text,shareScene);
}
```
* 接下来就可以在js中使用我们导出的代码了
```
QQSDK. shareText(”text“,
                    shareScene,(error)=>{
    console.log(error);
},(result)=>{
    console.log(result);
})

```

## 如有疏漏，请指出，谢谢

