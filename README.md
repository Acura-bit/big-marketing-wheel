# 项目构建

## 什么是构建？

项目构建指的是将源代码和资源文件转换为**可执行或可分发的软件制品**（如 JAR、WAR 文件）的过程。这个过程不仅包括编译代码，还包括运行测试、打包、部署等步骤。Maven 提供了一套标准化的方法来处理这些任务，使得构建过程更加自动化和可重复。如下图所示：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=ODM3NTdlYzAwYmU0YThkM2NlZThiOWMzYWQ4YWZkZTRfZmw3QUNEb3hNMHJmOU1YZVVkRzZLaWhXTE9LTXNVcnBfVG9rZW46TXFjNmJSdm5zb2tFQW94YUZxc2NtQktSbmpoXzE3MzQxNjI1NzI6MTczNDE2NjE3Ml9WNA)

构建过程的几个主要环节：

1. 清理：删除以前的编译结果，为重新编译做好准备。
2. 编译：将 Java 源程序编译为字节码文件。
3. 测试：针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性。
4. 报告：在每一次测试后，以标准的格式记录和展示测试结果。
5. 打包：将一个包含诸多文件的工程封装为一个压缩文件用于安装或部署。Java 工程对应 jar 包，Web 工程对应 war 包。
6. 安装：在 Maven 环境下特指将 jar 包安装到本地仓库中。这样该项目就可以被其他的 Maven 项目通过依赖的方式引入。
7. 部署：将 jar 包部署到私服上。

因此，上面的整个流程称为构建，而不是一个或几个步骤。

## **自动化构建**

看一个小故事：

这是阳光明媚的一天。托马斯像往常一样早早的来到了公司，冲好一杯咖啡，进入了自己的邮箱。很不幸，QA 小组发来了一封邮件，报告了他昨天提交的模块的测试结果有 BUG。"好吧，反正也不是第一 次"，托马斯摇摇头，进入 IDE，运行自己的程序，编译、打包、部署到服务器上，然后按照邮件中的操作路径进行测试。"嗯，没错，这个地方确实有问题"，托马斯说道。于是托马斯开始尝试修复这个 BUG，当他差不多有眉目的时候已经到了午饭时间。下午继续工作。BUG 很快被修正了，接着托马斯对模块重新进行了编译、打包、部署，测试之后确认没有问题了，回复了 QA 小组的邮件。 一天就这样过去了，明媚的阳光化作了美丽的晚霞，托马斯却觉得生活并不像晚霞那样美好啊。

**让我们来梳理一下托马斯这一天中的工作内容**：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=OWI3ODRmNjAyMTk2Yzc3MzA2NWU1ZjQzMDA4MWViMjhfUzBOWFJTSUQ0VnFwMGhNYzhLc3J1dW9SM0xUZE04YUFfVG9rZW46SHJXdGJpd01Bb3BCY054RGNEaGNoY2lDblBlXzE3MzQxNjI1NzI6MTczNDE2NjE3Ml9WNA)

从中我们发现，托马斯的很大一部分时间花在了"编译、打包、部署、测试"这些流程化的工作上面，而真正需要由"人"的智慧实现的分析问题和编码却只占了很少一部分。

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=OTFkZTI2NDIwOGQyYWE1ODQzYjU0MjcxNTJjNTk2OGNfR0ZqV3BEMVI1SW52SndIbUNCZW0ycDYxbDVMUmZxRGtfVG9rZW46VDVvdGJEZTRzb0twdjV4bjM0cWN4cEhSbjJjXzE3MzQxNjI1NzI6MTczNDE2NjE3Ml9WNA)

能否将这些程式化的工作交给机器自动完成呢？——当然可以！这就是自动化构建。

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=YTgyYjBmODdkNGU3MGI1MjM0NTIxNDYwZThhNjQ5MTFfZkc4cmprMk4zamhOa3lEb25jTXJIMzBld2hDeEVMa25fVG9rZW46QkRERWJKckpUbzNEQXR4ajc5TGNBNWhVbjZnXzE3MzQxNjI1NzI6MTczNDE2NjE3Ml9WNA)

此时 Maven 的意义就体现出来了，它可以自动的从构建过程的起点一直执行到终点，也就是一键式构建：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=MWM2ZDNhODgyOWUyYTU2NzRiODY2ODk1NWI4OTg0MmZfWU04Wlc0QjdJYklrZlNCTG1VUnl5ZVRMQmFNSTZ1cEhfVG9rZW46TDI2VWIwQXRzb2tBdlF4YkV1WmMwbFpQbjFkXzE3MzQxNjI1NzI6MTczNDE2NjE3Ml9WNA)

# 反应堆机制（Reactor Mechanism）

## 什么是反应堆？

Maven 中处理多模块项目的机制称为 reactor（反应堆）。作用如下：

- 收集要构建的所有可用模块
- 给收集到的模块指定一个构建顺序
- 按顺序构建选定的模块

## 反应堆如何确定多个模块的构建顺序？

由于多模块构建中的模块之间可能相互依赖，因此反应堆必须对所有项目进行排序，以确保任何项目在需要之前就已构建完成。

