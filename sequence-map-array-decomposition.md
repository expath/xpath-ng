# Sequence, Map, and Array Decomposition

**Author**: Reece H. Dunn. 67 Bricks.

**Revision**: 2

This proposal allows sequences and arrays to be decomposed and assigned to separate variables in a single declaration.


## Sequence Decomposition

Given a sequence such as `(1, 2, 3)`, the values within that sequence cannot easily be extracted. With the current version of XPath and XQuery, they need to be assigned to a temporary variable first. For example:

    let $result := get-camera-point()
    let $x := $result[1]
    let $y := $result[2]
    let $z := $result[3]
    return "(" || $x || "," || $y || "," || $z || ")"

This proposal would allow this to be written more concisely as:

    let $(x, y, z) := get-camera-point()
    return "(" || $x || "," || $y || "," || $z || ")"

These are equivalent in this proposal, except that `$result` is not a statically known variable binding in the sequence decomposition let clause.

For each variable declaration in the sequence decomposition at index `N`, and `$expr` being the result of the for/let expression, then `$expr[N]` is the value bound to the variable declaration as a new variable binding. If the value does not exist, an empty sequence is bound to the variable.

A sequence decomposition can be used in any for or let clause binding to decompose the items in a sequence. If the type of the for or let clause binding expression is not a sequence, an `err:XPTY0004` error is raised.


### Array Decomposition

Given an array such as `[1, 2, 3]`, the values within that array cannot easily be extracted. With the current version of XPath and XQuery, they need to be assigned to a temporary variable first. For example:

    let $result := get-camera-point()
    let $x := $result?(1)
    let $y := $result?(2)
    let $z := $result?(3)
    return "(" || $x || "," || $y || "," || $z || ")"

This proposal would allow this to be written more concisely as:

    let $[x, y, z] := get-camera-point()
    return "(" || $x || "," || $y || "," || $z || ")"

These are equivalent in this proposal, except that `$result` is not a statically known variable binding in the array decomposition let clause.

For each variable declaration in the array decomposition at index `N`, and `$expr` being the result of the for/let expression, then `$expr?(N)` is the value bound to the variable declaration as a new variable binding. If the value does not exist, an `err:FOAY0001` (array index out of bounds) error will be raised.

An array decomposition can be used in any for or let clause binding to decompose the items in an array. If the type of the for or let clause binding expression is not a sequence, an `err:XPTY0004` error is raised.


### Map Decomposition

Given a map such as `{x: 1, y: 2, z: 3}`, the values within that map cannot easily be extracted. With the current version of XPath and XQuery, they need to be assigned to a temporary variable first. For example:

    let $result := get-camera-point()
    let $x := $result?(x)
    let $y := $result?(y)
    let $z := $result?(z)
    return "(" || $x || "," || $y || "," || $z || ")"

This proposal would allow this to be written more concisely as:

    let ${x, y, z} := get-camera-point()
    return "(" || $x || "," || $y || "," || $z || ")"

These are equivalent in this proposal, except that `$result` is not a statically known variable binding in the map decomposition let clause.

For each variable declaration with EQName `var` in the map decomposition, and `$expr` being the result of the for/let expression, then `$expr?(var)` is the value bound to the variable declaration as a new variable binding. If the value does not exist, an empty sequence is bound to the variable.

An array decomposition can be used in any for or let clause binding to decompose the items in an array. If the type of the for or let clause binding expression is not a sequence, an `err:XPTY0004` error is raised.


### Assigning the rest of a sequence, map or array

It can be useful to only extract part of a sequence or array (e.g. the heading of a table), and store the rest of the items in another variable. For example:

    let $(heading as array(xs:string), rows as array(xs:string)...) :=
        load-csv("test.csv")

or without specifically declared types:

    let $(heading, rows ...) := load-csv("test.csv")

For a map decomposition, the value of the last variable binding contains all entries in the map that have not been assigned to previous variable bindings in the map decomposition.

If there are no items remaining in the sequence, map, or array, the result is an empty sequence, map, or array respectively.


### Influences

Tuple decomposition is found in various languages such as Python, Scala, and C#. These languages also have support for tuple types.

Python has support for specifying that a variable is assigned the remaining values in the tuple.


## Use Cases

There are many cases where fixed size sequences may be used such as points, complex and rational numbers, sin/cos, and mul/div. This makes extracting data from these simpler, and may also be used to aid readability by assigning descriptive names to each of the items in the sequence.


### Potential Confusion and Complexity

From a user's perspective it would be confusing if an item in the sequence is an empty sequence, as the items after that would be assigned to the wrong variable. However, this is no different from them using the long form to extract the values from the sequence.

There is a potential for confusion if changing from a sequence return type to an array, where the code may subsequently raise an `err:FOAY0001` error. The reason for this is hidden from the user.


## Examples

Extracting values from a sequence:

    declare function sincos($angle as xs:double?) {
        math:sin($angle), math:cos($angle)
    };

    let $angle := math:pi()
    let $(sin, cos) := sincos($angle)
    return $sin || "," || $cos

Extracting values from an array:

    declare function sincos($angle as xs:double?) {
        [ math:sin($angle), math:cos($angle) ]
    };

    let $angle := math:pi()
    let $[sin, cos] := sincos($angle)
    return $sin || "," || $cos

Extracting values with typed variables:

    let $(x as xs:double, y as xs:double) := polar-to-cartesian(1.0, math:pi())
    return "x=" || $x || ", y=" || $y


## Grammar

TBD.


## Version History

1.  Initial proposal.
1.  Remove references to tuples, except for the influences section. Update the syntax to use the suggested syntax from Michael Kay and Christian Grün. Define syntax and rules for map decomposition, as suggested by Michael Kay.
