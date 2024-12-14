# Maven生命周期

Maven 将项目的生命周期抽象成了 3 套生命周期，每套生命周期又包含多个阶段（phase）。每套中具体包含哪些阶段是 Maven 已经约定好的，但是每个阶段具体需要做什么，是用户可以自己指定的。

Maven 中定义的 3 套生命周期：

- **clean 生命周期**：清理项目，包含 3 个阶段，例如 clean。
- **default 生命周期**：构建应用，包含了 23 个阶段，例如 process-resources、compile、process-test-resources、test-compile、test、package、install、deploy。
- **site 生命周期**：建立和发布项目站点，包含了 4 个阶段。

说明：

- 3 套生命周期是相互独立的，没有依赖关系。
- 每套生命周期中有多个阶段，每套中的多个阶段是有先后顺序的，并且后面的阶段依赖于前面的阶段。
- 用户可以直接使用`mvn 阶段`命令来调用这些阶段去完成项目生命周期中具体的操作。
- 生命周期只是定义了项目构建的流程，实际起作用的是 Maven 插件。通过将插件目标绑定到阶段，执行具体的任务。例如将 maven-surefire-plugin 的 clean 目标绑定到 clean 生命周期的 clean 阶段，就可以完成清理项目的任务。

## clean生命周期

clean 生命周期的目的是清理项目，它包含 3 个阶段：

| **生命周期阶段** | **描述**                                | **绑定的插件:目标**      | **执行的任务**               |
| ---------------- | --------------------------------------- | ------------------------ | ---------------------------- |
| pre-clean        | 在实际清理项目之前，执行所需的预备过程  |                          |                              |
| clean            | 移除所有由上次构建生成的文件            | maven-clean-plugin:clean | 移除所有上一次构建生成的文件 |
| post-clean       | 执行一些需要在 clean 之后立刻完成的工作 |                          |                              |

说明：

- clean 周期中只有 clean 阶段默认绑定了`maven-clean-plugin`插件的`clean`目标。`maven-clean-plugin`插件的`clean`目标作用就是删除项目的输出目录。
- `mvn clean`命令中的 clean 指的是 clean 阶段。该命令是调用 clean 生命周期的 clean 阶段，实际执行的阶段为 clean 生命周期中的 pre-clean 和 clean 阶段。由上表格可知，只有`clean`阶段绑定了`maven-clean-plugin`插件的`clean`目标，所以运行`mvn clean`的时候，实际上会调用`maven-clean-plugin`插件的`clean`目标来移除所有上一次构建生成的文件。

## default生命周期

default 是 Maven 主要的生命周期，用于构建应用，包含了 23 个阶段。

| **生命周期阶段**        | **描述**                                                     | **绑定的插件:目标**                  | **执行的任务**                                               |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------ | ------------------------------------------------------------ |
| validate                | 验证项目的正确性并确保所有必要的信息都已就绪                 |                                      |                                                              |
| initialize              | 初始化构建状态，例如设置属性或创建目录                       |                                      |                                                              |
| generate-sources        | 生成要包含在编译中的源代码                                   |                                      |                                                              |
| process-sources         | 处理源代码，例如过滤其中的值                                 |                                      |                                                              |
| generate-resources      | 生成要包含在打包中的资源文件                                 |                                      |                                                              |
| process-resources       | 将资源文件复制并处理到目标目录，为打包做好准备               | maven-resources-plugin:resources     | 复制主资源文件到主输出目录：将 src/main/resources 目录下的资源文件复制到 target/classes 目录 |
| compile                 | 编译项目的源代码                                             | maven-compiler-plugin:compile        | 编译主代码至主输出目录：编译 src/main/java 目录下的源代码至  target/classes 目录 |
| process-classes         | 后处理编译生成的文件，例如对 Java 类进行字节码增强           |                                      |                                                              |
| generate-test-sources   | 生成要包含在编译中的测试源代码                               |                                      |                                                              |
| process-test-sources    | 处理测试源代码，例如过滤其中的值                             |                                      |                                                              |
| generate-test-resources | 创建用于测试的资源文件                                       |                                      |                                                              |
| process-test-resources  | 将资源文件复制并处理到测试目标目录                           | maven-resources-plugin:testResources | 复制测试资源文件到测试输出目录：将 src/test/resources 目录下的资源文件复制到 target/test-classes 目录 |
| test-compile            | 将测试源代码编译到测试目标目录                               | maven-compiler-plugin:testCompile    | 编译测试代码至测试输出目录：编译 src/test/java 目录下的测试源代码至  target/test-classes 目录 |
| process-test-classes    | 后处理测试编译生成的文件，例如对 Java 类进行字节码增强       |                                      |                                                              |
| test                    | 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署 | maven-surefire-plugin:test           | 执行测试用例                                                 |
| prepare-package         | 执行打包前所必需的任何操作。这通常会产生一个未打包、已处理的包版本 |                                      |                                                              |
| package                 | 将编译后的代码打包成可分发的格式，例如 JAR 文件              | maven-jar-plugin:jar                 | 创建项目 jar 包                                              |
| pre-integration-test    | 在执行集成测试之前，需要进行一些准备工作，这可能包括设置所需的环境。 |                                      |                                                              |
| integration-test        | 将包处理并部署到可以运行集成测试的环境中。                   |                                      |                                                              |
| post-integration-test   | 在集成测试执行完毕后，进行必要的后续操作。这可能包括清理环境。 |                                      |                                                              |
| verify                  | 执行任何必要的检查以验证打包的有效性，并确保其符合质量标准   |                                      |                                                              |
| install                 | 将打包好的文件安装到本地仓库，以便作为依赖被其他本地项目使用 | maven-install-plugin:install         | 将输出构件安装到本地仓库；后一次会覆盖前一次                 |
| deploy                  | 在集成或发布环境中，将最终的包复制到远程仓库（或私服），以便与其他开发者和项目共享 | maven-deploy-plugin:deploy           | 将输出的构件部署到远程仓库（或私服）                         |

> 附：上面列出的是打 jar 包和 war 包时的默认绑定，官方文档指明了不同的打包方式有不同的绑定，又如 pom 项目的打包绑定如下：
>
> ![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=NjE2ODg4ZWJiYzk0NTEyMDI1OWNkYjFmNjhjNjM5MTVfU0NRZjQ0YkhFdzZOREUzd0p4dkM0bExHa0JUN0dpMDVfVG9rZW46SWdjOGJCT0Rrb202RnZ4ZHhvbWNBVlJXbnNQXzE3MzQwMTEzNTI6MTczNDAxNDk1Ml9WNA)
>
> 官方文档：https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Built-in_Lifecycle_Bindings

### 演示

在目录结构中，既有源码、资源文件，也有测试代码、测试资源文件，如下图所示：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=ZTQzYTIwNjE0YWMxZWQxOTA3NWRjM2Y5YWM2NzQ1ZDBfbGVoUmNyU1BLTkdNbm5UUWRTQ0NtR3NkNGp6QVNYVnBfVG9rZW46TFJDNWJpY0hxb3BnOVd4Q0h6NmNCWVRrbkpmXzE3MzQwMTEzODI6MTczNDAxNDk4Ml9WNA)

**1、****mvn** **clean**

**说明**：该命令是调用 clean 生命周期的 clean 阶段，实际执行的阶段为 clean 生命周期中的 pre-clean 和 clean 阶段。但只有 clean 阶段绑定了 maven-clean-plugin:clean。

**实验**：执行命令后，查看控制台：

```Shell
(base) PS C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00> mvn clean
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ maven_00 ---
[INFO] Deleting C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.259 s
[INFO] Finished at: 2024-12-03T21:07:29+08:00
[INFO] ------------------------------------------------------------------------
```

**结果**：可以看到，执行上述命令会调用 maven-clean-plugin 的 clean 目标，将 maven_00/target 目录删除。

**2、****mvn** **compile**

**说明**：该命令调用 default 生命周期的 test 阶段，会从 default 生命周期的第一个阶段 validate 开始执行一直到 compile 阶段结束，如下图所示，执行的任务包含了复制资源文件、代码的编译，有 2 个阶段绑定了插件目标：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=OTFmNmQ4MmUxNTZiMzFiODNhN2M4MzAwOTFjZWE0NGJfeFlMR1Z3VTBIVzZwZFA4VE1GZWJoWHl1V1ZXMUxyb2RfVG9rZW46VnBLMWJKeXUxb1Z4eFF4YURPTGNhWHIxbkZlXzE3MzQwMTEzODI6MTczNDAxNDk4Ml9WNA)

**实验**：执行命令，查看控制台：

```Shell
(base) PS C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00> mvn compile
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 3 resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\classes
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.865 s
[INFO] Finished at: 2024-12-03T21:12:33+08:00
[INFO] ------------------------------------------------------------------------
```

**结果**：可以看到，执行上述命令会调用 maven-resources-plugin:resources 插件目标和 maven-compiler-plugin:3.1:compile 插件目标：

- `maven-resources-plugin:resources`：将 src\main\resources 下的 3 个资源文件复制到 target\classes 目录下。
- `maven-compiler-plugin:3.1:compile`：编译 src\main\java\cn\myphoenix\service 下的源代码到 maven_00\target\classes\cn\myphoenix 目录下。

> 附：执行`mvn compile`命令和双击 Lifecycle -> compile 一样，无论使用哪种方式，都会发现，没有执行 clean 阶段，这也**从侧面印证了 Maven 的 3 个生命周期相互独立、互不依赖**。

**3、****mvn** **test**

**说明**：该命令调用 default 生命周期的 test 阶段，实际上会从 default 生命周期的第一个阶段 validate 开始执行一直到 test 阶段结束，如下图所示，执行的任务包含了复制主资源文件、源代码的编译、复制测试资源文件、测试代码编译、运行测试用例。

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=OWM4MzQ2NzBiZDgwMTE1Yzk3NGRhNzA3ZWVjMmFlM2RfNkxyaWlFN2N4S3JVZHVvSlBXbmpoS2NHN0tKUEhzSEFfVG9rZW46Rzk5cmI2NnNOb3UxSEV4M2ZpcmNrNkZibk9oXzE3MzQwMTEzODI6MTczNDAxNDk4Ml9WNA)

**实验**：执行命令，查看控制台：

```Shell
(base) PS C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00> mvn test
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 3 resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ maven_00 ---
[INFO] Nothing to compile - all classes are up to date
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ maven_00 ---
[INFO] Nothing to compile - all classes are up to date
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ maven_00 ---
[INFO] Surefire report directory: C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running service.BallSportsTest
Everybody up!
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.063 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.011 s
[INFO] Finished at: 2024-12-03T21:53:47+08:00
[INFO] ------------------------------------------------------------------------
```

**结果**：可以看到，执行上述命令会调用 5 个插件目标：

- `maven-resources-plugin:resources`：将 src\main\resources 下的 3 个资源文件复制到 target\classes 目录下。
- `maven-compiler-plugin:compile`：编译 src\main\java\cn\myphoenix\service 下的源代码到 maven_00\target\classes\cn\myphoenix 目录下。
- `maven-resources-plugin:testResources`：将 src\test\resources 下的 1 个资源文件复制到 target\test-classes 目录下。
- `maven-compiler-plugin:testCompile`：编译 src\test\java\service下的测试代码到 maven_00\target\test-classes\service 目录下。
- `maven-surefire-plugin:test`：执行 BallSportsTest 单元测试。

**4、****mvn** **clean** **install**

**说明**：这是一个组合命令，其涉及到了两套生命周期：clean 和 default。这个命令会先从 clean 生命周期中的 pre-clean 阶段开始执行一直到 clean 生命周期的 clean 阶段；然后会继续从 default 生命周期的 validate 阶段开始执行一直到 default 生命周期的 install 阶段。

这里面包含了清理上次构建的结果、编译代码、测试、打包、将打好的包安装到本地仓库等步骤。

**回顾**：在执行此命令前，已经执行过 mvn package 命令，所以现在项目生成了一个输出目录，目录下有编译后的 class 文件、资源文件、jar 包，如下图所示：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=Zjg5NTllZmEyODk1MTZlZTNjYWU4YjQzMGE3N2JjNTFfODZINEh0TXZTN0FWbG9tTEliS1htaEJDQjNLeU05blRfVG9rZW46WmU3OWJCcUxFb2lVb0N4dHlEZ2NWNW0xblJjXzE3MzQwMTEzODI6MTczNDAxNDk4Ml9WNA)

**实验**：执行 mvn clean install 命令，查看控制台：

```Shell
(base) PS C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00> mvn clean install
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ maven_00 ---
[INFO] Deleting C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 3 resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ maven_00 ---
[INFO] Surefire report directory: C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running service.BallSportsTest
Everybody up!
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.063 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO]
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ maven_00 ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\maven_00-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ maven_00 ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\maven_00-1.0-SNAPSHOT.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\maven_00\1.0-SNAPSHOT\maven_00-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\maven_00\1.0-SNAPSHOT\maven_00-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.708 s
[INFO] Finished at: 2024-12-04T14:55:34+08:00
[INFO] ------------------------------------------------------------------------
```

**结果**：可以看到，执行上述命令会调用 8 个插件目标：

