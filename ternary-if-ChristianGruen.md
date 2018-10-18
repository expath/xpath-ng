# Ternary If

**Author**: Christian Grün, BaseX GmbH

## Description

The [ternary if](https://en.wikipedia.org/wiki/%3F:) operator provides a short syntax for conditions. It is also called **conditional operator** or **ternary operator**. The most common syntax for this operator is composed of `?` and `:`. The expression `a ? b : c` returns `b` if `a` is true, and `c` otherwise.

The proposed syntax for XQuery is derived from Perl 6:

```xquery
  a ?? b !! c
```

The reasoning for the chosen syntax is as follows:
* `?` cannot be taken as it represents the lookup operator.
* `:` cannot be used (at least not without being surrounded by whitespaces) as it is part of the QName (example: `prefix:local-name`).
* The combination of `??` and `::` is not available because `::` may be part of an axis step (example: `child::node`).


In XPath, the ternary if and the classical if expression will be equivalent, and an implementation is expected to yield identical query plans for both constructs.

## Use Cases

A ternary if operator is available in most other well-known programming languages (C, C++, Java, JavaScript, PHP, Perl, Ruby, etc.). Code written with ternary if are more compact than code written with the classical if expression.

The concise syntax may also be named as drawback: Some people may regard classical if/then/else constructs as being better readable.

## Examples

```xquery
declare variable $DEBUG := true();

(: Classical if expression :)
let $xml := <xml/>
return if($DEBUG) then trace($xml) else $xml,

(: Ternary if expression :)
let $xml := <xml/>
return $DEBUG ?? trace($xml) !! $xml
```

## Grammar

Rules for XPath (a first suggestion):

```
ExprSingle ::= ForExpr
               | LetExpr
               | QuantifiedExpr
               | IfExpr
               | TernaryIfExpr   /* OrExpr is dropped */
TernaryIfExpr ::= OrExpr ("??" OrExpr "!!" OrExpr)
```

Rules for XQuery:

```
ExprSingle ::= FLWORExpr
               | QuantifiedExpr
               | SwitchExpr
               | TypeswitchExpr
               | IfExpr
               | TryCatchExpr
               | TernaryIfExpr   /* OrExpr is dropped */
TernaryIfExpr ::= OrExpr ("??" OrExpr "!!" OrExpr)
```

Examples:

* valid: `a or b ?? b !! c`; equivalent to `(a or b) ?? b !! c`
* valid: `a ?? b !! (if(a) then b else c)`
* invalid: `a ?? b !! if(a) then b else c`
