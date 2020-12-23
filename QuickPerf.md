<div align="center">
<img src="https://pbs.twimg.com/profile_banners/926219963333038086/1518645789" alt="QuickPerf"/>
</div><br>

><h3>QuickPerf is a testing library for Java to quickly evaluate and improve some performance-related properties</h3>

---
<p align="center">
  <a href="https://search.maven.org/search?q=org.quickperf">
    <img src="https://maven-badges.herokuapp.com/maven-central/org.quickperf/quick-perf/badge.svg"
         alt="Maven Central">
  </a>
  &nbsp;&nbsp;
  <a href="https://github.com/quick-perf/quickperf/blob/master/LICENSE.txt">
    <img src="https://img.shields.io/badge/license-Apache2-blue.svg"
         alt = "License">
  </a>
  &nbsp;&nbsp;
  <a href="https://twitter.com/quickperf">       
    <img alt="@QuickPerf" src="https://img.shields.io/twitter/url?label=Twitter&style=social&url=https%3A%2F%2Ftwitter.com%2Fquickperf">
  </a>
  &nbsp;&nbsp;
  <a href="Frequently-Asked-Questions">
      :question:
  </a> 
  &nbsp;&nbsp;
  <a href="https://gitter.im/quickperf">
     <img src="https://img.shields.io/gitter/room/quick-perf/quickperf?color=orange"
          alt = "Gitter">
  </a>
  &nbsp;&nbsp;
  <a href="https://github.com/quick-perf/quickperf">
  :octocat:
  </a>
 &nbsp;&nbsp;
  <a href="https://www.youtube.com/playlist?list=PLyRtZQwOxA6ekhEr2H2nNV42ZLD8OkPEx">
:tv:
  </a>   
<p align="center">

---

<h3>🚀&nbsp;<a href="https://github.com/quick-perf/quickperf/releases">Release notes</a></h3>

QuickPerf works with a JDK 1.7+.<br>

## Configure
<table>
 <tr>
    <td align="center"><h3><a href="./Spring">Spring</a></h3></td>
    <td align="center"><h3><a href="./JUnit-4">JUnit 4</a></h3></td>
    <td align="center"><h3><a href="./JUnit-5">JUnit 5</a></h3></td>
    <td align="center"><h3><a href="./TestNG">TestNG</a></h3></td>
   </td>
 </tr>
 <tr>
    <td align="center"><h3><a href="https://github.com/quick-perf/quickperf-examples#micronaut">Micronaut</h3> <i><b>Preview</b></i></a></td>
    <td align="center"><h3><a href="https://github.com/quick-perf/quickperf-examples/tree/master/quarkus">Quarkus</h3> <i><b>Preview</b></i></a></td>
 <td align="center"><h3><a href="https:./Have-clickable-links-in-your-IDE">IDE<br/>clickable<br/>links</a></h3>
 </tr>
 <tr>
    </tr>
</table>

## Use QuickPerf features
### Annotation scopes
An annotation can have three scopes: **global**, **class**, **method**
<details open>
<summary>Scope definitions</summary>

* **Global scope** <br>
The annotation applies to each [QuickPerf test](QuickPerf-test).<br>
To configure global scope annotations, create a class implementing `SpecifiableGlobalAnnotations` interface in an `org.quickperf` package.  <br>
`CoreAnnotationBuilder`, `SqlAnnotationBuilder`, and `JvmAnnotationBuilder`are available to help to build global scope annotations.
* **Class scope** <br>
The annotation is on the test class. It overrides the configuration of the same annotation with global scope.
* **Test method scope** <br>
The annotation is on the test method. It overrides the configuration of the same annotation with test class and global scopes.

</details>
<br/>

💡 **[Examples illustrating how annotation scopes work](./Example-illustrating-how-annotation-scopes-work)**

### Annotations

<table>
 <tr>
    <td align="center"><h3><a href="./JVM-annotations">JVM</a></h3><i>Heap allocation, profiling, ...</i></td>
    <td align="center"><h3><a href="./SQL-annotations">SQL</a></h3><i>N+1 select, JDBC batching disabled, ...</i></td>
    <td align="center"><h3><a href="./core-annotations">Core</a></h3><i>Execution time, debugging, ...</i></td>
 </tr>
</table>

## How to

<table>
 <tr>
    <td align="center"><h3><a href="./Easily-detect-and-fix-N-plus-One-SELECT-with-QuickPerf">Detect and fix N+1 select</a></h3></td>
    <td align="center"><h3><a href="./Limit-console-verbosity">Limit console verbosity</a></h3></td>
    <td align="center"><h3><a href="./Disable-QuickPerf">Disable QuickPerf</a></h3></td>
    <td align="center"><h3><a href="./Create-an-annotation">Create an annotation</a></h3></td>
 </tr>
</table>

## Frequently Asked questions

The answer to your question may be [here](./Frequently-Asked-Questions).

## Project examples
[QuickPerf examples (*JUnit 4*, *JUnit 5*, *TestNG*, *Hibernate*, *Spring*, *Spring Boot*, *Micronaut*, *Quarkus*, *...*)](https://github.com/quick-perf/quickperf-examples)<br/><br/>
[Maven performance](https://github.com/quick-perf/maven-test-bench)