- maven-clean-plugin:clean 清理 target 文件夹
- maven-resources-plugin:resources 将 src\main\resources 下的 3 个资源文件复制到 target\classes 目录下。
- maven-compiler-plugin:compile 编译 src\main\java\cn\myphoenix\service 下的源代码到 maven_00\target\classes\cn\myphoenix 目录下。
- maven-resources-plugin:testResources 将 src\test\resources 下的 1 个资源文件复制到 target\test-classes 目录下。
- maven-compiler-plugin:testCompile 编译 src\test\java\service下的测试代码到 maven_00\target\test-classes\service 目录下。
- maven-surefire-plugin:test 执行 BallSportsTest.java 单元测试。
- 执行单元测试……
- maven-jar-plugin:jar 将 target\classes 目录下编译的源代码和资源文件打包成 maven_00-1.0-SNAPSHOT.jar，放到 target 目录下。
- maven-install-plugin:install 将 maven_00-1.0-SNAPSHOT.jar 和 maven_00-1.0-SNAPSHOT.pom 安装到 Maven 本地仓库的 cn\myphoenix\maven_00\1.0-SNAPSHOT 目录下。

**5、mvn clean deploy**

**说明**：这个命令会先按顺序执行 clean 生命周期的 [pre-clean, clean] 这个闭区间内所有的阶段；然后按序执行 default 生命周期的 [validate, deploy] 这个闭区间内的所有阶段（也就是 default 生命周期中的所有阶段）。这个命令内部包含了清理上次构建的结果、编译代码、运行单元测试、打包、将打好的包安装到本地仓库、将打好的包发布到私服等步骤。

**准备工作**：因为我在本地搭建了 Nexus 私服，所以需要在 pom.xml 文件中配置用于部署的信息，如下：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=NmJkYjNmMjMwYThjOWMxNjdlODZjMWZhNjZjMTQ1NTJfTmdQRE10UjZVU1RyV1FnbFFwT1hURTd3a2swWUJtdXNfVG9rZW46QW1XUGJlT0ZDb3gzTHB4ZEVCYWNhclk2bmRkXzE3MzQwMTEzODI6MTczNDAxNDk4Ml9WNA)

**实验**：执行 mvn clean deploy 命令，查看控制台：

```Shell
(base) PS C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00> mvn clean deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ maven_00 ---
[INFO] Deleting C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 3 resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ maven_00 ---

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running service.BallSportsTest
Everybody up!
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.065 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] 
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ maven_00 ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\maven_00-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ maven_00 ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\maven_00-1.0-SNAPSHOT.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\maven_00\1.0-SNAPSHOT\maven_00-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\maven_00\1.0-SNAPSHOT\maven_00-1.0-SNAPSHOT.pom
[INFO]
[INFO] --- maven-deploy-plugin:2.7:deploy (default-deploy) @ maven_00 ---
Downloading from myphoenix-nexus: http://localhost:8081/repository/myphoenix-snapshot/cn/myphoenix/maven_00/1.0-SNAPSHOT/maven-metadata.xml
Uploading to myphoenix-nexus: http://localhost:8081/repository/myphoenix-snapshot/cn/myphoenix/maven_00/1.0-SNAPSHOT/maven_00-1.0-20241204.071543-1.jar
Uploaded to myphoenix-nexus: http://localhost:8081/repository/myphoenix-snapshot/cn/myphoenix/maven_00/1.0-SNAPSHOT/maven_00-1.0-20241204.071543-1.jar (4.4 kB at 28 kB/s)
Uploading to myphoenix-nexus: http://localhost:8081/repository/myphoenix-snapshot/cn/myphoenix/maven_00/1.0-SNAPSHOT/maven_00-1.0-20241204.071543-1.pom
Uploaded to myphoenix-nexus: http://localhost:8081/repository/myphoenix-snapshot/cn/myphoenix/maven_00/1.0-SNAPSHOT/maven_00-1.0-20241204.071543-1.pom (1.1 kB at 8.6 kB/s)
Downloading from myphoenix-nexus: http://localhost:8081/repository/myphoenix-snapshot/cn/myphoenix/maven_00/maven-metadata.xml
Uploading to myphoenix-nexus: http://localhost:8081/repository/myphoenix-snapshot/cn/myphoenix/maven_00/1.0-SNAPSHOT/maven-metadata.xml
Uploaded to myphoenix-nexus: http://localhost:8081/repository/myphoenix-snapshot/cn/myphoenix/maven_00/1.0-SNAPSHOT/maven-metadata.xml (764 B at 9.4 kB/s)
Uploading to myphoenix-nexus: http://localhost:8081/repository/myphoenix-snapshot/cn/myphoenix/maven_00/maven-metadata.xml
Uploaded to myphoenix-nexus: http://localhost:8081/repository/myphoenix-snapshot/cn/myphoenix/maven_00/maven-metadata.xml (278 B at 4.6 kB/s)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  4.596 s
[INFO] Finished at: 2024-12-04T15:15:47+08:00
[INFO] ------------------------------------------------------------------------
```

**结果**：可以看到，执行上述命令会调用 9 个插件目标：

- maven-clean-plugin:clean
- maven-resources-plugin:resources
- maven-compiler-plugin:compile
- maven-resources-plugin:testResources
- maven-compiler-plugin:testCompile
- maven-surefire-plugin:test
- 执行单元测试……
- maven-jar-plugin:jar
- maven-install-plugin:install
- maven-deploy-plugin:2.7:deploy 将 maven_00-1.0-20241204.071543-1.jar 和 maven_00-1.0-20241204.071543-1.pom 等部署到了 Nexus 私服的 myphoenix-snapshot 快照仓库。如下图所示：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=MjcyMTAwMjJlYThhNGE4ZTI2Y2UzZmRjZjJmMDI3OWJfZW1GNkZrRkFCU3pSQjBnTEdUOWFOaHFTTDlCZ3N1VzJfVG9rZW46S2NSamJNME1qb0hlbVJ4b2k5TWNiT1o1bmZlXzE3MzQwMTEzODI6MTczNDAxNDk4Ml9WNA)

## site生命周期

site 生命周期的目的是建立和发布项目站点，Maven 能够基于 pom.xml 文件包含的信息，自动生成一个友好的站点，方便团队交流和发布项目信息。该生命周期主要包含以下 4 个阶段：

| **生命周期阶段** | **描述**                                               | **绑定的插件:目标**      | **执行的任务**                         |
| ---------------- | ------------------------------------------------------ | ------------------------ | -------------------------------------- |
| pre-site         | 执行项目站点生成前的必要准备工作                       |                          |                                        |
| site             | 生成项目的站点文档                                     | maven-site-plugin:site   | 生成项目的站点文档                     |
| post-site        | 执行完成站点生成所需的最终处理过程，并为站点部署做准备 |                          |                                        |
| site-deploy      | 将生成的站点文档部署到指定的网页服务器                 | maven-site-plugin:deploy | 将生成的站点文档部署到指定的网页服务器 |

### 演示

> 执行`mvn site`命令，查看生成的站点文档。

执行 mvn site 报错：

```Shell
(base) PS C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00> mvn site
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-site-plugin:3.3:site (default-site) @ maven_00 ---
[WARNING] Report plugin org.apache.maven.plugins:maven-project-info-reports-plugin has an empty version.
[WARNING]
[WARNING] It is highly recommended to fix these problems because they threaten the stability of your build.
[WARNING]
[WARNING] For this reason, future Maven versions might no longer support building such malformed projects.
[INFO] configuring report plugin org.apache.maven.plugins:maven-project-info-reports-plugin:3.8.0
[WARNING] Error injecting: org.apache.maven.report.projectinfo.CiManagementReport
java.lang.NoClassDefFoundError: org/apache/maven/doxia/siterenderer/DocumentContent
    at java.lang.Class.getDeclaredConstructors0 (Native Method)
    at java.lang.Class.privateGetDeclaredConstructors (Class.java:2671)
    at java.lang.Class.getDeclaredConstructors (Class.java:2020)
    at com.google.inject.spi.InjectionPoint.forConstructorOf (InjectionPoint.java:245)
    ……
    at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode (Launcher.java:406)
    at org.codehaus.plexus.classworlds.launcher.Launcher.main (Launcher.java:347)
Caused by: java.lang.ClassNotFoundException: org.apache.maven.doxia.siterenderer.DocumentContent
    at org.codehaus.plexus.classworlds.strategy.SelfFirstStrategy.loadClass (SelfFirstStrategy.java:50)
    ……
[ERROR] urls[60] = file:/C:/Softwares/Developer_Kits/apache-maven-3.8.8/repository/org/mortbay/jetty/servlet-api/2.5-20081211/servlet-api-2.5-20081211.jar
[ERROR] : org.apache.maven.doxia.siterenderer.DocumentContent
[ERROR] -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/PluginContainerException
```

报错原因：找不到 org.apache.maven.doxia.siterenderer.DocumentContent 类，缺少 doxia 依赖。

解决：引入相关插件和依赖：

```XML
<dependencies>
    <dependency>
        <groupId>org.apache.maven.doxia</groupId>
        <artifactId>doxia-site-renderer</artifactId>
        <version>1.11.1</version>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-site-plugin</artifactId>
            <version>4.0.0-M3</version>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-project-info-reports-plugin</artifactId>
            <version>3.8.0</version>
        </plugin>
    </plugins>
</build>
```

最终生成站点文档：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=MmE1ODdjOGY1NTI2MzEwYWZjZDMyNThiNWZmYjc5OWFfYlRoQUlLeVZ4cjZlSGszam1qYTZYY09lQ3E1RWtUYUdfVG9rZW46WnlRWmJ0WTJmb0xzZGF4cHM0ZGM5RUFnbmpnXzE3MzQwMTEzODI6MTczNDAxNDk4Ml9WNA)

使用浏览器打开：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=N2I3ODdlOGQ1ODg3Zjc1NGUwMDI0MzE1YTE2NDU2ZjhfVFU4Nnd5Ym9vU2piaHpnYnVXU0ZyRnN3cVVuR2pnd01fVG9rZW46Q2xFTWJ5aGxwb3A1aWl4NTl6UGNmYnF2bnJpXzE3MzQwMTEzODI6MTczNDAxNDk4Ml9WNA)

# Maven插件和插件目标

## 概念

### 插件

> 类比：在计算机网络中有 4 层架构的概念，但这仅仅是个概念，需要由具体的操作系统来实现。 

Maven 插件（plugin）主要是为 Maven 生命周期中的阶段服务的。Maven 中只是定义了 3 套生命周期，以及每套生命周期中有哪些阶段，具体每个阶段中执行什么操作，完全是交给**插件**去干的。

插件可以通过 mvn 命令的方式直接运行。或者将插件和 Maven 生命周期的阶段进行绑定，然后通过`mvn 阶段`的方式调用，在执行阶段的时候，会自动执行和这些阶段绑定的插件。上面在生命周期演示中使用的就是这种方式。

### 插件目标

Maven 中的插件以 jar 的方式存在于仓库中，和其他构件一样，也是通过 GAV 坐标的方式进行访问。一个插件

可能包含了多个功能，比如编译代码的插件 maven-compiler-plugin，可以编译源代码，也可以编译测试代码

插件中的功能叫**插件目标（Plugin Goal）**，每个插件中可能包含一个或多个插件目标。

### 目标参数

Maven 是由 Java 语言开发的，每个插件对应一个 jar 包，而插件目标就是 jar 包中的类。例如，我们常用的测试插件 maven-surefire-plugin 的 test 目标主要由一个叫 TestMojo 的类实现的。既然底层是类，那么就会有属性和方法，我们开发人员在使用插件目标的时候就可以设置属性，而这些属性被称为**目标参数**。



## 列出插件所有目标

使用以下命令可以列出插件所有目标：

```Shell
# 方式一：查看插件的所有目标
mvn 插件groupId:插件artifactId[:插件version]:help

# 方式二：查看插件的所有目标
mvn 插件前缀:help

# 方式三：查看插件的指定目标
mvn 插件前缀:help -Dgoal=目标名称 
```

### **方式一演示**

1、查看 maven-clean-plugin 插件的所有目标

```Shell
(base) PS C:\...\maven_00> mvn org.apache.maven.plugins:maven-clean-plugin:help   
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:help (default-cli) @ maven_00 ---
[INFO] org.apache.maven.plugins:maven-clean-plugin:2.5

Maven Clean Plugin
  The Maven Clean Plugin is a plugin that removes files generated at build-time
  in a project's directory.

This plugin has 2 goals:

clean:clean
  Goal which cleans the build.
  This attempts to clean a project's working directory of the files that were
  generated at build-time. By default, it discovers and deletes the directories
  configured in project.build.directory, project.build.outputDirectory,
  project.build.testOutputDirectory, and project.reporting.outputDirectory.

  Files outside the default may also be included in the deletion by configuring
  the filesets tag.

clean:help
  Display help information on maven-clean-plugin.
  Call
    mvn clean:help -Ddetail=true -Dgoal=<goal-name>
  to display parameter details.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.197 s
[INFO] Finished at: 2024-12-04T16:37:09+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到 maven-clean-plugin 有两个目标：clean 和 help。

2、查看 maven-compiler-plugin 插件的所有目标

```Shell
(base) PS C:\...\maven_00> mvn org.apache.maven.plugins:maven-compiler-plugin:help
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:help (default-cli) @ maven_00 ---
[INFO] Maven Compiler Plugin 3.1
  The Compiler Plugin is used to compile the sources of your project.

