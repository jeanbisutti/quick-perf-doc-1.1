Verifies that SQL statements do not contain a LIKE with a leading wildcard (% or _). If so, the test will fail. 

You can read this [article](https://use-the-index-luke.com/sql/where-clause/searching-for-ranges/like-performance-tuning) explaining why a LIKE with a leading wildcard could be a bad idea for performance.

We recommend configuring _DisableLikeWithLeadingWildcard_ annotation with a [global scope](QuickPerf#annotation-scopes). You can disable the global scope _DisableLikeWithLeadingWildcard_ by adding [_EnableLikeWithLeadingWildcard_](./@EnableLikeWithLeadingWildcard) annotation on specific methods.




