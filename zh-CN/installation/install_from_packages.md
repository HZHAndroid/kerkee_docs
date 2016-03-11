---
name: 包集成（推荐）
---

# Android客户端包集成

***前提：***有Android环境，本项目现在使用的是gradle构建，IDE建议Android Studio，当然你也可以使用idea或eclipse，甚至其他。

kerkee从包集成，非常简单。

在你的Android工程的build.gradle中找到 dependencies 块，添加
`compile 'com.kercer:kerkee:1.3.7'`即可。

如下所示

~~~
dependencies {
    compile fileTree(include: '*.jar', dir: 'libs')
    compile 'com.kercer:kerkee:1.3.7'
}
~~~


至此，基本使用已完成！！！恭喜你已经集成了kerkee，接下来就代码走起！！！