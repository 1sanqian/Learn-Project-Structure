# Rider：create project structure ——PractiesForFreya(考核项目)

## Rider 概述

Rider是一款由JetBrains开发的集成开发环境（IDE），主要用于开发.NET和.NET Core应用程序。它为开发人员提供了丰富的功能和工具，以提高.NET开发的效率和质量。

## Rider（2023·下） 构建基础结构 

![image.png](https://upload-images.jianshu.io/upload_images/29476859-07ccd9de8f1cf828.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建如上图所示的基本项目

### 1.构建PractiseForFreya应用程序

新建解决方案Xamarin下的应用程序，文件名字为PractiseForFreya，同时删除无用且多余的文件，仅留下PractiseForFreya空文件和对应的 *.sln文件（访达可以看见)*

![image.png](https://upload-images.jianshu.io/upload_images/29476859-f376d6c4c3f07e86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.构建Facade,Libraries,Tests解决方案项目文件

jetbider 项目更新后，改由这样创建项目
![image.png](https://upload-images.jianshu.io/upload_images/29476859-699317aa63585fa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![企业微信截图_8bd7df82-24a5-461e-b5fd-c67ec81dd322.png](https://upload-images.jianshu.io/upload_images/29476859-a66218f8d3acf48f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在PractiseForFreya的文件下新建src目录下

![image.png](https://upload-images.jianshu.io/upload_images/29476859-130b597974099428.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后分别创建Facade,Libraries,Tests 的文件解决方案文件

![image.png](https://upload-images.jianshu.io/upload_images/29476859-4a258459d9402185.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.构建Facade文件下的PractiseForFreya.Api项目

点击Facade右键添加>>新建项目

选择模版ASP.NET Core Web 应用程序

对应添加Startup.cs的接口

*注意文件要建在src路径的Facade路径下（图片没有截好）*

![image.png](https://upload-images.jianshu.io/upload_images/29476859-ea0db3aba12b7d8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4.Libraries,Tests构建文件下的项目

Libraries,Tests下的文件构建都相一致，可以类似创建类库

*注意更换地址路径和类库名字*

![image.png](https://upload-images.jianshu.io/upload_images/29476859-3516a38ee4b4375d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

_项目基础结构基本完成，注意PractiesForFreya.Api下的一些文件要改用.net5的写法，比如Program.cs，Startup.cs_
