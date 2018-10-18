[![Creative Commons Licence - BY 4.0](https://img.shields.io/badge/license-CC%20BY%204.0-blue.svg)](http://creativecommons.org/licenses/by/4.0/)
[![Code of Conduct](https://img.shields.io/badge/code%20of%20conduct-contributor%20covenant-%235e0d73.svg)](http://contributor-covenant.org/version/1/4/)

# XPath Syntax Extensions Wishlist 🧚

The purpose of this repository is to collaborate on and collate syntax extensions that XPath users would like to see in a future version of XPath.

This repository is not for proposing extension modules or functions for XPath (or XPath derived languages), such efforts belong elsewhere. The focus here is extending the core grammar of the XPath language.

Sometimes it is not immediately obvious whether a language extension is applicable to the whole XPath sphere or just to XQuery. We are also happy to accept proposals which may only be destined for XQuery. When a proposal is only relevant for XQuery, we will mark it as such.


## Proposal Process

1. Fork the GitHub repository.
2. Make a copy of the [proposal-template.md](https://github.com/expath/xpath-ng/blob/master/proposal-template.md) file to `your-short-proposal-name.md`.
3. Write up your proposal in your `your-short-proposal-name.md` file using Markdown syntax.
4. Git commit the `your-short-proposal-name.md` file.
5. Modify this [README.md](https://github.com/expath/xpath-ng/blob/master/README.md) file to add your proposal to the [Proposed Syntax Extensions](#proposed-syntax-extensions) list, and commit it.
6. Send a Pull Request just for `your-short-proposal.md` file.
7. Await comments and feedback in the Pull Request.

Pull requests comments will be used to discuss a proposal. Commits can be further added by the author to a Pull Request to incorporate feedback etc.

**TODO** - we need some sort of disagreement resolution policy / vote procedure. 


## Code of Conduct

Yes we all have different ideas about what we consider to be beautiful syntax. Please consider the underlying ideas of a proposal when commenting and not just whether you like the syntax or not. Once the idea being proposed is concrete enough, we can at the end discuss exact syntax etc.

Please treat others better than you would like to be treated yourself and avoid comments which might be interpreted as personally inappropriate. We have adopted the [Contributor Covenant](https://www.contributor-covenant.org/), so if in doubt please see the [Code of Conduct](https://www.contributor-covenant.org/version/1/4/code-of-conduct).


## Proposed Syntax Extensions

Here we categorise, list and link to each syntax extension that has been proposed so far.

Any proposals are XQuery specific, should be clearly marked as such with: "(**XQuery only**)"

Name | Contributor | Notes
--- | --- | ---
[Ternary If](ternary-if-ChristianGruen.md) | Christian Grün, BaseX GmbH | XPath & XQuery
[If Without Else](if-without-else-ChristianGruen.md) | Christian Grün, BaseX GmbH | XPath & XQuery
 
Everyone: Please add and link further proposals.
