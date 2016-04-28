
---
name: 快速上手（Android）
---

# 快速上手


## Android快速上手指南

***简述：***

只需三步即可代码快速上手

1. 注册js与native对应的类
2. 实现native部分与js对应的方法
3. 创建KCWebview进行调用

以下详情说明一下如何快速上手

- **注册对应的接口类或模块**

可以参考例子中的KCRegistMgr类的实现，全局只需注册一次就够了

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

- **实现JS对应的Native接口**

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


- **创建KCWebView**

在例子中提供了一个默认的**KCDefaultBrowser**，如果你有特殊的UI需求，你可以参照**KCDefaultBrowser**进行实现，这时你可以为所欲为地可以把**KCWebView**添加到任何view中进行呈现

> 所要注意的地方是：
> 创建**KCWebView**时，初始化时，若需要设置ChromeClient和WebViewClient，则注意以下两个函数的参数类型
>
> mWebView.setWebChromeClient(**KCWebChromeClient**)；
> mWebView.setWebViewClient(**KCWebViewClient**)

- **调用**

```
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
//        setContentView(R.layout.activity_main);

        //create browser that use KCWebview
        KCDefaultBrowser browser = new KCDefaultBrowser(this);
        View view = browser.getView();
        setContentView(view);

        //regist classes to JSBridge，the relationship between binding js objects and native classes
        //and you can use default browser'b registJSBridgeClient function
        KCRegistMgr.registClass();
        //you can registObject here;
//        KCJSBridge.registObject(new KCTest());

        browser.loadTestPage();
//        browser.loadUrl("http://www.baidu.com");

    }
```


至此，简单Demo即可运行起来