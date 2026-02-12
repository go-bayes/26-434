# mdBook Markdown Cheatsheet

## Serving Locally

```
mdbook serve
```

Renders at http://localhost:3000. Press `Ctrl+C` to stop.

## Headings

```
# H1
## H2
### H3
```

## Emphasis

```
*italic*  **bold**  ***bold italic***  ~~strikethrough~~
```

## Links and Images

```
[link text](url)
![alt text](image.png)
```

## Lists

```
- unordered item
  - nested item

1. ordered item
2. second item
```

## Tables

```
| Header 1 | Header 2 |
|---|---|
| cell | cell |
```

## Code

Inline: `` `code` ``

Fenced block:

````
```r
x <- 1:10
```
````

## Maths (KaTeX)

Inline: `$E[Y(1) - Y(0)]$`

Display:

```
$$
ATE = E[Y(1) - Y(0) \mid L]
$$
```

## Admonish Blocks

```
~~~admonish tip title="Title"
Content here.
~~~
```

Types: `tip`, `warning`, `note`, `info`, `example`, `question`, `danger`, `bug`, `abstract`, `success`, `failure`, `quote`

Alternatively use triple backticks:

````
```admonish warning title="Careful"
Content here.
```
````

## Blockquotes

```
> Quoted text.
```

## Horizontal Rule

```
---
```

## Hiding Content from Print

```
<div class="no-print">
Only visible in browser.
</div>
```

## Including Files

```
\{{#include path/to/file.md}}
```

(Remove the backslash; escaped here to prevent processing.)

## Runnable Code (not applicable to R, but available for Rust)

````
```rust,editable
fn main() {
    println!("hello");
}
```
````
