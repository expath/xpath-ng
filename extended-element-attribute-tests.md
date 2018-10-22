# Extended Element and Attribute Tests

**Author**: Reece H. Dunn. 67 Bricks.

Allows element and attribute tests to support union and wildcard name tests.


## Description

### Element and Attribute Unions

The XPath and XQuery grammar allows element and attribute unions in a path expression. For example:

    let $list := $current/(ul|ol)
    let $label := $anchor/@(title|name|alt)
    ...

However, it does not allow the precise value of these to be specified by the user (e.g. as parameters or return types of functions).

With a union item type proposal, these types could be specified verbosely as:

    (element(ul) | element(ol))
    (attribute(title) | attribute(name) | attribute(alt))

This proposal defines the following concise syntax for the `ElementNameOrWildcard` part of an `ElementTest`, and the `AttributeNameOrWildcard` part of an `AttributeTest`:

    element(ul|ol)
    attribute(title|name|alt)

These would be equivalent to the more verbose item type union.

The wildcard `*` cannot be combined in a union type as it would be redundant.

### Wildcard Names

The XPath and XQuery grammar allows element and attribute path expressions to have wildcard-based node tests of the following forms:
1.  `*` / `@*` -- match any element/attribute
1.  `prefix:*` / `@prefix:*` -- match any element/attribute in the in-scope namespace prefix
1.  `*:local-name` / `@*:local-name` -- match any element/attribute with the given local name, ignoring the namespace uri
1.  `Q{namespace-uri}*` / `@Q{namespace-uri}*` -- match any element/attribute in the specified namespace uri

Like element/attribute unions, a user cannot currently specify the precise type for these expressions except for the general `*` case.

Wildcard type tests would exclude the `*` form -- that is only allowed on its own (i.e. not as part of a union type). The other wildcard forms (prefix, local-name, braced uri literal) are allowed in a union expression, or in a single name. For example:

    element(xh:*)
    attribute(xh:* | Q{}*)

### Type Names

In all of the above extensions a `TypeName` can be optionally specified, like can be done in the current `ElementTest` and `AttributeTest` syntax. For example:

    element(ol|ul, html-list)
    attribute(price|cost, currency)

## Use Cases

A user can write an element path expression over a union of elements, but cannot define a precise type for the result.

A user can write an element path expression over a union of attributes, but cannot define a precise type for the result.

A user can write a wildcard node test on element path expression, but can only define a type for `*` wildcards.

A user can write a wildcard node test on attribute path expression, but can only define a type for `*` wildcards.

## Examples

### Matching different list types

    declare function print-list($list as element(ul|ol)) external;

    print-list($item/ul)

### Handling HTML and XHTML in the same expression

    declare function save-html(
        $html as element(xh:html|Q{}html),
        $path as xs:string
    ) external;

### Any XHTML (or namespaced) element

    declare function is-inline-element($e as element(xh:*)) external;

    (: union and wildcard: :)
    declare function is-block-element($e as element(xh:* | Q{}*)) external;

### Checking if an element is one of a set of element names

    let $e := ...
    return $e instance of element(a|span|em|i|strong|b)

## Grammar

TBD.