This plugin has 3 goals:

compiler:compile
  Compiles application sources

compiler:help
  Display help information on maven-compiler-plugin.
  Call mvn compiler:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

compiler:testCompile
  Compiles application test sources.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.499 s
[INFO] Finished at: 2024-12-04T16:45:37+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到 maven-compiler-plugin 有 3 个目标：compile、testCompile 和 help。

3、查看 maven-surefire-plugin 插件的所有目标

```Shell
(base) PS C:\...\maven_00> mvn org.apache.maven.plugins:maven-surefire-plugin:help
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:help (default-cli) @ maven_00 ---
[INFO] Maven Surefire Plugin 2.12.4
  Surefire is a test framework project.

This plugin has 2 goals:

surefire:help
  Display help information on maven-surefire-plugin.
  Call mvn surefire:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

surefire:test
  Run tests using Surefire.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.376 s
[INFO] Finished at: 2024-12-04T16:34:12+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到 maven-surefire-plugin 有两个目标：help 和 test。

4、查看 maven-jar-plugin 插件的所有目标

```Shell
(base) PS C:\...\maven_00> mvn org.apache.maven.plugins:maven-jar-plugin:help     
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-jar-plugin:2.4:help (default-cli) @ maven_00 ---
[INFO] org.apache.maven.plugins:maven-jar-plugin:2.4

Maven JAR Plugin
  Builds a Java Archive (JAR) file from the compiled project classes and
  resources.

This plugin has 5 goals:

jar:help
  Display help information on maven-jar-plugin.
  Call
    mvn jar:help -Ddetail=true -Dgoal=<goal-name>
  to display parameter details.

jar:jar
  Build a JAR from the current project.

jar:sign
  Deprecated. As of version 2.3, this goal is no longer supported in favor of
  the dedicated maven-jarsigner-plugin.

jar:sign-verify
  Deprecated. As of version 2.3, this goal is no longer supported in favor of
  the dedicated maven-jarsigner-plugin.

jar:test-jar
  Build a JAR of the test classes for the current project.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.349 s
[INFO] Finished at: 2024-12-04T16:50:49+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到 maven-jar-plugin 有 5 个目标：help、jar、sign、sign-verfy 和 test-jar。

5、查看 maven-install-plugin 插件的所有目标

```Shell
(base) PS C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00> mvn org.apache.maven.plugins:maven-install-plugin:help
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-install-plugin:2.4:help (default-cli) @ maven_00 ---
[INFO] Maven Install Plugin 2.4
  Copies the project artifacts to the user's local repository.

This plugin has 3 goals:

install:help
  Display help information on maven-install-plugin.
  Call mvn install:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

install:install
  Installs the project's main artifact, and any other artifacts attached by
  other plugins in the lifecycle, to the local repository.

install:install-file
  Installs a file in the local repository.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.304 s
[INFO] Finished at: 2024-12-04T17:09:00+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到 maven-install-plugin 有 3 个目标：help、install 和 install-file。

6、查看 maven-deploy-plugin 插件的所有目标

```Shell
(base) PS C:\...\maven_00> mvn org.apache.maven.plugins:maven-deploy-plugin:help 
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-deploy-plugin:2.7:help (default-cli) @ maven_00 ---
[INFO] org.apache.maven.plugins:maven-deploy-plugin:2.7

Maven Deploy Plugin
  Uploads the project artifacts to the internal remote repository.

This plugin has 3 goals:

deploy:deploy
  Deploys an artifact to remote repository.

deploy:deploy-file
  Installs the artifact in the remote repository.

deploy:help
  Display help information on maven-deploy-plugin.
  Call
    mvn deploy:help -Ddetail=true -Dgoal=<goal-name>
  to display parameter details.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.279 s
[INFO] Finished at: 2024-12-04T17:12:07+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到 maven-deploy-plugin 有 3 个目标：deploy、deploy-file 和 help。

7、查看 maven-resources-plugin 插件的所有目标

```Shell
(base) PS C:\...\maven_00> mvn org.apache.maven.plugins:maven-resources-plugin:help
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.6:help (default-cli) @ maven_00 ---
[INFO] Maven Resources Plugin 2.6
  The Resources Plugin handles the copying of project resources to the output
  directory. There are two different kinds of resources: main resources and test
  resources. The difference is that the main resources are the resources
  associated to the main source code while the test resources are associated to
  the test source code. Thus, this allows the separation of resources for the
  main source code and its unit tests.

This plugin has 4 goals:

resources:copy-resources
  Copy resources of the configured plugin attribute resources

resources:help
  Display help information on maven-resources-plugin.
  Call mvn resources:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

resources:resources
  Copy resources for the main source code to the main output directory. Always
  uses the project.build.resources element to specify the resources to copy.

resources:testResources
  Copy resources for the test source code to the test output directory. Always
  uses the project.build.testResources element to specify the resources to copy.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.307 s
[INFO] Finished at: 2024-12-04T17:16:58+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到 maven-resources-plugin 有 4 个目标：resources、testResources、help 和 copy-resources。

8、查看 maven-site-plugin 插件的所有目标

```Shell
(base) PS C:\...\maven_00> mvn org.apache.maven.plugins:maven-site-plugin:help     
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-site-plugin:3.3:help (default-cli) @ maven_00 ---
[INFO] Maven Site Plugin 3 3.3
  The Maven Site Plugin is a plugin that generates a site for the current
  project.

This plugin has 9 goals:

site:attach-descriptor
  Adds the site descriptor (site.xml) to the list of files to be
  installed/deployed.
  For Maven-2.x this is enabled by default only when the project has pom
  packaging since it will be used by modules inheriting, but this can be enabled
  for other projects packaging if needed.
  This default execution has been removed from the built-in lifecycle of Maven
  3.x for pom-projects. Users that actually use those projects to provide a
  common site descriptor for sub modules will need to explicitly define this
  goal execution to restore the intended behavior.

site:deploy
  Deploys the generated site using wagon supported protocols to the site URL
  specified in the <distributionManagement> section of the POM.
  For scp protocol, the website files are packaged by wagon into zip archive,
  then the archive is transfered to the remote host, next it is un-archived
  which is much faster than making a file by file copy.

site:effective-site
  Displays the effective site descriptor as an XML for this build, after
  inheritance and interpolation of site.xml.

site:help
  Display help information on maven-site-plugin.
  Call mvn site:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

site:jar
  Bundles the site output into a JAR so that it can be deployed to a repository.

site:run
  Starts the site up, rendering documents as requested for faster editing. It
  uses Jetty as the web server.

site:site
  Generates the site for a single project.
  Note that links between module sites in a multi module build will not work,
  since local build directory structure doesn't match deployed site.

site:stage
  Deploys the generated site to a local staging or mock directory based on the
  site URL specified in the <distributionManagement> section of the POM.
  It can be used to test that links between module sites in a multi-module build
  work.

site:stage-deploy
  Deploys the generated site to a staging or mock URL to the site URL specified
  in the <distributionManagement> section of the POM, using wagon supported
  protocols


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.986 s
[INFO] Finished at: 2024-12-04T17:23:37+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到 maven-site-plugin 有 9 个目标：attach-descriptor、deploy、effective-site、help、jar、run、site、stage 和 stage-deploy。

9、查看 maven-help-plugin 插件的所有目标

```Shell
(base) PS C:\...\maven_00> mvn org.apache.maven.plugins:maven-help-plugin:help
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-help-plugin:3.5.1:help (default-cli) @ maven_00 ---
[INFO] Apache Maven Help Plugin 3.5.1
  The Maven Help plugin provides goals aimed at helping to make sense out of
   the build environment. It includes the ability to view the effective
   POM and settings files, after inheritance and active profiles
   have been applied, as well as a describe a particular plugin goal to give
  usage information.

This plugin has 8 goals:

help:active-profiles
  Displays a list of the profiles which are currently active for this build.

help:all-profiles
  Displays a list of available profiles under the current project.
  Note: it will list all profiles for a project. If a profile comes up with a
  status inactive then there might be a need to set profile activation
  switches/property.

help:describe
  Displays a list of the attributes for a Maven Plugin and/or goals (aka Mojo -
  Maven plain Old Java Object).
  See also: What is a Mojo?
  <http://maven.apache.org/general.html#What_is_a_Mojo>

help:effective-pom
  Displays the effective POM as an XML for this build, with the active profiles
  factored in, or a specified artifact. If verbose, a comment is added to each
  XML element describing the origin of the line.

help:effective-settings
  Displays the calculated settings as XML for this project, given any profile
  enhancement and the inheritance of the global settings into the user-level
  settings.

help:evaluate
  Evaluates Maven expressions given by the user in an interactive mode.

help:help
  Display help information on maven-help-plugin.
  Call mvn help:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

help:system
  Displays a list of the platform details like system properties and environment
  variables.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.365 s
[INFO] Finished at: 2024-12-04T20:21:33+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到 maven-help-plugin 有 8 个目标：describe、effective-pom、active-profiles、all-profiles、effective-settings、evaluate、help 和 system。

10、查看 maven-dependency-plugin 插件的所有目标

如下所示，该插件有 21 个目标，其中 tree 常用，用于查看当前项目的依赖树，帮助排查依赖冲突。

```Shell
(base) PS C:\...\maven_09> mvn org.apache.maven.plugins:maven-dependency-plugin:help
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_09 >------------------------
[INFO] Building maven_09 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-dependency-plugin:2.8:help (default-cli) @ maven_09 ---
[INFO] Maven Dependency Plugin 2.8
  Provides utility goals to work with dependencies like copying, unpacking,
  analyzing, resolving and many more.

This plugin has 21 goals:

dependency:analyze
  Analyzes the dependencies of this project and determines which are: used and
  declared; used and undeclared; unused and declared. This goal is intended to
  be used standalone, thus it always executes the test-compile phase - use the
  dependency:analyze-only goal instead when participating in the build
  lifecycle.
  By default, maven-dependency-analyzer is used to perform the analysis, with
  limitations due to the fact that it works at bytecode level, but any analyzer
  can be plugged in through analyzer parameter.

dependency:analyze-dep-mgt
  This mojo looks at the dependencies after final resolution and looks for
  mismatches in your dependencyManagement section. In versions of maven prior to
  2.0.6, it was possible to inherit versions that didn't match your
  dependencyManagement. See MNG-1577 for more info. This mojo is also useful for
  just detecting projects that override the dependencyManagement directly. Set
  ignoreDirect to false to detect these otherwise normal conditions.

dependency:analyze-duplicate
  Analyzes the <dependencies/> and <dependencyManagement/> tags in the pom.xml
  and determines the duplicate declared dependencies.

dependency:analyze-only
  Analyzes the dependencies of this project and determines which are: used and
  declared; used and undeclared; unused and declared. This goal is intended to
  be used in the build lifecycle, thus it assumes that the test-compile phase
  has been executed - use the dependency:analyze goal instead when running
  standalone.
  By default, maven-dependency-analyzer is used to perform the analysis, with
  limitations due to the fact that it works at bytecode level, but any analyzer
  can be plugged in through analyzer parameter.

dependency:analyze-report
  Analyzes the dependencies of this project and produces a report that
  summarizes which are: used and declared; used and undeclared; unused and
  declared.

dependency:build-classpath
  This goal will output a classpath string of dependencies from the local
  repository to a file or log.

dependency:copy
  Goal that copies a list of artifacts from the repository to defined locations.

dependency:copy-dependencies
  Goal that copies the project dependencies from the repository to a defined
  location.

dependency:get
  Resolves a single artifact, eventually transitively, from the specified remote
  repositories. Caveat: will always check the central repository defined in the
  super pom. You could use a mirror entry in your settings.xml

dependency:go-offline
  Goal that resolves all project dependencies, including plugins and reports and
  their dependencies.

dependency:help
  Display help information on maven-dependency-plugin.
  Call mvn dependency:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

dependency:list
  Displays the list of dependencies for this project.

dependency:list-repositories
  Goal that resolves all project dependencies and then lists the repositories
  used by the build and by the transitive dependencies

dependency:properties
  Goal that sets a property pointing to the artifact file for each project
  dependency. For each dependency (direct and transitive) a project property
  will be set which follows the groupId:artifactId:type:[classifier] form and
  contains the path to the resolved artifact.

dependency:purge-local-repository
  Remove the project dependencies from the local repository, and optionally
  re-resolve them.

dependency:resolve
  Goal that resolves the project dependencies from the repository.

dependency:resolve-plugins
  Goal that resolves all project plugins and reports and their dependencies.

dependency:sources
  Goal that resolves the project source dependencies from the repository.

dependency:tree
  Displays the dependency tree for this project.

dependency:unpack
  Goal that retrieves a list of artifacts from the repository and unpacks them
  in a defined location.

