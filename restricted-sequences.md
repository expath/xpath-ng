# Restricted Sequences

**Author**: Reece H. Dunn. 67 Bricks.

Limit a sequence to a fixed number of items with explicit types for each element.


## Description

\[Definition: A *restricted sequence type* is a sequence type that is limited to containing a fixed number of items.\] This restriction places constraints on a sequence type like specifying the type and cardinality of a sequence, it does not define a new type.

> Example:
>
> A 2D point could be defined as:
>
>     declare type point2D = sequence-of( xs:double, xs:double );
>
> Here, the first value is the x coordinate and the second value is the y coordinate.

> Note:
>
> A restricted sequence must have more than one value. If a type in parenthesis only specifies a single item, it is a parenthesized item type.

A restricted sequence type may specify different types for the items in the sequence.

> Example:
>
>     declare function find-at(
>         $items, $value
>     ) as sequence-of( item(), xs:integer )? external;

If a restricted sequence is assigned a flattened sequence that contains a different number of items than is specified by the restricted sequence type, a type error is raised.

If an item in the flattened sequence being assigned to a restricted sequence type is not convertible to the corresponding type at the same position, a type error is raised.

If the types of two restricted sequence types are not compatible (see the judgement `subtype(A, B)` definition below) a type error is raised.

An XQuery processor may infer a restricted sequence type from an untyped sequence during the static analysis phase.


### The judgement subtype(A, B)

The following draft text is added before the table in this section of the XPath/XQuery specification.

1. `B` is a restricted sequence `sequence-of(Bi_1, Bi_2, ..., Bi_N)`, `A` is a restricted sequence `sequence-of(Ai_1, Ai_2, ..., Ai_M)`, where `N` equals `M`; for values of `I` between `1` and `N`, `subtype(Bi_I, Ai_I)`.
1. `B` is `Bi*` or `Bi+`, `A` is a restricted sequence `sequence-of(Ai_1, Ai_2, ..., Ai_N)`; for values of `I` between `1` and `N`, `subtype(Bi, Ai_I)`.
1. `B` is a restricted sequence `sequence-of(Bi_1, Bi_2, ..., Bi_N)`, `A` is `Ai*` or `Ai+`; for values of `I` between `1` and `N`, `subtype(Bi_I, Ai)`.
1. The result of the `subtype(A, B)` judgement can be determined from the table below, which makes use of the auxiliary judgement `subtype-itemtype(Ai, Bi)` defined in __2.5.6.2 The judgement `subtype-itemtype(Ai, Bi)`__. 


### Influences

Various other languages such as Python, Scala, C#, and C++ support restricted sequences as a distinct tuple type that has a fixed number of items. They allow the type of each item to be specified such that they can be different types.

There is support for restricted sequence constructs with the [Type](https://www.w3.org/TR/xquery-semantics/#doc-fs-Type) grammar in XQuery 1.0 and XPath 2.0 Formal Semantics (Second Edition). The difference is that the `Type` syntax allows for mixing tuple, union, and intersection without operator precedence or how to evaluate mixed types. The formal methods Type or similar syntax is implemented in the following XPath/XQuery processors:

1. XQuantum XQuery 1.0 -- http://www.cogneticsystems.com/xquery/alignment.html#reg
1. Galax 1.0 -- http://galax.sourceforge.net/doc/alignment.html#toc19

This is used in the MarkLogic definition of the vendor-specific [math:modf](https://docs.marklogic.com/math:modf) function. There, the return type of that function is given as:

> as (xs:double, xs:integer)

Due to incompatibilities with the XQuery 3.0 `ParenthesizedItemType` a different syntax is used in this proposal, based on a suggestion by Michael Kay.

## Use Cases

This allows better static type checking of fixed-length sequences.


## Examples

Calculating sin and cos at the same time:

    declare function sincos(
        $angle as xs:double?
    ) as sequence-of(xs:double, xs:double)? {
        (: could use a sincos assembly instruction :)
        math:sin($angle), math:cos($angle)
    };

Complex number conversion:

    declare function polar-to-cartesian(
        $polar as sequence-of(xs:double, xs:double)
    ) as sequence-of(xs:double, xs:double) {
        $polar[1] * math:cos($polar[2]),
        $polar[1] * math:sin($polar[2])
    };

Defining a rational number type (using the Saxon 9.8 type declaration syntax):

    declare type rational = sequence-of( xs:integer, xs:integer );

Using the MarkLogic modf function:

    let $ret as sequence-of(xs:double, xs:integer) := math:modf(1.25)
    return $ret[2] (: returns: 1 :)


## Grammar

__Modified (Integration Points):__

    SequenceType ::= ("empty-sequence" "(" ")")
                     | (ItemType OccurrenceIndicator?)
                     | (SequenceOfTest "?"?)           /* new */

__New:__

    SequenceOfTest ::= "sequence-of" "(" ItemType (","  ItemType)* ")"

## Related Proposals

1.  Sequence Decomposition -- Extracting the values from a sequence into separate variables in a single for/let/etc. clause.
