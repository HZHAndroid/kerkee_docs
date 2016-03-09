---
name: 包集成（推荐）
---

# Android客户端集成

前提：有Android环境，本项目现在使用的是gradle构建，IDE建议Android Studio，当然你也可以使用idea或eclipse，甚至其他。

kerkee从包集成，非常简单。

在你的Android工程的build.gradle中找到 dependencies 块，添加
`compile 'com.kercer:kerkee:1.3.7'`

### 快速上手
好了，到现在为止，kerkee已集成完成，接下来开始使用它。
我们以官网上的例子工程来讲解如何快速上手。

1. 先下载源码
`git clone https://github.com/kercer/kerkee_android.git`

2. 使用源码导入到IDE（也可以只导入`kerkee_example`文件夹到IDE中）

3. kerkee_example默认使用本地kerkee源码依赖的方式编译。

	你可以改成包依赖进行编译，使用包集成需要确认这两个文件
	
	在 ***build.gradle*** 中把`compile project(':kerkee')`改为`compile 'com.kercer:kerkee:1.3.7'`；
	
	在 ***setting.gradle*** 中把注掉`include ':kerkee'`
4. 一切完成，直接把example跑起来吧

### 代码使用

- **创建Browser**

在例子中提供了一个默认的**KCDefaultBrowser**，如果你有特殊的UI需求，你可以参照**KCDefaultBrowser**进行实现，这时你可以为所欲为地可以把**KCWebView**添加到任何view中进行呈现

> 所要注意的地方是：
> 创建**KCWebView**时，初始化时，若需要设置ChromeClient和WebViewClient，则注意以下两个函数的参数类型
>
> mWebView.setWebChromeClient(**KCWebChromeClient**)；
> mWebView.setWebViewClient(**KCWebViewClient**)

- **注册对应的接口类或模块**
可以参考例子中的KCRegistMgr类的实现

```
//注册类，所注册的native code中的类的函数必是静态函数，js都能调到
//第一个参数为js的类名，第二个参数为native的类名
KCJSBridge.registClass(KCJSObjDefine.kJSObj_testModule, KCApiTestModule.class);

//上层可以使用自己的类取代kerkee中的jsBridgeClient，对应的js对象类为jsBridgeClient
// KCJSBridge.registClass(KCJSDefine.kJS_jsBridgeClient, KCApiJSBridgeClient.class);//与下一行效果一致
KCJSBridge.registJSBridgeClient(KCApiJSBridgeClient.class);

//注册对象方式
//KCApiJSObjExample必须继承KCJSObject，并实现getJSObjectName()方法，返回js类名
//KCApiJSObjExample中的函数可以是静态函数也可以是成员函数，HybridRuntime会自动处理
KCJSBridge.registObject(new KCApiJSObjExample());

```

- **native接口的实现**

静态类如KCApiTestModule中的实现，修改一下方法名即可

```
public static void testInfo(final KCWebView aWebView, KCArgList aArgList)
{
}

```

KCApiJSObjExample（继承KCJSObject，并实现getJSObjectName()），修改一下方法名即可

```
//成员方法
public void objExampleNotStaticFunction(final KCWebView aWebView, KCArgList aArgList)
{
}

//静态方法
public static void objExampleStaticFunction(final KCWebView aWebView, KCArgList aArgList)
{
}

```

至此，基本使用已完成！！！恭喜你上手了kerkee，你学这么快，你妈知道吗！！！