排序规则如下：

1. **项目依赖于构建中的另一个模块**：如果一个模块依赖于另一个模块，则被依赖的模块会被优先构建。这是确保所有依赖项在使用它们的模块之前已经被构建和安装。
2. 插件声明，其中插件是构建中的另一个模块：如果一个模块使用的插件是由构建中的另一个模块提供的，那么该插件模块将被优先构建。
3. 插件依赖于构建中的另一个模块：如果一个模块使用的插件本身依赖于构建中的另一个模块，那么这个被依赖的模块也会被优先构建。
4. 构建扩展声明为构建中的另一个模块：如果一个模块定义了构建扩展，并且这个扩展是由构建中的另一个模块提供的，那么提供扩展的模块将会被优先构建。
5. **<modules> 元素中声明的顺序**：当上述规则不适用时，即模块之间没有相互依赖关系、插件声明或构建扩展依赖的情况下，Maven 将按照`<modules>`元素中列出的顺序来构建模块。

另外，Maven 在确定反应堆排序（reactor sort order）时只会考虑"实例化"的引用。这意味着只有实际使用的依赖和插件（实际在`<dependencies>`部分声明的依赖、实际在`<build><plugins>`部分声明并使用的插件）才会影响构建顺序，而`dependencyManagement`和`pluginManagement`中声明的元素不会直接影响反应堆排序。

> 附：由于 Maven 官方或第三方提供的插件几乎能满足企业开发中需求，所以 2 ~ 4 条规则很少会涉及到。

# 需求一：构建整个项目

## 准备工作

**1、创建项目，添加子模块**

为了演示多模块项目的构建，创建了一个名为 big-marketing-wheel 的项目。

如下图所示，big-marketing-wheel 是一个多模块的 Spring Boot（Maven）项目的骨架。该骨架不包含任何业务代码。

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=YmE5ZmI1MGNjNzA2YmJmMDYzMmFhOWRhOGRhYWFjZmJfNjExRFVZY3VWa3U2R3ZFdG9nM1JEY0FXOFBxcXl1Q09fVG9rZW46WGJXTmI3c3Exb1p1bTB4WGh6WWNwTWI1bnliXzE3MzQxNjUyNDQ6MTczNDE2ODg0NF9WNA)

> 项目地址：https://github.com/Acura-bit/big-marketing-wheel.git

**2、修改父** **pom**

在各个子模块的 pom 文件中引入了一些依赖，但这些依赖不影响本节要演示的项目构建。其中，模块间相互依赖关系如下图所示。该项目只是为了演示，在实际场景中，模块间的依赖关系不会是这样的。

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=OWQzNWU0OTIxZmU5OGU2ZDdjZTc4Yjc0ZTkzNmNjNjFfZWhSeTVqOFRib0hlNFlrck5JZG8zVkxEWE9Nd3dmTGNfVG9rZW46VXlXdWIwa29Zb3RoQll4Q2JZb2NXcGNBbnBjXzE3MzQxNjUyNDQ6MTczNDE2ODg0NF9WNA)

父 pom 的自模块声明如下所示：

```XML
<modules>
    <module>big-marketing-wheel-api</module>
    <module>big-marketing-wheel-app</module>
    <module>big-marketing-wheel-domain</module>
    <module>big-marketing-wheel-trigger</module>
    <module>big-marketing-wheel-infrastructure</module>
    <module>big-marketing-wheel-types</module>
</modules>
```

## 构建项目

**1、在** **IntelliJ IDEA** **中进入到** **Terminal****，如下图所示**

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=YmNiMmZlMzIxYjkxYzk5ZDg5MTY2NDlkNGMwOTc1NTJfbHFCU0NZWmx5VXRqbjlxMUhyUkxyOXI5YmJWNkN3MEdfVG9rZW46VWN6NWI4b1dlbzRmMDh4UHllOGNrR0M0bmllXzE3MzQxNjUyNDQ6MTczNDE2ODg0NF9WNA)

其实也就是在父工程的 pom 文件，即 big-marketing-wheel\pom.xml 下执行构建命令。

我们的目的是想观察不同模块的构建顺序，可以执行任意的生命周期，这里我们执行 install 命令。

**2、执行** **mvn** **install** **命令，将整个项目打包安装到本地仓库**

项目的构建过程如所示。

