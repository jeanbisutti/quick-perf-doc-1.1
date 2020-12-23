This annotation  displays on the console the SQL statements produced during the execution of the test method body.

Compared to [DisplaySql](./@DisplaySql) annotation, _DisplaySqlOfTestMethodBody_ does not display SQL statements before (JUnit 4: @Before, @BeforeClass) and after (JUnit 4: @After, @AfterClass) the execution of the test method body. <br>

⚠ *We don't recommend committing this annotation. Indeed, the SQL statements would pollute the logs and may slow down the continuous integration build.*