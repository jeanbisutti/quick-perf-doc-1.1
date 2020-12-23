Selected columns that you don't need can impact performance, particularly when you select all columns.<br>

Not selecting everything can save memory in the database and JVM sides and reduce IO between the JVM and the database.

Also, unwanted columns can prevent some [index-only scans](https://use-the-index-luke.com/sql/clustering/index-only-scan-covering-index) that avoid table access, saving a lot of IO. [Preventing index-only scans can seriously limit performance](https://use-the-index-luke.com/blog/2013-08/its-not-about-the-star-stupid).<br>

We invite you to view [a part of this talk from Lukas Eder and Vlad Mihacea (_to 56:00_)](https://vimeo.com/267560432#t=52m47s). It explains some negative impacts of selecting too many columns.

When you need some specific read-only data, as could be the case with DTOs, it is recommended to project the necessary columns.  Examples can be found [here](https://vladmihalcea.com/the-best-way-to-map-a-projection-query-to-a-dto-with-jpa-and-hibernate/) or [here](https://github.com/AnghelLeonard/Hibernate-SpringBoot) to do this with JPA, Hibernate, or Spring.

_With the [ExpectSelectedColumn](./@ExpectSelectedColumn) QuickPerf annotation, we can quickly check the number of selected columns._