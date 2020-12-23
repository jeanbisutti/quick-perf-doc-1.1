On this page, we gather frequently asked questions.

If you don't find the answer to your question, please explore the documentation for a few minutes. You still don't get your answer? Don't hesitate to [create a question issue](https://github.com/quick-perf/quickperf/issues/new?assignees=&labels=question&template=question.md&title=) or leave a message on the [chat](https://gitter.im/quickperf).


# General

## Why use QuickPerf?
 Explanations [here](Why-use-QuickPerf).

## QuickPerf annotations seem not executed
Check that the test is a [QuickPerf test](QuickPerf-test).

## Global annotations seem not executed
Check that the class implementing `SpecifiableGlobalAnnotations` uses the `org.quickperf` package.

💡 [What is a global annotation?](QuickPerf#annotation-scopes)

# JVM

## Is a long time spent doing GC?

The fastest way to answer this question with QuickPerf is to use [ProfileJvm annotation](JVM-annotations#profilejvm).
This annotation displays the GC time on the console: 
```
GARBAGE COLLECTION           
Total pause     : 1,264 s   
```
This information can help choose a heap size, for example, with [HeapSize annotation](JVM-annotations#heapsize), to limit GC duration and test length.

## _org.openjdk.jmc:flightrecorder.rules.jdk:pom:7.0.0_ not found
:point_right: <b>Maven</b>

The error message contains `Failure to find org.openjdk.jmc:flightrecorder.rules.jdk:pom:7.0.0 in`.

Add the `https://adoptopenjdk.jfrog.io/adoptopenjdk/jmc-libs` repository to the POM file:

```xml
<repositories>
        <repository>
            <id>AdoptOpenJDK</id>
            <name>AdoptOpenJDK</name>
            <url>https://adoptopenjdk.jfrog.io/adoptopenjdk/jmc-libs</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
</repositories>
```

:point_right: **Gradle** 

Add the `https://adoptopenjdk.jfrog.io/adoptopenjdk/jmc-libs` repository to the `build.gradle` file:

```XMLX
repositories {
	mavenCentral()
	maven { url 'https://adoptopenjdk.jfrog.io/adoptopenjdk/jmc-libs' }
} 
```

# SQL

## How to detect N+1 selects
Explanations [here](Easily-detect-and-fix-N-plus-One-SELECT-with-QuickPerf)