```Shell
(base) PS C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel> mvn install
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] big-marketing-wheel                                                [pom]
[INFO] big-marketing-wheel-types                                          [jar]
[INFO] big-marketing-wheel-api                                            [jar]
[INFO] big-marketing-wheel-app                                            [jar]
[INFO] big-marketing-wheel-infrastructure                                 [jar]
[INFO] big-marketing-wheel-trigger                                        [jar]
[INFO] big-marketing-wheel-domain                                         [jar]
[INFO]
[INFO] ------------------< cn.myphoenix:big-marketing-wheel >------------------
[INFO] Building big-marketing-wheel 1.0-SNAPSHOT                          [1/7]
[INFO]   from pom.xml
[INFO] --------------------------------[ pom ]---------------------------------
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-whee[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-types ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-types ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\src\test\resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-types ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-types ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-types ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\big-marketing-wheel-types.jar
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-types ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\big-marketing-wheel-types.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-types\1.0-SNAPSHOT\big-marketing-wheel-types-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-types\1.0-SNAPSHOT\big-marketing-wheel-types-1.0-SNAPSHOT.pom
[INFO]
[INFO] ----------------< cn.myphoenix:big-marketing-wheel-api >----------------
[INFO] Building big-marketing-wheel-api 1.0-SNAPSHOT                      [3/7]
[INFO]   from big-marketing-wheel-api\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-api ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-api ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-api ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-api ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-api ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-api ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\target\big-marketing-wheel-api-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-api ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\target\big-marketing-wheel-api-1.0-SNAPSHOT.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-api\1.0-SNAPSHOT\big-marketing-wheel-api-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-api\1.0-SNAPSHOT\big-marketing-wheel-api-1.0-SNAPSHOT.pom
[INFO] 
[INFO] ----------------< cn.myphoenix:big-marketing-wheel-app >----------------
[INFO] Building big-marketing-wheel-app 1.0-SNAPSHOT                      [4/7]
[INFO]   from big-marketing-wheel-app\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-app ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 7 resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-app ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 6 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-app ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-app ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\target\test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:2.6:test (default-test) @ big-marketing-wheel-app ---
[INFO] Tests are skipped.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-app ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\target\big-marketing-wheel-app.jar
[INFO] 
[INFO] --- spring-boot-maven-plugin:2.7.12:repackage (repackage) @ big-marketing-wheel-app ---
[INFO] Layout: JAR
[INFO] Replacing main artifact with repackaged archive
[INFO]
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-app ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\target\big-marketing-wheel-app.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-app\1.0-SNAPSHOT\big-marketing-wheel-app-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-app\1.0-SNAPSHOT\big-marketing-wheel-app-1.0-SNAPSHOT.pom
[INFO]
[INFO] ----------< cn.myphoenix:big-marketing-wheel-infrastructure >-----------
[INFO] Building big-marketing-wheel-infrastructure 1.0-SNAPSHOT           [5/7]
[INFO]   from big-marketing-wheel-infrastructure\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-infrastructure ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-infrastructure ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 7 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-infrastructure ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-infrastructure ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-infrastructure ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-infrastructure ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\target\big-marketing-wheel-infrastructure.jar
[INFO]
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-infrastructure ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\target\big-marketing-wheel-infrastructure.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-infrastructure\1.0-SNAPSHOT\big-marketing-wheel-infrastructure-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-infrastructure\1.0-SNAPSHOT\big-marketing-wheel-infrastructure-1.0-SNAPSHOT.pom
[INFO]
[INFO] --------------< cn.myphoenix:big-marketing-wheel-trigger >--------------
[INFO] Building big-marketing-wheel-trigger 1.0-SNAPSHOT                  [6/7]
[INFO]   from big-marketing-wheel-trigger\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-trigger ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-trigger ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-trigger ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-trigger ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-trigger ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-trigger ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\target\big-marketing-wheel-trigger.jar
[INFO]
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-trigger ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\target\big-marketing-wheel-trigger.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-trigger\1.0-SNAPSHOT\big-marketing-wheel-trigger-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-trigger\1.0-SNAPSHOT\big-marketing-wheel-trigger-1.0-SNAPSHOT.pom
[INFO] 
[INFO] --------------< cn.myphoenix:big-marketing-wheel-domain >---------------
[INFO] Building big-marketing-wheel-domain 1.0-SNAPSHOT                   [7/7]
[INFO]   from big-marketing-wheel-domain\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-domain ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-domain ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 12 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-domain ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-domain ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-domain ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-domain ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\target\big-marketing-wheel-domain.jar
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-domain ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\target\big-marketing-wheel-domain.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-domain\1.0-SNAPSHOT\big-marketing-wheel-domain-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-domain\1.0-SNAPSHOT\big-marketing-wheel-domain-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for big-marketing-wheel 1.0-SNAPSHOT:
[INFO]
[INFO] big-marketing-wheel ................................ SUCCESS [  0.201 s]
[INFO] big-marketing-wheel-types .......................... SUCCESS [  2.621 s]
[INFO] big-marketing-wheel-api ............................ SUCCESS [  1.019 s]
[INFO] big-marketing-wheel-app ............................ SUCCESS [  3.252 s]
[INFO] big-marketing-wheel-infrastructure ................. SUCCESS [  0.823 s]
[INFO] big-marketing-wheel-trigger ........................ SUCCESS [  0.104 s]
[INFO] big-marketing-wheel-domain ......................... SUCCESS [  0.891 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  9.146 s
[INFO] Finished at: 2024-12-13T21:22:46+08:00
[INFO] ------------------------------------------------------------------------
```

### **结论**：

(1) 由标红处可以看到，Maven 的反应堆给出了模块的构建顺序：

