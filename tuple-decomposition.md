# Sequence and Array Decomposition

**Author**: Reece H. Dunn. 67 Bricks.

**Revision**: 2

This proposal allows sequences and arrays to be decomposed and assigned to separate variables in a single declaration.


## Description

Given a sequence such as `(1, 2, 3)`, the values within that sequence or array cannot easily be extracted. With the current version of XPath and XQuery, they need to be assigned to a temporary variable first. For example:

    let $result := get-camera-point()
    let $x := $result[1]
    let $y := $result[2]
    let $x := $result[3]
    return "(" || $x || "," || $y || "," || $z || ")"

This proposal would allow this to be written more concisely as:

    let ($x, $y, $z) := get-camera-point()
    return "(" || $x || "," || $y || "," || $z || ")"

An XPath or XQuery processor may implement this by transforming it to the expanded form above with `$result` being a unique variable that is not visible to the expression.

For sequences, `$seq[N]` would be used to extract the nth item in the sequence. If the item does not exist, an empty sequence is returned.

For arrays, `$array(N)` or `array:get($array, N)` would be used to extract the nth item in the array. If the item does not exist, an `err:FOAY0001` (array index out of bounds) error will be raised.

This would apply to any variable declaration or binding where `:=` is used to assign a variable to an expression. Specifically:

1. variable declarations, including decomposition of default values (XQuery)
1. context item declaration (XQuery)
1. let clauses (XPath/XQuery)
1. for clauses (XPath/XQuery)
1. grouping spec (XQuery)

### Variation: Separate Tuple Array Decomposition Syntax

If the decomposition is being performed on an array, it may be better to use array syntax to define the composition:

    let [$x, $y, $z] := get-camera-point()
    return "(" || $x || "," || $y || "," || $z || ")"

The `(...)` syntax would then be *sequence decomposition*, while the `[...]` syntax would be *array decomposition*.

This would allow XPath/XQuery processors to report an error if sequence decomposition was used on arrays, and when array decomposition was used on sequences.

This would make it clearer to the user when a sequence is expected and when an array is expected, and thus when out of bounds access would result in an empty sequence or an error.

This does add an additional level of complexity to the language grammar, but may help processors decide how to decompose the values as determining whether the type being decomposed is a sequence or array can be determined during the static analysis phase.

### Assigning the rest of a sequence or array

It can be useful to only extract part of a sequence or array (e.g. the heading of a table), and store the rest of the items in another variable. For example:

    let ($heading as array(xs:string), $rows as array(xs:string)*) := load-csv("test.csv")

It may be useful to define a shorthand for selecting the rest of the sequence or array. Using the CSV example above:

    let ($heading, $rows*) := load-csv("test.csv")

The other occurrence indicators would also be usable after the last variable binding.

### Influences

Tuple decomposition is found in various languages such as Python, Scala, and C#. These languages also have support for tuple types.

Python has support for specifying that a variable is assigned the remaining values in the tuple.

## Use Cases

There are many cases where fixed size sequences may be used such as points, complex and rational numbers, sin/cos, and mul/div. This makes extracting data from these simpler, and may also be used to aid readability by assigning descriptive names to each of the items in the sequence.

### Potential Confusion and Complexity

From a user's perspective it would be confusing if an item in the sequence is an empty sequence, as the items after that would be assigned to the wrong variable. However, this is no different from them using the long form to extract the values from the sequence.

There is a potential for confusion if changing from a sequence return type to an array, where the code may subsequently raise an `err:FOAY0001` error. The reason for this is hidden from the user.

There is complexity for the semantics of variable declarations, especially those with external values. If this is deemed too complex, it can be dropped from this proposal.


## Examples

Extracting values from a sequence:

    declare function sincos($angle as xs:double?) {
        math:sin($angle), math:cos($angle)
    };

    let $angle := math:pi()
    let ($sin, $cos) := sincos($angle)
    return $sin || "," || $cos

Extracting values from an array:

    declare function sincos($angle as xs:double?) {
        [ math:sin($angle), math:cos($angle) ]
    };

    let $angle := math:pi()
    let ($sin, $cos) := sincos($angle)
    return $sin || "," || $cos

Extracting values with typed variables:

    let ($x as xs:double, $y as xs:double) := polar-to-cartesian(1.0, math:pi())
    return "x=" || $x || ", y=" || $y


## Grammar

TBD.


## Version History

1.  Initial proposal.
1.  Remove references to tuples, except for the influences section.