dependency:unpack-dependencies
  Goal that unpacks the project dependencies from the repository to a defined
  location.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.933 s
[INFO] Finished at: 2024-12-12T20:48:36+08:00
[INFO] ------------------------------------------------------------------------
```

### **方式二演示**

上面我们演示了 8 个常用的插件，查看了它们的所有目标，使用的是方式一的命令行。接下来，我们使用方式二来查看一下。

查看插件信息的命令：

```Shell
mvn help:describe -Dplugin=groupId:artifactId
```

必须是 groupId:artifactId 的形式，否则报错。

例如，查看 maven-resources-plugin 的信息：

```Shell
(base) PS C:\...\maven_00> mvn help:describe -Dplugin="org.apache.maven.plugins:maven-resources-plugin"
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-help-plugin:3.5.1:describe (default-cli) @ maven_00 ---
[INFO] org.apache.maven.plugins:maven-resources-plugin:2.6

Name: Maven Resources Plugin
Description: The Resources Plugin handles the copying of project resources to
  the output directory. There are two different kinds of resources: main
  resources and test resources. The difference is that the main resources are
  the resources associated to the main source code while the test resources are
  associated to the test source code. Thus, this allows the separation of
  resources for the main source code and its unit tests.
Group Id: org.apache.maven.plugins
Artifact Id: maven-resources-plugin
Version: 2.6
Goal Prefix: resources

This plugin has 4 goals:

resources:copy-resources
  Description: Copy resources of the configured plugin attribute resources

resources:help
  Description: Display help information on maven-resources-plugin.
    Call mvn resources:help -Ddetail=true -Dgoal=<goal-name> to display
    parameter details.

resources:resources
  Description: Copy resources for the main source code to the main output
    directory. Always uses the project.build.resources element to specify the
    resources to copy.

resources:testResources
  Description: Copy resources for the test source code to the test output
    directory. Always uses the project.build.testResources element to specify
    the resources to copy.

For more information, run 'mvn help:describe [...] -Ddetail'

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.833 s
[INFO] Finished at: 2024-12-04T19:59:37+08:00
[INFO] ------------------------------------------------------------------------
```

从 maven-resources-plugin 插件的 "Goal Prefix" 可以看到，该插件的前缀是 resources。

知道了前缀，我们就可以使用方式三查看插件的所有目标：

```Shell
(base) PS C:\...\maven_00> mvn resources:help
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.6:help (default-cli) @ maven_00 ---
[INFO] Maven Resources Plugin 2.6
  The Resources Plugin handles the copying of project resources to the output
  directory. There are two different kinds of resources: main resources and test
  resources. The difference is that the main resources are the resources
  associated to the main source code while the test resources are associated to
  the test source code. Thus, this allows the separation of resources for the
  main source code and its unit tests.

This plugin has 4 goals:

resources:copy-resources
  Copy resources of the configured plugin attribute resources

resources:help
  Display help information on maven-resources-plugin.
  Call mvn resources:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

resources:resources
  Copy resources for the main source code to the main output directory. Always
  uses the project.build.resources element to specify the resources to copy.

resources:testResources
  Copy resources for the test source code to the test output directory. Always
  uses the project.build.testResources element to specify the resources to copy.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.346 s
[INFO] Finished at: 2024-12-04T20:01:59+08:00
[INFO] ------------------------------------------------------------------------
```

### **方式三演示**

查看 maven-surefire-plugin 的 test 目标：

```Shell
(base) PS C:\...\maven_00> mvn surefire:help -Dgoal=help
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:help (default-cli) @ maven_00 ---
[INFO] Maven Surefire Plugin 2.12.4
  Surefire is a test framework project.

surefire:help
  Display help information on maven-surefire-plugin.
  Call mvn surefire:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.344 s
[INFO] Finished at: 2024-12-04T20:51:33+08:00
[INFO] ------------------------------------------------------------------------
```

> 附：Maven 提供了大量官方和社区开发的插件，用于支持各种构建生命周期、报告生成和其他任务。以下是一些常用且重要的 Maven 插件列表：
>
> **一、构建与打包插件**
>
> - **maven-clean-plugin**：清理项目，删除之前构建生成的所有文件。
> - **maven-resources-plugin**：复制应用程序所需的资源文件（如配置文件）到输出目录。
> - **maven-compiler-plugin**：编译项目的 Java 源代码。
> - **maven-surefire-plugin**：运行单元测试，并在失败时跳过构建过程。
> - **maven-jar-plugin**：将项目的类文件打包成 JAR 文件。
> - **maven-war-plugin**：将 Web 应用程序打包成 WAR 文件。
> - maven-assembly-plugin：创建项目分发包，可以包含依赖项、模块、站点文档等。
> - **maven-install-plugin**：将构建的工件安装到本地仓库。
> - **maven-deploy-plugin**：将构建的工件部署到远程仓库。
>
> 
>
> **二、依赖管理插件**
>
> - **maven-dependency-plugin：分析、复制或解析项目的依赖关系。**
> - maven-shade-plugin：创建包含所有依赖项的可执行 JAR 或 WAR 文件。
> - maven-enforcer-plugin：强制执行某些项目规则或限制。
>
> 
>
> **三、报告与文档插件**
>
> - **maven-site-plugin**：生成一个包含项目信息、报告和文档的网站。
> - maven-javadoc-plugin：生成 Java 文档（Javadoc）。
> - maven-checkstyle-plugin：根据 Checkstyle 规则检查源代码格式。
> - maven-pmd-plugin：使用 PMD 工具进行静态代码分析。
> - maven-findbugs-plugin 或 maven-spotbugs-plugin：使用 SpotBugs（FindBugs 的继承者）查找潜在的 Bug。
>
> 
>
> **四、集成测试插件**
>
> - maven-failsafe-plugin：执行集成测试，不会因为测试失败而阻止后续的打包和部署。
> - maven-invoker-plugin：运行一组独立的 Maven 项目以验证插件的行为。
>
> 
>
> **五、其他工具插件**
>
> - maven-release-plugin：自动化版本控制、构建发布和准备下一个开发周期的过程。
> - maven-antrun-plugin：允许直接在 POM 中嵌入 Ant 任务。
> - maven-scm-plugin：提供对源码管理系统的操作，如检出、提交、标签等。
> - **maven-help-plugin**：帮助用户获取关于项目的信息，例如有效 POM 和插件描述。
>
> 
>
> **六、版本控制插件**
>
> - versions-maven-plugin：管理和更新项目及其依赖项的版本号。
>
> 
>
> 这只是一个部分列表，实际上还有许多其他插件适用于特定需求或技术栈。Maven 插件生态系统非常丰富，几乎涵盖了软件开发生命周期的各个方面。
>
> 官网截图：
>
> ![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=OGQ1OGE2NTExNmU0ZDVjMDVjNmVjNmE4MTQxOGExYmZfbHhPcjVhb05EaEwxZm5ZS1ByVzJ0ODNlVDFKamd0eGdfVG9rZW46TGFRamJBZWI2b3lyZHB4ZzdzUmNyNEh6bjlOXzE3MzQwMTE5MzU6MTczNDAxNTUzNV9WNA)



## 列出插件目标参数列表

使用以下命令可以查看插件目标的参数列表：

```Shell
# 方式一：
mvn 插件groupId:插件artifactId[:插件version]:help -Dgoal=目标名称 -Ddetail

# 方式二：
mvn 插件前缀:help -Dgoal=目标名称 -Ddetail
```

- **-Dgoal**：查看指定目标
- **-Ddetail**：输出目标详细的参数列表信息

举例：查看 maven-surefire-plugin 插件 test 目标的参数列表

控制台输出如下，有非常多的参数，其中有几个熟悉的：

- `skip`：跳过测试代码的编译和测试
- `skipTests`：跳过测试，但仍会编译测试代码
- `testSourceDirectory`：测试源代码目录
- `testClassesDirectory`：编译后测试类文件目录

```Shell
(base) PS C:\...\maven_00> mvn surefire:help -Dgoal=test -Ddetail
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:help (default-cli) @ maven_00 ---
[INFO] Maven Surefire Plugin 2.12.4
  Surefire is a test framework project.

