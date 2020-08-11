## Gradle 基础

Gradle 是一个使用纯 Java 编写的基于 JVM 的通用构建工具。

### Gradle 的基础概念

**Distribution**

Distribution 分为三种版本：

sources：源代码的打包，不可以运行

bin:可运行的二进制

all:除了可运行的二进制，还包括文档等

**Wrapper**

通过 Wrapper 来安装和运行指定的 Gradle 版本

而不必和本机安装的版本绑定

保证项目在任意机器上都可以相同构建

**GradleUserHome**

一般在用户目录下有一个 .gradle 文件夹

此文件夹是 GradleUserHome

下载的 Gradle 版本，包括缓存数据等都在这个目录

项目通过 Gradle 构建，除了和项目文件打交道，就是和 GradleUserHome 打交道

**Daemon**

以 Maven 为例，当使用 Maven 构建时，本质上是启用了一个 Maven 的 JVM 进程，构建结束后关闭 JVM 进程

所以每次使用 Maven 构建时，都要启用一次 Maven 的 JVM 进程，包括 load 所需的 jar 文件，这是一个比较耗时的事情

在 Gradle 3.0 版本之后，默认使用 Daemon 模式

首先启动一个非常轻量的 Client JVM 进程，这个 JVM 只和后台的 Daemon JVM 进程通信

构建结束后，Client JVM 进程关闭，但是 Daemon JVM 进程仍然保留，当再次需要构建时，可以直接启用 Daemon，减少构建耗时等待

Daemon JVM 进程默认后台保留三小时，在此之间没有被启动则关闭

Gradle 构建时，也可以使用 --no-deamon 参数不使用 Daemon 模式，此时构建和 Maven 类似

在 3.0 版本之前，通过 CI 构建，官方文档建议使用--no-deamon 方式构建

在 4.0 版本开始，Daemon 已经足够稳定，建议统一使用 Daemon 模式构建

### Groovy 基础

Groovy 是运行在 JVM 的编程语言

**动态调用与 MOP**

Groovy 通过 MOP（元对象协议）的机制，使得 Groovy 对象更加灵活，我们可以根据对象的 metaClass，动态的查询对象的方法和属性

这里所属的动态指的是在运行时，根据所提供的方法或者属性的字符串，即可得到方法和属性。类似于 Java 反射方式。

**闭包**

通过闭包（Closure）来实现函数的传递

闭包的特性是使得 Groovy 作为一门 DSL 的基础

## gradle 构建

build.gradle 文件是核心构建文件

相当于 Maven 构建中的 POM 文件

### Gradle 的核心模型

**Project**

Gradle 通过一个一个的 Project 来构建

一个构建可以是单 Project，也可是是多个 Project 的

Project 之间可以相互有依赖关系

每个 Project 都有一个 build.gradle 文件

**Task**

Gradle 的构建基于 Task，Task 是 Gradle 构建中最小的执行单元

Task 之间也可以相互有依赖关系

**Lifecycle 与 Hook**

Lifecycle：

初始化阶段（initialization）：读取项目信息，判断是单项目工程/多项目工程，决定哪些项目参与构建，为每个参与构建的项目创建 Project instance

配置阶段（configuration）：不运行构建，只对构建进行配置。配置过程会把 build.gradle 从头执行到尾一次

执行阶段（execution）：构建配置完成后，真正运行构建所需要调用的 Task

Hook：

例如 afterEvaluate 函数调用，在 configuration 时，把 build.gradle 文件从头到尾执行一次之后，就 Evaluate 完毕，此时再执行 afterEvaluate 中的函数，最终完成 configuration 阶段

## 插件编写

插件本质上就是一些 Task 的集合，以利于构建逻辑的复用

**简单插件**

直接继承 Plugin 类型，然后实现其中的 apply 方法

**script 插件**

将自定义的 Plugin 插件，打包传入某个本地目录，或者 THHP URL 路径，供自己或者第三方使用

**buildSrc 插件**

Gradle 项目引入某个依赖给项目使用时，引入的依赖仅限项目中使用，无法使用到 build.gradle 构建文件本身

被 Gradle 构建的项目，通过 Gradle 引入某个依赖后，这个依赖的 classpath 就被引入到项目中，可以在项目中使用引入的依赖库中的方法

但是 Gradle 引入的依赖仅限项目中使用，其 classpath 没有被引入到 build.gradle 文件， build.gradle 构建文件本身无法使用依赖库中的方法

此时需要在 build.gradle 中添加 buildscript，在 dependencies 中添加依赖库的 classpath，才可以使用依赖库中的方法

在项目根目录的 build.gradle 文件构建之前，如果 build.gradle 同级目录中，有 buildSrc 目录，则会先构建 buildSrc 中的插件

会将 buildSrc 中的插件先依赖到根目录 build.gradle 文件中的 buildscript 之后，让根目录 build.gradle 可以使用 buildSrc 中的所有插件

**发布的插件**

当自己的 buildSrc 插件足够成熟时，可以独立抽取出来作为插件项目，发布到例如 maven 仓库中，作为公用插件供第三方使用

## 实际插件分析

以 android-sunflower 项目中的 Android Gradle Plugin 为例：

在根目录下有 build.gradle 文件，在 app 目录下有 build.gradle 文件

app 下的 build.gradle 文件执行 apply plugin: 'com.android.application' 的过程为：

要使用 com.android.application 这个插件，首先要把插件的 jar 包引入到根目录下 build.gradle 的 buildscript 依赖之中

可以看到，在根目录下 build.gradle 的 buildscript 里面，有 dependencies 这个路径：classpath "com.android.tools.build:gradle:4.0.0"

此时会下载这个路径中所包含的 jar 包文件

下载的 jar 包文件中，有一个名称为 com.android.application.properties 的文件

当构建过程中执行 apply plugin: 'com.android.application'命令时，会在根目录下 build.gradle 中所依赖的库中查找 com.android.application.properties 文件

找到该文件后，会打开该文件，发现里面只有一句话 implementation-class=com.android.build.gradle.AppPlugin%

此时就会再从根目录下 build.gradle 的 buildscript 所依赖的库中查找到 com.android.build.gradle.AppPlugin 这个类，进而调用里面的 apply 方法

以上就完成了 apply plugin: 'com.android.application' 的过程

> 参考资料：[来自Gradle开发团队的Gradle入门教程](https://www.bilibili.com/video/BV1DE411Z7nt?from=search&seid=17447930192953447238)
