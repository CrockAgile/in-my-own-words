# Rust Macros

## Common Macros

- `panic`
- `vec`
- `assert`
- `assert_eq`
- `try`
- `unreachable`
- `unimplemented`

## Using Macros

```rust
// only macros with #[macro_export] are public

// macros are not namespaced, so sometimes this could conflict
#[macro_use]
extern crate serde;

// select macros
#[macro_use(first_i_want, second_i_want)]
extern crate so_many_macros;

// only get macros, don't even link
#[macro_use]
#[no_link]
extern crate macros_and_unwanted_source;
```

## Kinds of Macros

### Excited Functions

Macros end with `!` and allow nearly arbitrary syntax as arguments.

```rust
let map = hashmap!{
    "a" => 1,
    "b" => 2,
};
```

### Custom Attributes

```rust
#[derive(Serialize, Deserialize)]
struct S {
    #[serde(rename = "a")]
    f: i32,
}
```

## The State of the Macros

### `macro_rules`

The first form of macros in Rust, and the most widely used are "declarative macros", sometimes referred to as "macros by example", "macro_rules macros", or just "macros". Their design is similar to rust match expressions, with a pattern to match for each handled syntax its resulting code.

#### Pattern Matching

Macro patterns do not handle parse ambiguity. A case like `$($i:ident)* $e:expr` will always fail to parse because it is unable to choose between a token being an identifier or an expression. This ambiguity can be resolved by introducing more tokens to the syntax, `$($i:ident)* | $e:expr`.

##### Pattern Fragment Specifiers

- `ident` - an identifier, like `x` or `my_var`
- `path` - a qualified name, like `T::from_str`
- `expr` - an expression, like `2 + 2`, `if ... else`, `sqrt(9)`, etc
- `ty` - a type, like `i32` or `&T`
- `pat` - a pattern, like `Some(x)` or `_`
- `stmt` - a single statement, like `let x = 1;`
- `block` - a block of delimited statements, and optionally returned expression
- `item` - an item of a crate, like `fn crate_func() {}`
- `tt` - a token tree
- `meta` - a meta item found in attributes, like `cfg(target_os = "windows")`

Let's break down an example:

```rust
macro_rules! vec {
    // series of (pattern => result)

    // first and only pattern
    // $x:expr matches an expression and names it $x
    // $( ... ),* matches zero or more of ... patterns
    // separated by ','
    ( $( $x:expr ),* ) => {
        {
            // only done once
            let mut temp_vec = Vec::new();
            // done for each match from $( ... ),*
            $(
                temp_vec.push($x);
            )*
            // only done once
            temp_vec
        }
    };
}

vec![1, 2, 3];
```

`$(...)+` may be used to match one or more patterns

#### Context

Each macro expansion gets its own variable context.

```rust
macro_rules! in_own_context {
    () => (let x = "in_own_context";);
}

macro_rules! from_parent_context {
    ($v:ident) => (let $v = "from_parent_context";)
}

fn main() {
    in_own_context!();
    // println!("x = {}", x); => Error! No x in scope
    from_parent_context!(x);
    println!("x = {}", x);
    // "x = from_parent_context"
}

// compiling also gives warning about unused variable from `in_own_context`
```

#### Debugging

Macros can be tricky to debug, and so there are some handy tools:

- `rustc --pretty expanded` - get results of macro expansion
- `rustc --pretty hygiene` - get syntax contexts annotations
- `log_syntax!(...)` - prints arguments and expands to nothing
- `trace_macros!(bool)` - set compiler messages for each macro expansion

#### Constraints

Pattern macros must:

- be invoked with valid rust syntax
- expand to zero or more items/methods, or an expression/statement/pattern

### Procedural Macros

Eventually, Procedural Macros will be able to handle all kinds of use cases. However for now they are limited to `#[derive(...)]`.

#### Derive Hello World

```rust
extern crate proc_macro;
extern crate syn; // crate for parsing rust code
#[macro_use]
extern crate quote; // crate for generating rust code

#[proc_macro_derive(HelloWorld)]
pub fn hello_world(input: TokenStream) -> TokenStream {
    // convert input tokens to string
    let input_string = input.to_string();
    // parse input string to AST for [derive(...)]
    let ast = syn::parse_derive_input(&input_string).unwrap();
    // build implementation using the ast
    let gen = impl_hello_world(&ast);
    // return generated implementation
    gen.parse().unwrap();
}

fn impl_hello_world(ast: &syn::DeriveInput) -> quote::Tokens {
    let name = &ast.ident;
    quote! {
        impl HelloWorld for #name {
            fn hello_world() {
                println!("Hello world! My name is {}", stringify!(#name));
            }
        }
    }
}
```

#### Custom Attributes

```rust
#[derive(HelloWorld)]
#[HelloWorldName = "Jeff"]
struct Jeff;

fn main() {
    Jeff::hello_world();
}

// requires changing macro
#[proc_macro_derive(HelloWorld, attributes(HelloWorldName))]
pub fn hello_world(input: TokenStream) -> TokenStream
```

#### Crate Requirements

```toml
[dependencies]
syn = "*"
quote = "*"

[lib]
proc-macro = true
```