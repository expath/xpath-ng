# Syntax Cleanup

**Author**: Reece H. Dunn. 67 Bricks.

This makes the EBNF grammar consistent.


## Description

This makes the variable bindings consistent with EBNF patterns in the XPath/XQuery grammar, such as `ForBinding`/`LetBinding`.


## Use Cases

These changes make it easier to implement things like variable and namespace lookup when traversing an AST generated from parsing an XPath/XQuery expression using the EBNF grammar.


## Examples

Not applicable. These changes do not change the behaviour.


## Grammar

### Quantified Expressions (XPath/XQuery)

    QuantifiedExpr ::= ("some" | "every") QuantifiedExprBinding ("," QuantifiedExprBinding)* "satisfies" ExprSingle
    QuantifiedExprBinding ::= "$" VarName TypeDeclaration? "in" ExprSingle

This follows the grammar production pattern used in other constructs like
`LetClause` and `ForClause`.

### Typeswitch (XQuery)

    TypeswitchExpr ::= "typeswitch" "(" Expr ")" CaseClause+ DefaultCaseClause
    DefaultCaseClause ::= "default" ("$" VarName)? "return" ExprSingle

The default case expression is factored out here into a separate grammar
production similar to the `CaseClause` expression.

### Node Constructors (XQuery)

    DirAttributeList  ::= (S DirAttribute?)* /* ws: explicit */
    DirAttribute ::= QName S? "=" S? DirAttributeValue /* ws: explicit */

This follows the grammar production pattern used in other constructs like
`ParamList`.

### Node Tests (XPath/XQuery)

    Wildcard ::= WildcardIndicator | (NCName ":" WildcardIndicator) | (WildcardIndicator ":" NCName) \| (BracedURILiteral WildcardIndicator) /* ws: explicit */
    WildcardIndicator ::= "*"

This makes the `Wildcard` syntax follow the pattern for `EQName`, with the `NCName`s being replaced with `WildcardIndicator`.
