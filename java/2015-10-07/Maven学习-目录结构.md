# Maven学习-目录结构

![](http://images2015.cnblogs.com/blog/579605/201510/579605-20151006201653362-859581753.png)

在前一篇文章中，我们介绍了什么是Maven，以及如何用Maven来构建我们的项目。不了解Maven的童鞋，可以看这里[Maven学习-入门](http://www.cnblogs.com/now-fighting/p/4857625.html)。在这篇文章中，我们将学习Maven的项目的目录结构相关的内容。

我们知道，Maven约定了一套规则来创建和构建我们的项目。得益于Maven的一些约定，我们只要学习相对很少的命令就可以创建和管理我们的项目。在项目的目录结构上，Maven有一套约定的通用的目录结构。

使用一套通用的目录结构的好处是，可以减少开发人员熟悉不同Maven项目时的认知负担。在使用相同的目录结构的情况下，开发人员可以很快的熟悉一个项目。接下来，我们将开始介绍Maven的通用的目录结构。

## 通用目录结构

Maven默认约定了一套目录结构，在通过Maven创建了项目以后，项目的目录结构就是以这套目录结构作为模板创建的。

```bash
${basedir}
|-- pom.xml
|-- src
|	|-- main
|	|	`-- java
|	|	`-- resources
|	|	`-- filters
|	`-- test
|	|	`-- java
|	|	`-- resources
|	|	`-- filters
|	`-- it
|	`-- assembly
|	`-- site
`-- LICENSE.txt
`-- NOTICE.txt
`-- README.txt

```

* `src/main/java` 项目的源代码所在的目录
* `src/main/resources` 项目的资源文件所在的目录
* `src/main/filters` 项目的资源过滤文件所在的目录
* `src/main/webapp` 如果是web项目，则该目录是web应用源代码所在的目录，比如html文件和web.xml等都在该目录下。
* `src/test/java` 测试代码所在的目录
* `src/test/resources` 测试相关的资源文件所在的目录
* `src/test/filters` 测试相关的资源过滤文件所在的目录

上面的目录结构，我们接触的比较多，而下面的这些目录结构，用的相对不是很多：

* `src/it` 集成测试代码所在的目录，主要是供别的插件使用的。
* `src/assembly` 组件（Assembly）描述符所在的目录
* `src/site` 站点文件
* `LICENSE.txt` 项目的许可文件
* `NOTICE.txt` 该项目依赖的库的注意事项
* `README.txt` 项目的readme文件

在一个Maven项目中，在项目根目录下，除了包含了上面约定的目录结构中的部分目录外，还包含了项目描述文件，pom.xml文件。
在根目录下，主要有两个目录，分别是`src`和`target`目录，除此之外，还会有一些版本控制系统的元数据文件，包括git的`.gitignore`或者svn的`.svn`。如果一个项目时由多个子项目组成的，那么在该项目的根目录下，还会包含它的子项目的目录。子项目的目录布局，默认也是和上面列出的目录结构一致的。

我们在上一篇文章中以及提及了`target`目录，这个目录只要用于存放项目构建的输出文件，比如class文件以及打包后的包文件等。

在`src`目录下，包含了项目所有的源代码和资源文件，以及其他项目相关的文件。在该目录的`main`目录下，包含了构建该项目的artifact（可以理解为时项目的程序部分）所需的代码和资源，而`test`目录包含了测试相关的代码和资源文件。

在目录`main`和`test`下的子目录结构，是非常相似的，都包含了两个子目录：`java`子目录和`resources`子目录，这两个目录分别放了源代码和资源文件。不同的是，在`main`目录下的这两个目录中放置了项目主程序的代码，而在`test`目录下，放置的是测试相关的代码和资源文件。其中`java`目录是代码的根目录，在该目录下的子目录，就是Java的包名对应的目录结构了。

现在，我们再来看Maven的目录结构，我们应该就可以清晰地知道每个目录以及其子目录的用途和存放的文件具体做什么的了。

## 参考

[http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html](http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)