This package is nowhere near finished - in fact not even started. Please don't try to use it yet!

I'm struggling to find a real-world use for this package, so I'm pausing development.

# Decorated Syntax Selectors

This package attempts to implement a dialect of CSS Selectors that can be used to easily select 
nodes in a Go syntax tree. Specifically, the Decorated Syntax Tree defined by [github.com/dave/dst](https://github.com/dave/dst). 

### Names

| Name       | Example |
| ---        | --- |
| Node       | `Ident` in `Ident` |
| Interface  | `Expr` |
| Field      | `Body` in `FuncLit` |
| Definition | `foo` in `Field.Names`&ast;, `ImportSpec.Name`, `ValueSpec.Names`&ast;&ast;, `TypeSpec.Name`, `FuncDecl.Name`, `File.Name`, `AssignStmt.Lhs`&ast;&ast;&ast; |
| Reference  | `foo` in `Ident.Name` |
| Package    | `foo.bar/baz` in `Ident.Path`|

&ast; `Field.Names` - all names are applied to the `Field`   
&ast;&ast; `ValueSpec.Names` - all names are applied to the `ValueSpec` node, and each name is applied to it's corresponding `Expr` node in `Values`  
&ast;&ast;&ast; `AssignStmt.Lhs` - only when `AssignStmt.Tok == token.DEFINE`, all names are applied to the `AssignStmt` node, and each name is applied to it's corresponding `Expr` node in `Rhs`  

### Proposed DSS Selectors for DST

| Implemented | Selector               | Example                 | Example description |
| ---         | ---                    | ---                     | ---  |
| ☐           | `.field`               | `.Fun`                  | Selects all child nodes in the field `Fun` |
| ☐           | `#name`                | `#main`                 | Selects all nodes that define the name `main` |
| ☐           | `node`                 | `CallExpr`              | Selects all `CallExpr` nodes | 
| ☐           | `interface`            | `Expr`                  | Selects all nodes that satisfy the `Expr` interface |
| ☐           | `node,node`            | `CallExpr, Ident`       | Selects all `CallExpr` nodes and all `Ident` nodes |
| ☐           | `node node`            | `CallExpr Ident`        | Selects all `Ident` nodes inside `CallExpr` nodes |
| ☐           | `node>node`            | `CallExpr > Ident`      | Selects all `Ident` nodes where the parent is a `CallExpr` node |

### Examples

```go
func main() {
	foo, bar := 1 + 1, 2 + 2
}

type T struct {
	i, j int
}
```

`FuncDecl#main Expr#foo BinaryExpr` -> `1 + 1` 

`#T #i.Type` -> `int`


### CSS Selectors for HTML

| Proposed | Implemented | Selector               | Example                 | Example description |
| ---      | ---         | ---                    | ---                     | ---  |
| ☒        | ☐           | `.class`               | `.intro`                | Selects all elements with `class="intro"` |
| ☒        | ☐           | `#id`                  | `#firstname`            | Selects the element with `id="firstname"` |
| ☐        | ☐           | `*`                    | `*`                     | Selects all elements |
| ☒        | ☐           | `element`              | `p`                     | Selects all `<p>` elements |
| ☒        | ☐           | `element,element`      | `div, p`                | Selects all `<div>` elements and all `<p>` elements |
| ☒        | ☐           | `element element`      | `div p`                 | Selects all `<p>` elements inside `<div>` elements |
| ☒        | ☐           | `element>element`      | `div > p`               | Selects all `<p>` elements where the parent is a `<div>` element |
| ☐        | ☐           | `element+element`      | `div + p`               | Selects all `<p>` elements that are placed immediately after `<div>` elements |
| ☐        | ☐           | `element1~element2`    | `p ~ ul`                | Selects every `<ul>` element that are preceded by a `<p>` element |
| ☐        | ☐           | `[attribute]`          | `[target]`              | Selects all elements with a target attribute |
| ☐        | ☐           | `[attribute=value]`    | `[target=_blank]`       | Selects all elements with `target="_blank"` |
| ☐        | ☐           | `[attribute~=value]`   | `[title~=flower]`       | Selects all elements with a title attribute containing the word "flower" |
| ☐        | ☐           | `[attribute|=value]`   | `[lang|=en]`            | Selects all elements with a lang attribute value starting with "en" |
| ☐        | ☐           | `[attribute^=value]`   | `a[href^="https"]`      | Selects every `<a>` element whose href attribute value begins with "https" |
| ☐        | ☐           | `[attribute$=value]`   | `a[href$=".pdf"]`       | Selects every `<a>` element whose href attribute value ends with ".pdf" |
| ☐        | ☐           | `[attribute*=value]`   | `a[href*="w3schools"]`  | Selects every `<a>` element whose href attribute value contains the substring "w3schools" |
| ☐        | ☐           | `:active`              | `a:active`              | Selects the active link |
| ☐        | ☐           | `::after`              | `p::after`              | Insert something after the content of each `<p>` element |
| ☐        | ☐           | `::before`             | `p::before`             | Insert something before the content of each `<p>` element |
| ☐        | ☐           | `:checked`             | `input:checked`         | Selects every checked `<input>` element |
| ☐        | ☐           | `:default`             | `input:default`         | Selects the default `<input>` element |
| ☐        | ☐           | `:disabled`            | `input:disabled`        | Selects every disabled `<input>` element |
| ☐        | ☐           | `:empty`               | `p:empty`               | Selects every `<p>` element that has no children (including text nodes) |
| ☐        | ☐           | `:enabled`             | `input:enabled`         | Selects every enabled `<input>` element |
| ☐        | ☐           | `:first-child`         | `p:first-child`         | Selects every `<p>` element that is the first child of its parent |
| ☐        | ☐           | `::first-letter`       | `p::first-letter`       | Selects the first letter of every `<p>` element |
| ☐        | ☐           | `::first-line`         | `p::first-line`         | Selects the first line of every `<p>` element |
| ☐        | ☐           | `:first-of-type`       | `p:first-of-type`       | Selects every `<p>` element that is the first `<p>` element of its parent |
| ☐        | ☐           | `:focus`               | `input:focus`           | Selects the input element which has focus |
| ☐        | ☐           | `:hover`               | `a:hover`               | Selects links on mouse over |
| ☐        | ☐           | `:in-range`            | `input:in-range`        | Selects input elements with a value within a specified range |
| ☐        | ☐           | `:indeterminate`       | `input:indeterminate`   | Selects input elements that are in an indeterminate state |
| ☐        | ☐           | `:invalid`             | `input:invalid`         | Selects all input elements with an invalid value |
| ☐        | ☐           | `:lang(language)`      | `p:lang(it)`            | Selects every `<p>` element with a lang attribute equal to "it" (Italian) |
| ☐        | ☐           | `:last-child`          | `p:last-child`          | Selects every `<p>` element that is the last child of its parent |
| ☐        | ☐           | `:last-of-type`        | `p:last-of-type`        | Selects every `<p>` element that is the last `<p>` element of its parent |
| ☐        | ☐           | `:link`                | `a:link`                | Selects all unvisited links |
| ☐        | ☐           | `:not(selector)`       | `:not(p)`               | Selects every element that is not a `<p>` element |
| ☐        | ☐           | `:nth-child(n)`        | `p:nth-child(2)`        | Selects every `<p>` element that is the second child of its parent |
| ☐        | ☐           | `:nth-last-child(n)`   | `p:nth-last-child(2)`   | Selects every `<p>` element that is the second child of its parent, counting from the last child |
| ☐        | ☐           | `:nth-last-of-type(n)` | `p:nth-last-of-type(2)` | Selects every `<p>` element that is the second `<p>` element of its parent, counting from the last child |
| ☐        | ☐           | `:nth-of-type(n)`      | `p:nth-of-type(2)`      | Selects every `<p>` element that is the second `<p>` element of its parent |
| ☐        | ☐           | `:only-of-type`        | `p:only-of-type`        | Selects every `<p>` element that is the only `<p>` element of its parent |
| ☐        | ☐           | `:only-child`          | `p:only-child`          | Selects every `<p>` element that is the only child of its parent |
| ☐        | ☐           | `:optional`            | `input:optional`        | Selects input elements with no "required" attribute |
| ☐        | ☐           | `:out-of-range`        | `input:out-of-range`    | Selects input elements with a value outside a specified range |
| ☐        | ☐           | `::placeholder`        | `input::placeholder`    | Selects input elements with placeholder text |
| ☐        | ☐           | `:read-only`           | `input:read-only`       | Selects input elements with the "readonly" attribute specified |
| ☐        | ☐           | `:read-write`          | `input:read-write`      | Selects input elements with the "readonly" attribute NOT specified |
| ☐        | ☐           | `:required`            | `input:required`        | Selects input elements with the "required" attribute specified |
| ☐        | ☐           | `:root`                | `:root`                 | Selects the document's root element |
| ☐        | ☐           | `::selection`          | `::selection`           | Selects the portion of an element that is selected by a user |
| ☐        | ☐           | `:target`              | `#news:target`          | Selects the current active #news element (clicked on a URL containing that anchor name) |
| ☐        | ☐           | `:valid`               | `input:valid`           | Selects all input elements with a valid value |
| ☐        | ☐           | `:visited`             | `a:visited`             | Selects all visited links |
