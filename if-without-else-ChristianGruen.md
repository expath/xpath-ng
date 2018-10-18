# If without Else

**Author**: Christian Grün, BaseX GmbH

## Description

The if expression in XQuery requires both branches to be specified: `then` and `else`. In many cases, only one branch is required.

The proposed change is to make the `else` branch optional. If omitted, an empty sequence is returned if the effective boolean value of the test expression is `false`.

```xquery
if (a) then b
```

## Use Cases

In many programming languages, the negative branch is optional. The reason is that a test requires only an action for the positive case.

From a syntactical point of view, code for the `then` branch may span multiple lines, and it can be hard to associate a trailing `else ()` wih the initial `if` keyword.

A drawback may be that nested if expressions could be confusing to read, in particular if they have not been properly formatted.

## Examples

Open document if it’s available:

```xquery
(: Before: :)
if (doc-available($doc)) then doc($doc) else ()

(: After: :)
if (doc-available($doc)) then doc($doc)
```

Delete file if it exists:

```xquery
(: Before: :)
if (file:exists($file)) then file:delete($file) else ()

(: After: :)
if (file:exists($file)) then file:delete($file)
```

Return HTML page if user has sufficient permissions:

```xquery
(: Before: :)
if (permissions:valid($user)) then (
  <html>Welcome!</html>
) else ()

(: After: :)
if (permissions:valid($user)) then
  <html>Welcome!</html>
```

Nested if expressions:

```xquery
(: Before :)
if(1) then if(2) then 3 else () else ()

(: After :)
if(1) then if(2) then 3
```

The example indicates that if expressions may be better readable if additional parentheses are used. This applies to both the existing syntax and the proposed new syntax. See the following examples:

```xquery
if(1) then if(2) then 3 else () else (),  (: ...is equivalent to... :)
if(1) then (if(2) then 3 else ()) else ()
```

```xquery
if(1) then if(2) then 3 else 4,  (: ...is equivalent to... :)
if(1) then (if(2) then 3 else 4)
```

## Grammar

The required change is minimal:

```
IfExpr ::= "if" "(" Expr ")" "then" ExprSingle ("else" ExprSingle)?
```
