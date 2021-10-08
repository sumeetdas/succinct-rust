# Advanced Traits

## Supertraits
* When trait `A` requires trait `B` to also be implemented in order to function, then trait `B` is called a **supertrait**.
* Example:
  ```rust
  use std::fmt;

  trait OutlinePrint: fmt::Display {
    fn outline_print(&self) {
        // `to_string` method defined in `Display` trait
        let output = self.to_string();
        let len = output.len();
        println!("{}", "*".repeat(len + 4));
        println!("*{}*", " ".repeat(len + 2));
        println!("* {} *", output);
        println!("*{}*", " ".repeat(len + 2));
        println!("{}", "*".repeat(len + 4));
    }
  }
  ```
* Trying to implement `OutlinePrint` trait for `Point` struct:
  ```rust
  struct Point {
    x: i32,
    y: i32,
  }

  impl OutlinePrint for Point {}
  ```
  will result in the following error:
  ```
  error[E0277]: `Point` doesn't implement `std::fmt::Display`
  ```
  That's correct! `Point` doesn't implement `Display` trait.
* Let's fix the above error by implementing `Display` trait for `Point`:
  ```rust
  use std::fmt;

  impl fmt::Display for Point {
      fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
          write!(f, "({}, {})", self.x, self.y)
      }
  }
  ```
* Now, running `point.outline_print()`, where `point` is an instance of `Point` struct will give the following output:
  ```
  **********
  *        *
  * (1, 3) *
  *        *
  **********
  ```

## Newtype Pattern
* In Rust, in order to implement a trait for a type, either the trait or the type should be local to our crate.
* Using **newtype pattern**, we can implement external traits on external types.
  * This involves creating a new type in a tuple struct.
  * The tuple struct will have one field and be a thin wrapper around the type we want to implement a trait for.
  * Then the wrapper type is local to our crate, and we can implement the trait on the wrapper.
  * **Newtype** is a term that originates from the Haskell programming language.
  * There is no runtime performance penalty for using this pattern
* Example, `Point` struct and `Distance` trait are external to our crate and are defined in `das` crate. We can implement `Distance` trait for `Point` struct as follows:
  ```rust
  // das crate
  pub struct Point {
    x: u32,
    y: u32
  }
  pub trait Distance {
    pub distance_from_origin(&self) -> f64;
  }

  // local crate
  use das::{Point, Distance};
  use std::num::sqrt;

  struct Wrapper(Point);

  impl Distance for Wrapper {
    pub distance_from_origin(&self) -> f64 {
      let sum = (self.0.x * self.0.x) + (self.0.y * self.0.y);
      (sum as f64).sqrt()
    }
  }

  fn main() {
    let wrapped_point = Wrapper(Point{x: 3, y: 4});
    println!("Distance from origin: {}", wrapped_point.distance_from_origin());
  }
  ```
  Output:
  ```
  Distance from origin: 5.0
  ```
* Disadvantage: `Wrapper` is a new type, so it doesn’t have the methods of the value it’s holding (here, `Point`'s methods are absent from `wrapped_point`). 
* For new type to have all methods of the inner type, implement `Deref` trait on the `Wrapper` to return the inner type. (TODO: example code)
* If `Wrapper` type need to have select methods of inner type, we would have to manually implement them.

# Advanced Smart Pointers

## `Rc<T>`, the Reference Counted Smart Pointer
* To enable multiple ownership, Rust has a type called `Rc<T>`, which is an abbreviation for reference counting. 
* The `Rc<T>` type keeps track of the number of references to a value which determines whether or not a value is still in use. 
* If there are zero references to a value, the value can be cleaned up without any references becoming invalid.
* `Rc<T>` is only for use in single-threaded scenarios.
* This could be useful in designing graph data structures, where a single value may have multiple owners:
  * Multiple edges might point to the same node, and that node is conceptually owned by all of the edges that point to it. 
  * A node shouldn’t be cleaned up unless it doesn’t have any edges pointing to it.


## `RefCell<T>` and the Interior Mutability Pattern
* **Interior mutability** is a design pattern in Rust that allows you to mutate data even when there are immutable references to that data; normally, this action is disallowed by the borrowing rules. 
* To mutate data, the pattern uses unsafe code inside a data structure to bend Rust’s usual rules that govern mutation and borrowing.
* We can use types that use the interior mutability pattern when we can ensure that the borrowing rules will be followed at runtime, even though the compiler can’t guarantee that. 
* The unsafe code involved is then wrapped in a safe API, and the outer type is still immutable.
* The `RefCell<T>` type represents single ownership over the data it holds.
* With references and `Box<T>`, the borrowing rules’ invariants are enforced at compile time. 
  * With `RefCell<T>`, these invariants are enforced **at runtime**. 
* With references, if you break the borrowing rules, you’ll get a compiler error. 
  * With `RefCell<T>`, if you break the borrowing rules, your program will panic and exit.
* `RefCell<T>` is only for use in single-threaded scenarios and will give you a compile-time error if you try using it in a multithreaded context. 

### Interior Mutability: A Mutable Borrow to an Immutable Value
* A consequence of the borrowing rules is that when you have an immutable value, you can’t borrow it mutably:
  ```rust
  fn main() {
    let x = 5;
    let y = &mut x;
  }
  ```
  Error:
  ```
    error[E0596]: cannot borrow `x` as mutable, as it is not declared as mutable
  --> src/main.rs:3:13
    |
  2 |     let x = 5;
    |         - help: consider changing this to be mutable: `mut x`
  3 |     let y = &mut x;
    |             ^^^^^^ cannot borrow as mutable
  ```
* However, there are situations in which it would be useful for a value to mutate itself in its methods but appear immutable to other code. 
  * Code outside the value’s methods would not be able to mutate the value. 
  * Using `RefCell<T>` is one way to get the ability to have interior mutability. 
* Example:
  ```rust
  pub trait Messenger {
    fn send(&self, msg: &str);
  }

  #[cfg(test)]
  mod tests {
      use super::*;
      use std::cell::RefCell;

      struct MockMessenger {
          sent_messages: RefCell<Vec<String>>,
      }

      impl MockMessenger {
          fn new() -> MockMessenger {
              MockMessenger {
                  sent_messages: RefCell::new(vec![]),
              }
          }
      }

      impl Messenger for MockMessenger {
          fn send(&self, message: &str) {
              self.sent_messages.borrow_mut().push(String::from(message));
          }
      }

      #[test]
      fn it_sends_an_over_75_percent_warning_message() {
          assert_eq!(mock_messenger.sent_messages.borrow().len(), 1);
      }
  }
  ```
  * In `send` method, the first parameter is an immutable borrow of self, which matches the trait `Messenger` definition. 
  * We call `borrow_mut` on the `RefCell<Vec<String>>` in `self.sent_messages` to get a mutable reference to the value inside the `RefCell<Vec<String>>`, which is the vector. 
  * Then we can call push on the mutable reference to the vector to keep track of the messages sent during the test.
  * In `assert_eq!`, to see how many items are in the inner vector, we call borrow on the `RefCell<Vec<String>>` to get an immutable reference to the vector.
* 


