# Namespace prefix binding expression

**Author**: Liam  Quin, https://www.delightfulcomputing.com/

A difficulty with XPath has always been that there is no way to associate a namespace URI with a prefix in pure XPath, so you end up writing stuff like /*[local-name()  eq 'html' and namespace-uri(.) = 'http://www.w3.org/1999/xhtml']/*[local-name() eq 'body' and namespace-uri(.) eq 'http://www.w3.org/1999/xhtml']/...

So this is a proposal for
with prefix h := "http://www.w3.org/1999/xhtml" return //html/body

I seem to remember that for this to work in XQuery as well as XPath it has to have a pair of tokens, hence "with prefix". It would of course take a list,
with prefix p1 := n1, p2 := n2 return singleExpr

The list should be allowed to be empty, to help automatic generation of XPath or XQuery fragments.

I raised this idea within the Working group meetings, but then the feeling was EQNames would satisfy this. So, you can write,
/"http://www.w3.org/1999/xhtml"html/"http://www.w3.org/1999/xhtml"body
but i don't find this helps make the resulting expression very readable.

In the examples i've used, the prefix is an NCName - it could be any expression evaluating to an NCName i suppose, although i don't have a use case for that, and there would be no way to use the result in the expression, so a literal NCName seems best to me.

