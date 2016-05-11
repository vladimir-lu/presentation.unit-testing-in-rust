%Unit testing in rust
%Vladimir Lushnikov
%05-May-2016

The basics
----------

* Rust has built-in unit testing support
* Cargo makes running tests easy
* And allows separation of unit + integration tests

What a test looks like
----------------------

```rust
#[test]
fn test_something() {
  assert!(true);
}
```

```
$ cargo test

running 1 test
test test_something ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured
```

Where do tests go?
------------------

* Inside a module alongside code
* Inside a nested module:

```rust
#[cfg(test)]
mod tests {
  #[test]
  #[ignore]
  fn lets_panic() {
    panic!("aaaaaa")
  }

  #[test]
  #[should_panic]
  fn expect_panic() {
    assert_eq!(1, 2)
  }
}
```

Where do tests go? (output)
---------------------------

```
running 2 tests                                                                                                                                                
test tests::lets_panic ... ignored                                                                                                                             
test tests::expect_panic ... ok                                                                                                                                
                                                                                                                                                               
test result: ok. 1 passed; 0 failed; 1 ignored; 0 measured 
```

Where do tests go? (part 2)
---------------------------

* In the `tests/` directory (integration tests)
* Inside this presentation...
* In documentation strings:

```rust

/// Add two to the number given
/// <code>```</code>
/// use adder::add_two;
///
/// assert_eq!(4, add_two(2));
/// <code>```</code>
fn add_two(x: i32) -> i32 {
	x + 2
}
```

Is that it?
-----------

A lot of popular crates are using just the basics covered above.

Is that it? (part 2)
--------------------

But you can also use:

* Custom testing macros
* Libraries

Macros
------

```rust
macro_rules! add_test {
  ($name:ident $expr:expr) => (
    #[test]
    fn $name() {
      // call some setup/teardown
      $expr;
    }
  )
}

add_test!(hello_world {
  assert_eq!(1, 2-1);
});
```

Libraries
---------

* hamcrest
* expectest
* quickcheck
* compiletest_rs
* skeptic

Just a selection!

Hamcrest and Expectest
----------------------

```rust
// Expectest
expect!("hello".to_string()).to(be_equal_to("hello"));
expect!("4".parse::<u32>()).to(be_ok().value(4));

// Hamcrest
assert_that(None, is(none::<int>()));
assert_that(&vec!(1i, 2, 3), contains(vec!(1i, 2)));
```

Quickcheck
----------

```rust
extern crate quickcheck;

use quickcheck::quickcheck;

fn reverse<T: Clone>(xs: &[T]) -> Vec<T> {
    let mut rev = vec!();
    for x in xs.iter() {
        rev.insert(0, x.clone())
    }
    rev
}

fn main() {
    fn prop(xs: Vec<isize>) -> bool {
        xs == reverse(&reverse(&xs))
    }
    quickcheck(prop as fn(Vec<isize>) -> bool);
}
```

What is missing?
---------------

* Mocking
* BDD-style frameworks (e.g. stainless)

Stainless example
-----------------

```rust
  describe! stainless {
    before_each {
        // Start up a test.
        let mut stainless = true;
    }

    it "makes organizing tests easy" {
        // Do the test.
        assert!(stainless);
    }

    after_each {
        // End the test.
        stainless = false;
    }
```


Thank you!
----------

Other things
------------

* https://doc.rust-lang.org/book/testing.html
* AFL - https://github.com/frewsxcv/afl.rs
