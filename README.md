# tokio-retry

Extensible, asynchronous retry behaviours based on [futures](https://crates.io/crates/futures), for the ecosystem of [tokio](https://tokio.rs/) libraries.

[![Build Status](https://travis-ci.org/srijs/rust-tokio-retry.svg?branch=master)](https://travis-ci.org/srijs/rust-tokio-retry)
[![crates](http://meritbadge.herokuapp.com/tokio-retry)](https://crates.io/crates/tokio-retry)
[![dependency status](https://deps.rs/repo/github/srijs/rust-tokio-retry/status.svg)](https://deps.rs/repo/github/srijs/rust-tokio-retry)


[Documentation](https://docs.rs/tokio-retry)

## Installation

Add this to your `Cargo.toml`:

```toml
[dependencies]
tokio-retry = "0.2"
```

## Examples

```rust
extern crate futures;
extern crate tokio;
extern crate tokio_retry;

use futures::Future;
use futures03::compat::Future01CompatExt;
use tokio_retry::Retry;
use tokio_retry::strategy::{ExponentialBackoff, jitter};

fn action() -> Result<u64, ()> {
    // do some real-world stuff here...
    Ok(42)
}

#[tokio::main]
async fn main() {
    let retry_strategy = ExponentialBackoff::from_millis(10)
        .map(jitter)
        .take(3);

    let future = Retry::spawn(retry_strategy, action).map(|result| {
        println!("result {:?}", result);
    });

    future.compat().await;
}
```
