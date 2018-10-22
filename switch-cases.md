# Switch Cases

**Author**: Christian Grün, BaseX GmbH

## Description

[XQuery switch cases](https://www.w3.org/TR/xquery-31/#id-switch) have a peculiar restriction: The operand of a single case must yield an empty sequence or a single item. There seem to be no (obvious) reasons why this restriction exists, so I believe we should not lift it and allow arbitrary sequences.

A similar extension is planned for Java 12 ([JEP 325: Switch Expressions](http://openjdk.java.net/jeps/325)). The required changes in XQuery are simpler, though, as the 3.1 grammar already supports arbitrary expressions as operands.

## Examples

```xquery
switch($value)
  case 1
  case 2
  case 3
  case 4
  case 5
    return 'small'
  default
    return 'big'
```

Proposed syntax:

```xquery
switch($value)
  case 1 to 5
    return 'small'
  default
    return 'big'
```

## Grammar

No changes are required.