- big-marketing-wheel → big-marketing-wheel-types → big-marketing-wheel-api → big-marketing-wheel-app → big-marketing-wheel-infrastructure → big-marketing-wheel-trigger → big-marketing-wheel-domain

(2) 由标绿处可以看到，每个模块的构建过程是相互独立的，并且是从 default 生命周期的头开始执行，一直到 install 阶段。

(3) 在 Maven 本地仓库找到 install 的内容，如下图所示，父工程和子工程位于不同的文件夹中。

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=NDRkZjNjMTJlYzUyMjA4ZDY5N2Q2MDUwMWY5NmU2MzJfY2lKMjMzS2pQRTc3VXVFTWNldzNMS2xFUnRqaFdKREpfVG9rZW46VkFGSWJuY1lqb0pJS3V4WFhLTGNxeUszbjBnXzE3MzQxNjUyNDQ6MTczNDE2ODg0NF9WNA)

### 思考：反应堆的模块构建顺序是如何得到的？

项目的构建顺序如下所示：

```XML
[INFO] big-marketing-wheel                                                [pom]
[INFO] big-marketing-wheel-types                                          [jar]
[INFO] big-marketing-wheel-api                                            [jar]
[INFO] big-marketing-wheel-app                                            [jar]
[INFO] big-marketing-wheel-infrastructure                                 [jar]
[INFO] big-marketing-wheel-trigger                                        [jar]
[INFO] big-marketing-wheel-domain                                         [jar]
```

解释：

由反应堆确定模块构建顺序的原则可知，在执行项目构建时，先读取父 pom 文件的 modules 标签，如果当前 module 有依赖的 module，则先构建被依赖的 module，再构建当前 module。构建过程如下：

**<module>big-marketing-wheel-api</module>**：

- api 模块依赖于 types 模块，不能直接加入构建序列；
- types 模块没有依赖的模块，则 types[1] 模块加入构建序列；
- 依赖项 types 模块已加入构建序列，则 api[2] 模块可以加入构建序列。

**<module>big-marketing-wheel-app</module>**：

- app 模块依赖于types 模块，而 types 模块已经加入到构建序列，则 app[3] 模块可以加入到构建序列。

**<module>big-marketing-wheel-domain</module>**：

- domain 模块依赖于 infrastructure、trigger，且后两者尚未加入到构建序列，所以此时不能将 domain 加入到构建序列：
  - infrastructure[4] 模块没有依赖项，可以加入到构建序列；
  - trigger[5] 模块没有依赖项，可以加入到构建序列。
- domain[6] 模块的所有依赖项已加入到构建序列，其可以加入到构建序列。

上述过程可以参照父 pom 声明子模块的顺序和子模块间的相互依赖关系，如下图所示：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=NjM0YWE5YzYyMjI3MTQ1OWM5OWE1MzYzYTQyNzI3NmVfb0F3YktZbklqbXdZaTQyZHhSZUhCcWlKM2dLelRTb0NfVG9rZW46WFcwaWI2cW9Ob0UyQkJ4U2lSZWNVRHB3blViXzE3MzQxNjUyNDQ6MTczNDE2ODg0NF9WNA)

# 需求二：构建某个子模块

> 场景：
>
> types 模块被 api 和 app 依赖，当 types 模块有修改时，我们希望 api 和 app 也能构建。只构建这 3 个模块，而不是将所有的模块都重新构建。
>
> types 模块只是被 2 个模块依赖，真实的企业项目可能会存在一个模块被很多模块依赖的情况。如果每次修改这个模块时，我们都要重新打包所有的模块，那构建时间可能会非常久。有没有什么办法实现按需构建呢？

Maven 提供了命令行选项，可以实现实现按需构建，下面介绍一些命令行选项：

## 命令行选项

### **1、-pl <arg>**

- 等价于：`--projects <arg>`。
- 作用：构建指定的模块，而不是构建整个项目。arg 表示多个模块，之间用逗号分开，模块有两种写法

```XML
# 写法一
-pl 模块1相对路径 [,模块2相对路径] [,模块n相对路径]

# 写法一
-pl [模块1的groupId]:模块1的artifactId [,[模块2的groupId]:模块2的artifactId, [模块n的groupId]:模块n的artifactId]
```

#### 演示

依旧在项目根目录 big-marketing-wheel\pom.xml 下执行构建命令

**(1) 构建 types 模块**

```Shell
# 写法一
mvn install -pl big-marketing-wheel-types

# 写法二 (1)
mvn install -pl cn.myphoenix:big-marketing-wheel-types

# 写法二 (2)
mvn install -pl :big-marketing-wheel-types
```

> 说明：虽然"写法一"和"写法二 (2)"很相似，但含义完全不同。在写法一中，"big-marketing-wheel-types" 表示该子模块的相对路径；而在写法二 (2) 中，"big-marketing-wheel-types" 则表示该子模块的的 artifactId。

上述命令的执行结果相同，如下所示：

