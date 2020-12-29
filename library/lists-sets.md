# What is wrong with insersection/3 and friends?

The library(lists) contains a number of old predicates for manipulating
sets represented as _unordered_ lists, notably intersection/3, union/3,
subset/2 and subtract/3. These predicates all use memberchk/2 to find
_equivalent_ elements. As a result these are not logical while
unification can easily lead to dubious results. Operating on unordered
sets these predicates typically have complexity |Set1|*|Set2|.

When using these predicates

  - Make sure to respect the mode, i.e., make sure the input lists
    are proper lists.  See is_list/1.
  - Make sure elements are sufficiently instantiated such that their
    equality can safely be checked using unification.  See `(?=)/2`.


## Intended behavior

```
?- intersection([a,b,c], [b,e], X).
X = [b].
?- union([a,b,c], [b,e], X).
X = [a, c, b, e].
?- subset([a,b,c], [b,e]).
false.
?- subset([b], [b,e]).
true.
?- subtract([a,b,c], [b,e], X).
X = [a, c].
```

## Dubious behavior

```
?- intersection([a,b,c], [b,E], X).
E = a,
X = [a, b].
?- subtract([a,b,c], [b,E], X).
E = a,
X = [c].
```

Insufficient instantiation also leads to problems


```
?- subtract([a,b,c], X, [c]).
false.
```

Note that duplicates in the input may result in duplicates in the output
(example by Boris).

```
?- intersection([a,b,a], [b,a,b], I).
I = [a, b, a].
```

@deprecated New code should use library(ordsets) instead.
