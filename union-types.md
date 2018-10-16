# Union Types

**Author**: Michael Kay, Saxonica

Proposal to add anonymous union types as a new kind of ItemType. 


## Description

If you want to write a function that accepts instances of `xs:date`, `xs:time`, and `xs:dateTime`, there
are several approaches available:

* You can define three separate functions (as the standard function library does, for example for `format-dateTime` and `adjust-dateTime-to-timezone`).
* You can define the function signature to accept `xs:anyAtomicType`, and then check that the supplied value
is one of the accepted types at run-time.
* You can define a union type in a schema, import the schema, and then reference the union type in the function's type signature.

Using a union type clearly has attractions over the other two approaches, but the downside is the paraphernalia
of creating a schema and importing it. Even in XSLT, where `xsl:import-schema` allows inline schemas to
be defined, this is a lot of boilerplate.

This proposal extends the `ItemType` syntax with an option to define a new anonymous union type, for example:

```
union(xs:dateTime, xs:date, xs:time)
```

This can then be used in function signatures, for example:

```
declare function f:format($value as union(xs:dateTime, xs:date, xs:time)) as xs:string {...};
```

Specifically, the grammar for `ItemType` is extended with the option `LocalUnionType`, where the
grammar for `LocalUnionType` is defined as:

```
LocalUnionType ::= "union" "(" QName ("," QName)* ")"
```

*Note: one could argue for the minimum number of member types in a union type being zero, one, or two. I've made
it one for a number of reasons: it gives the simplest grammar, the case of an empty union serves almost no practical purpose,
while restricting it to two or more is unnecessarily paternalistic.*

Each of the QNames must identify a **generalized atomic type** as defined in the XPath/XQuery specification,
which must be present in the in-scope schema types.

The production `SingleType` (used in `cast as` and `castable as` expressions) is also extended to 
allow a `LocalUnionType` as an alternative to a `QName`.

The semantics of a `LocalUnionType` are exactly the same as the semantics of a union type defined in a schema 
with the same member types, in the same order. For example, the rules for type subsumption 
mean that `union(A, B)` is a subtype of `union(A, B, C)`. The type defined meets the definition of
a *pure union type*.

A union type can appear wherever an `ItemType` or `SingleType` is permitted, for example as part of a `SequenceType`
in variable and function declarations, or as the operand of a `cast as` or `castable as` expression.


## Use Cases

The primary use case, given in the introduction, is to make it easier to define functions that are polymorphic in 
the sense of accepting several different atomic types as an argument, without the administrative machinery of 
creating the type in a schema and importing the schema.

The ability to cast to a union type can also be convenient, for example casting to `union(xs:integer, xs:decimal, xs:double)` 
will give different results depending on the lexical form of the supplied operand, which would otherwise require
a complex sequence of `castable as` tests.

Defining the result of a function as a union type can also give more precise typing.

## Grammar

TBA.
