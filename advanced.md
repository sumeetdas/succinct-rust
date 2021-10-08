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



