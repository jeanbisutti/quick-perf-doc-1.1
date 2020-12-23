Verifies that the code doesn't produce the same SELECT statements with different parameter values (see the example below).

This annotation can reveal some [N+1 selects](Easily-detect-and-fix-N-plus-One-SELECT-with-QuickPerf). We recommend to configure it with a [global scope](QuickPerf#annotation-scopes).
You can disable the annotation at method level with [EnableSameSelectTypesWithDifferentParamValues](./@EnableSameSelectTypesWithDifferentParamValues) annotation.

### :mag_right: Example
A test using the _DisableSameSelectTypesWithDifferentParamValues_ annotation and generating the statements below will fail.

```sql
    select
        team0_.id as id1_1_0_,
        team0_.name as name2_1_0_ 
    from
        Team team0_ 
    where team0_.id=?
    
    Params:[(1)]
```

```sql
    select
        team0_.id as id1_1_0_,
        team0_.name as name2_1_0_ 
    from
        Team team0_ 
    where
        team0_.id=?"
    
    Params:[(2)]
```
