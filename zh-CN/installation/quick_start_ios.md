
---
name: 快速上手（iOS）
---

# 快速上手


## iOS快速上手指南

***简述：***

只需三步即可代码快速上手

1. 注册js与native对应的类
2. 实现native部分与js对应的方法
3. 创建KCWebview进行调用

以下详情说明一下如何快速上手

- **注册对应的接口类或模块**

在使用之前，你必须先注册对应的接口类或模块，使得js对象能与Native类进行映射，全局只需要注册一次就够了

~~~
    //have rewritten jsBridgeClient in kerkee
    //you can use this way, first you can import "KCJSDefine.h"
    //[KCJSBridge registClass:[KCApiOverrideJSBridgeClient class] jsObjName:kJS_jsBridgeClient];
    [KCJSBridge registJSBridgeClient:[KCApiOverrideJSBridgeClient class]];
    [KCJSBridge registClass:[KCApiTest class] jsObjName:kJS_TestModule];
    
    //you can regist class which inherit from KCJSObject,js call static function
    //[KCJSBridge registClass:[KCApiJSObjExample class] jsObjName:kJS_JSObjExampleModule];
    [KCJSBridge registObject:[[KCApiJSObjExample alloc]init] ];
~~~

- **实现JS对应的Native接口**

*KCApiOverrideJSBridgeClient*

~~~
#import "KCApiOverrideJSBridgeClient.h"
#import "KCBaseDefine.h"
#import "KCJSBridge.h"

@implementation KCApiOverrideJSBridgeClient

+(void)testJSBrige:(KCWebView*)aWebView argList:(KCArgList*)args
{
    NSString* jsonInfo = [args getString:@"info"];
    KCLog(@"%@", jsonInfo);
}

+(void)commonApi:(KCWebView*)aWebView argList:(KCArgList*)args
{
    NSString* jsonInfo = [args getString:@"info"];
    KCLog(@"%@", jsonInfo);
    
    NSMutableDictionary *dic = [NSMutableDictionary dictionary];
    [dic setObject:@"OK!" forKey:@"info"];
    NSString *json = [[NSString alloc] initWithData:[NSJSONSerialization dataWithJSONObject:dic options:0 error:nil] encoding:NSUTF8StringEncoding];
    KCAutorelease(json);
    //回调
    [KCJSBridge callbackJS:aWebView callBackID:[args getObject:@"callbackId"] jsonString:json];
    
}
@end
~~~


*KCApiTest*

~~~
#import "KCApiTest.h"
#import "KCBaseDefine.h"
#import "KCJSBridge.h"

@implementation KCApiTest

+(void)testInfo:(KCWebView*)aWebView argList:(KCArgList*)args
{
    NSString* jsonInfo = [args getString:@"testInfo"];
    KCLog(@"%@", jsonInfo);
    
    KCJSCallback* callback = [args getCallback];
    if (callback)
    {
        [callback callbackJS:aWebView jsonString:@"{}"];
    }
    
    //也可以这么回调
    //[KCJSBridge callbackJS:aWebView callBackID:[args getString:@"callbackId"] string:@"This is testInfo callball"];
}

@end

~~~

*KCApiJSObjExample*

~~~
#import "KCApiJSObjExample.h"
#import "KCJSObjDefine.h"

@implementation KCApiJSObjExample

- (NSString*)getJSObjectName
{
    return kJS_JSObjExampleModule;
}

-(void)objExampleNotStaticFunction:(KCWebView*)aWebView argList:(KCArgList*)args
{
    KCLog(@"objExampleNotStaticFunction");
}

+(void)objExampleStaticFunction:(KCWebView*)aWebView argList:(KCArgList*)args
{
    KCLog(@"objExampleStaticFunction");
}
@end
~~~

- **创建KCWebView**

在ViewController中，添加成员变量

~~~
@interface ViewController ()
{
    KCWebView* m_webView;
    KCJSBridge* m_jsBridge;
}
~~~

初始化KCWebView和KCJSBridge对象

~~~
    m_webView = [[KCWebView alloc] initWithFrame:self.view.bounds];
    //add webview in your view
    [self.view addSubview:m_webView];
    //you can implement webview delegate
    m_jsBridge = [[KCJSBridge alloc] initWithWebView:m_webView delegate:self];
~~~

- **实现KCWebView代理**

~~~
#pragma mark --
#pragma mark KCWebViewProgressDelegate

-(void)webView:(KCWebView*)webView identifierForInitialRequest:(NSURLRequest*)initialRequest
{
}

#pragma mark - UIWebView Delegate
- (void)webViewDidFinishLoad:(UIWebView *)aWebView
{
    NSString *scrollHeight = [aWebView stringByEvaluatingJavaScriptFromString:@"document.body.scrollHeight;"];
    NSLog(@"scrollHeight: %@", scrollHeight);
    NSLog(@"webview.contentSize.height %f", aWebView.scrollView.contentSize.height);
    NSLayoutConstraint *heightConstraint = [NSLayoutConstraint constraintWithItem:aWebView attribute:NSLayoutAttributeHeight relatedBy:NSLayoutRelationEqual toItem:nil attribute:NSLayoutAttributeNotAnAttribute multiplier:1.0 constant:[scrollHeight floatValue]];
    
//    [aWebView addConstraint:heightConstraint];
    NSLog(@"webview frame %@", NSStringFromCGRect(aWebView.frame));
}

- (void)webView:(UIWebView *)webView didFailLoadWithError:(NSError *)error
{
}

- (BOOL)webView:(UIWebView *)aWebView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
    return YES;
}
~~~

至此，简单Demo即可运行起来