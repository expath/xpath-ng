# Annotation Sequence Types (XQuery only)

**Author**: Reece H. Dunn. 67 Bricks.

This proposal adds annotation signatures to the XQuery type system.


## Description

It is not currently possible to access annotation information for a function in XQuery. Adding support for that to the XQuery Functions and Operators would need a way of specifying the annotations in return and parameter types, and thus be describable in the type system.

The new annotation types may also be used when specifying the type of the proposed annotation declarations.

The annotation type is modelled on the `FunctionTest` syntax. Annotation types cannot have a return type. For example:

1. `annotation(*)`
1. `%compatibility-annotation annotation()`
1. `annotation(xs:string)`
1. `annotation(variadic array(xs:string))` -- using the syntax from the variadic function parameter proposal.

The type matching rules should follow the same logic as matching functions, excluding the return type.

The type of an `Annotation` will be as follows:

1. The number of parameter types will match the number of arguments the annotation is called with.
1. If a parameter is a `StringLiteral`, it will have the type `xs:string`.
1. If a parameter is a `DoubleLiteral`, it will have the type `xs:double`.
1. If a parameter is a `DecimalLiteral`, it will have the type `xs:decimal`.
1. If a parameter is a `IntegerLiteral`, it will have the type `xs:integer`.

### Extending the Annotation Declaration Proposal

The type of an annotation declaration will match the type signature for that declaration.

The type of a named annotation reference will be the type signature of the matching annotation declaration.

If a named annotation reference refers to a variadic function parameter (if that proposal is accepted), the reference will refer to the expanded non-variadic signature, and the parameters after the last non-variadic parameter will have the type specified in the `ArrayTest` or `item()` if a type is not specified.


## Use Cases

Provide access to annotations in the XQuery code.

Make the XQuery type system more complete by making annotations part of the data model.


## Examples

    let $post as annotation()? := annotation-for-function($f, %rest:POST#1)
    return exists($post)


## Grammar

TBD.
