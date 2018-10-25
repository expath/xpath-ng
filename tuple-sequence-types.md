# Tuple Sequence Types

**Author**: Reece H. Dunn. 67 Bricks.

Define a sequence type with a fixed number of items and explicit types for each element.


## Description

\[Definition: A *tuple sequence* is a sequence where each instance of that sequence always has the same number of items, and the type of the items in the sequence do not change between different instances of the same tuple sequence.\] The tuple sequence applies to the flattened sequence, where nested sequences have been combined and empty sequences have been removed.

> Example:
>
> A 2D point could be defined as:
>
>     declare type point2D = ( xs:double, xs:double );
>
> Here, the first value is the x coordinate and the second value is the y coordinate.

> Note:
>
> A tuple sequence must have more than one value. If a type in parenthesis only specifies a single item, it is a parenthesized item type.

If a tuple sequence is assigned a flattened sequence that contains a different number of items than is specified by the tuple sequence type, a type error is raised.

If an item in the flattened sequence being assigned to a tuple sequence type is not convertible to the corresponding type at the same position, a type error is raised.

If the types of two tuple sequence types are not compatible (see the judgement `subtype(A, B)` definition below) a type error is raised.

An XQuery processor may infer a tuple sequence type from an untyped sequence during the static analysis phase.


### The judgement subtype(A, B)

The following draft text is added before the table in this section of the XPath/XQuery specification.

1. `B` is a tuple sequence type `(Bi_1, Bi_2, ..., Bi_N)`, `A` is a tuple sequence type `(Ai_1, Ai_2, ..., Ai_M)`, where `N` equals `M`; for values of `I` between `1` and `N`, `subtype(Bi_I, Ai_I)`.
1. `B` is `Bi*` or `Bi+`, `A` is a tuple sequence type `(Ai_1, Ai_2, ..., Ai_N)`; for values of `I` between `1` and `N`, `subtype(Bi, Ai_I)`.
1. `B` is a tuple sequence type `(Bi_1, Bi_2, ..., Bi_N)`, `A` is `Ai*` or `Ai+`; for values of `I` between `1` and `N`, `subtype(Bi_I, Ai)`.
1. The result of the `subtype(A, B)` judgement can be determined from the table below, which makes use of the auxiliary judgement `subtype-itemtype(Ai, Bi)` defined in __2.5.6.2 The judgement `subtype-itemtype(Ai, Bi)`__. 


### Influences

Various other languages such as Python, Scala, C#, and C++ have support for tuple types that have a fixed number of items. They allow the type of each item to be specified such that they can be different types.

The syntax for this is the syntax that MarkLogic use to describe the MarkLogic [math:modf](https://docs.marklogic.com/math:modf) function. There, the return type of that function is given as:

> as (xs:double, xs:integer)

There is support for tuple-like constructs with the [Type](https://www.w3.org/TR/xquery-semantics/#doc-fs-Type) grammar in XQuery 1.0 and XPath 2.0 Formal Semantics (Second Edition). The difference is that the `Type` syntax allows for mixing tuple, union, and intersection without operator precedence or how to evaluate mixed types. The formal methods Type or similar syntax is implemented in the following XPath/XQuery processors:

1. XQuantum XQuery 1.0 -- http://www.cogneticsystems.com/xquery/alignment.html#reg
1. Galax 1.0 -- http://galax.sourceforge.net/doc/alignment.html#toc19

## Use Cases

This allows better static type checking of these fixed-length sequence types.


## Examples

Calculating sin and cos at the same time:

    declare function sincos($angle as xs:double?) as (xs:double, xs:double)? {
        (: could use a sincos assembly instruction :)
        math:sin($angle), math:cos($angle)
    };

Complex number conversion:

    declare function polar-to-cartesian($polar as (xs:double, xs:double)) as (xs:double, xs:double) {
        $polar[1] * math:cos($polar[2]),
        $polar[1] * math:sin($polar[2])
    };

Defining a rational number type (using the Saxon 9.8 type declaration syntax):

    declare type rational = ( xs:integer, xs:integer );

Using the MarkLogic modf function:

    let $ret as (xs:double, xs:integer) := math:modf(1.25)
    return $ret[2] (: returns: 1 :)


## Grammar

__Modified (Integration Points):__

    ItemType ::= KindTest | ("item" "(" ")") | FunctionTest | MapTest | ArrayTest | AtomicOrUnionType | ParenthesizedSequenceType

__New:__

    ParenthesizedSequenceType ::= ParenthesizedItemType | TupleSequenceType
    TupleSequenceType ::= "(" ItemType ("," ItemType)* ")"

__NOTE:__ This allows other sequence-based types to be added to the `ParenthesizedSequenceType` EBNF symbol.

## Related Proposals

1.  Tuple Decomposition -- Extracting the values from a sequence into separate variables in a single for/let/etc. clause.
