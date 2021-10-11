# Iterators
* The iterator pattern allows you to perform some task on a sequence of items in turn. 
* An **iterator** is responsible for the logic of iterating over each item and determining when the sequence has finished.
* In Rust, iterators are **lazy**, meaning they have no effect until you call methods that consume the iterator to use it up.
* Using an iterator in for loop:
  ```rust
  let v1 = vec![1, 2, 3];

  let v1_iter = v1.iter();

  for val in v1_iter {
      println!("Got: {}", val);
  }
  ```
  * You cannot use `v1_iter` again after `for` loop because the loop takes the ownership of `v1_iter`

## The `Iterator` Trait and the `next` Method
* All iterators implement a trait named Iterator that is defined in the standard library. 
* The definition of the trait looks like this:
  ```rust
  pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations elided
  }
  ```
  * Implementing the `Iterator` trait requires that you also define an `Item` type
  * The `Item` type will be the type returned from the iterator (via `next` method).
* Example of `next` method:
  ```rust
  #[test]
  fn iterator_demonstration() {
      let v1 = vec![1, 2, 3];

      let mut v1_iter = v1.iter();

      assert_eq!(v1_iter.next(), Some(&1));
      assert_eq!(v1_iter.next(), Some(&2));
      assert_eq!(v1_iter.next(), Some(&3));
      assert_eq!(v1_iter.next(), None);
  }
  ```
  * Here, we needed to make `v1_iter` mutable: calling the `next` method on an iterator changes internal state that the iterator uses to keep track of where it is in the sequence. 
  * In other words, this code **consumes**, or uses up, the iterator. 
    * Each call to next eats up an item from the iterator. 
  * We didn’t need to make `v1_iter` mutable when we used a `for` loop because the loop took ownership of `v1_iter` and made it mutable behind the scenes.
* The `iter` method produces an iterator over immutable references.
  * `next` method above returns immutable references to the values stored in `v1` (e.g. `Some(&3)`)
* `into_iter` method takes ownership of `v1` and returns owned values.
* `into_mut` method produces iterator over mutable references.

## Methods that consume iterators
* Methods that call `next` are called **consuming adaptors**, because calling them uses up the iterator. 
* One example is the `sum` method, which takes ownership of the iterator and iterates through the items by repeatedly calling `next`, thus consuming the iterator. 
* As it iterates through, it adds each item to a running total and returns the total when iteration is complete. 
* Example:
  ```rust
  #[test]
  fn iterator_sum() {
      let v1 = vec![1, 2, 3];

      let v1_iter = v1.iter();

      let total: i32 = v1_iter.sum();

      assert_eq!(total, 6);
  }
  ```
  * We aren’t allowed to use `v1_iter` after the call to `sum` because `sum` takes ownership of the iterator we call it on.

## Methods that Produce Other Iterators
* **Iterator adaptors** are methods on `Iterator` trait that allows you to transform current iterator into a new one.
* You can chain multiple calls to iterator adaptors to perform complex actions in a readable way. 
* But because all iterators are lazy, you have to call one of the consuming adaptor methods to get results from calls to iterator adaptors.
* Using just the iterator adaptors would give a warning:
  ```rust
  let v1: Vec<i32> = vec![1, 2, 3];

  v1.iter().map(|x| x + 1);
  ```
  Warning:
  ```
    warning: unused `Map` that must be used
  --> src/main.rs:4:5
    |
  4 |     v1.iter().map(|x| x + 1);
    |     ^^^^^^^^^^^^^^^^^^^^^^^^^
    |
    = note: `#[warn(unused_must_use)]` on by default
    = note: iterators are lazy and do nothing unless consumed
  ```
* Consuming iterator adaptors:
  ```rust
  let v1: Vec<i32> = vec![1, 2, 3];

  let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

  assert_eq!(v2, vec![2, 3, 4]);
  ```
  * Here, `collect` methods consumes the `map` iterator adaptor and creates a new vector `[2, 3, 4]`
  
## Closures and iterator
* Closures capture the environment:
  ```rust
  struct Shoe {
    size: u32,
    style: String,
  }

  fn shoes_in_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
      shoes.into_iter().filter(|s| s.size == shoe_size).collect()
  }
  ```
  * `into_iter` takes ownership of vector `shoes`
  * `filter` adapts iterator into a new vector that allows only those elements for which the closure `|s| s.size == shoe_size` returns true
  * This closure captures `shoe_size` from its environment. 
  * Calling `collect` gathers the values returned by the adapted iterator into a vector that’s returned by the function.

## Creating Our Own Iterators with the `Iterator` Trait
* Struct `Counter` for which we want to create an iterator:
  ```rust
  struct Counter {
    count: u32,
  }

  impl Counter {
      fn new() -> Counter {
          Counter { count: 0 }
      }
  }
  ```
* Implementing `Iterator` for `Counter`:
  ```rust
  impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        if self.count < 5 {
            self.count += 1;
            Some(self.count)
        } else {
            None
        }
    }
  }
  ```
  * We set the associated `Item` type for our iterator to `u32`, meaning the iterator will return `u32` values.
* Test for the above iterator:
  ```rust
  #[test]
  fn calling_next_directly() {
      let mut counter = Counter::new();

      assert_eq!(counter.next(), Some(1));
      assert_eq!(counter.next(), Some(2));
      assert_eq!(counter.next(), Some(3));
      assert_eq!(counter.next(), Some(4));
      assert_eq!(counter.next(), Some(5));
      assert_eq!(counter.next(), None);
      assert_eq!(counter.next(), None);
  }
  ```
  * `next` method starts with `Some(1)` till `Some(5)` and finally `None`
  * Calling `next` method subsequently will still return `None` and not panic.
* We can now use other iterator methods. For example, if you want to 
  * create a pair of counter's current value and next value (e.g. `(1, 2)`), 
  * take a product of the pair numbers (e.g. `1 * 2`), 
  * keep those products which are multiple of 3 and then
  * sum them
  you can use iterators to implement this:
  ```rust
  #[test]
  fn using_other_iterator_trait_methods() {
      let sum: u32 = Counter::new()
          .zip(Counter::new().skip(1))
          .map(|(a, b)| a * b)
          .filter(|x| x % 3 == 0)
          .sum();
      assert_eq!(18, sum);
  }
  ```
  * Note that `zip` returns `None` if either of the pair is `None`, so `(5, None)` is never produced.

## Performance of Iterators
* Iterators, although a high-level abstraction, get compiled down to roughly the same code as if you’d written the lower-level code yourself. 
* Iterators are one of Rust’s zero-cost abstractions, by which we mean using the abstraction imposes no additional runtime overhead.
* In words of Bjarne Stroustrup (creator of C++):
  ```
  In general, C++ implementations obey the zero-overhead principle: What you don’t use, you don’t pay for. And further: What you do use, you couldn’t hand code any better.
  ```
