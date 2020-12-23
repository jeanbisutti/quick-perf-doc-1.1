# 🚩 Table of contents
[@MeasureExecutionTime](#MeasureExecutionTime) <br><br>
[@ExpectMaxExecutionTime](#ExpectMaxExecutionTime) <br><br>
[@DisplayAppliedAnnotations](#DisplayAppliedAnnotations) <br><br>
[@DisableGlobalAnnotations](#DisableGlobalAnnotations) <br><br>
[@DisableQuickPerf](#DisableQuickPerf) <br><br>
[@FunctionalIteration](#FunctionalIteration) <br><br>
[@DebugQuickPerf](#DebugQuickPerf) <br><br>
[Configure core annotations with a global scope](#Configure-core-annotations-with-a-global-scope)

## @MeasureExecutionTime
Measures the execution time of the test method.

⚠️ *Be cautious  with time measurement results. It is a rough and first level result. Data has no meaning below the ~second/millisecond. JIT warm-up, GC, or [safe points](https://loonytek.com/2020/01/20/long-jvm-pauses-without-gc/) can impact the measure and its reproducibility. We recommend [JMH](https://openjdk.java.net/projects/code-tools/jmh/) to do more in-depth experiments.* 

See also [ExpectMaxExecutionTime](#ExpectMaxExecutionTime) annotation.

### :mag_right: Example

```java
      @MeasureExecutionTime
      public void test_method() {
          ...
      }
```

On the console:
```
[QUICK PERF] Execution time of the test method: 5 s 289 ms (5 289 245 600 ns)
```

## @ExpectMaxExecutionTime

The test will fail if the execution time exceeds the maximum expected.

It can be useful to [configure _@ExpectMaxExecutionTime_ with a global scope](#configure-core-annotations-with-a-global-scope).

⚠️ *Be cautious  with time measurement results. It is a rough and first level result. Data has no meaning below the ~second/millisecond. JIT warm-up, GC, or [safe points](https://loonytek.com/2020/01/20/long-jvm-pauses-without-gc/) can impact the measure and its reproducibility. We recommend [JMH](https://openjdk.java.net/projects/code-tools/jmh/) to do more in-depth experiments.* 

See also [MeasureExecutionTime](#MeasureExecutionTime) annotation.

### :wrench: Elements
|Name          |Type    | Meaning                 | Default value |
| ------------ |:------:|-------------------------|:-------------:|
| hours        | int    | Number of hours         |  0            |
| minutes      | int    | Number of minutes       |  0            |
| seconds      | int    | Number of seconds       |  0            |
| milliSeconds | int    | Number of milli seconds |  0            |

You can use several @ExpectMaxExecutionTime elements together, as shown in the following example.

### :mag_right: Example
```java
@ExpectMaxExecutionTime(seconds = 2)
```     
On the console:
```
[PERF] Execution time of the test method expected to be less than <2 s> but is <5 s 286 ms (5 285 734 000 ns)>
```  

## @DisplayAppliedAnnotations
Displays applied QuickPerf annotations in the console.<br><br>
Displays all applied QuickPerf annotations.<br><br>
An annotation can have three [scopes](QuickPerf#Use-QuickPerf-annotations) (*global*, *test class*, *test method*). _@DisplayAppliedAnnotations_ is useful to see which annotations are applied to a test method.

### :mag_right: Example
```java
      @DisplayAppliedAnnotations
      @RunWith(QuickPerfJUnitRunner.class)
      public class AClassWithVariousCoolAnnotations {

           @Test
           @DisableLikeWithLeadingWildcard
           @ExpectMaxSelect(5)
           @ProfileJvm
           public void test_method {
              
           }

       }

```

On the console:
```
[QUICK PERF] Applied annotations:
@DisableLikeWithLeadingWildcard, @ExpectMaxSelect(value=5), @ProfileJvm, @DebugQuickPerf
 ```
QuickPerf won't display @DisplayAppliedAnnotations as part of the applied annotations in the output message.

## @DisableGlobalAnnotations
Disables QuickPerf annotations having a [global scope](QuickPerf#annotation-scopes).

### :wrench:  Elements
|Name     |Type    | Meaning                                                     | Default value  |
| --------|:------:|:------------------------------------------------------------|:--------------:|
| comment | String |To comment on the reason why global annotations are disabled.|      -         |


## @DisableQuickPerf
Disables all QuickPerf annotations on every [scope](QuickPerf#annotation-scopes) (test, method, global).

### :wrench: Elements
|Name     |Type    | Meaning                                                        | Default value  |
| --------|:------:|:---------------------------------------------------------------|:--------------:|
| comment | String |To comment on the reason why QuickPerf annotations are disabled.|      -         |

## @FunctionalIteration

The <code>FunctionalIteration</code> annotation shows we are implementing the functional, not performance-related behavior. A typical use case is the development of a new feature. First, this annotation allows us to focus on the implementation of functional behavior by disabling global annotations. In a second step, we remove <code>FunctionalIteration</code>  to re-enable
global annotations. Later on, we may also add other QuickPerf annotations on the test method.

FunctionalIteration and [@DisableQuickPerf](#DisableQuickPerf) annotations both disables QuickPerf annotations. ***FunctionalIteration  helps to explicitly describe that we want to work on the functional behavior first.***

## @DebugQuickPerf

Allows to debug QuickPerf execution by displaying information on the 
console. For example, this annotation gives the JVM options added by QuickPerf.
 
### :mag_right: Example

```java
       @DebugQuickPerf
       @RunWith(QuickPerfJUnitRunner.class)
       public class ADebuggedClass {
 
            @Test
            @ExpectMaxQueryExecutionTime(thresholdInMilliSeconds = 2)
            @ExpectMaxSelect(5)
            @ProfileJvm
            public void retrieveSomething() {
               
            }
 
        }
```

On the console:

```
  [QUICK PERF] [DEBUG]
 
  JVM OPTIONS
  -XX:+FlightRecorder
  -XX:+UnlockDiagnosticVMOptions
  -XX:+DebugNonSafepoints
  -XX:+HeapDumpOnOutOfMemoryError
  -XX:HeapDumpPath=/tmp/QuickPerf-7240108834516423370/heap-dump.hprof
 
  PRIORITY OF RECORDERS EXECUTED BEFORE TEST METHOD
  ---- | -----------------------------------------
  Prio | Recorder
  ---- | -----------------------------------------
  2000 | org.quickperf.sql.PersistenceSqlRecorder
  2001 | org.quickperf.sql.display.DisplaySqlRecorder
  2002 | org.quickperf.sql.display.DisplaySqlOfTestMethodBodyRecorder
  2003 | org.quickperf.sql.batch.SqlStatementBatchRecorder
  5070 | org.quickperf.jvm.rss.ProcessStatusRecorder
  6000 | org.quickperf.jvm.jfr.JfrEventsRecorder
  6030 | org.quickperf.jvm.allocation.bytewatcher.ByteWatcherRecorder
 
  PRIORITY OF RECORDERS EXECUTED AFTER TEST METHOD
  ---- | -----------------------------------------
  Prio | Recorder
  ---- | -----------------------------------------
  3000 | org.quickperf.jvm.allocation.bytewatcher.ByteWatcherRecorder
  3030 | org.quickperf.jvm.jfr.JfrEventsRecorder
  3060 | org.quickperf.jvm.rss.ProcessStatusRecorder
  7000 | org.quickperf.sql.PersistenceSqlRecorder
  7001 | org.quickperf.sql.display.DisplaySqlRecorder
  7002 | org.quickperf.sql.display.DisplaySqlOfTestMethodBodyRecorder
  7003 | org.quickperf.sql.batch.SqlStatementBatchRecorder
```

## Configure core annotations with a global scope
Annotations with a [global scope](QuickPerf#annotation-scopes) apply to each test.

`org.quickperf.annotation.CoreAnnotationBuilder` helps to configure core annotations with a global scope.

### :mag_right: Example

```java
package org.quickperf;

import org.quickperf.annotation.CoreAnnotationBuilder;
import org.quickperf.config.SpecifiableGlobalAnnotations;

import java.lang.annotation.Annotation;
import java.util.Arrays;
import java.util.Collection;

public class QuickPerfConfiguration implements SpecifiableGlobalAnnotations {

    public Collection<Annotation> specifyAnnotationsAppliedOnEachTest() {

        return Arrays.asList(
          
                CoreAnnotationBuilder.expectMaxExecutionTimeOfSeconds(1)

        );

    }

}
```

⚠️ *The class implementing `SpecifiableGlobalAnnotations` has to be in the `org.quickperf` package.*