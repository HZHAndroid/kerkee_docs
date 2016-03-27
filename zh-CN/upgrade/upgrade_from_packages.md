---
name: 从包管理升级
---

# Android客户端使用包管理方式升级

***前提：***有Android环境，本项目现在使用的是gradle构建，IDE建议Android Studio，当然你也可以使用idea或eclipse（需要安装gradle，可自行Google安装），甚至其他。

kerkee从包管理升级，非常简单，建议使用此方式，干净简洁。

在你的Android工程的build.gradle中找到 dependencies 块，修改版本号（`1.3.8`）至最新，最新版： [ ![Download](https://api.bintray.com/packages/zihong/maven/kerkee/images/download.svg) ](https://bintray.com/zihong/maven/kerkee/_latestVersion)

`compile 'com.kercer:kerkee:1.3.8'`即可。

如下所示

~~~
dependencies {
    compile fileTree(include: '*.jar', dir: 'libs')
    compile 'com.kercer:kerkee:1.3.8'
}
~~~
至此，kerkee升级完毕。


# iOS客户端包集成


iOS的kerkee使用CocoaPods进行管理，在升级的时候很方便。
***
**简述：** 如果你已知道CocoaPods如何使用，你只需在Podfile中添加`pod ‘kerkee’, ’~> 1.0.0’` 或者 `pod ‘kerkee’`（若是团队协作，建议要加下版本号），如果你懂得如何使用CocoaPods，你可不用看以下的详细说明。

***

CocoaPods的安装及深入使用：[请点击这里](http://blog.linzihong.com/use_cocoapods)

***前提：***cocopods安装，安装非常简单，本来想略去这一部分的，但还是贴一下命令吧，如果不明白网络上搜索一搜一大把。

~~~
sudo gem install cocoapods
pod setup
~~~

**1、 创建Podfile**

使用也比较简单，cd到你的工程的根目录$projectPath

~~~
cd $projectPath
pod init
~~~
这个命令就会再工程根目录下面生成一个Podfile文件,podfile用文本编辑就行了

**2、 配置Podfile**

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

#or this way，If it is a team work,please do not use this way.
#because it use last version of kerkee,it is a bad way
#pod ‘kerkee’
pod ‘kerkee’, ’~> 1.0.0’

end

target 'TestKerkeePodTests' do
end

target 'TestKerkeePodUITests' do
end
~~~

**3、执行导入命令**

使用终端cd 到你的Podfile所在的目录，然后再使用`pod install`就可以把这些开源库导入，CocoaPods就开始为我们做下载源码、配置依赖关系、引入需要的framework等一些列工作。

~~~
cd $projectPath
pod install --verbose
~~~

安装完，这些库都放在一个Pods的工程中，然后会用xcode的workspace来管理这个工程和你自己的工程。以后就打开`yourproject.xcworkspace`就行了。

至此，kerkee的升级已完毕！
