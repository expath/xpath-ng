# Annotation Declarations (XQuery only)

**Author**: Reece H. Dunn. 67 Bricks.

This proposal adds the definition of annotation signatures into the XQuery language.


## Description

Users can add annotations to functions and variables, but cannot verify that those annotations have the correct form. This is especially the case for libraries such as RESTXQ that make use of annotations to provide additional information about the behaviour of the function.

A possible approach in the current XQuery syntax is to define annotations as external functions that are annotated as being annotations. For example:

    declare %annotation function annotation() external;
    declare %annotation function deprecated(
        $replacement as xs:string,
        $deprecation-message as xs:string?
    ) external;

The above approach has limitations in that the function gets added to the statically known functions, and it is hard to provide the annotation validation in a general way without having support from the XQuery processor.

### Annotation Declarations

This proposal describes a dedicated syntax based on the function declaration syntax for declaring annotations. This allows the example above to be rewritten as:

    declare annotation deprecated(
        $replacement as xs:string,
        $deprecation-message as xs:string?
    );

If an annotation does not have any parameters, the `()` may be omitted to be consistent with using annotations. For example:

    declare %xquery1-compatibility annotation updating for functions;

This adds the annotations of the specified signature to a list of *statically-known annotations* that works in a similar manner to the list of statically-known functions.

If an annotation is found in the list of statically-known annotations, any use of that annotation may be statically checked to see if it matches the declaration. This includes:

1. checking the number and type of the arguments passed to the annotation;
1. checking the context (function, variable, annotation, type) the annotation is used.

__NOTE:__ Providing annotations on types is listed here for completeness. If a proposal to allow annotations on types is not supported, then it will not apply to this proposal. If they are, then the signatures for type-specific annotation declarations should be possible.

If the annotation is not found in the list of statically-known annotations, no static checking is performed. This is to provide compatibility with existing XQuery code that is using annotations.

### Named Annotation References

Extending the function analogy, it would be useful to provide a way of referencing an annotation. For example:

    %private#0

This is prefixed with `%` to differentiate it from named function references.

Using an annotation sequence type proposal, the returned item could have a precise `annotation()` type.

The returned objects will not be callable (unlike functions, arrays, and maps), but may be passed to functions.


## Use Cases

Provide better static type checking of annotations, detecting usage errors earlier.

Make the annotation definitions accessible to the static context.

This is modelled on how function declarations work in XQuery.


## Examples

Defining the signature for existing XQuery annotations:

    declare annotation private;

    (: XQuery Update Facility 1.0 "updating" annotation: :)
    declare %xquery1-compatibility annotation updating for functions;

Defining RESTXQ annotations:

    declare annotation rest:POST for functions;
    declare annotation rest:POST($body-param as xs:string) for functions;

Defining RESTXQ annotations with variadic parameters, using the variadic function parameter proposal syntax:

    declare annotation rest:consumes(
        $mimetype1 as xs:string,
        $mimetypes as variadic array(xs:string)
    ) for functions;

    declare annotation rest:cookie-param(
        $name as xs:string,
        $param-reference as xs:string,
        $default-value as variadic array(xs:string)
    ) for functions;


## Grammar

### Annotation Declarations

Extension Point (added `AnnotationDecl`):

    AnnotatedDecl ::= "declare" Annotation* (VarDecl | FunctionDecl | AnnotationDecl)

New:

    AnnotationDecl ::= "annotation" EQName ArgumentList? ("for" AnnotationScopeList)?
    AnnotationScopeList ::= AnnotationScope ("," AnnotationScope)*
    AnnotationScope ::= "functions" | "variables" | "annotations" | "types"

### Named Annotation References

TBD.
