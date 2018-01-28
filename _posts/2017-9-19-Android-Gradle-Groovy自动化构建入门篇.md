---
layout:     post
title:      Android-Gradle-Groovy自动化构建入门篇
subtitle:   Gradle是现在很流行的**自动化构建工具**，而且studio也在使用这个构建，我们很有必要了解它。
date:       2017-9-19
author:     klvens
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - gradle
---

Gradle是一个基于Apache Ant和Apache Maven概念的项目**自动化构建工具**。它使用一种基于Groovy的特定领域语言(DSL)来声明项目设置，抛弃了基于XML的各种繁琐配置。
## 为什么要学习它
我们知道主流的的构建工具有Ant，Maven，那我们为什么还要学习Gradle那？
* Ant    只负责  编译、测试、打包
* Maven  新增依赖管理、发布,采用xml管理，xml配置项目大的话，容易啰嗦和臃肿
* Gradle    采用Groovy管理，具有更高的灵活性和可扩展性。
所以我们很有必要一块来学习一下。

##  手动安装Gradle
1.确保已安装JDK，在终端运行 
```
   	$ java -version
	java version "1.8.0_121"	
```
2.从Gradle官网下载最新[Gradle](https://gradle.org/releases/)，然后解压.
3.配置环境变量   
			*  Linux和MacOS用户
     		       配置您的PATH环境变量以包含bin解压缩分发的目录：
			` $ export PATH=$PATH:/opt/gradle/gradle-4.4.1/bin`             
			* Windows用户
     			点击：我的电脑- 属性-高级属性 —环境变量，
			选择下 CLASSPATH，然后编辑。添加
			`C:\Gradle\gradle-4.4.1\bin`,点击保存。
			* 验证安装
			执行`$ gradle -v` 如果出现了类似一下界面，
```
------------------------------------------------------------
Gradle 4.4.1
------------------------------------------------------------ 
```

那么恭喜你，你已安装成功，接下来我们就从Gradle的 基本语法开始，一起来揭开Gradle的神秘面纱吧。
## Groovy基本语法理论介绍
前面我们知道Gradle是一种基于Groovy语言(DSL)来声明的，所以我们很有必要先了解下Groovy 语言。Groovy 语言是用于Java虚拟机的敏捷语言，是可以用于面对对象编程，又可以用作纯粹的脚本语言，同时有具有闭包和动态语言的其他特性。

#### 基本特性：
```
1. Groovy 完全兼容java语法
2. 末尾的分号是可选的
3. 类、方法默认是public的
4. 编译器会自动添加getter/setter方法
5. 属性可以用.号获取
6. 最后一个表达式的值会作为返回值的
7. ==等同于equals(),不会有NullPointerExceptions
```

#### 高效特性
``` 
1. assert 断言语句 可以写在任何地方
2. 可选类型定义，相当于若类型语言 ，类型会自动推导出来的
3. 可选的括号 ，方法如过有有参数，（）可以不写
4. 字符串 有三种 分别为 '' 、""、'''  '''
5. 闭包
```

## Groovy  常用api
打开Ide工具新建工程，选择new -- project —依次点击
![D4FA8425-0201-4DC5-8045-5BF238E3ECF2.png](https://user-gold-cdn.xitu.io/2018/1/14/160f44c8d55c3c07?w=1240&h=846&f=png&s=201259)
![EEEB527E-6D12-40B8-AE4C-E9CDE8FF8A3E.png](https://user-gold-cdn.xitu.io/2018/1/14/160f44c8d6eb56c4?w=1240&h=833&f=png&s=169765)
#### 基本特性：
![Snip20180114_3.png](https://user-gold-cdn.xitu.io/2018/1/14/160f44c8d7ed47d1?w=1240&h=507&f=png&s=291680)
#### 高效特性
![Snip20180114_4.png](https://user-gold-cdn.xitu.io/2018/1/14/160f44c8d572d856?w=1240&h=512&f=png&s=252147)
闭包是一种代码块，叫做Closure，这是一种类似于C语言中函数指针的东西。用起来非常方便，在Groovy中闭包可以作为方法的参数和返回值，也可以作为一个变量赋值存在。
如何声明闭包？
```
{ parameters ->
   code
}
```
闭包可以有返回值和参数，当然也可以没有。下面是几个具体的例子
```
//有参闭包，赋值给一个变量
def c1 = {
    v ->
        println v
}
//无参闭包，赋值给一个变量c2
def c2 = {
    println 'hello'
}
//c2闭包作为参数传入方法method2中
method2(c2)
```
另外，如果闭包不指定参数，那么它会有一个隐含的参数 it，闭包是一个难点
建议多看官方[API](http://www.groovy-lang.org/api.html)
```
def test = {
   println "隐含的参数 ${it}, I am a closure!"
}
test(100)
outputs:
find 100, I am a closure! 
```
#### 常用集合api
* list
```
def fruits = ['apple', 'orange']//声明
fruits << 'banana'//添加数据
fruits[0] = "gradle"//集合中第一项重新赋值
println fruits[0]//根据key取出数据
println fruits.size
println fruits.getClass()

outputs:
gradle
3
class java.util.ArrayList
```
 由此可以看出Groovy中国list就是java中的`ArrayList`，`<<`表示向List中添加新元素
* map
```
def dataSource = ['groupId': 1, 'year': '2017']//声明
dataSource.groupName = '行业'//添加数据
dataSource['year'] = '2018'//更新数据
println dataSource['year']//获取value
println dataSource.groupId//获取value
println dataSource.size()
println dataSource.getClass()

outputs:
2018
1
3
class java.util.LinkedHashMap
```
 由此可以看出Groovy中国map就是java中的`LinkedHashMap`，访问key不仅可以通过map.获取`dataSource.groupName `也通过map[key]获取`dataSource['year']`获取。
假如我们现在想采用Groovy方式遍历和这个集合该怎么办那？查文档发现
[image:422EA4A3-C96F-40A7-9E74-3AD85AF88A43-4899-00001DBAD2F11AC4/Snip20180114_6.png]
可以发现，这两个each方法的参数都是一个闭包，如果我们传递的闭包是一个参数，那么它就把entry作为参数；如果我们传递的闭包是2个参数，那么它就把key和value作为参数。
接下来我们遍历上面的 `dataSource`
```
dataSource.each { key, value ->
    println "two parameters, find [${key} : ${value}]"
}
println()
dataSource.each {
    println "one parameters, find [${it.key} : ${it.value}]"
}

outputs:
two parameters, find [groupId : 1]
two parameters, find [year : 2018]
two parameters, find [groupName : 行业]

one parameters, find [groupId : 1]
one parameters, find [year : 2018]
one parameters, find [groupName : 行业]
```
* 加强的IO
在Groovy中，文件访问要比Java简单的多，不管是**普通文件**还是**xml文件**。
#### 读取普通文件
在根目录下，新建`a.txt`
[image:88688DC4-000C-4FF4-8ABA-0234A13A2A84-4899-0000206B8BF3CA1E/C6E33E21-322F-4A1A-9AAA-835B7A00F0A5.png]
接下里我们就把a.txt的文本按行取出来
```
def file = new File("a.txt")
file.eachLine {
    line,lineNo->
        println "${lineNo} ${line}"
}
file.eachLine { line ->
    println "${line}"
}

outputs:
1 hello
2 gradle
hello
gradle

```
除了eachLine外，File还提供了很多Java所没有的方法，我们需要用的时候再去查就行了。
#### 接下来让我们在试下读取xml文件
Groovy访问xml有两个类：XmlParser和XmlSlurper，二者几乎一样，查看官方demo如下
```
def xml = '<root><one a1="uno!"/><two>Some text!</two></root>'
 def rootNode = new XmlParser().parseText(xml)
 assert rootNode.name() == 'root'
 assert rootNode.one[0].@a1 == 'uno!'
 assert rootNode.two.text() == 'Some text!'
 rootNode.children().each { assert it.name() in ['one','two'] }
```

好了，今天是我们这个Gradle系列的第一篇文章，相信现在你已经了解了Gradle的基本语法了，当然也是一些最常用的用法。下一篇文章当中，我们会介绍Gradle构建脚本，自定义任务，构建生命周期，解决依赖冲突，多项目构建等高阶技巧。
如有问题可以通过如下方式联系我
[简书](https://www.jianshu.com/p/20cdcb1bce1b)
[csdn](http://blog.csdn.net/qq_19307133/article/details/79058392)
[掘金](https://juejin.im/post/5a5b366af265da3e58594f00)
