# Gradle Task note


## Project和Task

Project:
>Every Gradle build is made up of one or more projects. What a project represents depends on what it is that you are doing with Gradle. For example, a project might represent a library JAR or a web application. It might represent a distribution ZIP assembled from the JARs produced by other projects. A project does not necessarily represent a thing to be built. It might represent a thing to be done, such as deploying your application to staging or production environments. Don't worry if this seems a little vague for now. Gradle's build-by-convention support adds a more concrete definition for what a project is.

Task:
>Each project is made up of one or more tasks. A task represents some atomic piece of work which a build performs. This might be compiling some classes, creating a JAR, generating Javadoc, or publishing some archives to a repository.

对于build.gradle配置文件，当运行Gradle <Task> 时，Gradle会为我们创建一个Project的对象，来映射build.gradle中的内容。

对于不属于任何Task范畴的代码，Gradle会创建一个Script类的对象，来执行这些代码.

对于Task的定义，Gradle会创建Task对象，并将它会作为project的属性存在(实际上是通过getTaskName完成的)。

例子, 新建文件basic/build.gradle,然后加入如下部分代码：

```
println "the project name is $name"
task hello << {
    println "the current task name is $name"
    println "hello world"
}
```

当运行这个例子时，首先Gradle会创建一个Porject的对象，然后将它和build.gradle的内容映射起来。在上面的例子中，project包括两部分：

1. 可执行脚本定义

   按照之前提到的，可执行脚本的定义将直接被创建成对应的Script类的对象.
   在这个例子中，Script对应的部分就是第一行println的部分，然后执行的结果就是打印出 "the project name is basic"。
   默认的，Project的名字是当前build.gradle所在目录的名字，在这个例子中，build.gradle放在basic目录下，因此，project的name也就是basic.

1. Task定义

    在这个例子中，Gradle将创建一个Task的实例，将其和定义的task内容关联起来。


## Task的创建

调用Project的task()方法创建Task

在使用Gradle时，创建Task最常见的方式便是：

```
task hello1 << {
   println 'hello1'
}
```

这里的“<<”表示追加的意思，即向hello中加入执行过程。我们还可以使用doLast来达到同样的效果：

```
task hello2 {
   doLast {
      println 'hello2'}
}
```
另外，如果需要向Task的最前面加入执行过程，我们可以使用doFirst：

```
task hello3 {
   doLast {
      println 'hello3'}
}
```


## Task依赖


例如，定义如下两个Task，并且在"intro"里加上"dependendsOn"的关键字，如下所示：

```
task hello << {
    println 'Hello world!'
}
task intro(dependsOn: hello) << {
    println "I'm Gradle" 
}
````

输入结果:

```
Hello World
I'm Gradle
```

除此之外，dependensOn也支持定义多个task的依赖，使用[]括起来即可。例如:

```
task A(dependensOn:['B','C','D']) <<{ xxx }
```

除了使用dependensOn跟字符串来定义依赖，我们也可以使用taskX.dependensOn taskY这种形式:

```
task taskX << {
    println 'taskX'
}
task taskY << {
    println 'taskY'
} 
taskX.dependsOn taskY
```

或者，也可以使用闭包：

```
task taskX << {
    println 'taskX'
}
taskX.dependsOn {
    tasks.findAll { task -> task.name.startsWith('lib') }
}
task lib1 << {
    println 'lib1'
}
```


## 监听器
我们还可以在上述build lifecycle中添加额外的监听器，监听某件事情的完成，这样我们就可以对gradle的正常运行流程做干预，比如我们想监听某个subProject 配置完毕，打印日志。我们可以这样

```
gradle.afterProject { project ->
    println('Project ' + project + '  has evaluated')
}
```

这段代码通常需要添加在rootProject的build.gradle脚本中，也可以添加到subProject中(只要能通过gradle获取到Gradle对象），但是这个监听器的安装需要发生在subProject在evaluated时，因此如果前面已经有project参与过evaluate，就不会得到监听。
还可以通过如下方法监听：

```
allprojects {
    afterEvaluate { project ->
        if (project.hasTests) {
            println "Adding test task to $project"
            project.task('test') << {
                println "Running tests for $project"
            }
        }
    }
}
```


1. 监听task的创建:可以在build.gradle中通过如下方式监听task的创建

    ```
    tasks.whenTaskAdded { task ->
        task.ext.srcDir = 'src/main/java'
    }
    ```

1. 监听整个task关系图的创建:

    ```
    gradle.taskGraph.whenReady {

    }
    ```
1. 监听某个task的执行:

    ```
    //监听整个build完毕
    gradle.buildFinished {

    }
    ```

## 配置Task 
一个Task除了执行操作之外，还可以包含多个Property，其中有Gradle为每个Task默认定义的Property，比如description，logger等。
另外，每一个特定的Task类型还可以含有特定的Property，比如Copy的from和to等。当然，我们还可以动态地向Task中加入额外的Property。

在执行一个Task之前，我们通常都需要先设定Property的值，Gradle提供了多种方法设置Task的Property值。 

首先，我们可以在定义Task的时候对Property进行配置：

```
task hello7 << {
   description = "this is hello7" 
   println description
}
```

## Reference:
1. [从零开始学习Gradle之二---如何使用Task]( http://www.blogjava.net/wldandan/archive/2012/07/05/382246.html)
1. [Chapter 15. Build Script Basics](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)
1. [Gradle学习系列之二——创建Task的多种方法 - 程序员Davenkin](http://www.tuicool.com/articles/Avy2Ir)