surefire:test
  Run tests using Surefire.

  Available parameters:

    additionalClasspathElements
      Additional elements to be appended to the classpath.

    argLine (Default: null)
      Arbitrary JVM options to set on the command line.

    basedir
      The base directory of the project being tested. This can be obtained in
      your integration test via System.getProperty('basedir').

    childDelegation (Default: false)
      When false it makes tests run using the standard classloader delegation
      instead of the default Maven isolated classloader. Only used when forking
      (forkMode is not 'none').
      Setting it to false helps with some problems caused by conflicts between
      xml parsers in the classpath and the Java 5 provider parser.

    classesDirectory
      The directory containing generated classes of the project being tested.
      This will be included after the test classes in the test classpath.

    classpathDependencyExcludes
      List of dependencies to exclude from the test classpath. Each dependency
      string must follow the format groupId:artifactId. For example:
      org.acme:project-a

    classpathDependencyScopeExclude
      A dependency scope to exclude from the test classpath. The scope should be
      one of the scopes defined by org.apache.maven.artifact.Artifact. This
      includes the following:
      - compile - system, provided, compile
      - runtime - compile, runtime
      - compile+runtime - system, provided, compile, runtime
      - runtime+system - system, compile, runtime
      - test - system, provided, compile, runtime, test

    debugForkedProcess (Default: null)
      Attach a debugger to the forked JVM. If set to 'true', the process will
      suspend and wait for a debugger to attach on port 5005. If set to some
      other string, that string will be appended to the argLine, allowing you to
      configure arbitrary debuggability options (without overwriting the other
      options specified through the argLine parameter).

    disableXmlReport (Default: false)
      Flag to disable the generation of report files in xml format.

    enableAssertions (Default: true)
      By default, Surefire enables JVM assertions for the execution of your test
      cases. To disable the assertions, set this flag to 'false'.

    environmentVariables
      Additional environment variables to set on the command line.

    excludedGroups (Default: null)
      (TestNG/JUnit47 provider with JUnit4.8+ only) Excluded groups. Any
      methods/classes/etc with one of the groups specified in this list will
      specifically not be run.
      For JUnit, this parameter forces the use of the 4.7 provider
      This parameter is ignored if the suiteXmlFiles parameter is specified.

    excludes
      A list of <exclude> elements specifying the tests (by pattern) that should
      be excluded in testing. When not specified and when the test parameter is
      not specified, the default excludes will be
      <excludes>
       <exclude>**/*$*</exclude>
      </excludes>
      (which excludes all inner classes).
      This parameter is ignored if the TestNG suiteXmlFiles parameter is
      specified. Each exclude item may also contain a comma-separated sublist of
      items, which will be treated as multiple  <exclude> entries.

    failIfNoSpecifiedTests (Default: null)
      Set this to 'true' to cause a failure if the none of the tests specified
      in -Dtest=... are run. Defaults to 'true'.

    failIfNoTests (Default: null)
      Set this to 'true' to cause a failure if there are no tests to run.
      Defaults to 'false'.

    forkedProcessTimeoutInSeconds (Default: null)
      Kill the forked test process after a certain number of seconds. If set to
      0, wait forever for the process, never timing out.

    forkMode (Default: once)
      Option to specify the forking mode. Can be 'never', 'once', 'always' or
      'perthread'. 'none' and 'pertest' are also accepted for backwards
      compatibility. 'always' forks for each test-class. 'perthread' will create
      'threadCount' parallel forks.

    groups (Default: null)
      (TestNG/JUnit47 provider with JUnit4.8+ only) Groups for this test. Only
      classes/methods/etc decorated with one of the groups specified here will
      be included in test run, if specified.
      For JUnit, this parameter forces the use of the 4.7 provider
      This parameter is ignored if the suiteXmlFiles parameter is specified.

    includes
      A list of <include> elements specifying the tests (by pattern) that should
      be included in testing. When not specified and when the test parameter is
      not specified, the default includes will be
      <includes>
       <include>**/IT*.java</include>
       <include>**/*IT.java</include>
       <include>**/*ITCase.java</include>
      </includes>
      Each include item may also contain a comma-separated sublist of items,
      which will be treated as multiple  <include> entries.
      This parameter is ignored if the TestNG suiteXmlFiles parameter is
      specified.

    junitArtifactName (Default: junit:junit)
      Allows you to specify the name of the JUnit artifact. If not set,
      junit:junit will be used.

    jvm (Default: null)
      Option to specify the jvm (or path to the java executable) to use with the
      forking options. For the default, the jvm will be a new instance of the
      same VM as the one used to run Maven. JVM settings are not inherited from
      MAVEN_OPTS.

    objectFactory (Default: null)
      (TestNG only) Define the factory class used to create all test instances.

    parallel (Default: null)
      (TestNG only) When you use the parallel attribute, TestNG will try to run
      all your test methods in separate threads, except for methods that depend
      on each other, which will be run in the same thread in order to respect
      their order of execution. (JUnit 4.7 provider) Supports values
      'classes'/'methods'/'both' to run in separate threads, as controlled by
      threadCount.

    perCoreThreadCount (Default: true)
      (JUnit 4.7 provider) Indicates that threadCount is per cpu core.

    printSummary (Default: true)
      Option to print summary of test suites or just print the test cases that
      have errors.

    properties
      List of properties for configuring all TestNG related configurations. This
      is the new preferred method of configuring TestNG.

    redirectTestOutputToFile (Default: false)
      Set this to 'true' to redirect the unit test standard output to a file
      (found in reportsDirectory/testName-output.txt).

    remoteRepositories
      The remote plugin repositories declared in the POM.

    reportFormat (Default: brief)
      Selects the formatting for the test report to be generated. Can be set as
      'brief' or 'plain'.

    reportNameSuffix (Default: )
      Add custom text into report filename:
      TEST-testClassName-reportNameSuffix.xml,
      testClassName-reportNameSuffix.txt and
      testClassName-reportNameSuffix-output.txt. File
      TEST-testClassName-reportNameSuffix.xml has changed attributes
      'testsuite'--'name' and 'testcase'--'classname' - reportNameSuffix is
      added to the attribute value.

    reportsDirectory
      Base directory where all reports are written to.

    runOrder
      Defines the order the tests will be run in. Supported values are
      'alphabetical', 'reversealphabetical', 'random', 'hourly' (alphabetical on
      even hours, reverse alphabetical on odd hours), 'failedfirst', 'balanced'
      and 'filesystem'. Odd/Even for hourly is determined at the time the of
      scanning the classpath, meaning it could change during a multi-module
      build. Failed first will run tests that failed on previous run first, as
      well as new tests for this run. Balanced is only relevant with
      parallel=classes, and will try to optimize the run-order of the tests to
      make all tests complete at the same time, reducing the overall execution
      time. Note that the statistics are stored in a file named
      .surefire-XXXXXXXXX beside pom.xml, and should not be checked into version
      control. The 'XXXXX' is the SHA1 checksum of the entire surefire
      configuration, so different configurations will have different statistics
      files, meaning if you change any config settings you will re-run once
      before new statistics data can be established.

    skip (Default: false)
      Set this to 'true' to bypass unit tests entirely. Its use is NOT
      RECOMMENDED, especially if you enable it using the 'maven.test.skip'
      property, because maven.test.skip disables both running the tests and
      compiling the tests. Consider using the skipTests parameter instead.

    skipExec (Default: null)
      Deprecated. Use skipTests instead.

      This old parameter is just like skipTests, but bound to the old property
      'maven.test.skip.exec'.

    skipTests (Default: false)
      Set this to 'true' to skip running tests, but still compile them. Its use
      is NOT RECOMMENDED, but quite convenient on occasion.

    suiteXmlFiles
      (TestNG) List of <suiteXmlFile> elements specifying TestNG suite xml file
      locations. Note that suiteXmlFiles is incompatible with several other
      parameters of this plugin, like includes/excludes.
      This parameter is ignored if the test parameter is specified (allowing you
      to run a single test instead of an entire suite).

    systemProperties
      Deprecated. Use systemPropertyVariables instead.

      List of System properties to pass to the JUnit tests.

    systemPropertiesFile
      List of System properties, loaded from a file, to pass to the JUnit tests.

    systemPropertyVariables
      List of System properties to pass to the JUnit tests.

    test (Default: null)
      Specify this parameter to run individual tests by file name, overriding
      the includes/excludes parameters. Each pattern you specify here will be
      used to create an include pattern formatted like **/${test}.java, so you
      can just type '-Dtest=MyTest' to run a single test called
      'foo/MyTest.java'.
      This parameter overrides the includes/excludes parameters, and the TestNG
      suiteXmlFiles parameter. Since 2.7.3, you can execute a limited number of
      methods in the test by adding #myMethod or #my*ethod. For example,
      '-Dtest=MyTest#myMethod'. This is supported for junit 4.x and testNg.

    testClassesDirectory
      The directory containing generated test classes of the project being
      tested. This will be included at the beginning of the test classpath. *

    testFailureIgnore (Default: false)
      Set this to 'true' to ignore a failure during testing. Its use is NOT
      RECOMMENDED, but quite convenient on occasion.

    testNGArtifactName (Default: org.testng:testng)
      Allows you to specify the name of the TestNG artifact. If not set,
      org.testng:testng will be used.

    testSourceDirectory
      The test source directory containing test class sources.
      Required: Yes

    threadCount (Default: null)
      (forkMode=perthread or TestNG/JUnit 4.7 provider) The attribute
      thread-count allows you to specify how many threads should be allocated
      for this execution. Only makes sense to use in conjunction with the
      parallel parameter. (forkMode=perthread does not support/require the
      parallel parameter)

    trimStackTrace (Default: true)
      Whether to trim the stack trace in the reports to just the lines within
      the test, or show the full trace.

    useFile (Default: true)
      Option to generate a file test report or just output the test report to
      the console.

    useManifestOnlyJar (Default: true)
      By default, Surefire forks your tests using a manifest-only JAR; set this
      parameter to 'false' to force it to launch your tests with a plain old
      Java classpath. (See
      http://maven.apache.org/plugins/maven-surefire-plugin/examples/class-loading.html
      for a more detailed explanation of manifest-only JARs and their benefits.)
      Beware, setting this to 'false' may cause your tests to fail on Windows if
      your classpath is too long.

    useSystemClassLoader (Default: true)
      Option to pass dependencies to the system's classloader instead of using
      an isolated class loader when forking. Prevents problems with JDKs which
      implement the service provider lookup mechanism by using the system's
      classloader.

    useUnlimitedThreads (Default: false)
      (JUnit 4.7 provider) Indicates that the thread pool will be unlimited. The
      parallel parameter and the actual number of classes/methods will decide.
      Setting this to 'true' effectively disables perCoreThreadCount and
      threadCount. Defaults to 'false'.

    workingDirectory (Default: null)
      Command line working directory.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.360 s
[INFO] Finished at: 2024-12-04T20:55:58+08:00
[INFO] ------------------------------------------------------------------------
```

如果不指定 -Dgoal，会列出该插件的所有目标的所有参数，例如

```Shell
(base) PS C:\...\timer-maven-plugin> mvn org.apache.maven.plugins:maven-surefire-plugin:help -Ddetail
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------< cn.myphoenix:timer-maven-plugin >-------------------
[INFO] Building timer-maven-plugin Maven Plugin 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] ----------------------------[ maven-plugin ]----------------------------
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:help (default-cli) @ timer-maven-plugin ---
[INFO] Maven Surefire Plugin 2.12.4
  Surefire is a test framework project.

This plugin has 2 goals:

surefire:help
  Display help information on maven-surefire-plugin.
  Call mvn surefire:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

  Available parameters:

    detail (Default: false)
      If true, display all settable properties for each goal.

    goal (Default: null)
      The name of the goal for which to show help. If unspecified, all goals
      will be displayed.

    indentSize (Default: 2)
      The number of spaces per indentation level, should be positive.

    lineLength (Default: 80)
      The maximum length of a display line, should be positive.

surefire:test
  Run tests using Surefire.

  Available parameters:

    additionalClasspathElements
      Additional elements to be appended to the classpath.

    argLine (Default: null)
      Arbitrary JVM options to set on the command line.

    basedir
      The base directory of the project being tested. This can be obtained in
      your integration test via System.getProperty('basedir').

    childDelegation (Default: false)
      When false it makes tests run using the standard classloader delegation
      instead of the default Maven isolated classloader. Only used when forking
      (forkMode is not 'none').
      Setting it to false helps with some problems caused by conflicts between
      xml parsers in the classpath and the Java 5 provider parser.

    classesDirectory
      The directory containing generated classes of the project being tested.
      This will be included after the test classes in the test classpath.

    classpathDependencyExcludes
      List of dependencies to exclude from the test classpath. Each dependency
      string must follow the format groupId:artifactId. For example:
      org.acme:project-a

    classpathDependencyScopeExclude
      A dependency scope to exclude from the test classpath. The scope should be
      one of the scopes defined by org.apache.maven.artifact.Artifact. This
      includes the following:
      - compile - system, provided, compile
      - runtime - compile, runtime
      - compile+runtime - system, provided, compile, runtime
      - runtime+system - system, compile, runtime
      - test - system, provided, compile, runtime, test

    debugForkedProcess (Default: null)
      Attach a debugger to the forked JVM. If set to 'true', the process will
      suspend and wait for a debugger to attach on port 5005. If set to some
      other string, that string will be appended to the argLine, allowing you to
      configure arbitrary debuggability options (without overwriting the other
      options specified through the argLine parameter).

    disableXmlReport (Default: false)
      Flag to disable the generation of report files in xml format.

    enableAssertions (Default: true)
      By default, Surefire enables JVM assertions for the execution of your test
      cases. To disable the assertions, set this flag to 'false'.

    environmentVariables
      Additional environment variables to set on the command line.

    excludedGroups (Default: null)
      (TestNG/JUnit47 provider with JUnit4.8+ only) Excluded groups. Any
      methods/classes/etc with one of the groups specified in this list will
      specifically not be run.
      For JUnit, this parameter forces the use of the 4.7 provider
      This parameter is ignored if the suiteXmlFiles parameter is specified.

    excludes
      A list of <exclude> elements specifying the tests (by pattern) that should
      be excluded in testing. When not specified and when the test parameter is
      not specified, the default excludes will be
      <excludes>
       <exclude>**/*$*</exclude>
      </excludes>
      (which excludes all inner classes).
      This parameter is ignored if the TestNG suiteXmlFiles parameter is
      specified. Each exclude item may also contain a comma-separated sublist of
      items, which will be treated as multiple  <exclude> entries.

    failIfNoSpecifiedTests (Default: null)
      Set this to 'true' to cause a failure if the none of the tests specified
      in -Dtest=... are run. Defaults to 'true'.

    failIfNoTests (Default: null)
      Set this to 'true' to cause a failure if there are no tests to run.
      Defaults to 'false'.

    forkedProcessTimeoutInSeconds (Default: null)
      Kill the forked test process after a certain number of seconds. If set to
      0, wait forever for the process, never timing out.

    forkMode (Default: once)
      Option to specify the forking mode. Can be 'never', 'once', 'always' or
      'perthread'. 'none' and 'pertest' are also accepted for backwards
      compatibility. 'always' forks for each test-class. 'perthread' will create
      'threadCount' parallel forks.

    groups (Default: null)
      (TestNG/JUnit47 provider with JUnit4.8+ only) Groups for this test. Only
      classes/methods/etc decorated with one of the groups specified here will
      be included in test run, if specified.
      For JUnit, this parameter forces the use of the 4.7 provider
      This parameter is ignored if the suiteXmlFiles parameter is specified.

    includes
      A list of <include> elements specifying the tests (by pattern) that should
      be included in testing. When not specified and when the test parameter is
      not specified, the default includes will be
      <includes>
       <include>**/IT*.java</include>
       <include>**/*IT.java</include>
       <include>**/*ITCase.java</include>
      </includes>
      Each include item may also contain a comma-separated sublist of items,
      which will be treated as multiple  <include> entries.
      This parameter is ignored if the TestNG suiteXmlFiles parameter is
      specified.

    junitArtifactName (Default: junit:junit)
      Allows you to specify the name of the JUnit artifact. If not set,
      junit:junit will be used.

    jvm (Default: null)
      Option to specify the jvm (or path to the java executable) to use with the
      forking options. For the default, the jvm will be a new instance of the
      same VM as the one used to run Maven. JVM settings are not inherited from
      MAVEN_OPTS.

    objectFactory (Default: null)
      (TestNG only) Define the factory class used to create all test instances.

    parallel (Default: null)
      (TestNG only) When you use the parallel attribute, TestNG will try to run
      all your test methods in separate threads, except for methods that depend
      on each other, which will be run in the same thread in order to respect
      their order of execution. (JUnit 4.7 provider) Supports values
      'classes'/'methods'/'both' to run in separate threads, as controlled by
      threadCount.

    perCoreThreadCount (Default: true)
      (JUnit 4.7 provider) Indicates that threadCount is per cpu core.

    printSummary (Default: true)
      Option to print summary of test suites or just print the test cases that
      have errors.

    properties
      List of properties for configuring all TestNG related configurations. This
      is the new preferred method of configuring TestNG.

    redirectTestOutputToFile (Default: false)
      Set this to 'true' to redirect the unit test standard output to a file
      (found in reportsDirectory/testName-output.txt).

    remoteRepositories
      The remote plugin repositories declared in the POM.

    reportFormat (Default: brief)
      Selects the formatting for the test report to be generated. Can be set as
      'brief' or 'plain'.

    reportNameSuffix (Default: )
      Add custom text into report filename:
      TEST-testClassName-reportNameSuffix.xml,
      testClassName-reportNameSuffix.txt and
      testClassName-reportNameSuffix-output.txt. File
      TEST-testClassName-reportNameSuffix.xml has changed attributes
      'testsuite'--'name' and 'testcase'--'classname' - reportNameSuffix is
      added to the attribute value.

    reportsDirectory
      Base directory where all reports are written to.

    runOrder
      Defines the order the tests will be run in. Supported values are
      'alphabetical', 'reversealphabetical', 'random', 'hourly' (alphabetical on
      even hours, reverse alphabetical on odd hours), 'failedfirst', 'balanced'
      and 'filesystem'. Odd/Even for hourly is determined at the time the of
      scanning the classpath, meaning it could change during a multi-module
      build. Failed first will run tests that failed on previous run first, as
      well as new tests for this run. Balanced is only relevant with
      parallel=classes, and will try to optimize the run-order of the tests to
      make all tests complete at the same time, reducing the overall execution
      time. Note that the statistics are stored in a file named
      .surefire-XXXXXXXXX beside pom.xml, and should not be checked into version
      control. The 'XXXXX' is the SHA1 checksum of the entire surefire
      configuration, so different configurations will have different statistics
      files, meaning if you change any config settings you will re-run once
      before new statistics data can be established.

    skip (Default: false)
      Set this to 'true' to bypass unit tests entirely. Its use is NOT
      RECOMMENDED, especially if you enable it using the 'maven.test.skip'
      property, because maven.test.skip disables both running the tests and
      compiling the tests. Consider using the skipTests parameter instead.

    skipExec (Default: null)
      Deprecated. Use skipTests instead.

      This old parameter is just like skipTests, but bound to the old property
      'maven.test.skip.exec'.

    skipTests (Default: false)
      Set this to 'true' to skip running tests, but still compile them. Its use
      is NOT RECOMMENDED, but quite convenient on occasion.

    suiteXmlFiles
      (TestNG) List of <suiteXmlFile> elements specifying TestNG suite xml file
      locations. Note that suiteXmlFiles is incompatible with several other
      parameters of this plugin, like includes/excludes.
      This parameter is ignored if the test parameter is specified (allowing you
      to run a single test instead of an entire suite).

    systemProperties
      Deprecated. Use systemPropertyVariables instead.

      List of System properties to pass to the JUnit tests.

    systemPropertiesFile
      List of System properties, loaded from a file, to pass to the JUnit tests.

    systemPropertyVariables
      List of System properties to pass to the JUnit tests.

    test (Default: null)
      Specify this parameter to run individual tests by file name, overriding
      the includes/excludes parameters. Each pattern you specify here will be
      used to create an include pattern formatted like **/${test}.java, so you
      can just type '-Dtest=MyTest' to run a single test called
      'foo/MyTest.java'.
      This parameter overrides the includes/excludes parameters, and the TestNG
      suiteXmlFiles parameter. Since 2.7.3, you can execute a limited number of
      methods in the test by adding #myMethod or #my*ethod. For example,
      '-Dtest=MyTest#myMethod'. This is supported for junit 4.x and testNg.

    testClassesDirectory
      The directory containing generated test classes of the project being
      tested. This will be included at the beginning of the test classpath. *

    testFailureIgnore (Default: false)
      Set this to 'true' to ignore a failure during testing. Its use is NOT
      RECOMMENDED, but quite convenient on occasion.

    testNGArtifactName (Default: org.testng:testng)
      Allows you to specify the name of the TestNG artifact. If not set,
      org.testng:testng will be used.

    testSourceDirectory
      The test source directory containing test class sources.
      Required: Yes

    threadCount (Default: null)
      (forkMode=perthread or TestNG/JUnit 4.7 provider) The attribute
      thread-count allows you to specify how many threads should be allocated
      for this execution. Only makes sense to use in conjunction with the
      parallel parameter. (forkMode=perthread does not support/require the
      parallel parameter)

    trimStackTrace (Default: true)
      Whether to trim the stack trace in the reports to just the lines within
      the test, or show the full trace.

    useFile (Default: true)
      Option to generate a file test report or just output the test report to
      the console.

    useManifestOnlyJar (Default: true)
      By default, Surefire forks your tests using a manifest-only JAR; set this
      parameter to 'false' to force it to launch your tests with a plain old
      Java classpath. (See
      http://maven.apache.org/plugins/maven-surefire-plugin/examples/class-loading.html
      for a more detailed explanation of manifest-only JARs and their benefits.)
      Beware, setting this to 'false' may cause your tests to fail on Windows if
      your classpath is too long.

    useSystemClassLoader (Default: true)
      Option to pass dependencies to the system's classloader instead of using
      an isolated class loader when forking. Prevents problems with JDKs which
      implement the service provider lookup mechanism by using the system's
      classloader.

    useUnlimitedThreads (Default: false)
      (JUnit 4.7 provider) Indicates that the thread pool will be unlimited. The
      parallel parameter and the actual number of classes/methods will decide.
      Setting this to 'true' effectively disables perCoreThreadCount and
      threadCount. Defaults to 'false'.

    workingDirectory (Default: null)
      Command line working directory.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.359 s
