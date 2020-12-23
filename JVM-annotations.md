# 🚩 Table of contents
[One JVM by test method](#One-JVM-by-test-method) <br>


[How to get the JVM options added by QuickPerf?](#how-to-get-the-jvm-options-added-by-quickperf)


[Configure your test JVM](#Configure-your-test-JVM) <br>
&nbsp;  &nbsp; [@HeapSize](#heapsize) &nbsp;|&nbsp; [@Xms](#xms) &nbsp;|&nbsp;[@Xmx](#xmx) &nbsp;|&nbsp; [@UseGC](#usegc)  &nbsp;|&nbsp; [@EnableGcLogging](#enablegclogging) &nbsp;|&nbsp; [@JvmOptions](#jvmoptions)


[Verify heap allocation](#Verify-heap-allocation) <br>
&nbsp;  &nbsp; [@MeasureHeapAllocation](#measureheapallocation) &nbsp;|&nbsp;[@ExpectMaxHeapAllocation](#expectmaxheapallocation) &nbsp;|&nbsp; [@ExpectNoHeapAllocation](#expectnoheapallocation)

[Dump the heap](#dump-the-heap) <br>

[Verify resident set size (RSS)](#Verify-resident-set-size-rss) <br>
&nbsp;  &nbsp; [@MeasureRSS](#measurerss) &nbsp;|&nbsp; [@ExpectMaxRSS](#expectmaxrss)

[Profile or check your JVM](#Profile-or-check-your-JVM) <br>
&nbsp;  &nbsp; [@ProfileJvm](#profilejvm) &nbsp;|&nbsp; [@ExpectNoJvmIssue](#expectnojvmissue)

[Global JVM annotations](#Global-JVM-annotations) <br>

[Test examples](#Test-examples)

# One JVM by test method
⚠️ *If you use one of the JVM annotations, the test method is executed in a dedicated JVM.*

# How to get the JVM options added by QuickPerf?
Some JVM annotations configure JVM options.

You can use *@DebugQuickPerf* to get the JVM options added by QuickPerf.

### :mag_right: Example
```java
 @DebugQuickPerf
```

```
JVM OPTIONS
-Xms20m
-Xmx20m
-XX:+UnlockExperimentalVMOptions
-XX:+AlwaysPreTouch
-XX:+UseEpsilonGC
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=C:\Users\JEANBI~1\AppData\Local\Temp\QuickPerf-16618400885811126911\heapDump.hprof

```

# Configure your test JVM
## @HeapSize
This annotation makes the test executed in a specific JVM with the given heap size.

### :wrench: Elements
|Name      |Type           | Meaning                  |
| -------- |:--------------:|--------------------------|
| value    | long           |Heap size value (Xms=Xmx) |
| unit     | AllocationUnit |Allocation unit           |
### :mag_right: Example
 ```java
   @HeapSize(value = 20, unit = AllocationUnit.MEGA_BYTE)
  ```

### :bulb: [Fixing maximum heap size as a threshold test](Fixing-maximum-heap-size-as-a-threshold-test)

## @Xms
This annotation makes the test executed in a specific JVM having the given initial and minimum heap size value.
### :wrench: Elements
|Name      |Type           | Meaning                            |
| -------- |:--------------:|------------------------------------|
| value    | long           |Initial and minimum heap size value |
| unit     | AllocationUnit |Allocation unit                     |
### :mag_right: Example
  ```java
   @Xms(value = 20, unit = AllocationUnit.MEGA_BYTE)
  ```
## @Xmx
This annotation  annotation makes the test executed in a specific JVM having the given maximum heap size value.

### :wrench: Elements
|Name      |Type           | Meaning                 |
| -------- |:--------------:|-------------------------|
| value    | long           |Maximum heap size value  |
| unit     | AllocationUnit |Allocation unit          |
### :mag_right: Example
  ```java
   @Xmx(value = 20, unit = AllocationUnit.MEGA_BYTE)
  ```

### :bulb: [Fixing maximum heap size as a threshold test](Fixing-maximum-heap-size-as-a-threshold-test)

## @UseGC 

This annotation makes the test executed in a specific JVM with the given Garbage Collector (GC).

### :wrench: Elements
|Name      |Type                       | Meaning    | Default value |
| -------- |:-------------------------:|:----------:|:-------------:|
| value    | org.quickperf.jvm.gc.GC   |GC type     | GC.DEFAULT    |


The following GC types are available:
* GC.EPSILON_GC:  [Epsilon GC - Doc 1](https://openjdk.java.net/jeps/318), [Epsilon GC - Doc 2](https://shipilev.net/jvm/diy-gc/#_epsilon_gc)
* GC.ZGC: [ZGC - Doc](https://wiki.openjdk.java.net/display/zgc/Main)
* GC.SHENANDOAH: [Shenandoah - Doc](https://wiki.openjdk.java.net/display/shenandoah/Main)

### :mag_right: Example
```java
@UseGC(GC.EPSILON_GC)
```

## @EnableGcLogging

This annotation enables GC logging.

The path of the GC log file is displayed on the console:
```
GC log file: C:\Users\JEANBI~1\AppData\Local\Temp\QuickPerf-1127741195724299919\gc.log
```

This file can be analysed with the help of a GC log analyzer:
* [GCViewer](https://github.com/chewiebug/GCViewer), [GCViewer Download 1](https://mvnrepository.com/artifact/com.github.chewiebug/gcviewer), [GCViewer Download 2](https://sourceforge.net/projects/gcviewer/)
* [Censum](https://www.jclarity.com/censum/)
* [GCeasy](https://gceasy.io/gc-index.jsp), the GC log file can be uploaded
* ...

## @JvmOptions
This annotation makes the test executed in a specific JVM having the given JVM options.

A [tool](https://chriswhocodes.com/vm-options-explorer.html) developed by [Chris Newland](https://github.com/chriswhocodes) can be used to explore the available JVM options.

### :wrench: Elements
|Name      |Type           | Meaning       |
| -------- |:--------------:|:-------------:|
| value    | String           |JVM options  |

# Verify heap allocation
The following annotations use ByteWatcher under the hood:
* https://github.com/danielshaya/ByteWatcher
* https://www.javaspecialists.eu/archive/Issue232.html

⚠️  *They measure heap allocation of the test method thread*.

For example, you can use _MeasureHeapAllocation_ and _ExpectMaxHeapAllocation_ annotations to check the heap allocation cost of a large data structure (e.g., 1 000 000 elements).

You can use _ExpectNoHeapAllocation_ annotation to verify that the tested code does not allocate on the heap.

## @MeasureHeapAllocation

This annotation measures the heap allocation of the test method thread and displays it on the console.

### :wrench: Elements
|Name      |Type           | Meaning                  | Default value |
| -------- |--------------|------------------------|-------------|
| format| java.lang.String|     Provides the format used to print the measured heap allocation on the console. This format will be called with a preformatted allocation as a String. So the only element you can use in this format is `%s`. |[QUICK PERF] Measured heap allocation (test method thread): %s|
| writerFactory|Class<? extends WriterFactory> |  Allows you to provide a way to build a `Writer` instance to print your messages. The `WriterFactory`class is used to built this `Writer`. This `WriterFactory` class is constructed using reflection, so it should have an empty constructor. If it does not an exception will be raised and the default `Writer` will be used. The default value `DefaultWriterFactory` builds a `Writer`that writes to `System.out`. In case an exception is raised in the use of a provided factory, the system falls back on this default value. |DefaultWriterFactory.class|

### :mag_right: Example 1
```java
@RunWith(QuickPerfJUnitRunner.class)
public class ClassWithMethodAnnotatedWithMeasureAllocation {

    @MeasureHeapAllocation
    @JvmOptions("-XX:+UseCompressedOops -XX:+UseCompressedClassPointers")
    @Test
    public void array_list_with_size_100_should_allocate_440_bytes() {
        // java.util.ArrayList: 24 bytes
        //            +
        //  Object[]: 16 + 100 x 4 = 416
        //       = 440 bytes
        ArrayList<Object> data = new ArrayList<>(100);
    }

}
```
Console:
```
[QUICK PERF] Measured heap allocation (test method thread): 440 bytes
```

### :mag_right: Example 2 (*writerFactory* and *format*)
```java
@QuickPerfTest
public class TestClass {


    @MeasureHeapAllocation(writerFactory = FileWriterBuilder.class, format = "Heap allocation: %s\n")
    @Test
    public void test_method() {
        //...
    }

    public static class FileWriterBuilder implements WriterFactory {

        @Override
        public Writer buildWriter() throws IOException {
            return new FileWriter("C:\\Users\\UserName\\Allocation.txt", true);
        }
    }

}


```

## @ExpectMaxHeapAllocation

This annotation makes the test fail if the heap allocation of the test method thread is greater than expected.

### :wrench: Elements
|Name      |Type           | Meaning          | 
| -------- |:--------------:|------------------|
| value    | long           |Allocation value  |   
| unit     | AllocationUnit |Allocation unit   |

### :mag_right: Example
 ```java
    @ExpectMaxHeapAllocation(value = 440, unit = AllocationUnit.BYTE)
    @Test
    public void array_list_with_size_100_should_allocate_440_bytes() {
        ArrayList<Object> data = new ArrayList<>(100);
    }
  ```
## @ExpectNoHeapAllocation
This annotation makes the test fail if the test thread allocates on the heap.

# Dump the heap

## HeapDumper

You can use the two following methods of `org.quickperf.jvm.heap.HeapDumper` class to dump the Java heap:
* `public static void dumpHeap(String fileName)`
* `public static void dumpHeapWithOnlyLiveObjects(String fileName)`

### :bulb: How to analyze the heap dump?
Several tools can be used to analyze the heap dump:
* [Memory Analyzer Tool (MAT)](https://www.eclipse.org/mat/downloads.php)
* [JOverflow JMC plugin](http://hirt.se/blog/?p=854)
* [VisualVM](https://visualvm.github.io/)
* YourKit (*commercial*)
* JProfiler (*commercial*)
* ...

### :mag_right: Example

```java
@HeapSize(value = 50, unit = AllocationUnit.MEGA_BYTE)
@Test
public void do_something_and_dump_heap() {

  IntegerAccumulator integerAccumulator = new IntegerAccumulator();
  integerAccumulator.accumulateInteger(3_000_000);

  HeapDumper.dumpHeap("C:\\Users\\UserName\heap-dump.hprof");

}
```

Console:
```
[QUICK PERF] Heap dump file 
👉 C:\Users\UserName\heap-dump.hprof
```

# Verify resident set size (RSS)

## @MeasureRSS

You can measure the [Resident Set Size (RSS)](https://en.wikipedia.org/wiki/Resident_set_size) with this annotation. <br><br>
The measured RSS is displayed on the console.

⚠️ _Today this annotation only woks on Linux. You can work on this [issue](https://github.com/quick-perf/quickperf/issues/56) to make the RSS annotations work on MacOS._

### :mag_right: Example
```
[QUICK PERF] Measured RSS (process 5227): 46.64 Mega bytes (48 902 144 bytes)
```

## @ExpectMaxRSS

This annotation will make the test fail if the [Resident Set Size (RSS)](https://en.wikipedia.org/wiki/Resident_set_size)is greater than expected.

⚠️ _Today this annotation only woks on Linux. You can work on this [issue](https://github.com/quick-perf/quickperf/issues/56) to make the RSS annotations work on MacOS._

### :wrench: Elements
|Name      |Type           | Meaning   | 
| -------- |:--------------:|:---------:|
| value    | long           |value      |   
| unit     | AllocationUnit |RAM unit   |

# Profile or check your JVM

To be able to use the [ProfileJvm](#profilejvm) and [ExpectNoJvmIssue](#expectnojvmissue) annotations, you must add the following dependency:
```xml
<dependency>
   <groupId>org.quickperf</groupId>
   <artifactId>quick-perf-jfr-annotations</artifactId>
   <version>1.1.0</version>
   <scope>test</scope>
</dependency>
```
If you get an org.openjdk.jmc:flightrecorder.rules.jdk:pom:7.0.0 not found issue, read [this](Frequently-Asked-Questions#orgopenjdkjmcflightrecorderrulesjdkpom700-not-found) to fix it.

With these annotations, the JVM is profiled with the [JDK Flight Recorder](https://en.wikipedia.org/wiki/JDK_Flight_Recorder) (JFR), an event recorder built into the JVM.

The profiling content is saved in a _.jfr_ file.

⚠️ JFR profiling works with 
* OpenJDK JDK >= 11
* OpenJDK JDK 8 with a version greater than u262/u272 (following vendors) <br>
  👉 Article from Marcus Hirt giving details: http://hirt.se/blog/?p=1235
* Oracle JDK >= 1.7u40


## @ProfileJvm
To profile the JVM with the [JDK Flight Recorder](https://en.wikipedia.org/wiki/JDK_Flight_Recorder) (JFR).<br>

The console displays the path to the recording file. You can open this file with JDK Mission Control.

_ProfileJvm_ annotation also displays some JVM profiling data (GC times, heap allocation estimation, exception numbers, ...) in standard output.

:bulb: [Where to find JDK Mission Control (JMC)?](Where-to-find-JDK-Mission-Control-(JMC))

### :mag_right: Example
```
[QUICK PERF] JVM was profiled with JDK Flight Recorder (JFR).
The recording file is available here: C:\Users\JEANBI~1\AppData\Local\Temp\QuickPerf-9292511997956298899\jvm-profiling.jfr
You can open it with JDK Mission Control (JMC).
Where to find JDK Mission Control? 👉 https://tinyurl.com/find-jmc
```

```
------------------------------------------------------------------------------
 ALLOCATION (estimations)     |   GARBAGE COLLECTION           |  THROWABLE
 Total       : 3,68 GiB       |   Total pause     : 1,264 s    |  Exception: 0
 Inside TLAB : 3,67 GiB       |   Longest GC pause: 206,519 ms |  Error    : 36
 Outside TLAB: 12,7 MiB       |   Young: 13                    |  Throwable: 36
 Allocation rate: 108.1 MiB/s |   Old  : 3                     |
------------------------------------------------------------------------------
 COMPILATION                  |   CODE CACHE
 Number : 157                 |   The number of full code cache events: 0
 Longest: 1,615 s             |   
------------------------------------------------------------------------------
 JVM
 Name     : OpenJDK 64-Bit Server VM
 Version  : OpenJDK 64-Bit Server VM (11.0.1+13) for windows-amd64 JRE (11.0.1+13), built on Oct  6 2018 13:18:13 by "mach5one" with MS VC++ 15.5 (VS2017)
 Arguments: -Xms6g -Xmx6g -XX:+FlightRecorder -XX:+UnlockDiagnosticVMOptions -XX:+DebugNonSafepoints -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=C:\Users\JEANBI~1\AppData\Local\Temp\QuickPerf-1155358826815951142\heap-dump.hprof -DquickPerfToExecInASpecificJvm=true -DquickPerfWorkingFolder=C:\Users\JEANBI~1\AppData\Local\Temp\QuickPerf-1155358826815951142
------------------------------------------------------------------------------
 HARDWARE
 Hardware threads: 8
 Cores           : 4
 Sockets         : 1
 CPU
		Brand: Intel(R) Core(TM) i7-8550U CPU @ 1.80GHz, Vendor: GenuineIntel
		Family: <unknown> (0x6), Model: <unknown> (0x8e), Stepping: 0xa
		Ext. family: 0x0, Ext. model: 0x8, Type: 0x0, Signature: 0x000806ea
		Features: ebx: 0x03100800, ecx: 0xfedaf387, edx: 0xbfebfbff
		Ext. features: eax: 0x00000000, ebx: 0x00000000, ecx: 0x00000121, edx: 0x2c100800
		Supports: On-Chip FPU, Virtual Mode Extensions, Debugging Extensions, Page Size Extensions, Time Stamp Counter, Model Specific Registers, Physical Address Extension, Machine Check Exceptions, CMPXCHG8B Instruction, On-Chip APIC, Fast System Call, Memory Type Range Registers, Page Global Enable, Machine Check Architecture, Conditional Mov Instruction, Page Attribute Table, 36-bit Page Size Extension, CLFLUSH Instruction, Debug Trace Store feature, ACPI registers in MSR space, Intel Architecture MMX Technology, Fast Float Point Save and Restore, Streaming SIMD extensions, Streaming SIMD extensions 2, Self-Snoop, Hyper Threading, Thermal Monitor, Streaming SIMD Extensions 3, PCLMULQDQ, 64-bit DS Area, Enhanced Intel SpeedStep technology, Thermal Monitor 2, Supplemental Streaming SIMD Extensions 3, Fused Multiply-Add, CMPXCHG16B, xTPR Update Control, Perfmon and Debug Capability, Process-context identifiers, Streaming SIMD extensions 4.1, Streaming SIMD extensions 4.2, MOVBE, Popcount instruction, AESNI, XSAVE, OSXSAVE, AVX, F16C, LAHF/SAHF instruction support, Advanced Bit Manipulations: LZCNT, SYSCALL/SYSRET, Execute Disable Bit, RDTSCP, Intel 64 Architecture, Invariant TSC
------------------------------------------------------------------------------
```

## @ExpectNoJvmIssue

*Today we consider this annotation as experimental.*

With this annotation, JVM is profiled with Java Flight Recorder (JFR).<br>

Based on the profiling, some [JMC rules](http://hirt.se/blog/?p=920) are evaluated. For each rule a score is attributed. The maximum score value is 100. The test will fail if one rule has a score greater than this expected (by default 60)<br><br> 
Things like significant primitives to object conversions can be detected:
```
[PERF] JMC rules are expected to have score less than <50>.

Rule: Primitive To Object Conversion
Severity: INFO
Score: 74
Message: 79 % of the total allocation (45,6 MiB) is caused by conversion from primitive 
types to object types.

The most common object type that primitives are converted into is 
'java.lang.Integer', which causes 45,6 MiB to be allocated. The most common 
call site is 
'org.quickperf.jvm.jmc.JmcJUnit4Tests$ClassWithFailingJmcRules$IntegerAccumulator.accumulateInteger(int):40'.
```

:bulb: With this annotation you can also detect that most of the time is spent to do garbage collection in your test.

:bulb: If you have the following message in the console
```
Rule: Stackdepth Setting
Severity: WARNING
Score: 97
Message: Some stack traces were truncated in this recording.
```
then you can increase the stack depth value in this way:
```java
@JvmOptions("-XX:FlightRecorderOptions=stackdepth=128")
```

### :wrench: Elements
|Name      |Type           | Meaning           | Default value |
| -------- |:--------------:|:-----------------:|:-------------:|
| score    | int            |Rule score (<=100) |      60       |

# Global JVM annotations

Annotations with a [global scope](QuickPerf#annotation-scopes) apply to each test.

`org.quickperf.jvm.annotation.JvmAnnotationBuilder` and `org.quickperf.jvm.jfr.annotation.JfrAnnotationBuilder` help to configure JVM annotations with a global scope.

# Test examples
[With JUnit 4](https://github.com/quick-perf/quickperf-examples/tree/master/jvm-junit4)<br><br>
[With JUnit 5](https://github.com/quick-perf/quickperf-examples/tree/master/jvm-junit5)<br><br>
[With TestNG](https://github.com/quick-perf/quickperf-examples/tree/master/jvm-testng)