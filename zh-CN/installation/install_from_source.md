---
name: 源码集成
---

# 源码集成（Android、iOS）

kerkee的native部分目前支持Android和iOS平台，两个平台的架构设计及接口设计保持一致，大大降低了跨平台的成本，下面介绍一下如何源码集成

## Android

- 1、 先拉取源码 

		git clone https://github.com/kercer/kerkee_android.git

	源码的根目录包含`kerkee`和`kerkee_example`目录

	`kerkee`目录是kerkee的工程，可以直接导入工程进行lib库依赖
	

- 2、把kerkee目录拷贝到自己的工程根目录（通常与你工程的setting.gradle同一目录）

- 3、在你自己工程的`setting.gradle`文件中添加include ':kerkee'

- 4、在你自己工程`build.gradle`文件的dependencies 代码块中添加`compile project(':kerkee')`

## iOS

- 1、拉取源码

		git clone https://github.com/kercer/kerkee_ios.git

- 2、拷贝根目录下的kerkee目录到你的工程目录（这步也可以忽略）
- 3、打开你的工程，把kerkee.xcodeproj文件拉到你的工程里进行lib依赖
- 4、找到你工程的TARGETS，在`Embedded Binaried`添加kerkee.framework

恭喜你，源码集成完成！