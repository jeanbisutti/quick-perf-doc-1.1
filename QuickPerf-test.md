## What is a QuickPerf test?

A _QuickPerf test_ executes QuickPerf annotations.

## How to create a QuickPerf test?

* **JUnit 4:** Add `@RunWith(QuickPerfJUnitRunner.class)` on the test class

* **JUnit 4 + Spring:** Replace `@RunWith(SpringRunner.class)` by `@RunWith(QuickPerfSpringRunner.class)`

* **JUnit 5:** [Enable JUnit 5 automatic extension registration](JUnit-5#automatically-register-quickperf-extension) or add `@QuickPerfTest` on the test classes 

* **TestNG:** No annotation to add