```Shell
(base) PS C:\...\big-marketing-wheel> mvn install -pl cn.myphoenix:big-marketing-wheel-types
[INFO] Scanning for projects...
[INFO] 
[INFO] ---------------< cn.myphoenix:big-marketing-wheel-types >---------------
[INFO] Building big-marketing-wheel-types 1.0-SNAPSHOT
[INFO]
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-types ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-types ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-types ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\src\test\resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-types ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-types ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-types ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\big-marketing-wheel-types.jar
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-types ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\big-marketing-wheel-types.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-types\1.0-SNAPSHOT\big-marketing-wheel-types-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-types\1.0-SNAPSHOT\big-marketing-wheel-types-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.863 s
[INFO] Finished at: 2024-12-13T21:57:52+08:00
[INFO] ------------------------------------------------------------------------
```

**(2) 构建 domain 模块**

执行构建命令：

```Shell
mvn install -pl :big-marketing-wheel-domain
```

构建结果如下：

```Shell
(base) PS C:\...\big-marketing-wheel> mvn install -pl :big-marketing-wheel-domain
[INFO] Scanning for projects...
[INFO] 
[INFO] --------------< cn.myphoenix:big-marketing-wheel-domain >---------------
[INFO] Building big-marketing-wheel-domain 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-domain ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\main\resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-domain ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 12 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-domain ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-domain ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-domain ---
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-domain ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-domain ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\target\big-marketing-wheel-domain.jar
[INFO]
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-domain ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\target\big-marketing-wheel-domain.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-domain\1.0-SNAPSHOT\big-marketing-wheel-domain-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-domain\1.0-SNAPSHOT\big-marketing-wheel-domain-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.766 s
[INFO] Finished at: 2024-12-13T22:02:21+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到，**只构建了 domain 模块，它的依赖项 infrastructure 和 trigger 都没有构建**。

### 2、-am

等价于：`--also-make`

作用：同时构建当前模块 x 的依赖模块，也就是 **x 依赖的模块也一同构建。**

#### 演示

在前面构建 domain 模块时，我们使用了 -pl 选项，发现只构建了 domain 模块，而其依赖的 infrastructure 和 trigger 都没有构建。

现在，我们使用 -am 选项就可以将 domain 的依赖项一同完成构建：

```Shell
mvn install -pl :big-marketing-wheel-domain -am
```

构建过程如下：

```Shell
(base) PS C:\...\big-marketing-wheel> mvn install -pl :big-marketing-wheel-domain -am
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] big-marketing-wheel                                                [pom]
[INFO] big-marketing-wheel-infrastructure                                 [jar]
[INFO] big-marketing-wheel-trigger                                        [jar]
[INFO] big-marketing-wheel-domain                                         [jar]
[INFO] 
[INFO] ------------------< cn.myphoenix:big-marketing-wheel >------------------
[INFO] Building big-marketing-wheel 1.0-SNAPSHOT                          [1/4]
[INFO]   from pom.xml
[INFO] --------------------------------[ pom ]---------------------------------
[INFO] 
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel\1.0-SNAPSHOT\big-marketing-wheel-1.0-SNAPSHOT.pom
[INFO]
[INFO] ----------< cn.myphoenix:big-marketing-wheel-infrastructure >-----------
[INFO] Building big-marketing-wheel-infrastructure 1.0-SNAPSHOT           [2/4]
[INFO]   from big-marketing-wheel-infrastructure\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-infrastructure ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\src\main\resources  
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\src\main\resources  
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-infrastructure ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 7 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-infrastructure ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\src\test\resources  
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-infrastructure ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-infrastructure ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-infrastructure ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\target\big-marketing-wheel-infrastructure.jar
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-infrastructure ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\target\big-marketing-wheel-infrastructure.jar
 to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-infrastructure\1.0-SNAPSHOT\big-marketing-wheel-infrastructure-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-infrastructure\1.0-SNAPSHOT\big-marketing-wheel-infrastructure-1.0-SNAPSHOT.pom
