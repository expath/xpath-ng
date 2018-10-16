# Concise Inline Functions

**Author**: Michael Kay, Saxonica

Discussion of alternative syntax for inline functions, and a proposal. 


## Description

The inline function syntax for XPath/XQuery 3.1 is clumsy and verbose. No one wants to write

```
sort(//employee, function($emp as element(employee)) as xs:decimal {$emp/@salary})
```
when one could write, for example
```
sort(//employee, 【@salary】)
```
where【@salary】 is some simple function representation whose concrete syntax is discussed in this proposal.

The benefits of a more concise representation of inline functions are not merely cosmetic. Higher-order functions are a tough concept for people to 
get to grips with, and a simpler intuitive syntax will aid understanding and uptake. No one has problems with
XPath's simple predicate syntax `//emp[@salary > 20000]`, and the reason is that no-one needs to think of the
predicate as a function or as the filter expression containing a higher-order operator. They just learn the syntax,
and it works.

In this working paper I present three alternatives for inline functions (each with possible syntax alternatives),
and then make recommendations.

### Focus Functions

A focus function (like a predicate in a filter expression) is a function with a single argument, of type `item()`, which
is bound to the context item ".". Many of the contexts where inline functions are useful (for example,
the arguments to `fn:filter`, `fn:sort`, `fn:for-each`, and the proposed `houtil:highest`, `houtil:lowest`,
`houtil:index-of`, `houtil:before-first`, etc, are functions of arity one where the argument is a single item.
A concise notation for this special case would therefore be widely applicable. Above, I used the abstract
syntax `【@salary】` to represent such a function. Here we will discuss possible concrete syntax.

Whatever the concrete syntax, the semantics of `【EXPR】` are defined to be equivalent to `function($x as item()) as item()*{$x!EXPR}`,
where `EXPR` is any expression, and `$x` is a variable name that is otherwise unused. Note in particular that like
regular inline functions, the function has a closure and can thus refer to variables declared outside the function itself.

Alternatives for concrete syntax:


- `{ EXPR }` - Bare braces. Nice and simple, parses without difficulty. Might clash with other existing extensions (e.g. JSONiq), might reduce options available for other language extensions in the future, might lead to syntax errors being difficult to diagnose.
- `fn{ EXPR }` - currently implemented as an extension in Saxon. Easily recognized visually, easy to parse, low risk of conflict with other grammatical constructs; but not particularly appealing visually.
- `{| EXPR |}` - or various other possibilities for composite brackets.
- `-> EXPR` - for example `sort(employee, ->@salary)`. Concise; the arrow symbol is associated with inline functions in other languages; easy to parse; visually distinctive; slightly suggestive of the semantics.

I'm going to propose we adopt the arrow notation. The grammar here is that `ExprSingle` is extended to allow an additional option `FocusFunction` whose syntax is `"->" ExprSingle`.

Other examples of this syntax:

* `filter(//employee, ->@salary gt 20000)`
* `houtil:index-of(*, ->boolean(self::h1))`

Like other constructs that rely on the context item, it breaks down if you want to do more complex things like joins. It's a simple syntax for simple cases.

### Short Inline Functions

This proposal is for a slightly more powerful syntax that can handle multiple arguments, and arguments of any sequence type, while still being considerably more concise than the current inline function syntax.

The idea is to bind the arguments of a function to the variables `$1`, `$2`, etc, thus avoiding the need for explicit declarations of arguments.

For example: `map:for-each($map, ->($1 || "=" || $2))`

The higher-order function `map:for-each` takes a second argument that is a function designed to process key-value pairs, so it has signature
`function($k as xs:anyAtomicType, $v as item()*) as item()*`; this example supplies a function that concatenates the key and value
into a single string. The parentheses around the function body are not essential, but are added for readability. I have again used the same syntax,
a leading arrow, as the syntactic marker, but other choices are possible.

This example appears in the F&O specification:

```
let $dimensions := map{'height': 3, 'width': 4, 'depth': 5}
return
  <box>{
     map:for-each($dimensions, function ($k, $v) { attribute {$k} {$v} })
  }</box>
```

This can now be rewritten:

```
let $dimensions := map{'height': 3, 'width': 4, 'depth': 5}
return
  <box>{
     map:for-each($dimensions, -> attribute {$1} {$2})
  }</box>
```

Detailed rules:

* The grammar introduces a new construct `ParamRef` which is a new kind of `Primary` and which has the syntax `"$" integer-literal`.
* `ExprSingle` is extended in the same way as for Focus Functions, above
* The arity of the inline function is the largest integer appearing in a `ParamRef` 
directly contained in the function body (where
 "directly contained" means that it is not contained in a nested inline function). It is not required that all lower-numbered parameters be referenced (a function is not required to reference all its arguments).

If we choose to support both focus functions and short inline functions then we could use the same syntax for both (distinguishing them by the presence or absence of a `ParamRef`), or we could use distinct syntax. If we use the same syntax, then it would not be possible to represent functions of arity zero.

### Arrow Syntax with Declared Parameters

The third proposal is to allow inline functions to be expressed using the arrow syntax familiar
to C# and Java users:

```
fn:sort(//employee, $e -> $e/@salary)
map:for-each($map, ($k, $v) -> $k || "=" || $v)
```

I believe that this syntax is unambiguous but we would need to verify this very carefully. Perhaps more probematically, it requires unbounded lookahead to parse it,
which could cause problems for some implementations if they are based on parser technology with 
insufficient power. Lookahead like this also tends to result in poor diagnostics on errors, 
and causes difficulty for syntax-directed editors. Nevertheless, the final result looks 
attractive, at least to users familiar with similar notations from other languages.

In these examples I haven't given any types for the parameters or the function result. The syntax could probably
be embellished to add this, but once you do that it seems to have no benefits over the current inline function syntax.




## Recommendation

My preference is to add both "focus functions" and "short inline functions" as described above,
using the same syntax with a leading arrow.

I don't feel that the third proposal ("Arrow Syntax with Declared Parameters) gives sufficient benefits to justify the complexity.


## Grammar

TBA.
