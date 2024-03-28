# Flight Network Assignment FAQ

- [Flight Network Assignment FAQ](#flight-network-assignment-faq)
  - [Updates to the spec](#updates-to-the-spec)
  - [Can I use built-in predicate `P/n`?](#can-i-use-built-in-predicate-pn)
  - [How can I compute \& carry "so-far" results?](#how-can-i-compute--carry-so-far-results)

## Updates to the spec

- [#29](https://edstem.org/au/courses/15661/discussion/1850209): reversible assumption on flight links only applies to the particular network in Exercise 1.
- [#28](https://edstem.org/au/courses/15661/discussion/1849446): clarification on pair of cities not as lists; so predicate arity is correct in spec.


## Can I use built-in predicate `P/n`?

This project assignment can be solved neatly using plain basic Prolog built-in predicates. As the Language Restriction section in the specs states you are not to use advanced predicates or libraries. These include all-solution predicates like `findall/3` for example or the constraint libraries: none are needed anyways.

If in doubt always ask in the forum. Some other predicates that you shall not use are:

- `table/1`, `order_by/1`.

Some predicates that you can use as they are still basic are:

- `nth0/3`, `nth1/3`, `list_to_set/2`


## How can I compute & carry "so-far" results?

A usual strategy for computing something is to carry intermediate results until the end, and once there, just equate the final result with the result so far.

For example, this is how I can sum a list of numbers:

```prolog
sumar(L, R) :- sumar(L, 0, R).

sumar([], SumSoFar, SumSoFar).
sumar([X|L], SumSoFar, Result) :-
    SumSoFar2 is  SumSoFar + X,
    sumar(L, SumSoFar2, Result).
```

Check a run:

```prolog
?- sumar([1,2,3,4], R).
R = 10.
```

