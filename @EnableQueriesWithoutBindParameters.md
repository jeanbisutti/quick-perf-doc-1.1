Cancels behavior of [@DisableQueriesWithoutBindParameters](./@DisableQueriesWithoutBindParameters).

See the [PostgreSQL “bind variable peeking”](https://medium.com/@FranckPachot/postgresql-bind-variable-peeking-fb4be4942252) paper from Franck Pachot.

### :wrench: Elements 
|Name     |Type    | Meaning                                                                     | Default value  |
| --------|:------:|:----------------------------------------------------------------------------|:--------------:|
| comment | String |To comment on the reason why SQL queries without bind parameters are enabled.|      -         |

### :mag_right: Example
```java
@DisableQueriesWithoutBindParameters(comment = "Skewed data")
@Test
public void execute() {
   ...
}
```