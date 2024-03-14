# Prolog FAQ and Guidelines

- [Prolog FAQ and Guidelines](#prolog-faq-and-guidelines)
  - [General Prolog Guidelines](#general-prolog-guidelines)
    - [Development environment](#development-environment)
    - [Loading files](#loading-files)
    - [Singleton variables](#singleton-variables)
    - [Use of `;/2`](#use-of-2)
    - [Documentation](#documentation)
    - [Strings as atoms](#strings-as-atoms)
    - [Indentation](#indentation)
    - [Efficiency](#efficiency)
    - [Duplicate answers](#duplicate-answers)
    - [Style](#style)
  - [On evaluating and comparing arithmetic expressions: the `is/2` construct](#on-evaluating-and-comparing-arithmetic-expressions-the-is2-construct)


## General Prolog Guidelines

### Development environment

We highly and strongly recommend using a proper IDE for developing Prolog code. The best we know so far today is [Visual Studio Code (VSCode)](https://code.visualstudio.com/) with extension [VSC-Prolog](https://github.com/arthwang/vsc-prolog). You will get syntax highlighting, snippets and completion suggestions, hover information, grammar linter (fundamental!), and many many other features for proper development.

### Loading files

Unless specified otherwise, one should not load a database in the same file where the reasoning rules are located. For example, if we have a database of Uni courses in file `courses.pl` and a Prolog reasoning system to reason about program plans named `uni_programs.pl`, then the latter should NOT load `courses.pl` itself, as it should work with any course database possible. 

Instead, to test it one can consult both as follows:

```prolog
-? ['courses.pl'].
-? ['uni_programs.pl'].
```

Alternatively, one can design a single program `main.pl` with:

```prolog
:- ['courses.pl'].
:- ['uni_programs.pl'].
```

and just consult `main.pl`.

### Singleton variables

To get more marks and more robust code, we strongly suggest to **avoid singleton variables!** A singleton variable is a variable that only occurs once in the arguments or the body
of a predicate and is thus useless. For example in the following two definitions:

```prolog
calculate(X, Y, Z) :- Z is X + 2.
```
Here `Y` is a singleton variable, and should be prefixed by an underscore `_Y` or replaced by an anonymous variable `_`.

Singleton variables make it harder to read the code and can signal a bug, like in the following example:

```prolog
inc(FirstNr, Result) :- Result is Firstnr + 1.
```

Here, both `FirstNr`and `Firstnr` will be reported as singleton variables! This is very useful information because it often helps you finding typos, a common source of bugs in Prolog, as shown above (`Firstnr` should have been `FirstNr`).

### Use of `;/2`

It is generally OK to use `;/2` as part of a simple _if-then-else_ construct when the whole logic is simple, for example to set a variable:

```prolog
(<condition> -> Speed = 0 ; Speed = MaxSpeed)
```

This is fine as it only sets `Speed` and has a very simple logic, no complex code is either branch of the "if" construct.

However, code like this one:

```prolog
p(X,Y,Z) :-
 <code A> ; <code B>.
 ```

should instead be written as two separate clauses:

```prolog
p(X,Y,Z) :- <code A>.
p(X, Y,Z):- <code B>.
```

### Documentation

Each predicate you write should have a concise and clear documentation also indicating its mode of usage. You can find plenty of good documentation examples in the SWI-Prolog manual and even help system (e.g., try `help(append)`).

For **documentation style** similar to JavaDoc, refer to [SWI-Prolog Source Documentation](https://www.swi-prolog.org/pldoc/doc_for?object=section(%27packages/pldoc.html%27)).

### Strings as atoms

In Prolog, any string starting with capital letter is considered a variable. So, to represent a string a name like "Alan Turing" you have two options:

1. You explicitly single quote it, e.g., `'Alan Turing'` to indicate the whole name is an atom.
2. You use snake case notation, e.g., `alan_turing`.

Note that SWI Prolog does include a type String, but is used mostly for I/O operations and where the text does not denote an identifier.

In most cases, you would want to represent the names as _atoms_.

See [this explaination](https://www.swi-prolog.org/pldoc/man?section=string) for more information.


### Indentation

In Prolog, indentation is not strictly required for the correct execution of the code, as Prolog relies on the position of terms rather than indentation to determine the structure of the program. 

However, proper indentation is crucial for code readability and maintaining a clear structure. As always, your code should be easy to read.

A common practice is to put every subgoal on a new line. For example, instead of:

```prolog
% Define a complex rule using multiple predicates.
complex_rule(A, B, C, D, E, F) :- predicate1(A), predicate2(B), predicate3(C), predicate4(D), predicate5(E), predicate6(F).
```

you should instead write:

```prolog
% Define a complex rule using multiple predicates.
complex_rule(A, B, C, D, E, F) :-
    predicate1(A),
    predicate2(B),
    predicate3(C),
    predicate4(D),
    predicate5(E),
    predicate6(F).
```


### Efficiency

Unless specifically stressed, you don't have to go out of your way for optimization purposes. However, the code must aim to be reasonably efficient. You should not be traversing the lists unnecessarily, for example. Basically, without doing crazy tricks, your code should aim to be efficient.

### Duplicate answers

In general, you should eliminate duplicates whenever you can. Sometimes, however, you may not be able to eliminate all duplicates, and that's OK. In each query, you should make a decision as to whether or not it is possible to eliminate duplicates.  Sometimes there are different causes of duplicates, and you can deal with one, but not the other. It is part of the task to figure it out when it is doable and when it is not.

### Style

* Simpler, shorter code is generally best.
* Try to use unification variables when possible; for example `swap([A,B], [B,A]).` rather than `swap(L1, L2) :- L1=[A,B], L2=[B,A].`
* Pick intuitive names for your predicates and arguments.
* good naming convention for a helper predicate used by a predicate `pred` is `pred_aux` (`aux` stands for"auxiliary"). This makes it easier to understand what it is used for.


## On evaluating and comparing arithmetic expressions: the `is/2` construct

Be mindful how Prolog evaluates arithmetic expressions as it is different from languages like Python or Java. An arithmetic expression is just a (compound) term and, unlike Python for example, Prolog will not evaluate them when part of an argument in a predicate. For example:

```prolog
?- number(2).
true.

?- number(3-1).
false.

?- functor(3-1,X,Y).
X =  (-),
Y = 2.
```

Here `3-1` is NOT `3`, it is the term `3-1` (with functor `-` and arity 2!).

So, to do evaluation of an expression, you should use the [`is/2`](https://www.swi-prolog.org/pldoc/doc_for?object=(is)/2) built-in construct. So, instead of writing things like `distance(X, Y, N-1)`, you should instead write `distance(X, Y, N2), N2 is N-1`. It is very important that at the time of execution of an `is/2` goal, the expression is ground and there are no variables:


```prolog
?- N is (3+2)*8.
N = 40.

?- X = 8, N is (3+2)*X.
X = 8,
N = 40.

?- N is (3+2)*X.
ERROR: Arguments are not sufficiently instantiated
ERROR: In:
ERROR:   [10] _6050 is (3+2)*_6058
ERROR:    [9] toplevel_call('<garbage_collected>') at /usr/lib/swi-prolog/boot/toplevel.pl:1158
?- 
```

See how in the last goal, the `X` is still a variable, so Prolog is not able to evaluate a non-ground  expression. _Can you understand why?_

Finally, note that the same above issues apply when using comparison relations, like `>` or `<`: the two sides cannot be uninstantiated and they will not be evaluated automatically:

```prolog
?- 2 < X.
ERROR: Arguments are not sufficiently instantiated
ERROR: In:
ERROR:   [10] 2 <_2596
ERROR:    [9] toplevel_call(user:user: ...) at /usr/lib/swi-prolog/boot/toplevel.pl:1158

?- 2+3 > 0.
true.

?- X is 2+3, X > 0.
X = 5.
```

The only evaluating comparison operator that is provided is `=:=/2` which is True if two expressions evaluate to the same number. This could be simpler than using `is/2` to evaluate first and then `=/2` to compare later:

```prolog
?- 2+5 =:= 3+4.
true.

?- 2+5 = 3+4.
false.

?- X1 is 2+5, X2 is 3+4, X1 = X2.
X1 = X2, X2 = 7.
```