[INFO]
[INFO] --------------< cn.myphoenix:big-marketing-wheel-trigger >--------------
[INFO] Building big-marketing-wheel-trigger 1.0-SNAPSHOT                  [3/4]
[INFO]   from big-marketing-wheel-trigger\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-trigger ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-trigger ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-trigger ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-trigger ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-trigger ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-trigger ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\target\big-marketing-wheel-trigger.jar
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-trigger ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\target\big-marketing-wheel-trigger.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-trigger\1.0-SNAPSHOT\big-marketing-wheel-trigger-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-trigger\1.0-SNAPSHOT\big-marketing-wheel-trigger-1.0-SNAPSHOT.pom
[INFO]
[INFO] --------------< cn.myphoenix:big-marketing-wheel-domain >---------------
[INFO] Building big-marketing-wheel-domain 1.0-SNAPSHOT                   [4/4]
[INFO]   from big-marketing-wheel-domain\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-domain ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-domain ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 12 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-domain ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-domain ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-domain ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-domain ---
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-domain ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\target\big-marketing-wheel-domain.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-domain\1.0-SNAPSHOT\big-marketing-wheel-domain-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-domain\1.0-SNAPSHOT\big-marketing-wheel-domain-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for big-marketing-wheel 1.0-SNAPSHOT:
[INFO]
[INFO] big-marketing-wheel ................................ SUCCESS [  0.169 s]
[INFO] big-marketing-wheel-infrastructure ................. SUCCESS [  2.357 s]
[INFO] big-marketing-wheel-trigger ........................ SUCCESS [  0.208 s]
[INFO] big-marketing-wheel-domain ......................... SUCCESS [  1.029 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  3.997 s
[INFO] Finished at: 2024-12-13T22:15:01+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到，**不仅构建了 domain 模块，该模块的依赖项 infrastructure 和 trigger 也一同完成了构建**。

### 3、-amd

- 等价于：`--also-make-dependents`
- 作用：同时构建依赖于当前模块 x 的其他模块，就是**哪些模块依赖了 x 模块，也一同构建**

#### 演示

使用此选项可以实现场景中提到的需求，即构建 types 模块，可以将 api 和 app 一同构建，而且不需要重新构建整个项目。

```Shell
mvn install -pl :big-marketing-wheel-types -amd
```

构建过程如下：

```Shell
(base) PS C:\...\big-marketing-wheel> mvn install -pl :big-marketing-wheel-types -amd
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] big-marketing-wheel-types                                          [jar]
[INFO] big-marketing-wheel-api                                            [jar]
[INFO] big-marketing-wheel-app                                            [jar]
[INFO]
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-types ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-types ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\src\test\resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-types ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-types ---
[INFO] No tests to run.
[INFO] 
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-types ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\big-marketing-wheel-types.jar
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-types ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\big-marketing-wheel-types.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-types\1.0-SNAPSHOT\big-marketing-wheel-types-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-types\1.0-SNAPSHOT\big-marketing-wheel-types-1.0-SNAPSHOT.pom
[INFO] 
[INFO] ----------------< cn.myphoenix:big-marketing-wheel-api >----------------
[INFO] Building big-marketing-wheel-api 1.0-SNAPSHOT                      [2/3]
[INFO]   from ..\big-marketing-wheel-api\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-api ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-api ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-api ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-api ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-api ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-api ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\target\big-marketing-wheel-api-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-api ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\target\big-marketing-wheel-api-1.0-SNAPSHOT.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-api\1.0-SNAPSHOT\big-marketing-wheel-api-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-api\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-api\1.0-SNAPSHOT\big-marketing-wheel-api-1.0-SNAPSHOT.pom
[INFO]
[INFO] ----------------< cn.myphoenix:big-marketing-wheel-app >----------------
[INFO] Building big-marketing-wheel-app 1.0-SNAPSHOT                      [3/3]
[INFO]   from ..\big-marketing-wheel-app\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-app ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 7 resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-app ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 6 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-app ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-app ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\target\test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:2.6:test (default-test) @ big-marketing-wheel-app ---
[INFO] Tests are skipped.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-app ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\target\big-marketing-wheel-app.jar
[INFO] 
[INFO] --- spring-boot-maven-plugin:2.7.12:repackage (repackage) @ big-marketing-wheel-app ---
[INFO] Layout: JAR
[INFO] Replacing main artifact with repackaged archive
[INFO]
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-app ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\target\big-marketing-wheel-app.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-app\1.0-SNAPSHOT\big-marketing-wheel-app-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-app\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-app\1.0-SNAPSHOT\big-marketing-wheel-app-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for big-marketing-wheel-types 1.0-SNAPSHOT:
[INFO]
[INFO] big-marketing-wheel-types .......................... SUCCESS [  2.766 s]
[INFO] big-marketing-wheel-api ............................ SUCCESS [  1.059 s]
[INFO] big-marketing-wheel-app ............................ SUCCESS [  3.719 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  7.766 s
[INFO] Finished at: 2024-12-13T22:20:44+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到，**在构建 types 模块时，依赖 types 模块的 api 和 app 也完成了构建**。

### 4、-rf <arg>

- 等价于：`--resume-from <arg>`
- 作用：跳过所有在此之前已经成功构建的模块，并从指定的模块开始继续构建。

#### 演示

在"需求一：构建整个项目"一节中，模块的构建顺序如下所示：

```XML
[INFO] big-marketing-wheel                                                [pom]
[INFO] big-marketing-wheel-types                                          [jar]
[INFO] big-marketing-wheel-api                                            [jar]
[INFO] big-marketing-wheel-app                                            [jar]
[INFO] big-marketing-wheel-infrastructure                                 [jar]
[INFO] big-marketing-wheel-trigger                                        [jar]
[INFO] big-marketing-wheel-domain                                         [jar]
```

加入在构建 infrastructure 模块时失败了，现在我们想从 infrastructure 继续构建，而不是从头开始构建，有什么办法吗？

可以使用 -rf 选项，从指定模块开始构建：

```Shell
mvn install -rf :big-marketing-wheel-infrastructure
```

构建过程如下：

```Shell
(base) PS C:\...\big-marketing-wheel> mvn install -rf :big-marketing-wheel-infrastructure
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] big-marketing-wheel-infrastructure                                 [jar]
[INFO] big-marketing-wheel-trigger                                        [jar]
[INFO] big-marketing-wheel-domain                                         [jar]
[INFO] 
[INFO] ----------< cn.myphoenix:big-marketing-wheel-infrastructure >-----------
[INFO] Building big-marketing-wheel-infrastructure 1.0-SNAPSHOT           [1/3]
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-infrastructure ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\src\main\resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-infrastructure ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 7 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-infrastructure ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-infrastructure ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-infrastructure ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-infrastructure ---
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-infrastructure ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\target\big-marketing-wheel-infrastructure.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-infrastructure\1.0-SNAPSHOT\big-marketing-wheel-infrastructure-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-infrastructure\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-infrastructure\1.0-SNAPSHOT\big-marketing-wheel-infrastructure-1.0-SNAPSHOT.pom
[INFO]
[INFO] --------------< cn.myphoenix:big-marketing-wheel-trigger >--------------
[INFO] Building big-marketing-wheel-trigger 1.0-SNAPSHOT                  [2/3]
[INFO]   from ..\big-marketing-wheel-trigger\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-trigger ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-trigger ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-trigger ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-trigger ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-trigger ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-trigger ---
[INFO]
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-trigger ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\target\big-marketing-wheel-trigger.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-trigger\1.0-SNAPSHOT\big-marketing-wheel-trigger-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-trigger\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-trigger\1.0-SNAPSHOT\big-marketing-wheel-trigger-1.0-SNAPSHOT.pom
[INFO] 
[INFO] --------------< cn.myphoenix:big-marketing-wheel-domain >---------------
[INFO] Building big-marketing-wheel-domain 1.0-SNAPSHOT                   [3/3]
[INFO]   from ..\big-marketing-wheel-domain\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-domain ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\main\resources
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-domain ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 12 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-domain ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-domain ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-domain ---
[INFO] No tests to run.
[INFO]
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-domain ---
[INFO]
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ big-marketing-wheel-domain ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\target\big-marketing-wheel-domain.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-domain\1.0-SNAPSHOT\big-marketing-wheel-domain-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-domain\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\big-marketing-wheel-domain\1.0-SNAPSHOT\big-marketing-wheel-domain-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for big-marketing-wheel-infrastructure 1.0-SNAPSHOT:
[INFO]
[INFO] big-marketing-wheel-infrastructure ................. SUCCESS [  2.218 s]
[INFO] big-marketing-wheel-trigger ........................ SUCCESS [  0.177 s]
[INFO] big-marketing-wheel-domain ......................... SUCCESS [  0.947 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  3.574 s
[INFO] Finished at: 2024-12-14T10:17:49+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到，**确实是从 infrastructure 模块开始构建的**。

> 附：需要指出的是，在"构建某个子模块"一节中，笔者举得例子都是执行 install。但在"项目构建"一节中，构建指的是从清理到部署的整个流程，也就是整个生命周期。由于 install 也是生成了 jar 包，所以也就当做构建了。

#### 思考 1

使用 -rf 指定开始恢复的模块后，就一定会从此模块开始构建吗？

1、将安装到本地 Maven 仓库的三个文件夹删除：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=MjA5ZWFmMzcxY2JjNThlYzc2MjhjYmFiMjJiYjM3NmZfMG9GOUZrVDg2VDFYenNPck5KUTdod2k5NnhsSmJDWlpfVG9rZW46UjJHZ2JQalZ6b29IZHh4NjV1SGN1cjBrbmhmXzE3MzQxNjU0OTM6MTczNDE2OTA5M19WNA)

