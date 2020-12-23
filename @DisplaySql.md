This annotation displays on the console the SQL statements produced during the test execution.<br>

Compared to [DisplaySqlOfTestMethodBody](./@DisplaySqlOfTestMethodBody) annotation, _DisplaySql_ also displays SQL statements before (JUnit 4: @Before, @BeforeClass) and after (JUnit 4: @After, @AfterClass) the execution of the test method body. <br>

⚠*We don't recommend committing this annotation. Indeed, the SQL statements would pollute the logs and may slow down the continuous integration build.*