---
name: 包集成
---

# Android客户端包集成

***前提：***有Android环境，本项目现在使用的是gradle构建，IDE建议Android Studio，当然你也可以使用idea或eclipse，甚至其他。

kerkee从包集成，非常简单，建议使用此方式，干净简洁。

在你的Android工程的build.gradle中找到 dependencies 块，添加
`compile 'com.kercer:kerkee:1.3.7'`即可。

如下所示

~~~
dependencies {
    compile fileTree(include: '*.jar', dir: 'libs')
    compile 'com.kercer:kerkee:1.3.7'
}
~~~
至此，基本集成已完成！！！恭喜你已经集成了kerkee，接下来就代码走起！！！

# iOS客户端包集成


iOS的kerkee使用cocoapods进行管理，在集成的时候很方便。
***
**简述：** 如果你已知道cocoapods如何使用，你只需在podfile中添加`pod ‘kerkee’, ’~> 1.0.0’` 或者 `pod ‘kerkee’`，然后即可走起，此时你可不用看以下的详细说明了

***


***前提：***cocopods安装，安装非常简单，本来想略去这一部分的，但还是贴一下命令吧，如果不明白网络上搜索一搜一大把。

~~~
sudo gem install cocoapods
pod setup
~~~

1、 使用pod
使用也比较简单，cd到你的工程的根目录$projectPath

~~~
cd $projectPath
pod init
~~~
这个命令就会再工程根目录下面生成一个Podfile文件,podfile用文本编辑就行了

2、 配置podfile

在你工程的***Podfile***文件添加

~~~
pod ‘kerkee’, ’~> 1.0.0’
~~~

或者你想直接使用最新版可把版本号去掉，使用以下这句

~~~
pod ‘kerkee’
~~~

我帖出个podfile文件配置的例子

~~~
# Uncomment this line to define a global platform for your project
# platform :ios, '8.0'
# Uncomment this line if you're using Swift
# use_frameworks!

target 'TestKerkeePod' do

#or this way
#pod ‘kerkee’, ’~> 1.0.0’
pod ‘kerkee’

end

target 'TestKerkeePodTests' do
end

target 'TestKerkeePodUITests' do
end
~~~

3、安装
使用终端cd 到你的Podfile所在的目录，并导入库，CocoaPods就开始为我们做下载源码、配置依赖关系、引入需要的framework等一些列工作

~~~
cd $projectPath
pod install --verbose
~~~

然后再使用`pod install`就可以把这些开源控件安装了。安装完，这些控件都放在一个Pods的工程中，然后会用xcode的workspace来管理这个工程和你自己的工程。以后就打开`yourproject.xcworkspace`就行了。

至此，基本集成已完成！！！恭喜你已经集成了kerkee，接下来就代码走起！！！
