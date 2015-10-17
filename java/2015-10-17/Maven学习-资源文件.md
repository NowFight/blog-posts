# Maven学习-资源文件

在前面两篇文章中，我们学习了Maven的[基本使用方式](http://www.cnblogs.com/now-fighting/p/4857625.html)和Maven项目的[标准目录结构](http://www.cnblogs.com/now-fighting/p/4858982.html)。接下来，我们来看下Maven是如果管理项目中的资源文件的。


Java项目的资源文件，主要用于存储系统的配置信息，以及提供系统集成的配置文件。项目中的资源文件夹下一般都存储了以`.properties`为后缀的文件以及`.xml`为后缀的文件，用于记录系统的上下文关系、log以及jdbc相关的配置信息。

## 为Jar包添加资源文件

在我们使用`POM`管理项目的时候，我们在进行打包的时候，需要将项目中的资源文件一起打包到最终的jar包中。所以，我们需要将那些资源文件放置在Maven可以识别的目录下。

在前一篇介绍[Maven目录结构](http://www.cnblogs.com/now-fighting/p/4858982.html)的文章中，我们提到了在Maven的项目目录下，有一个默认的目录用于存放项目的资源文件：`${basedir}/src/main/resources`。所有存放在这个目录下的资源文件，当Maven进行打包的时候，就会将该目录下的资源文件一起打包到jar包中。Maven打包的时候，会按照该`${basedir}/src/main/resources`目录下资源文件组织的目录结构，在jar包的根目录下也会有相同的目录层次结构。

比如：

```bash
my-app
|-- pom.xml
`-- src
    |-- main
    |   |-- java
    |   |   `-- com
    |   |       `-- mycompany
    |   |           `-- app
    |   |               `-- App.java
    |   `-- resources
    |       `-- META-INF
    |           `-- application.properties
    `-- test
        `-- java
            `-- com
                `-- mycompany
                    `-- app
                        `-- AppTest.java

```

上面的项目打包成jar包以后，jar包中的目录结构如下所示：

```bash
|-- META-INF
|   |-- MANIFEST.MF
|   |-- application.properties
|   `-- maven
|       `-- com.mycompany.app
|           `-- my-app
|               |-- pom.properties
|               `-- pom.xml
`-- com
    `-- mycompany
        `-- app
            `-- App.class

```

我们可以看到，资源文件`application.properties`在我们的项目中是在`META-INFO`目录下，而打包以后，该文件仍旧是在该目录下，而`META-INFO`目录是存放在jar包的根目录下的。

上面我们只是介绍了我们项目中系统需要的资源文件的存放位置，对于项目中的单元测试，也可以由一套自己的资源文件。这些资源文件存放的目录结构和上面的类似，只不过存放的目录名不同。单元测试相关的资源文件存放在`${basedir}/src/test/resources`目录下，和前面的目录对比，我们可以发现，区别只是src目录下的`main`目录和`test`目录而已。这也是为什么测试相关的代码和资源都是在`test`目录下，而系统相关的代码和资源都存放在`main`目录下，这样可以方便识别。

我们可以看下一个包含单元测试资源文件的目录结构：

```bash
my-app
|-- pom.xml
`-- src
    |-- main
    |   |-- java
    |   |   `-- com
    |   |       `-- mycompany
    |   |           `-- app
    |   |               `-- App.java
    |   `-- resources
    |       `-- META-INF
    |           |-- application.properties
    `-- test
        |-- java
        |   `-- com
        |       `-- mycompany
        |           `-- app
        |               `-- AppTest.java
        `-- resources
            `-- test.properties

```

如果我们需要在Java代码中访问项目的资源文件，比如上面的`test.properties`文件，我们只需要书写类似下面的代码：

```java
InputStream is = getClass().getResourceAsStream( "/test.properties" );

```

我们在写代码读取资源文件时，定位资源文件的位置的时候，需要参考实际该资源文件最终在jar包中存放的位置，而不是项目中的位置。按照上面提到的，在jar包中，是按照项目的`resources`目录下的目录结构，在jar包的根目录下产生相同的目录结构的。对于上面的`test.properties`文件，在jar包中就是存放在jar包的根目录下，所以我们可以在代码中直接这么定位该文件的位置`"/test.properties"`。

现在，我们已经知道了Maven是如何管理资源文件的了。接下来，我们来看下Maven提供的一种处理资源文件的机制，可以很方便的配置项目的资源。

## 过滤资源文件

有时候，我们的资源文件中设置的值，只能在构建项目的时候才会被指定。为了完成这个需求，在Maven中可以为这个需要在构建的时候才可以确定值的位置，放置一个占位符，来表示这个未来会被设置的值。通过使用`${property}`的方式指定。其中`property`可以是指定在`pom.xml`文件中的值，或者是在`setting.xml`文件中设置的值，或者是放在项目的filters目录（参考[Maven的项目目录结构](http://www.cnblogs.com/now-fighting/p/4858982.html)）下的外部的properties文件中的值，亦或是一个系统属性。这种方式在Maven中称为对资源文件的过滤。

为了使得Maven在将资源文件打包时对一个资源文件进行过滤处理，可以在`pom.xml`中设置`filtering`元素的值为true。

比如下面的例子：

```bash
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>

  ...
 
  <build>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
</project>

```

在上面的例子中，我们在`pom.xml`文件中添加了一个`<build>...</build>`元素，在这个`<build>`元素中，我们定义了`<resources>`元素来指定资源相关的配置，在`<resources>`元素下的`<resource>`元素中，我们可以指定哪个资源文件需要Maven进行过滤处理。在这里，我们通过`<directory>`元素指定了需要被Maven进行过滤的资源文件目录为`src/main/resources`。`<filtering>`元素指定这个资源目录是否需要被Maven进行过滤处理，默认是false，表示不进行处理。

当我们需要引用定义在`pom.xml`的文件中属性的时候，引用的属性的名字使用定义这个属性的XML元素的名字。使用`pom`前缀表示项目根元素的别名。比如，`${pom.name}`引用定义在`pom.xml`文件中的项目的名字，`${pom.version}`引用项目的版本，`${pom.build.finalName}`引用项目的`finalName`（finalName是项目最终被打包以后的名字）。类似的，如果是引用定义在`setting.xml`文件中的名字，可以使用`${setting.localRepository}`名字来引用，引用的时候需要加上`setting`前缀。

下面是一个使用Maven资源过滤的一个例子：

假设我们有一个`application.properties`文件，放在目录`src/main/resources`下，文件中的内容如下：

```bash
application.name=${pom.name}
application.version=${pom.version}

```

然后，我们执行下面的命令来处理资源文件：

```bash
$ mvn process-resources

```

`process-resources`命令会复制项目中的资源文件到目录`target/classes`中，并进行过滤处理。

运行完以后，我们可以看到`application.properties`文件已经被实际的属性值替换了：

```bash
application.name=Maven Quick Start Archetype
application.version=1.0-SNAPSHOT

```

接下来，我们来看下怎样引用定义在外部的properties文件中的值。首先，我们在Maven项目的的`/src/main/filters`目录下添加一个properties文件`filter.properties`，我们在文件中定义一个属性键-值对：

```bash
my.filter.value=test

```
接下来，为了让Maven可以知道这个文件的存在，我们需要在`pom.xml`中引用这个文件：

```bash
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>

  ....
 
  <build>
    <filters>
      <filter>src/main/filters/filter.properties</filter>
    </filters>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
</project>

```

在上面的`pom.xml`文件中，有一条定义在`<filters>`元素中的语句`<filter>src/main/filters/filter.properties</filter>`，指定了这个外部properties文件来对资源文件进行过滤处理。如果存在多个可以用于过滤的外部properties文件，可以定义多个`<filter>`元素来指定。

下面，我们在原来的`application.properties`文件中添加一条属性来引用外部properties文件中的`my.filter.value`的值：

```bash
application.name=${pom.name}
application.version=${pom.version}
message=${my.filter.value}

```

然后当我们执行`mvn process-resources`命令的时候，会使用外部properties文件中的值来替换`application.properties`文件中的`message`属性的`${my.filter.value}`的值了。

除了我们使用外部properties文件来实现上面的效果之外，我们也可以使用另外一种方式来达到上面一样的效果。只需要在`pom.xml`文件中定义`<properties></properties>`元素来定义我们需要过滤的属性，比如上面的`my.filter.value`属性：

```bash
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>

  ...
 
  <build>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
 
  <properties>
    <my.filter.value>test</my.filter.value>
  </properties>
</project>

```

上面的`pom.xml`文件中，我们在`<properties></properties>`元素中定义了一个名为`<my.filter.value>`的元素，这样，我们就可以在资源文件中直接引用这个元素中定义的属性值了。达到和上面使用外部properties文件一样的效果。

下面，我们看下如何获得系统级别的属性吧。

被Maven过滤的资源文件，可以获得系统级别的属性，以及Java内建的属性，比如`java.version`和`java.home`。我们也可以获得命令行下传递过来的属性值，通过`-D`选项来指定：

我们修改之前的`application.properties`文件，添加以下的内容：

```bash
java.version=${java.version}
command.line.prop=${command.line.prop}

```

现在，我们执行下面的命令：

```bash
$ mvn process-resources "-Dcommand.line.prop=hello world"

```

上面的命令，在命令行上通过`-D`选项指定了一个属性值，所以我们在过滤资源文件的时候会获得这个值，并进行替换。

执行以后，在`target/classes`目录下的`application.properties`文件中，会变成这样：

```bash
java.version=1.7.0_79
command.line.prop=hello world

```

好了，到这里，我们已经把Maven对资源文件过滤的内容介绍的差不多了。接下来，我们来看一个使用Maven资源文件过滤的实际例子：

现在我们有这样一个问题，我们在开发过程中需要多套配置数据来配置开发环境，比如数据库地址的配置等信息。我们需要在多个开发情景下进行切换，如果我们将配置信息写死在资源文件中，那么在我们切换的时候，会比较繁琐，还可能出现拼写错误，特别是涉及到IP地址的修改的时候。所以，我们可以定义两套配置方案，通过Maven的资源过滤机制，可以很方便的进行切换。

首先，假设我们有开发和测试两个开发情景，那么，我们可以定义两个对应的properties文件：`dev-filter.properties`和`test-filter.properties`。把这两个文件放在项目的`filters`目录下，作为外部properties文件。

两个文件的定义如下：

```bash
# dev-filter.properties
db.url=192.168.31.101

# test-filter.properties
db.url=192.168.32.102

```

现在，加上我们的数据库连接的配置是放在`jdbc.properties`这个资源文件中：

```bash
jdbc.url=${db.url}

```

那么，我们需要在`pom.xml`中指定需要被过滤的资源文件，以及外部的properties文件：

```bash
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>

  ....
 
  <build>
    <filters>
      <filter>src/main/filters/filter-${env}.properties</filter>
    </filters>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
</project>

```

现在，我们只是可以对`jdbc.properties`这个资源文件进行过滤了，但是，我们注意到，在上面的`pom.xml`文件中，在定义外部properties文件的时候，使用了一个名为`${env}`的变量，所以，我们接下来要定义这个`env`变量，来实现不同开发情景的切换。我们使用了Maven的`profile`来实现：

```bash
<profiles>
   <!-- 开发环境，默认激活 -->
    <profile>
        <id>dev</id>
        <properties>
        	<env>dev</env>
    	</properties>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    <!-- 测试环境 -->
    <profile>
        <id>test</id>
        <properties>
            <env>test</env>
        </properties>
    </profile>
</profiles>

```

上面的代码中定义了变量`env`，将上面的代码放到`pom.xml`文件中，就可以实现开发环境和测试环境的切换了。

我们可以用下面的命令来进行切换：

```bash
$ mvn clean package -Pdev	# 将Maven切换到开发情景
$ mvn clean package -Ptest	# 将Maven切换到测试情景

```

好了，对Maven的资源的处理，就介绍到这里。如果您觉得文章写的还行，不妨动动您的鼠标，点个赞吧~

## 引用

[https://maven.apache.org/guides/getting-started/index.html](https://maven.apache.org/guides/getting-started/index.html)