[INFO] Finished at: 2024-12-12T16:00:55+08:00
[INFO] ------------------------------------------------------------------------
```

## 运行插件

使用以下命令可以运行插件：

```Shell
# 方式一：
mvn 插件groupId:插件artifactId[:插件version]:插件目标 [-D目标参数1] [-D目标参数2] [-D目标参数n]

# 方式二：
mvn 插件前缀:插件目标 [-D目标参数1] [-D目标参数2] [-D目标参数n]
```

举例：运行 maven-surefire-plugin 的 test 目标，并设置 skipTests 参数，可以看到跳过测试了

```Shell
(base) PS C:\...\maven_00> mvn surefire:test -DskipTests=true
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-cli) @ maven_00 ---
[INFO] Tests are skipped.
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.391 s
[INFO] Finished at: 2024-12-04T21:40:03+08:00
[INFO] ------------------------------------------------------------------------
```

## 深入理解插件

Maven 是由 Java 语言开发的，从代码角度来看，插件目标就是由一个或多个类实现的。

### **一、得到插件目标的实现类**

使用 help 插件的 effective-pom 命令获取插件的部分信息，将 pom 输出到 effective-pom.xml 文件：

```Shell
mvn help:effective-pom -Doutput=effective-pom.xml
```

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=MDUwMDczNzNjMTE0ZDJiMWIyMjE4Y2U5NzkyYzkzODRfREc4enVOcmNORG9JS2lqOEFwZk1oR1VJUklZWnJ0UFFfVG9rZW46Q0EydWI2TERTb2ZTRFZ4ekNwdWNvQjRGbktMXzE3MzQwMTIxNzE6MTczNDAxNTc3MV9WNA)

在 effective pom 文件中找到 maven-surefire-plugin 插件，并找到 test 目标，点进去

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=OTU2ZDVkNjc4YTk0NWUzNzMwZDMwYWM2OGU1YmMyNTJfd1VIVnk3bXg3aEZlOEJzbjdOdE01dkVSeGQ0NTVJdVZfVG9rZW46SlliYWJDNk5ub2ZGVGJ4SWt1SGNyZ3JVbnRJXzE3MzQwMTIxNzE6MTczNDAxNTc3MV9WNA)

可以看到，mojo 标签，在 mojo 标签中定位到 implementation 标签，即为该目标的实现类：

```Shell
    <mojo>
      <goal>test</goal>
      <description>Run tests using Surefire.</description>
      <requiresDependencyResolution>test</requiresDependencyResolution>
      <requiresDirectInvocation>false</requiresDirectInvocation>
      <requiresProject>true</requiresProject>
      <requiresReports>false</requiresReports>
      <aggregator>false</aggregator>
      <requiresOnline>false</requiresOnline>
      <inheritedByDefault>true</inheritedByDefault>
      <phase>test</phase>
      <implementation>org.apache.maven.plugin.surefire.SurefirePlugin</implementation>
      <language>java</language>
      <instantiationStrategy>per-lookup</instantiationStrategy>
      <executionStrategy>once-per-session</executionStrategy>
      <threadSafe>true</threadSafe>
      <parameters>
        <parameter>
          <name>additionalClasspathElements</name>
          <type>java.util.List</type>
          <since>2.4</since>
          <required>false</required>
          <editable>true</editable>
          <description>Additional elements to be appended to the classpath.</description>
        </parameter>
        <parameter>
          <name>argLine</name>
          <type>java.lang.String</type>
          <since>2.1</since>
          <required>false</required>
          <editable>true</editable>
          <description>Arbitrary JVM options to set on the command line.</description>
        </parameter>
        
        ......
        
      </parameters>
      <configuration>
        <argLine implementation="java.lang.String">${argLine}</argLine>
        <basedir implementation="java.io.File" default-value="${basedir}"/>
        <childDelegation implementation="boolean" default-value="false">${childDelegation}</childDelegation>
        <classesDirectory implementation="java.io.File" default-value="${project.build.outputDirectory}"/>
        
        ...
        
        <skip implementation="boolean" default-value="false">${maven.test.skip}</skip>
        <skipExec implementation="boolean">${maven.test.skip.exec}</skipExec>
        <skipTests implementation="boolean" default-value="false">${skipTests}</skipTests>
        <test implementation="java.lang.String">${test}</test>
        <testClassesDirectory implementation="java.io.File" default-value="${project.build.testOutputDirectory}"/>
        <testFailureIgnore implementation="boolean" default-value="false">${maven.test.failure.ignore}</testFailureIgnore>
        <testNGArtifactName implementation="java.lang.String" default-value="org.testng:testng">${testNGArtifactName}</testNGArtifactName>
        <testSourceDirectory implementation="java.io.File" default-value="${project.build.testSourceDirectory}"/>
        <threadCount implementation="int">${threadCount}</threadCount>
        <trimStackTrace implementation="boolean" default-value="true">${trimStackTrace}</trimStackTrace>
        <useFile implementation="boolean" default-value="true">${surefire.useFile}</useFile>
        <useManifestOnlyJar implementation="boolean" default-value="true">${surefire.useManifestOnlyJar}</useManifestOnlyJar>
        <useSystemClassLoader implementation="boolean" default-value="true">${surefire.useSystemClassLoader}</useSystemClassLoader>
        <useUnlimitedThreads implementation="boolean" default-value="false">${useUnlimitedThreads}</useUnlimitedThreads>
        <workingDirectory implementation="java.io.File">${basedir}</workingDirectory>
        <project implementation="org.apache.maven.project.MavenProject" default-value="${project}"/>
        <session implementation="org.apache.maven.execution.MavenSession" default-value="${session}"/>
      </configuration>
      <requirements>
        <requirement>
          <role>org.apache.maven.artifact.factory.ArtifactFactory</role>
          <field-name>artifactFactory</field-name>
        </requirement>
        <requirement>
          <role>org.apache.maven.artifact.resolver.ArtifactResolver</role>
          <field-name>artifactResolver</field-name>
        </requirement>
        <requirement>
          <role>org.apache.maven.artifact.metadata.ArtifactMetadataSource</role>
          <field-name>metadataSource</field-name>
        </requirement>
        <requirement>
          <role>org.apache.maven.toolchain.ToolchainManager</role>
          <field-name>toolchainManager</field-name>
        </requirement>
      </requirements>
    </mojo>
```

### **二、下载****源码****，查看插件目标实现类 org.apache.maven.plugin.surefire.SurefirePlugin**

**1、查看使用的 maven-surefire-plugin 插件版本：2.12.4**

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=MjkwNmRhZWRkNzlkN2YxMDc2MTFhYjU0ZTBlYTUyZWZfUzFISTV4WnQzbTVoNURRbTJFcml3NzMzdE5yMVBkM2tfVG9rZW46WlFJN2JsVWo0b25IYTd4TFpyeWN6b0RrbmJiXzE3MzQwMTIxNzE6MTczNDAxNTc3MV9WNA)

**2、在** **[Maven 中央仓库](https://central.sonatype.com/)****查找 maven-surefire-plugin 插件：**

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=MGQxZWUyZDJkMmI2NDM2NDY3ZWNiYmJiNTE3YWUzMGZfa1RKNEgwYmQ3WHlqQWNWdXpxcnQ2WERUNlJ6V2tXT1pfVG9rZW46S25FdGJHcWVPb0k0STJ4V1FFQWNMcG50bllkXzE3MzQwMTIxNzE6MTczNDAxNTc3MV9WNA)

选 apache 下的插件：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=OWQ2ODA4YzcwMTg4ODJhOWU0MzBjN2JkNjIyMTVjMWFfVDhteXZmdVNXWkRvbVdieExMQWtBeVlJTUZkUndic3dfVG9rZW46RjF4a2I4M1pIb1YwSnl4b1VJMmM1TkVabkpmXzE3MzQwMTIxNzE6MTczNDAxNTc3MV9WNA)

查找插件源码：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=OTlhMDg2ZGFhMjMxY2FhZTlkODViMjIzNGQxOTNhM2RfNlRuMDZETzdZTGNTaUFVZjFzWGJvMGx4QldZTm93Y1VfVG9rZW46VVpqYWJKVmJvb2JpY0d4WHp0c2NZWnNvbjJjXzE3MzQwMTIxNzE6MTczNDAxNTc3MV9WNA)

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=Yzg5ZjBiYzIyYjllZjk4NzY5ZjkwMzM1ZGU2MjlkNGZfczZleUoxbnBqM3lOVDJGUWtqV1YwZVo5aTZXNlNOYUtfVG9rZW46RHpxcWJwSnR3b1JkM3h4c0lqcWNDdHJ2bmZjXzE3MzQwMTIxNzE6MTczNDAxNTc3MV9WNA)

在 master 分支，选择 tags，定位到 [2.12.4 版本](https://github.com/apache/maven-surefire/tree/surefire-2.12.4)（可选，最终克隆到本地时可以切换分支）：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=ZjZkYzE2MTAxMjMzMTQzM2NiNzFjOTJjMTAyY2JhZGNfTUFuNFJZeXhicmRaYUprU1duOXE2UHVkaU9kaGtDSUNfVG9rZW46Qm1lVGJ3NGhkb0N3aUp4VkhkaGNZWjJzbklkXzE3MzQwMTIxNzE6MTczNDAxNTc3MV9WNA)

test 目标的实现类在 maven-surefire-plugin/src/main/java/org/apache/maven/plugin/surefire/SurefirePlugin.java

**3、****克隆****代码**

将代码克隆到本地，使用 IDEA 打开，这时打开的是当前的最新版本 3.5.2，定位到上面的路径发现类名以变为 SurefireMojo。

切换分支到 2.12.4 版本：

```Shell
git checkout surefire-2.12.4
```

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=ZTY4OTdmMjU1NDNiMjYwNWQ1YjRlMGE2MWE3YzcwNzRfQ1RFZ3NkZ2hxRVhqN0JFUGVZamRiMHVlczh4cnpvYklfVG9rZW46QmVsa2JtNDE1b1pSaGd4TlRWSmN5RHNlbkRmXzE3MzQwMTIxNzE6MTczNDAxNTc3MV9WNA)

### **三、阅读****源码**

查看类的继承关系如下：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDNkMGE3ZGNkYTNkNWQzNmQzZjBhMzZkNmIyYTc1MzBfdmMxalV1Skk0VlJzTnhPeDJpZFd6THUwY3gxWng4dmxfVG9rZW46S2ZoQ2JhS0pUb1lOZTd4Zm4wSmNIVklhbkJmXzE3MzQwMTIxNzE6MTczNDAxNTc3MV9WNA)

查看类名定义：

```Java
@Mojo( name = "test", defaultPhase = LifecyclePhase.TEST, threadSafe = true,
       requiresDependencyResolution = ResolutionScope.TEST )
