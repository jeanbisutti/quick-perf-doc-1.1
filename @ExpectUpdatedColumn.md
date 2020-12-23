With this annotation, the test will fail if the number of updated columns is not equal to the one expected.

See also the [ExpectMaxUpdatedColumn](./@ExpectMaxUpdatedColumn) annotation.

### :wrench: Elements 
|Name       |Type | Meaning                             | Default value  |
| --------  |:---:|:----------------------------------:|:--------------:|
| value     | int |Expected number of updated columns  |        0       |

### :mag_right: Example
```java
    @ExpectUpdatedColumn(3)
```