2、执行命令

```Shell
mvn install -rf :big-marketing-wheel-domain
```

构建过程如下：

```Shell
(base) PS C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel> mvn install -rf big-marketing-wheel-domain
[INFO] Scanning for projects...
[INFO] 
[INFO] --------------< cn.myphoenix:big-marketing-wheel-domain >---------------
[INFO] Building big-marketing-wheel-domain 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[WARNING] The POM for cn.myphoenix:big-marketing-wheel-infrastructure:jar:1.0-SNAPSHOT is missing, no dependency information available
[WARNING] The POM for cn.myphoenix:big-marketing-wheel-trigger:jar:1.0-SNAPSHOT is missing, no dependency information available
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.410 s
[INFO] Finished at: 2024-12-14T10:38:59+08:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal on project big-marketing-wheel-domain: 
Could not resolve dependencies for project cn.myphoenix:big-marketing-wheel-domain:jar:1.0-SNAPSHOT: 
The following artifacts could not be resolved: cn.myphoenix:big-marketing-wheel-infrastructure:jar:1.0-SNAPSHOT, 
cn.myphoenix:big-marketing-wheel-trigger:jar:1.0-SNAPSHOT: Could not find artifact cn.myphoenix:big-marketing-wheel-infrastructure:jar:1.0-SNAPSHOT -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/DependencyResolutionException
```

可以看到发生了错误，错误原因是 domain 依赖 infrastructure 和 trigger，而本地 Maven 仓库的 infrastructure 和 trigger 已经被删除了，domain 找不到，所以报错。

