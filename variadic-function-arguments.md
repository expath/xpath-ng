# Variadic Function Arguments

**Author**: Reece H. Dunn. 67 Bricks.

Allow support for a variable-length sequence of typed parameters to a function.


## Description

A variadic function parameter has at least 1 item. The upper bound is unbounded, but may be an implementation defined limit.

For a function with `N` non-variadic parameters and a variadic parameter, that function defines a set of functions in the static context with the specified function name, and arity of `N+1` upward to the maximum number of arguments supported by an implementation.

A function reference to a variadic function will behave the same as if the function was defined with the specified arity, or an error if there are fewer arguments than the requested arity.

If the function reference specifies an arity equal to one more than the number of non-variadic arguments (i.e. where the variadic size is 1), the last argument is treated as having a type equal to the union of the specified type and an array of the specified type.

When a function with variadic arguments is called, the arguments after the argument that corresponds with the last non-variadic parameter are collected into an array in the same order and passed to the variadic parameter.

If any of these arguments are an array or sequence, that array or sequence will be included as is at the place in the variadic array it occurs in the argument list from the variadic parameter. For example, if the variadic parameter is the third parameter in a function definition, and an array is passed as the fourth parameter, that array will be the second argument of the variadic array passed to the declared function.

### Influences

There are many languages that have support for variadic function arguments, including C/C++ and Java. Java supports variadic parameters by mapping them to an object array.


## Use Cases

Define the `fn:concat` function in the XPath and XQuery Functions and Operators 3.1 specification using valid XPath/XQuery syntax, instead of hand-waiving the variadic nature of the function.

Support other vendor-specific functions that allow variadic arguments.

Support user-defined functions like sum or product that take a variable number of arguments without requiring nested parenthesis (e.g. using `sum(1, 2, 3)` instead of `sum((1, 2, 3))`).

### Requirements

1. MUST be backward compatible with XPath 3.1 and XQuery 3.1.

1. MUST limit the variadic function syntax to the last parameter of an inline function (XPath/XQuery), or named function declaration.

1. MUST make use of arrays or similar functionality to preserve the position of empty sequences in function calls.

1. MUST support named function references (XPath/XQuery) to the function with any arity above the number of defined arguments.

1. MUST support defining `fn:concat` using the variadic function parameter syntax.

1. MUST allow access to the variadic arguments within the function body of an XPath/XQuery expression or function declaration.

## Examples

### fn:concat

    declare function fn:concat(
        $arg1 as xs:anyAtomicType?,
        $args as variadic array(xs:anyAtomicType?)
    ) external;

    let $f := fn:concat#4
    let $g := fn:concat#1 (: error: fn:concat requires at least 2 arguments :)
    return $f(1, 2, 3, 4) (: returns "1234" :)

### sum

    declare function sum($values as variadic array(xs:numeric)) {
        array:fold-left($values, 0, function ($a, $b) { $a + $b })
    };

## Grammar (Preliminary; Unverified)

__Modified (Integration Points):__

    ParamList ::= Param ("," Param)* ("," VariadicParam)?

__New:__

    VariadicParam ::= "$" EQName "as" "variadic" ArrayTest
