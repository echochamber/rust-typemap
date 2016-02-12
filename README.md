# `TypeMap`

> A typesafe store keyed by types and containing different types of values.

## [Documentation](https://crates.fyi/crates/typemap/0.3.3)

It provides functionality similar to [AnyMap](https://github.com/chris-morgan/anymap), but is more flexible because it
also allows you to define the type of the value for a given key type, rather than forcing keys and values to be the same type.

Key-value associations are defined through the `Key` trait, which uses an
associated type parameter and trait coherence rules to enforce the invariants
of `TypeMap`.

## Example

```rust
extern crate typemap;
use typemap::{TypeMap, Key};

// Some arbitrary type that we will use as a Key in Typemap
struct MyKeyType;

#[derive(Debug, PartialEq)]
struct MyValueType(i32);

// Define the key-value relationship between MyKeyType and MyValueType
impl Key for MyKeyType { type Value = MyValueType; }

#[test] fn test_pairing() {
    let mut map = TypeMap::new();
    map.insert::<MyKeyType>(MyValueType(42));
    assert_eq!(*map.get::<MyKeyType>().unwrap(), MyValueType(42));
}
```

