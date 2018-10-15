# New Conditional Expression Syntax

**Author**: Michael Kay, Saxonica

Proposal for extensions to the unary and binary lookup operators.


## Description

The lookup operator in XPath 3.1 allows the RHS operand to be any of the following:

* NCName
* Integer literal
* Parenthesized expression
* "*"

This proposal adds three further options

* String literal
* Variable reference

The effect of using a string literal or variable reference is the same as using a parenthesized expression containing the string literal or variable reference: it enables you to write `$emp?"date of birth"` in place of `$emp?("date of birth")`, and `$array?$i` in place of `$array?($i)`.

Note: the justification for not allowing a general expression on the RHS of the lookup operator is essentially so that an unquoted string (an NCName) can be treated differently from its usual meaning of `child::elemName`. It also allows special meaning to be attached to `*` as the right-hand operand. Integer literals were allowed without parentheses because they are clearly useful and clearly cause no parsing problems. The same argument applies to string literals and variable references; the parentheses in these cases are totally unnecessary, and all this proposal does it to remove the need for them.



## Use Cases

The two new options bring no new functionality but some extra conciseness. See examples below.

Note that `$array?$i` means `$array?($i)` which is not quite the same as `$array($i)` - the meaning is different if `$array` is a sequence of arrays rather than a single array. Avoiding the parentheses differentiates the two expressions more clearly.

## Examples

Map lookup using a string-valued key containing spaces can now be written

```
$map?"date of birth"
$map?"123-reg"
```

Such keys arise commonly with JSON data. In XPath 3.1 the string literal must be enclosed by parentheses.

Similarly, map lookup and array lookup where the key is held in a variable no longer need parentheses:

```
for $i in 1 to 50 return $array?$i
for $k in map:keys($map1) return $map2?$k
```

Disadvantages: the benefits are fairly cosmetic and users may feel that the benefits do not compensate for the costs of being non-standard.


## Grammar

TBA.
