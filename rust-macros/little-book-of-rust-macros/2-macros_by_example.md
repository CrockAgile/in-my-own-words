# Macros By Example

`macro_rules!`

First the rust source code is tokenized.

- tokens can be both identifiers and keywords
- some keywords are reserved for future use (`yield` and `macro`)
- some symbols are reserved for historical reasons (`<-`)
- `::` is a different token than two `:`

Next, the Abstract Syntax Tree is built from the tokens. All information is still purely lexical at this stage.

Now, macros are processed.

## Token Trees

- exist between tokens and AST
- AST is different
- there is not a single root node
- grouping tokens `(...)`/`[...]`/`{...}` are not leaves
- cannot have unpaired parenthesis/bracket/brace

## Syntax Extensions

The input of every macro is a single non-leaf token tree. Macros can take the place of:

- patterns
- statements
- expressions
- items
- impl items

### Attributes

- `# [ $arg ]` like `#[derive(Clone)]`
- `# ! [ $arg ]` like `#![allow(dead_code)]`

### Generic

- `$name ! $arg` like `println!("Hello!")`
- `$name ! $arg0 $arg1` like `macro_rules! noop { () => {}; }`

### Expansion

Macros are expanded after building the AST and before the compiler begins semantics. The result of a macro is limited to what would be valid in that context.

The default macro recursion limit is `32` but can be changed via `#![recursion_limit="N"]`.

## `macro_rules!`

```rust
macro_rules! $name {
    $rule0 ;
    $rule1 ;
    // ...
    $ruleN ;
}

// each rule looks like
($pattern) => {$expansion}
```

### Matching

- first rule with complete pattern match is expanded
- if no rule matches then expansion fails
- patterns can contain token literals

### Captures

- patterns can contain captures
- capture based on grammar category
- written as `$var:kind`

#### Kinds

- `item` - function, structure, module, etc
- `block` - `{ statements & expressions }`
- `stmt` - statement
- `pat` - pattern
- `expr` - expression
- `ty` - type
- `ident` - identifier
- `path` - `foo`, `::std::mem::replace`, etc
- `meta` - items within `#[...]` and `#![...]` attributes
- `tt` - a token tree

### Repetitions

#### Pattern

Of the form `$ ( ... ) sep rep`

- `$` is token literal
- `( ... )` is the pattern being repeated
- `sep` is optional separator, like `,`
- `rep` is required repeat control, either `*` or `+`

#### Expansion

Use same syntax as pattern, `$ ( ... )`

### Considerations

- write rules from most to least specific
- do not use adjacent repetitions
- capturing anything but `tt` or `ident` resolves to AST
- `self` is both a keyword and an identifier

### Debugging

#### Trace Macros

```rust
#![feature(trace_macros)]

trace_macros!(true);
debug_me!()
trace_macros!(false);
```

or via command line `-Z trace-macros`

#### Log Syntax

```rust
#![feature(log_syntax)]

macro_rules! sing {
    () => {};
    ($tt:tt $($rest:tt)*) => {log_syntax!($tt); sing!($($rest)*);};
}

sing! {
    ^ < @ < . @ *
    '\x08' '{' '"' _ # ' '
    - @ '$' && / _ %
    ! ( '\t' @ | = >
    ; '\x08' '\'' + '$' ? '\x7f'
    , # '"' ~ | ) '\x07'
}
```