public class SurefirePlugin
    extends AbstractSurefireMojo
    implements SurefireReportParameters
```

> 说明：
>
> **@Mojo 注解详解：**用来配置 Maven 插件的目标（goal）。这个注解用于定义一个 MOJO 的元数据，即 Maven 插件中的一个特定任务或功能。
>
> ```Java
> @Mojo(
>     name = "test", 
>     defaultPhase = LifecyclePhase.TEST, 
>     threadSafe = true,
>     requiresDependencyResolution = ResolutionScope.TEST
> )
> ```
>
> - **name**：指定**插件目标的名称**。在这个例子中，目标名为 `"test"`。
> - **defaultPhase**：指定了**默认的生命周期阶段**，在该阶段自动执行此目标。这里设置为 `LifecyclePhase.TEST`，表示这个目标会在 Maven 构建生命周期的`test`阶段被调用。
> - **threadSafe**：表明这个 MOJO 是否是线程安全的。如果设置为 `true`，则表示可以在多线程环境中安全地执行。这在并行构建时非常重要。
> - **requiresDependencyResolution**：指示这个 MOJO **执行之前需要解析依赖项的范围**。这里设置为 `ResolutionScope.TEST`，意味着在执行前需要解析测试范围内的所有依赖项。这确保了所有必要的测试库都可用。

SurefirePlugin 的主要属性和 execute 方法都来自其抽象父类 AbstractSurefireMojo：

(1) skip 属性：

```Java
/**
 * Set this to "true" to bypass unit tests entirely. Its use is NOT RECOMMENDED, especially if you enable it using
 * the "maven.test.skip" property, because maven.test.skip disables both running the tests and compiling the tests.
 * Consider using the <code>skipTests</code> parameter instead.
 */
@Parameter( property = "maven.test.skip", defaultValue = "false" )
protected boolean skip;
```

(2) skipTests 属性：

```Java
/**
 * Set this to "true" to skip running tests, but still compile them. Its use is NOT RECOMMENDED, but quite
 * convenient on occasion.
 *
 * @since 2.4
 */
@Parameter( property = "skipTests", defaultValue = "false" )
protected boolean skipTests;
```

(3) execute 方法：

```Java
public void execute()
    throws MojoExecutionException, MojoFailureException
{
    // Stuff that should have been final
    setupStuff();

    if ( verifyParameters() && !hasExecutedBefore() )
    {
        DefaultScanResult scan = scanDirectories();
        if ( scan.isEmpty() )
        {
            if ( getEffectiveFailIfNoTests() )
            {
                throw new MojoFailureException(
                    "No tests were executed!  (Set -DfailIfNoTests=false to ignore this error.)" );
            }
            handleSummary( Summary.noTestsRun() );
            return;
        }
        logReportsDirectory();
        executeAfterPreconditionsChecked( scan );
    }
}
```

execute 方法**执行流程**：

1. **初始化**：SurefirePlugin 类首先会读取项目的 POM 文件，解析出与测试相关的配置信息，比如测试框架、测试类的位置、是否包含或排除某些测试等。
2. **创建测试运行器**：根据所使用的测试框架（JUnit 或 TestNG），Surefire 插件会选择合适的测试运行器（Runner）。对于 JUnit，这通常意味着使用 `junit-platform-surefire-provider` 来执行基于 JUnit 5 的测试，或者使用 `surefire-junit47` 来执行 JUnit 4.x 版本的测试。
3. **执行测试**：测试运行器会加载并执行所有符合条件的测试类。每个测试类中的测试方法会被逐一执行，并且测试结果会被收集起来。
4. **报告生成**：测试完成后，Surefire 插件会根据测试结果生成报告文件。默认情况下，这些报告会保存在 `target/surefire-reports` 目录下，包括 XML 格式的详细报告和 TXT 格式的简要报告。如果配置了其他格式的报告，如 HTML 报告，也会相应地生成。
5. **失败处理**：如果有任何测试失败，`TestMojo` 会抛出 `MojoFailureException` 异常，导致构建过程停止（除非配置为即使有测试失败也继续构建）。

## 插件传参方式

在上一节中，看到 SurefirePlugin 类中的属性上都有 @Parameter 注解，这个注解是读取参数的核心注解。有 3 种参数配置的方式，@Parameter 可以从通过读取参数的值为属性赋值。

- `@Parameter`注解是 Maven 专门为插件开发提供的自定义注解，不属于 JDK。它使得插件开发者可以方便地从多个来源读取配置参数，并将其注入到插件实例中。
- `@Parameter`注解用于标注插件类中的字段或方法参数，使得这些字段或参数可以从 POM 文件、命令行、环境变量或系统属性中读取值。Maven 在构建过程中会自动解析这些注解，并将相应的配置值注入到插件实例中。

注解源码：

```Java
@Documented
@Retention( RetentionPolicy.CLASS )
@Target( { ElementType.FIELD } )
@Inherited
public @interface Parameter
{
    String alias() default "";

    String property() default "";

    String defaultValue() default "";

    boolean required() default false;

    boolean readonly() default false;
}
```

说明：

- **`property`****：**指定可以从命令行或 POM 文件中读取的属性名称。例如，`property = "maven.test.skip"` 表示可以通过`-Dmaven.test.skip=true`来设置这个属性。
- **`defaultValue`****：**如果没有从其他来源读取到值，则使用默认值。
- **`required`****：**是否必须提供该参数。如果设置为`true`，并且没有提供值，Maven 会在构建时抛出错误。
- **`readonly`****：**是否只读。如果设置为`true`，则不允许在插件执行期间修改该参数的值。
- **`alias`****：**为参数指定别名，允许通过不同的名称访问同一个参数。

skipTests 属性的作用是跳过测试但不跳过测试代码的编译。接下来，我们通过不同的方式配置 skipTests 的值。

### **1、在 pom.xml 文件中设置 skipTests 属性的值**

```Shell
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.12.4</version>
            <configuration>
                <skipTests>true</skipTests>
            </configuration>
        </plugin>
    </plugins>
</build>
```

控制台打印如下：

```Shell
(base) PS C:\...\maven_00> mvn test
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 3 resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\test-classes
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ maven_00 ---
[INFO] Tests are skipped.
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.045 s
[INFO] Finished at: 2024-12-05T16:53:10+08:00
[INFO] ------------------------------------------------------------------------
```

由下图可知，测试代码编译了，但测试直接跳过了：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2UwN2Q3ZWUzZmIyNjY3YzAzN2Q3NWY5YTIwNTI2NGRfQWVQUkhrczhCODlQcEJVRTdoWTFrUURuNzdoYjJ4QURfVG9rZW46T1laNmJUNTRab0pXZFJ4NWRTcWNnQ2tPblZkXzE3MzQxNjI3OTY6MTczNDE2NjM5Nl9WNA)

### **2、命令行参数**

可以通过命令行传递`skipTests`参数。Maven 会优先使用命令行参数，覆盖`pom.xml`中的配置。

在 pom.xml 文件配置的 skipTests=false，在命令行中，赋值为 true，观察运行结果：

控制台打印如下：

```Shell
(base) PS C:\...\maven_00> mvn test -DskipTests=true
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 3 resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ maven_00 ---
[INFO] Surefire report directory: C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running service.BallSportsTest
Everybody up!
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.063 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.447 s
[INFO] Finished at: 2024-12-05T17:00:48+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到，命令行优先，会覆盖 pom.xml 文件中的配置。

### 3、环境变量---TODO

也可以通过环境变量设置`skipTests`。这通常需要通过系统属性来传递给 Maven。例如，在 Unix 系统上，可以这样做：

```Bash
export MAVEN_OPTS="-DskipTests=true"
```

### 附：优先级

当多个来源都提供了`skipTests` 的值时，Maven 会按照以下优先级顺序来决定最终使用的值：

1. **命令行参数**：这是最高优先级。如果在命令行中指定了`-DskipTests=true`，它会覆盖`pom.xml`中的配置。
2. **环境变量**：通过`MAVEN_OPTS`或其他方式设置的环境变量。
3. **POM** **文件**：这是最低优先级。如果在`pom.xml`中配置了`skipTests`，但命令行或环境变量中也提供了相同的属性，那么`pom.xml`中的配置会被覆盖。

## 插件和生命周期的阶段绑定

Maven 只是定义了生命周期中的阶段，而没有定义每个阶段中具体的实现，这些实现是**由插件的目标来完成**的。所以需要将阶段和插件目标进行绑定，来让插件目标帮助生命周期的阶段做具体的工作。生命周期中的每个阶段支持绑定多个插件的多个目标。

当我们将生命周期中的阶段和插件的目标进行绑定的时候，执行`mvn 阶段`就可以执行和这些阶段绑定的`插件目标`。

### 内置绑定

**思考**：我们并没有做过绑定的事，为啥能成功执行`mvn 阶段`命令呢？

**答**：这是因为 Maven 提供了一些插件，并将一些插件的目标绑定到生命周期的某些阶段上。

如何知道哪些阶段绑定了哪些插件目标呢？

我们可以新建一个 Maven 项目，使用 mvn help:effective-pom 命令查看当前工程的 pom 配置情况。

- 新建了一个 maven_00 项目，pom.xml 文件未做修改
- 执行命令 mvn help:effective-pom -Doutput=effective-pom.xml
- 查看输出文件

```XML
<plugins>

  <plugin>
    <artifactId>maven-clean-plugin</artifactId>
    <version>2.5</version>
    <executions>
      <execution>
        <id>default-clean</id>
        <phase>clean</phase>
        <goals>
          <goal>clean</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
  
  <plugin>
    <artifactId>maven-resources-plugin</artifactId>
    <version>2.6</version>
    <executions>
      <execution>
        <id>default-testResources</id>
        <phase>process-test-resources</phase>
        <goals>
          <goal>testResources</goal>
        </goals>
      </execution>
      <execution>
        <id>default-resources</id>
        <phase>process-resources</phase>
        <goals>
          <goal>resources</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
  
  <plugin>
    <artifactId>maven-jar-plugin</artifactId>
    <version>2.4</version>
    <executions>
      <execution>
        <id>default-jar</id>
        <phase>package</phase>
        <goals>
          <goal>jar</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
  
  <plugin>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.1</version>
    <executions>
      <execution>
        <id>default-compile</id>
        <phase>compile</phase>
        <goals>
          <goal>compile</goal>
        </goals>
      </execution>
      <execution>
        <id>default-testCompile</id>
        <phase>test-compile</phase>
        <goals>
          <goal>testCompile</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
  
  <plugin>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.12.4</version>
    <executions>
      <execution>
        <id>default-test</id>
        <phase>test</phase>
        <goals>
          <goal>test</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
  
  <plugin>
    <artifactId>maven-install-plugin</artifactId>
    <version>2.4</version>
    <executions>
      <execution>
        <id>default-install</id>
        <phase>install</phase>
        <goals>
          <goal>install</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
  
  <plugin>
    <artifactId>maven-deploy-plugin</artifactId>
    <version>2.7</version>
    <executions>
      <execution>
        <id>default-deploy</id>
        <phase>deploy</phase>
        <goals>
          <goal>deploy</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
  
  <plugin>
    <artifactId>maven-site-plugin</artifactId>
    <version>3.3</version>
    <executions>
      <execution>
        <id>default-site</id>
        <phase>site</phase>
        <goals>
          <goal>site</goal>
        </goals>
        <configuration>
          <outputDirectory>C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\site</outputDirectory>
          <reportPlugins>
            <reportPlugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-project-info-reports-plugin</artifactId>
            </reportPlugin>
          </reportPlugins>
        </configuration>
      </execution>
      <execution>
        <id>default-deploy</id>
        <phase>site-deploy</phase>
        <goals>
          <goal>deploy</goal>
        </goals>
        <configuration>
          <outputDirectory>C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\site</outputDirectory>
          <reportPlugins>
            <reportPlugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-project-info-reports-plugin</artifactId>
            </reportPlugin>
          </reportPlugins>
        </configuration>
      </execution>
    </executions>
    <configuration>
      <outputDirectory>C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\site</outputDirectory>
      <reportPlugins>
        <reportPlugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-project-info-reports-plugin</artifactId>
        </reportPlugin>
      </reportPlugins>
    </configuration>
  </plugin>
  
</plugins>
```

