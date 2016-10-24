[![](https://jitpack.io/v/xinayida/PluginDemo.svg)](https://jitpack.io/#xinayida/PluginDemo)
# PluginDemo
A demo for test publish lib/plugin to Jitpack

### Gradle语法
如果要学习gradle相关的东西，请查看[Gradle for Android](https://segmentfault.com/a/1190000004229002)
### Gradle插件开发
Gradle插件是使用Groovy进行开发的，而Groovy其实是可以兼容Java的。Android Studio其实除了开发Android App外，完全可以胜任开发Gradle插件这一工作，下面来讲讲具体如何开发。
- 首先，新建一个Android项目。
- 之后，新建一个Android Module项目，类型选择Android Library。
- 将新建的Module中除了build.gradle文件外的其余文件全都删除，然后删除build.gradle文件中的所有内容。
- 在新建的module中新建目录结构src.main.groovy，这时候groovy文件夹会被Android识别为groovy源码目录。除了在main目录下新建groovy目录外，你还要在main目录下新建resources目录，同理resources目录会被自动识别为资源文件夹。在groovy目录下新建项目包(com.xinayida.plugin)，就像Java包那样。resources目录下新建文件夹META-INF，META-INF文件夹下新建gradle-plugins文件夹。这样，就完成了gradle 插件的项目的整体搭建，之后就是小细节了。

目前，项目的结构是这样的：
![](http://chuantu.biz/t5/39/1477288410x3070664390.png)
打开Module下的build.gradle文件，输入
```
apply plugin: 'groovy'

dependencies {
    compile gradleApi()
    compile localGroovy()
}

repositories {
    mavenCentral()
}
```
下面我们在刚才的包中添加一个文件，命名为PluginImpl.groovy，注意有groovy后缀，然后在里面输入(注意包名替换为你自己的包名)。

```
package com.xinayida.plugin

import org.gradle.api.Plugin
import org.gradle.api.Project

public class PluginImpl implements Plugin<Project> {
    void apply(Project project) {
        project.task('testTask') << {
            println "Hello gradle plugin"
        }
    }
}
```
然后在resources/META-INF/gradle-plugins目录下新建一个properties文件，==注意该文件的命名就是你之后使用插件的名字==，这里命名为==plugin.test==.properties，在里面输入

`implementation-class=com.xinayida.plugin.PluginImpl`

ps:注意包名需要替换为你自己的包名。

这样就完成了最简单的一个gradle插件，里面有一个叫testTask的Task，执行该task后会输出一段文字，就像当初我们输出HelloWorld一样。
### 发布到Jitpack

之前的参考文章中是将库发布到Jcenter中，但是经过注册使用后发现https://bintray.com 只有30天的试用期，而且不翻墙的话打开速度很慢。
而Jitpack的话使用起来更加方便，基本上在Github上创建项目后，简单配置一下就可以发布使用了。[官网发布教程请戳这里](https://jitpack.io/docs/ANDROID/)

#### 发布

1. 在主工程的build.gradle中添加：
```
buildscript {
  dependencies {
  classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5' // Add this line
```

2. 在library/build.gradle中添加：
    ```
    apply plugin: 'com.github.dcendents.android-maven'

    group='com.github.YourUsername'
    ```
3. 在Github上创建一个release或添加一个tag

#### 使用

1.在build文件中加入JitPack库
```
	allprojects {
		repositories {
			...
			maven { url "https://jitpack.io" }
		}
	}
```
2.加入依赖库
```
    dependencies {
            compile 'com.github.User:Repo:Tag'
        }
```
例如：
```
    dependencies {
        classpath 'com.github.xinayida:PluginDemo:v0.0.1'
    }
```
如果Github上没有Release,可以使用short commit hash 或者 'anyBranch-SNAPSHOT' 的方式来作为version字段

如果发布的库是一个插件的话，需要在app/build.gradle中加入
`apply plugin: 'pluginname'` 
eg.
`apply plugin: 'plugin.test'`
这个名字就是之前在创建插件工程时，esources/META-INF/gradle-plugins目录下的properties文件名


### 参考文章
[如何使用Android Studio开发Gradle插件](http://blog.csdn.net/sbsujjbcy/article/details/50782830)

