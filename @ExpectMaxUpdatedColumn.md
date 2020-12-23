With this annotation, the test will fail if the number of updated columns is greater than expected.

See also the [ExpectUpdatedColumn](./@ExpectUpdatedColumn) annotation.

### :wrench: Elements 
|Name       |Type | Meaning                             | Default value  |
| --------  |:---:|:----------------------------------:|:--------------:|
| value     | int |Maximum number of updated columns   |        0       |

### :mag_right: Example
```java
    @ExpectMaxUpdatedColumn(5)
```