结论：Maven 内置了 8 个插件的默认绑定

- maven-clean-plugin:clean → clean 阶段
- maven-resources-plugin:resources → process-resources 阶段
- maven-resources-plugin:testResources→ process-test-resources 阶段
- maven-compiler-plugin:compile → compile 阶段
- maven-compiler-plugin:testCompile → test-compile 阶段
- maven-surefire-plugin:test → test 阶段
- maven-jar-plugin:jar → package 阶段
- maven-install-plugin:install → install 阶段
- maven-deploy-plugin:deploy → deploy 阶段
- maven-site-plugin:site → site 阶段
- maven-site-plugin:deploy → site-deploy 阶段

Maven 的内置的生命周期和插件绑定是其核心功能的一部分，是**硬编码**在 Maven 内部的。这意味着**无法移除这些的默认绑定**。

> 附：如果想看看各阶段默认绑定的插件目标，可以新建一个 Maven 项目，然后执行`mvn help:effective-pom`查看项目最终的 pom.xml 文件即可。
>
> `mvn help:effective-pom` 是 Maven 中的一个命令，用于显示项目的"有效 POM"（Effective POM）。所谓有效 POM，是指在**考虑了所有父 POM 的****继承****、激活的 profile、以及从设置文件和****环境变量****中引入的属性之后，Maven 实际使用的****项目对象模型**。换句话说，它展示了 **Maven 在构建过程中实际应用的所有配置**。
>
> 
>
> 当你运行`mvn help:effective-pom`命令时，Maven 会输出一个包含了所有继承下来和覆盖掉的设置的完整 POM 文件。这个输出可以帮助你理解你的项目是如何被配置的，包括：
>
> - 继承自父 POM 的配置。
> - 激活的 profiles 对 POM 的影响。
> - 来自命令行参数、settings.xml 文件或环境变量的属性值替换。
>
> 
>
> 如果你希望将输出保存到一个文件中以便进一步分析，可以在命令后面加上 `-Doutput=filename.xml` 参数，例如：
>
> ```Shell
> mvn help:effective-pom -Doutput=effective-pom.xml
> ```
>
> 这将会把有效 POM 输出到名为 `effective-pom.xml` 的文件中。

### 自定义绑定

除了内置的绑定，我们开发者也可以将一些阶段和插件目标进行绑定，用于完成一些操作。这种自定义绑定让 Maven 项目在构建的过程中可以执行更丰富的操作。

#### 示例一：将源码打包的插件目标绑定到 verify 阶段

Maven 内置绑定了 maven-jar-plugin:jar 和 package 阶段，因此，我们执行`mvn package`命令实际执行的就是 jar 目标。jar 目标的作用是将编译后的 .class 类文件和一些资源文件打成 jar 包。

现在，我们想把 .java 源代码文件进行打包，并安装到本地仓库。内置的绑定关系中没有涉及到这一任务的，所以需要用户自己配置。

**准备工作：**

- 插件选择：插件`maven-source-plugin`的`jar-no-fork`目标可以打包源代码文件。
- 生命周期阶段选择：`default`生命周期的`verify`阶段是在测试完成之后、将构件安装到本地仓库之前执行的阶段。而且该阶段没有任何默认绑定。

**实现：**

1、pom.xml 配置如下：

```XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.myphoenix</groupId>
    <artifactId>maven_00</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
                <version>3.2.0</version>
                <executions>
                    <execution>
                        <id>default-jar-no-fork</id>
                        <goals>
                            <!--绑定的插件目标-->
                            <goal>jar-no-fork</goal>
                        </goals>
                        <!--绑定的阶段-->
                        <phase>verify</phase>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

</project>
```

2、执行命令`mvn install -DskipTests=true`，查看控制台：

```Shell
(base) PS C:\...\maven_00> mvn install -DskipTests=true
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 3 resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ maven_00 ---
[INFO] Tests are skipped.
[INFO]
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ maven_00 ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\maven_00-1.0-SNAPSHOT.jar
[INFO]
[INFO] --- maven-source-plugin:3.2.0:jar-no-fork (default-jar-no-fork) @ maven_00 ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\maven_00-1.0-SNAPSHOT-sources.jar
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ maven_00 ---
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\maven_00-1.0-SNAPSHOT.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\maven_00\1.0-SNAPSHOT\maven_00-1.0-SNAPSHOT.jar
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\pom.xml to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\maven_00\1.0-SNAPSHOT\maven_00-1.0-SNAPSHOT.pom
[INFO] Installing C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\maven_00-1.0-SNAPSHOT-sources.jar to C:\Softwares\Developer_Kits\apache-maven-3.8.8\repository\cn\myphoenix\maven_00\1.0-SNAPSHOT\maven_00-1.0-SNAPSHOT-sources.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.574 s
[INFO] Finished at: 2024-12-05T20:57:56+08:00
[INFO] ------------------------------------------------------------------------
```

3、验证

(1) 将 jar 包格式修改为 rar 格式，方便打开：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=MTIwODQ5ZGQ3OWY1ZTAzM2JmNDEyM2E0YmFiYjZjMWFfM1czTUVmS2IzWmdQdFVRbGhvR05IbUFRaFIwZ3RoUjlfVG9rZW46QUdPbWJSY29qb3JERE14ZEZVVWNzWXhVbk9mXzE3MzQxNjI5ODY6MTczNDE2NjU4Nl9WNA)

可以看到，.java 源码文件和主资源文件被打成了 jar 包。

(2) 查看本地 Maven 仓库，发现源码 jar 包安装成功：

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=OGQwNTRlMWI3ZDIxYmQ3ZWVhZDU4ODMxM2I3MjUwMGJfTEp6NG5ncVFGaTZ2ODBXWmZmdFJQZmI3VVBkZ0VldzhfVG9rZW46UWN6RGJnRkhzb3VGRzR4ckZOTWNJY3VmblBlXzE3MzQxNjI5ODY6MTczNDE2NjU4Nl9WNA)

> 附：maven-source-plugin:jar-no-fork 和 maven-jar-plugin:jar 的区别？
>
> \1. **用途**
>
> - **`maven-source-plugin:jar-no-fork`**:
>   - **主要用途：**打包项目的源代码（即`.java`文件）到一个 JAR 文件中。
>   - **输出：**生成的 JAR 文件包含的是源代码文件（如`.java`文件）和主资源文件，而不是编译后的`.class`文件。
> - **`maven-jar-plugin:jar`**:
>   - **主要用途：**打包项目的编译后的类文件（即`.class`文件）到一个 JAR 文件中。
>   - **输出：**生成的 JAR 文件包含的是编译后的`.class`文件，可能还包括资源文件（如配置文件、图片等）。
>
> 
>
> \2. **JVM 进程**
>
> - **`maven-source-plugin:jar-no-fork`****：**
>   - **不启动新 JVM 进程：**`jar-no-fork` 目标不会启动新的 JVM 进程来执行任务，而是直接在当前 Maven 进程中完成源代码的打包。这可以提高构建效率，尤其是在频繁构建的开发环境中。
> - **`maven-jar-plugin:jar`****：**
>   - **默认不启动新 JVM 进程：**`maven-jar-plugin`的`jar`目标通常也不会启动新的 JVM 进程，除非你在配置中明确指定了某些需要 fork 的参数（如`forkMode`）。大多数情况下，它也是在当前 Maven 进程中执行的。
>
> 
>
> \3. **附加到构建输出**
>
> - **`maven-source-plugin:jar-no-fork`****：**
>   - **通常附加为额外的 artifact：**生成的源代码 JAR 文件通常会被附加到构建输出中，作为项目的次要 artifact。这意味着它**不会覆盖主 JAR 文件**，而是作为一个单独的 JAR 文件发布。你可以通过 `attach` 配置项控制是否将源代码 JAR 附加到构建输出中。
> - **`maven-jar-plugin:jar`****：**
>   - **作为主 artifact：**生成的编译后的 JAR 文件通常是项目的主 artifact，即它是项目的主要输出文件。如果你没有配置其他插件来生成额外的 JAR 文件，`maven-jar-plugin` 生成的 JAR 文件将是唯一被发布的 JAR 文件。
>
> 
>
> \4. **生命周期绑定**
>
> - **`maven-source-plugin:jar-no-fork`****：**
>   - **没有默认的生命周期绑定****：**`jar-no-fork` 目标不会自动绑定到任何 Maven 生命周期阶段。你需要在 `pom.xml` 中显式地配置它，并将其绑定到你希望执行的生命周期阶段（如`package`、`verify`等）。
> - **`maven-jar-plugin:jar`****：**
>   - **默认绑定到****`package`**阶段：`maven-jar-plugin`的`jar` 目标默认绑定到`package`生命周期阶段，负责在项目打包时生成编译后的 JAR 文件。

#### 示例二：将clean目标绑定到validate阶段

Maven 的 3 套生命周期的运行相互独立、互不依赖。但是每套中的阶段是有先后顺序的，运行某个阶段的时候，会先执行他前面所有的阶段。

清理代码使用的是`clean`周期中的`clean`阶段，编译代码用的是`default`周期中的`compile`阶段，当直接运行`mvn compile`编译代码的时候并不会去清理代码。如果我们想每次编译之前强制先清理代码，我们经常这么写：

```Shell
mvn clean compile
```

我们也可以利用自定义绑定功能，在 default 生命周期的第一个阶段 validate 绑定清理代码的插件，这样执行 default 生命周期的任何阶段都会先执行 clean 目标清理代码。

**实现：**

1、pom.xml 配置如下：

```XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.myphoenix</groupId>
    <artifactId>maven_00</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-clean-plugin</artifactId>
                <version>3.4.0</version>
                <executions>
                    <execution>
                        <id>clean-goal</id>
                        <goals>
                            <!-- 绑定的目标 -->
                            <goal>clean</goal>
                        </goals>
                        <!-- 绑定的阶段 -->
                        <phase>validate</phase>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

</project>
```

2、执行命令`mvn package -DskipTests=true`，查看控制台：

```Shell
(base) PS C:\...\maven_00> mvn package -DskipTests=true
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< cn.myphoenix:maven_00 >------------------------
[INFO] Building maven_00 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:3.4.0:clean (clean-goal) @ maven_00 ---
[INFO] Deleting C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 3 resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ maven_00 ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ maven_00 ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ maven_00 ---
[INFO] Tests are skipped.
[INFO]
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ maven_00 ---
[INFO] Building jar: C:\Coding_Gallery\Intellij_IDEA_Workspace\maven_lifecycle_plugin_demo\maven_00\target\maven_00-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.253 s
[INFO] Finished at: 2024-12-05T21:16:24+08:00
[INFO] ------------------------------------------------------------------------
```

可以看到，执行流程一开始就调用 maven-clean-plugin 插件的 clean 目标清理了 target 文件夹，执行`mvn package`命令就会进行代码清理。

## 总结

### 1、常用插件&插件目标思维导图

![img](https://tw9tzjv8tr6.feishu.cn/space/api/box/stream/download/asynccode/?code=MDM4ZTNlODJiMDI0YTUxMDc4MzM3NDMyN2ViMTlmNDBfd3luNG9mM1gwd0ZDMk1qQkhMc0UwM21PZTdjSUY5R0hfVG9rZW46TXAwZ2JFU3pOb2NrVVZ4S1pEa2N5UzlUbmVmXzE3MzQxNjMyMTc6MTczNDE2NjgxN19WNA)

# 常用网站

1、Maven 中央仓库：https://central.sonatype.com/

可以搜索插件，查看插件目标的源码等。

2、查找依赖的 GAV 坐标：https://mvnrepository.com/



**参考**：

- https://maven.apache.org/guides/plugin/guide-java-plugin-development.html
- https://juejin.cn/post/7130090246133252109#heading-19
- https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference
- 插件目标参数配置：https://maven.apache.org/guides/mini/guide-configuring-plugins.html#Configuring_Parameters