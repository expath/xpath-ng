# Restricted Sequences

**Author**: Reece H. Dunn. 67 Bricks.

**Revision**: 3

Limit a sequence to a fixed number of items with explicit types for each element.


## Description

\[Definition: A *restricted sequence type* is a sequence type that is limited to containing a fixed number of items.\] This restriction places constraints on a sequence type like specifying the type and cardinality of a sequence. It does not define a new distinct type, but instead defines a subtype of a sequence type.

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

A restricted sequence type may specify different types for the items in the sequence. Additionally, the restricted sequence may be made optional by using the `?` occurrence relation.

> Example:
>
>     declare function find-at(
>         $items, $value
>     ) as sequence-of( item(), xs:integer )? external;


### The judgement subtype(A, B)

\[Definition: The *cardinality* of a sequence is a possibly infinite set of non-negative integers.\] This constrains the number of items in a sequence.

\[Definition: The *required item type list* of a restricted sequence subtype is the specified `ItemType`s of each item in the restricted sequence.\] If the sequence type is not a restricted sequence, each type in the *required item type list* is the same as the *item type* of the sequence type.

The *cardinality* and *item type* of a sequence are given as follows:

| Type                 | Cardinality     | Item Type         | Description                          |
|----------------------|-----------------|-------------------|--------------------------------------|
| `xs:error`           | `{}`            | *missing*         | An XSD error type.                   |
| `xs:error?`          | `{0}`           | *missing*         | An optional XSD error type.          |
| `xs:error+`          | `{}`            | *missing*         | An XSD error type sequence.          |
| `xs:error*`          | `{0}`           | *missing*         | An optional XSD error type sequence. |
| `empty-sequence()`   | `{0}`           | *missing*         | An empty sequence `()`.              |
| `T?`                 | `{0,1}`         | `T`               | An optional item.                    |
| `T`                  | `{1}`           | `T`               | A single item.                       |
| `T*`                 | `{0..infinity}` | `T`               | An optional sequence.                |
| `T+`                 | `{1..infinity}` | `T`               | A sequence.                          |
| `(T1, T2, ..., Tn)?` | `{0..n}`        | `item()`          | An optional restricted sequence.     |
| `(T1, T2, ..., Tn)`  | `{1..n}`        | `item()`          | A restricted sequence.               |

> Proposal Note:
>
> The rules for `xs:error?`, `xs:error+`, and `xs:error*` here are for compatibility with the XPath/XQuery 3.1 rules for those sequence types.

The `subtype(A,B)` judgement is true if and only if both of the following are true:

1.  The permitted cardinality of `A` is a subset of the permitted cardinality of `B`.
1.  Let `Ai` be the required item type of item `i` in `A` and let `Bi` be the required item type of `i` in `B`; for all `i` in the permitted cardinality of `A`, `itemtype-subtype(Ai, Bi)` is true.


### Influences

Various other languages such as Python, Scala, C#, and C++ support restricted sequences as a distinct tuple type that has a fixed number of items. They allow the type of each item to be specified such that they can be different types.

There is support for restricted sequence constructs with the [Type](https://www.w3.org/TR/xquery-semantics/#doc-fs-Type) grammar in XQuery 1.0 and XPath 2.0 Formal Semantics (Second Edition). The difference is that the `Type` syntax allows for mixing tuple, union, and intersection without operator precedence or how to evaluate mixed types. The formal methods Type or similar syntax is implemented in the following XPath/XQuery processors:

1. XQuantum XQuery 1.0 -- http://www.cogneticsystems.com/xquery/alignment.html#reg
1. Galax 1.0 -- http://galax.sourceforge.net/doc/alignment.html#toc19

This is used in the MarkLogic definition of the vendor-specific [math:modf](https://docs.marklogic.com/math:modf) function. There, the return type of that function is given as:

> as (xs:double, xs:integer)

Due to incompatibilities with the XQuery 3.0 `ParenthesizedItemType` a different syntax is used in this proposal, based on a suggestion by Michael Kay.


## Rationale

The proposal allows the type of a sequence to be further constrained. This (a) makes code more understandable by documenting the interface between functions; (b) enables improved diagnostics as a result of more precise type checking of function arguments and function results; and (c) enables processors (if they wish) to perform more precise static type checking and to use the extra information for optimization.

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


## Version History

1.  Initial proposal.
1.  Revised the *tuple sequence type* terminology as *restricted sequences*, and updated the syntax to avoid parse conflicts with `ParenthesizedItemType`.
1.  Updated the `subtype` judgement semantics using a combination of a proposed wording by Michael Kay and static analysis rules for the XQuery IntelliJ plugin. The rationale text wording has been written by Michael Kay.
