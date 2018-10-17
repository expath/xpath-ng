# Variadic Function Arguments

**Author**: Reece H. Dunn. 67 Bricks.

Allow support for a variable-length sequence of typed parameters to a function.


## Description

Variadic function parameters are defined using an `ArrayTest`. For example:

    $mimetypes as variadic array(xs:string)

In this example, `$mimetypes` is a *variadic function parameter*. A variadic function parameter must be the last parameter of a function defined by an inline function expression or function declaration.

The *variadic parameter type* is the type of the `ArrayTest` associated with the variadic function parameter, or `item()` if it is not specified.

A function definition where the last parameter is a variadic function parameter has a minimum arity equal to the number of non-variadic parameters, and an unbounded maximum arity.

A function reference to a variadic function will behave the same as if the function was defined with the specified arity, or an error if there are fewer arguments than the requested arity.

If a named function reference is created with an arity equal to the number of parameters including the variadic function parameter, the type of the last parameter in the bound function is the variadic parameter type.

When a function with variadic arguments is called, the arguments after the argument that corresponds with the last non-variadic parameter are collected into an array in the same order and passed to the variadic parameter.

If any of these arguments are an array or sequence, that array or sequence will be included as is at the place in the variadic array it occurs in the argument list from the variadic parameter. For example, if the variadic parameter is the third parameter in a function definition, and an array is passed as the fourth parameter, that array will be the second argument of the variadic array passed to the declared function.

### Influences

There are many languages that have support for variadic function arguments, including C/C++ and Java. Java supports variadic parameters by mapping them to an object array.


## Use Cases

Define the `fn:concat` function in the XPath and XQuery Functions and Operators 3.1 specification using valid XPath/XQuery syntax, instead of hand-waiving the variadic nature of the function.

Support other vendor-specific functions that allow variadic arguments. These include:

1. [out:format](http://docs.basex.org/wiki/Output_Module#out:format) -- BaseX
1. [xdmp:apply](https://docs.marklogic.com/xdmp:apply) -- MarkLogic
1. [sem:coalesce](https://docs.marklogic.com/sem:coalesce) -- MarkLogic

Support user-defined functions like sum or product that take a variable number of arguments without requiring nested parenthesis (e.g. using `sum(1, 2, 3)` instead of `sum((1, 2, 3))`).

### Requirements

1. MUST be backward compatible with XPath 3.1 and XQuery 3.1.

1. MUST limit the variadic function syntax to the last parameter of an inline function (XPath/XQuery), or named function declaration.

1. MUST make use of arrays or similar functionality to preserve the position of empty sequences in function calls.

1. MUST support named function references (XPath/XQuery) to the function with any arity above the number of defined arguments.

1. MUST support defining `fn:concat` using the variadic function parameter syntax.

1. MUST allow access to the variadic arguments within the function body of an XPath/XQuery expression or function declaration.

1. MAY allow variadic arguments to be applied to annotation definitions.

## Examples

### fn:concat

    declare function fn:concat(
        $arg1 as xs:anyAtomicType?,
        $arg2 as xs:anyAtomicType?,
        $other-args as variadic array(xs:anyAtomicType?)
    ) external;

    let $f := fn:concat#4
    let $g := fn:concat#2
    return ($f(1, 2, 3, 4), $g("a", "b")) (: returns ("1234", "ab") :)

### sum

Using a function declaration (XQuery):

    declare function sum($values as variadic array(xs:numeric)) {
        array:fold-left($values, 0, function ($a, $b) { $a + $b })
    };

    sum(1, 2, 3)

Using inline function expressions (XPath/XQuery):

    let $sum := function ($values as variadic array(xs:numeric)) {
        array:fold-left($values, 0, function ($a, $b) { $a + $b })
    };
    return $sum(1, 2, 3)

### rest:form-params (EXQuery RESTXQ)

    declare %annotation function rest:form-param(
        $name as xs:string,
        $param-reference as xs:string,
        $default-values as variadic array(xs:string)
    ) external;

or with support for annotation declarations:

    declare annotation rest:form-param(
        $name as xs:string,
        $param-reference as xs:string,
        $default-values as variadic array(xs:string)
    ) for function;

## Grammar (Preliminary; Unverified)

__Modified (Integration Points):__

    ParamList ::= Param ("," Param)* ("," VariadicParam)?

__New:__

    VariadicParam ::= "$" EQName "as" "variadic" ArrayTest