因此，**-rf 确实是从指定模块 x 开始构建的**。如果模块 x 的所有依赖项都已经构建成功，则模块 x 构建成功；否则，模块 x 构建失败。

一般情况下，我们使用 -rf 的目的是从指定模块开始构建，**默认指定模块 x 依赖的模块已经构建成功**。

#### 思考 2

另外，我又想到了一个问题，如果模块 m 上一次构建成功，假设为打包操作。紧接着：

- 情况 1：再次执行打包操作
- 情况 2：修改了模块 a 的内容，然后再次打包

Maven 会如何处理这两种情况？

**准备工作**：

在 types 模块中进行实验，types 模块的结构如下所示：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=YzFkNmMzNjNkZjFmYmZlZmRhYjNjMjM3YWNhZDUwZGZfODR4WHBvcDFnMU14TEVFUFpzaUZQZHpvdjd0aEhzMlRfVG9rZW46WDhOMmJVVUNpb09ONml4WERmN2NwYW1vbkViXzE3MzQxNjU0OTM6MTczNDE2OTA5M19WNA)

**实验步骤**：

1、创建 3 个类

- BasketballSports：用于演示文件内增加代码
- FootballSports：用于演示文件内减少代码
- TennisSports：用于演示删除整个文件。

2、只打包 types 模块

```Shell
mvn package -pl big-marketing-wheel-types
```

3、修改类文件

- 在 BasketballSports#playBasketball 内添加代码
- 在 FootballSports#playFootball 内删减代码
- 删除 TennisSports
- 增加 VolleyballSports 类

4、再次打包 types 模块

```Shell
mvn package -pl big-marketing-wheel-types
```

结果如下图所示，可以看到，Maven 进行了重新遍历，并重新打包。**说明了情况 2。**

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=M2E4NDBkZmQ5MGRjYWM5YmM5MWNjNGY3MzYzOTQ3ZjRfNVB4MVU1OTA2Qmxaa09tNlY4SHVGTDROY1hrdG1LVGJfVG9rZW46SHAxWmIyZDVRb2o3NGt4bk82aWNuaWYwbkFoXzE3MzQxNjU0OTM6MTczNDE2OTA5M19WNA)

另外， 也可以从命令行的日志观察：

```Shell
(base) PS C:\...\big-marketing-wheel> mvn package -pl big-marketing-wheel-types
[INFO] Scanning for projects...
[INFO] 
[INFO] ---------------< cn.myphoenix:big-marketing-wheel-types >---------------
[INFO] Building big-marketing-wheel-types 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-types ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 0 resource
[INFO] Copying 0 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-types ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-types ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 0 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-types ---
[INFO] Changes detected - recompiling the module!
[INFO] 
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-types ---
[INFO] 
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-types ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\big-marketing-wheel-types.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-types ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 0 resource
[INFO] Copying 0 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-types ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-types ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 0 resource
[INFO] 
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-types ---
[INFO] Changes detected - recompiling the module!
[INFO] 
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-types ---
[INFO] 
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-types ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\big-marketing-wheel\big-marketing-wheel-types\target\big-marketing-wheel-types.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.750 s
[INFO] Finished at: 2024-12-14T15:15:20+08:00
[INFO] ------------------------------------------------------------------------
```

由标绿部分可以看到，compiler 插件检测到了代码发生了改变，重新进行了编译；即使没有执行 clean 操作，jar 插件依旧重新进行了打包操作。

5、不做任何更改，再次打包 types 模块

```Shell
mvn package -pl big-marketing-wheel-types
```

结果如下：

```Shell
(base) PS C:\...\big-marketing-wheel> mvn package -pl big-marketing-wheel-types
[INFO] Scanning for projects...
[INFO] 
[INFO] ---------------< cn.myphoenix:big-marketing-wheel-types >---------------
[INFO] Building big-marketing-wheel-types 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ big-marketing-wheel-types ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 0 resource
[INFO] Copying 0 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.0:compile (default-compile) @ big-marketing-wheel-types ---
[INFO] Nothing to compile - all classes are up to date
[INFO]
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ big-marketing-wheel-types ---
[debug] execute contextualize
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 0 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.0:testCompile (default-testCompile) @ big-marketing-wheel-types ---
[INFO] Nothing to compile - all classes are up to date
[INFO] 
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ big-marketing-wheel-types ---
[INFO] 
[INFO] --- maven-jar-plugin:3.2.2:jar (default-jar) @ big-marketing-wheel-types ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.291 s
[INFO] Finished at: 2024-12-14T15:31:13+08:00
[INFO] ------------------------------------------------------------------------
```

由标绿处可以看到，既没有重新编译，也没有重新打包。**说明了情况 1。**

> 注：在实际开发场景中，我们在 compile、package、install、deploy 时，会先执行 clean。

**参考**：

- https://www.jb51.net/article/223600.htm
- https://juejin.cn/post/7359083767564238863?searchId=202412131535127F99F7999BFA089607E3
- https://maven.apache.org/guides/mini/guide-multiple-modules.html?spm=5176.28103460.0.0.297c5d275vOOxH
- https://juejin.cn/post/7130089666115534855/