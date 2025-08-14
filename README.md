# int_like

[![Crates.io](https://img.shields.io/crates/v/int_like.svg)](https://crates.io/crates/int_like)
[![Documentation](https://docs.rs/int_like/badge.svg)](https://docs.rs/int_like)
[![License](https://img.shields.io/crates/l/int_like.svg)](https://github.com/fslongjin/int_like/blob/master/LICENSE)

A Rust macro for defining integer-backed opaque types safely.

Originally inspired by code from Redox OS, this crate provides a convenient way to create new types that are backed by primitive integers (typically `usize`), providing type safety without compromising performance.

## Features

- Create simple opaque types backed by any integer type
- Generate corresponding atomic types with thread-safe operations
- Zero-cost abstraction - the new types have the same size as their backing types
- Full support for common derives (`Debug`, `Clone`, `Copy`, `Hash`, `Eq`, `PartialEq`, `Ord`, `PartialOrd`)

## Installation

Add this to your `Cargo.toml`:

```toml
[dependencies]
int_like = "0.1.0"
```

## Usage

### Simple Opaque Type

Define a simple type backed by `usize`:

```rust
use int_like::int_like;

/// Define an opaque type `Pid` backed by a `usize`.
int_like!(Pid, usize);

const ZERO: Pid = Pid::from(0);
let pid = Pid::from(42);
assert_eq!(pid.into(), 42);
```

### With Atomic Support

Define both a regular type and its atomic counterpart:

```rust
use int_like::int_like;
use std::sync::atomic::Ordering;

/// Define opaque types `Pid` and `AtomicPid`, backed respectively by a `usize`
/// and an `AtomicUsize`.
int_like!(Pid, AtomicPid, usize, AtomicUsize);

const ZERO: Pid = Pid::from(0);
let ATOMIC_PID: AtomicPid = AtomicPid::default();

// Atomic operations
ATOMIC_PID.store(Pid::from(100), Ordering::SeqCst);
let value = ATOMIC_PID.load(Ordering::SeqCst);
assert_eq!(value, Pid::from(100));
```

### Available Methods

For simple types:
- `from(x: T) -> Self` - Create from the backing type
- `new(x: T) -> Self` - Alternative constructor
- `into() -> T` - Convert back to the backing type
- `data() -> T` - Get the backing value

For atomic types:
- `new(x: T) -> Self` - Create from the non-atomic type
- `default() -> Self` - Create with zero value
- `load(ordering) -> T` - Atomic load
- `store(val, ordering)` - Atomic store
- `swap(val, ordering) -> T` - Atomic swap
- `compare_exchange(current, new, success, failure) -> Result<T, T>` - CAS operation
- `compare_exchange_weak(current, new, success, failure) -> Result<T, T>` - Weak CAS
- `fetch_add(val, ordering) -> T` - Atomic add

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
