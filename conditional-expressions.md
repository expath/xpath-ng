# New Conditional Expression Syntax

**Author**: Michael Kay, Saxonica

Proposal for new a new ternary conditional expression, and a new "Elvis" operator.


## Description

Two new expressions are proposed to express conditional evaluation more concisely:

* A ternary conditional `C ?? A !! B` equivalent in semantics to `if (C) then A else B`

* An "Elvis operator" `A ?: B` equivalent to `if (exists(A)) then A else B` except that A is only evaluated once.

Technically, it appears fairly clear (without actually doing the full analysis) that the syntax is unambiguous. We have "?" as a binary operator already, and it cannot be followed by either "?" or ":". Similarly, we have "!" as a binary operator, and it cannot be followed by "!".

The only tricky part of the specification is defining the operator precedence. I would suggest they go between the "wordy" expressions (FLOWR, some, every, if etc) and the first "binary" operator `or`, with `?:` having higher priority than the ternary operator.

The choice of symbols for the ternary expression is from Perl6. A summary of the notations used in a wide range of languages can be
found in Wikipedia: https://en.wikipedia.org/wiki/%3F%3A The familiar syntax `C ? A : B` is not available because "?" is already in use as a binary operator.

The Elvis operator is described at https://en.wikipedia.org/wiki/Elvis_operator The semantics varies
from one language to another; we adapt it here to test for "emptiness" which is our nearest equivalent to the "null" of other languages.



## Use Cases

The two new operators bring no new functionality but some extra conciseness. See examples below.

## Examples

Here's an example from the knight's tour query:

```
for $i in 1 to 64 return
    if ($i = $square + 1) then $move else $board[$i]
```
This can be rewritten
```
for $i in 1 to 64 
return $i = $square + 1 ?? $move !! $board[$i]
```

The most common way of expressing a default value today is the construct
```
(@status, 'open')[1]
```
This works OK once you get used to it but it is very obscure to the uninitiated; also it is far from obvious that it only works for singletons. You can't extend it, for example, to
```
(author, 'anonymous')[1]
```
if there might be more than one author.

These constructs can now be written `@status?:'open'` and `author?:'anonymous'` respectively.

Disadvantages: the benefits are fairly cosmetic and users may feel that they do not compensate for the costs of being non-standard.


## Grammar

TBA.
