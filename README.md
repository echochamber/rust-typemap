# `TypeMap`

## [Documentation](https://crates.fyi/crates/typemap/0.3.3)

## What is typemap?

The easiest way to think about TypeMap is as a friendly wrapper around HashMap<TypeId, ValueType>. 
The key will always be **THE NAME** of the TypeId, and the value will always be **AN INSTANCE** of ValueType. 
What this means that you may store 0 or 1 values type for every TypeId in a TypeMap.

It provides functionality similar to [AnyMap](https://github.com/chris-morgan/anymap), but is more flexible because it
also allows you to define the type of the value for a given key type, rather than forcing keys and values to be the same type.

Key-value associations are defined through the `Key` trait, which uses an
associated type parameter and trait coherence rules to enforce the invariants
of `TypeMap`.

## Examples

### Basic Example

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

### Multiple Key/Value Relationships In A Single TypeMap

```rust
extern crate typemap;
use typemap::{TypeMap, Key};

// Two arbitrary types that we will use as our Keys in Typemap
struct FirstKeyType;
struct SecondKeyType;

#[derive(Debug, PartialEq)]
struct FirstValueType(i32);

#[derive(Debug, PartialEq)]
struct SecondValueType(u64);

// Define the key-value relationships between each of the key types and values
impl Key for FirstKeyType { type Value = FirstValueType; }
impl Key for SecondKeyType { type Value = SecondValueType; }

#[test] fn test_pairing() {
    let mut map = TypeMap::new();

    // Valid insertion,
    // The "Value Types" are the same as we defined above for the given "Key Types".
    map.insert::<FirstKeyType>(FirstValueType(42i32));
    map.insert::<SecondKeyType>(SecondValueType(21u64));
    
    // Will cause a type mismatch error
    // only instances of FirstValueType can be inserted for FirstKeyType
    // map.insert::<FirstKeyType>(SecondValueType(21u64));

    // Likewise, will also cause a type mismatch
    // map.insert::<SecondKeyType>(FirstValueType(42i32));

    assert_eq!(*map.get::<FirstKeyType>().unwrap(), FirstValueType(42i32));
    assert_eq!(*map.get::<SecondKeyType>().unwrap(), SecondValueType(21u64));
}
```