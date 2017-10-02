# Non Lexical Lifetimes

Interpetation of [Rust's nonlexical lifetime RFC Summary](#https://github.com/nikomatsakis/nll-rfc/blob/master/0000-nonlexical-lifetimes.md)

> How to extend Rust's borrow system to support lifetimes based on control flow rather than scope?

## What is a lifetime?

Whenever you borrow something, the reference is assigned a corresponding lifetime where the reference may be used. In Rust, the term "lifetime" should particularly not be confused with variable scope. In short:

- lifetime, span in which a reference is used
- scope, span before the value is destroyed

```rust
fn capitalize(data: &mut [char]) {
    // do whatever with the mutable borrow
}

fn example() {
    let mut data = vec!['a','b','c']; // scope of `data` begins here
    capitalize(&mut data[..]); // lifetime of borrow begins and ends here

    // because `data` is in scope and no borrow lifetime may be mutated
    data.push('d');
}   // scope of `data` ends here
```

### Variable lifetime

The above example compiles and operates as expected. However, by only introducing an intermediary variable it will fail to compile:

```rust
fn example_with_variable() {
    let mut data = vec!['a','b','c']; // scope of `data` begins here
    let slice = &mut data[..]; // lifetime of borrow begins here
    capitalize(slice);

    // `data` is in scope BUT the borrow lifetime held by `slice` is as well
    // which blocks mutation
    data.push('d'); // ERROR!
}   // scope of `data` and `slice` end here
```

This arrises because a variable holding a reference extends the lifetime until the variable's scope ends. This is currently resolved by introducing a new block to limit the variable's scope:

```rust
fn example_with_variable() {
    let mut data = vec!['a','b','c']; // scope of `data` begins here
    {
        let slice = &mut data[..]; // lifetime of borrow begins here
        capitalize(slice);
    } // `slice` scope and lifetime end here

    // because `data` is in scope and no borrow lifetime may be mutated
    data.push('d');
}   // scope of `data` end here
```

There are other limitations imposed by lexical lifetimes, but they did not add any deeper understanding of the problem beyond recognizing traps I have stumbled into before.

## Proposed solution

> lifetimes defined via control flow graph

The proposed solution is as follows:

1. Definitions
1. Limited basic control flow of one function
1. Extend to handle dropck and `#[may_dangle]`
1. Extend to named lifetime parameters
1. Describe the borrow checker

### Definitions

#### Lvalues

> a path that leads to a memory location

A simplified view of Lvalues in MIR grammar:

```
Lvalue = local_variable | Lvalue.field | *Lvalue
```

#### Lvalue prefixes

> all the lvalues you get by stripping away fields and derefs of an lvalue

```
prefixes(*a.b) -> [*a.b, a.b, a]
```

#### Control flow graph

> MIR uses a control flow graph rather than an abstract syntax tree

Each block in the control flow is a series of statements and a terminator.
Statements relevant to this RFC are:

- statements, `LHS = RHS`
- drops, `drop(lvalue)`
- stack deallocation, `StorageDead(x)`

### Control flow within a function

```rust
let mut first = 1;
let mut other = 2;

let p = &first;
// Point 0

if condition {
    print(*p);
    // Point 1
    p = &other;
    // Point 2
}
// Point 3
print(*p);
// Point 4
```

At what points does each variable need to be considered borrowed?

| variable | 0   | 1   | 2   | 3   | 4   |
|:--------:|:---:|:---:|:---:|:---:|:---:|
| first    | X   |     |     | X   |     |
| other    |     |     | X   | X   |     |