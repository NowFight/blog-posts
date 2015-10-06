# Maven 学习 - 入门

![](https://maven.apache.org/images/maven-logo-black-on-white.png)

在进行Java开发的过程中，我接触到了Maven这套构建工具。所以，花费了点时间学习了一下这套构建工具，在学习过程中学习到的有关Maven的知识，在这里分享给大家。

## 什么是Maven

首先，在介绍Maven这套工具之前，我们得了解Maven是什么。
Maven，在依地语中的意思是知识的积累，Maven在最初的时候，是作为Jakarta的Turbine项目的构建工具。在Maven出现之前，不同的Java项目使用不同的Ant配置进行构建，并且将jar包包含在版本控制系统（CVS）中，没有一套统一的构建工具。如果有一套工具，可以拥有一套标准的方法来构建一个项目，并且可以清楚的定义一个项目是由哪些部分组成的，拥有一种简单的方法发布项目的信息，在不同的项目之间可以分享jar包。出于这个目的，Maven被创造了出来。

Maven最初始的目标是让开发者可以在最短的周期内可以快速的理解一个项目。为了达到这个目的，Maven解决了一下几个问题：

* 让构建过程变的简单
* 提供一套统一的构建系统
* 提供项目相关的信息
* 可以透明得迁移新的特性

## 使用Maven

在使用Maven之前，我们需要确保我们已经安装了Maven构建工具。没有安装Maven构建工具的，可以参考官方提供的手册参考[如何安装Maven](http://maven.apache.org/install.html)

在安装了Maven以后，可以通过命令 `mvn -version`查看Maven的版本信息：

```bash

$ mvn -version

Apache Maven 3.3.3 (7994120775791599e205a5524ec3e0dfe41d4a06; 2015-04-22T19:57:37+08:00)
Maven home: /usr/local/opt/maven
Java version: 1.7.0_79, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.7.0_79.jdk/Contents/Home/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.10.2", arch: "x86_64", family: "mac"

```

接下来，我们通过Maven创建一个新的项目开始。

### 使用Maven创建一个新的Java项目

```bash

$ mvn -B archetype:generate \
	-DarchetypeGroupId=org.apache.maven.archetypes \
	-DgroupId=com.myapp \
	-DartifactId=myapp

```

在上面的Maven命令中，我们创建了一个名为`myapp`的普通Java项目。一旦我们执行了上面的命令，就会在当前目录下创建一个目录为`myapp`的工程。在这个工程目录下，包含了一个名为`pom.xml`的文件，这个新创建的pom文件内容大致如下：

```xml

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.myapp</groupId>
  <artifactId>myapp</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>myapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>

```

这个pom文件表示了当前这个项目的`项目对象模型（Project Object Model(POM)`。在Maven中，POM是它进行操作的基本单位。Maven的本质是以围绕POM这个概念为中心来管理整个项目的。POM中包含了项目中所有和项目相关的重要的信息，并且提供了类似一站式购物那样的依赖管理方式，可以很方便的找到当前项目关联的所有依赖来项目。

上面的pom.xml文件中的内容，包含了当前这个项目的相关信息。我们首先就上面这个POM文件中的一些元素，解释下具体的含义：

* `project` 这个元素是Maven的pom.xml文件的顶层元素。每个POM文件都是以一个`project`元素包裹的。
* `modelVersion` 这个元素指定了当前这个pom.xml文件使用的项目对象模型(POM)的版本。
* `groupId` 这个元素表示创建这个项目的组织的唯一标识。这个元素的值是区分一个项目的关键信息之一，它的值通常是由该项目的组织的域名的反写产生的（和Java的包名的机制一样）。
* `artifactId` 这个元素是唯一标识了该项目最终生成的artifact（可以理解为是我们编写代码产生的一个程序，所以可以理解为是手工作品）。一个项目的artifact一般是一个`jar`文件。一个项目最终生成的artifact的名字的格式是`<artifactId>-<version>.<extension>`，比如上面的项目生成的artifact为`myapp-1.0.jar`。
* `packaging` 这个元素指定了该artifact打包的类型（比如：jar、war、ear等）。这不只是意味着该artifact是以什么方式打包，也指定了在构建过程中会用到的特定的生命周期（所谓的生命周期，简单的说就是构建的过程）。`packaging`元素的默认值是`JAR`。
* `version` 这个元素指定了项目生成的artifact的版本号。在版本号中会包含当前项目的进度，比如这里的`SNAPSHOT`。
* `name` 这个元素表示项目的名字，常用于生成的文档中。
* `url` 这个元素表示该项目所在的站点，常用于生成的文档中。
* `description` 这个元素提供了对项目的描述，通常用于生成的文档中。 

在项目被创建以后，除了会生成上面的pom.xml文件，还会产生一个标准的目录结构：

```bash

myapp
|-- pom.xml
`-- src
    |-- main
    |   `-- java
    |       `-- com
    |           `-- myapp
    |               `-- App.java
    `-- test
        `-- java
            `-- com
                `-- myapp
                    `-- AppTest.java

```

上面的目录结构是Maven项目的标准布局，项目的源代码在目录`${basedir}/src/main/java`目录下，而项目的测试代码存放在目录`${basedir}/src/test/java`目录下。对于${basedir}，这里表示的是包含了pom.xml文件的目录，也就是项目的根目录。

现在，我们已经简单介绍了maven相关的概念和maven标准的目录结构。但是，在我们使用Maven构建项目之前，我们先简单了解下Maven的生命周期。

## Maven的构建的生命周期

Maven的构建机制，是围绕生命周期（lifecycle）这个概念进行构建的。这意味着构建和发布一个项目的整个过程是预先被定义了的。采用这种方式进行项目构建，可以让用户可以在学习很少的一部分命令的情况下通过POM对项目进行构建。

在Maven中，内建了三种构建生命周期：`default`、`clean`、`site`。`default`生命周期处理项目的部署，`clean`生命周期处理项目构建后的清理工作，`site`生命周期对项目的站点文档进行生成。

Maven的生命周期，又是由不同的阶段（phase）构成的。每一个生命周期，都包含了一系列的阶段，每一个阶段都表示了生命周期中的一个状态。比如，对于`default`生命周期来说，包含了如下的这些阶段：

* validate
* compile
* test
* package
* integration-test
* verify
* install
* deploy

Maven在执行`default`生命周期的时候，会顺序执行这些阶段来完成不同的工作。

## 使用maven构建项目

现在我们已经安装好Maven，并且对Maven的相关概念有了一些认识。下面，我们可以开始使用Maven对我们的项目进行构建了。

### 编译项目

在前面，我们已经创建了一个名为`myapp`的项目。现在，我们可以通过下面的命令对这个项目进行编译。

```bash

$ mvn compile

```

这个命令执行以后，会有类似下方的输出：

```bash

[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building myapp 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ myapp ---
...
[INFO] skip non existing resourceDirectory /Users/duke/practise/myapp/src/main/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ myapp ---
[INFO] Changes detected - recompiling the module!
...
[INFO] Compiling 1 source file to /Users/duke/practise/myapp/target/classes
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.159 s
[INFO] Finished at: 2015-10-06T18:57:37+08:00
[INFO] Final Memory: 11M/156M
[INFO] ------------------------------------------------------------------------

```

在执行这个命令以后，Maven会首先去下载所有构建当前项目所需要的插件，以及所有被当前项目所依赖所有依赖项目。从上面的输出中可以看出，我们最终构建后的文件被输出到目录`${basedir}/target/classes`中，这个目录是Maven约定的一个标准目录。使用Maven的好处之一，就是当我们遵循Maven的约定以后，我们可以使用尽可能少的配置就可以让Maven为我们构建项目。

### 编译单元测试

现在，我们已经成功地编译了我们的项目。接下来，我们可以让Maven对单元测试进行编译和执行，通过执行下面的命令，可以让Maven编译和执行我们写的单元测试：

```bash

$ mvn test

```

上面的命令会有大致如下的输出：

```

[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building myapp 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
...
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ myapp ---
[INFO] Nothing to compile - all classes are up to date 
...
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ myapp ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to /Users/duke/practise/myapp/target/test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ myapp ---
[INFO] Surefire report directory: /Users/duke/practise/myapp/target/surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running com.myapp.AppTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.004 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.799 s
[INFO] Finished at: 2015-10-06T19:08:08+08:00
[INFO] Final Memory: 13M/156M
[INFO] ------------------------------------------------------------------------

```

从输出中我们可以看到，Maven在编译和执行单元测试之前，会首先编译`main`中的项目代码，然后才会对`test`中的单元测试进行编译和执行，并提供一个有关单元测试结果的简单报告。

在这里，我们使用`surefire`插件对单元测试进行执行。`surefire`插件会对符合该插件约定的命名规则的单元测试文件进行搜索，然后进行执行。默认会执行以下命名规则的文件：

* **/*Test.java
* **/Test*.java
* **/*TestCase.java

默认会排除以下命名规则的文件

* **/Abstract*Test.java
* **/Abstract*TestCase.java

当然，如果只是需要对单元测试进行编译而不需要执行，可以运行命令`mvn test-comiple`实现。

### 项目打包

现在，我们已经对项目进行编译测试过了，我们接下来需要对项目进行打包。当前项目，我们在pom.xml中的`packaging`元素中指定了打包的类型为`jar`。所以我们可以通过下面的命令来打jar包：

```bash

$ mvn package

```

执行这个命令的输出大致如下：

```bash

[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building myapp 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
...
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myapp ---
[INFO] Building jar: /Users/duke/practise/myapp/target/myapp-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.741 s
[INFO] Finished at: 2015-10-06T19:41:05+08:00
[INFO] Final Memory: 10M/156M
[INFO] ------------------------------------------------------------------------

```

Maven在执行这个命令的时候，会从pom.xml文件中获得打包的类型，在这个项目中为jar包类型。然后将打出来的jar输出到目录`${basedir}/target`下。

在打完包以后，我们的jar包现在还是留在我们的项目的target目录下，为了将这个jar部署到我们的本地仓库（默认是`~/.m2/repository`）中以供别的项目使用，我们需要使用如下命令将当前项目的jar包部署到本地仓库中：

```bash

$ mvn install

```

到目前为止，我们已经学习了怎么对项目进行打包和部署到本地仓库中了。

### 清理项目

现在，我们如果需要对构建除的文件进行清理，我们需要使用如下的命令：

```bash

$ mvn clean

[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building myapp 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ myapp ---
[INFO] Deleting /Users/duke/practise/myapp/target
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.255 s
[INFO] Finished at: 2015-10-06T19:47:25+08:00
[INFO] Final Memory: 6M/123M
[INFO] ------------------------------------------------------------------------

```

这个命令会删除`target`目录，因为在这个目录下包含了我们构建这个项目后输出的所有文件。现在，我们的项目就回到被构建之前了。

### IDE相关的命令

我们如果使用IDE开发环境，我们可以使用Maven生成当前项目相关的IDE的描述文件。

对于IntelliJ IEDA环境，可以用如下命令进行生成：

```bash

$ mvn idea:idea

```

对于eclipse环境，可以用如下的命令生成：

```bash

$ mvn eclipse:eclipse

```

## 参考

[http://maven.apache.org/guides/getting-started/index.html](http://maven.apache.org/guides/getting-started/index.html)