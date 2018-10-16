# Syntax Cleanup

**Author**: Reece H. Dunn. 67 Bricks.

This makes the EBNF grammar consistent.


## Description

This makes the variable bindings consistent with EBNF patterns in the XPath/XQuery grammar, such as `ForBinding`/`LetBinding`.

These changes are based on my experience when implementing an XQuery parser for an IntelliJ IDE plugin.


## Use Cases

These changes make it easier to implement things like variable and namespace lookup when traversing an AST generated from parsing an XPath/XQuery expression using the EBNF grammar.


## Examples

Not applicable. These changes do not change the behaviour.


## Grammar (Preliminary; Unverified)

### Quantified Expressions (XPath/XQuery)

__Modified (Integration Points):__

    QuantifiedExpr ::= ("some" | "every") QuantifiedExprBinding ("," QuantifiedExprBinding)* "satisfies" ExprSingle

__New:__

    QuantifiedExprBinding ::= "$" VarName TypeDeclaration? "in" ExprSingle

This follows the grammar production pattern used in other constructs like
`LetClause` and `ForClause`.

### Typeswitch (XQuery)

__Modified (Integration Points):__

    TypeswitchExpr ::= "typeswitch" "(" Expr ")" CaseClause+ DefaultCaseClause

__New:__

    DefaultCaseClause ::= "default" ("$" VarName)? "return" ExprSingle

The default case expression is factored out here into a separate grammar
production similar to the `CaseClause` expression.

### Node Constructors (XQuery)

__Modified (Integration Points):__

    DirAttributeList ::= (S DirAttribute?)* /* ws: explicit */ 

__New:__

    DirAttribute ::= QName S? "=" S? DirAttributeValue /* ws: explicit */ 

This follows the grammar production pattern used in other constructs like
`ParamList`.

### Node Tests (XPath/XQuery)

__Modified (Integration Points):__

    Wildcard ::= WildcardIndicator | (NCName ":" WildcardIndicator) | (WildcardIndicator ":" NCName) | (BracedURILiteral WildcardIndicator) /* ws: explicit */ 

__New:__

    WildcardIndicator ::= "*"

This makes the `Wildcard` syntax follow the pattern for `EQName`, with the `NCName`s being replaced with `WildcardIndicator`.

## Implementation Experience

1. [XQuery IntelliJ Plugin](https://github.com/rhdunn/xquery-intellij-plugin) -- hand-written recursive-descent parser; static context (variable bindings and namespace lookup).
