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

# Trait objects
* Suppose we have the following trait `Draw`:
  ```rust
  pub trait Draw {
    fn draw(&self);
  }
  ```
* We have two structs `Button` and `SelectBox` implementing `Draw` trait:
  ```rust
  pub struct Button {
    pub label: String,
  }

  impl Draw for Button {
      fn draw(&self) {
          // code to actually draw a button
      }
  }

  struct SelectBox {
    options: Vec<String>,
  }

  impl Draw for SelectBox {
      fn draw(&self) {
          // code to actually draw a select box
      }
  }
  ```
* Now, let's say we want to define a struct `Screen` storing instances of types implementing `Draw` trait in a field called `components`.
  * This way, we can store instances of `Button` and `SelectBox` in `components` field.
  * We can't define `Screen` as follows:
    ```rust
    pub struct Screen<T: Draw> {
      pub components: Vec<T>,
    }
    ```
    because a generic type parameter can only be substituted with one concrete type at a time.
* In order to allow for multiple concrete types, we use *trait objects*.
* **Trait objects** allow for multiple concrete types to fill in for the trait object at runtime.
  * They are defined as `Box<dyn TRAIT_NAME>`, where `TRAIT_NAME` represents name of the trait the concrete types implement.
* So, using trait objects, we can defined `Screen` struct as follows:
  ```rust
  pub struct Screen {
    pub components: Vec<Box<dyn Draw>>,
  }
  ```
* Now, we can store instances of `Button` and `SelectBox` in `components` field of `Screen` struct as follows:
  ```rust
  fn main() {
    let screen = Screen {
        components: vec![
            Box::new(SelectBox {
                options: vec![
                    String::from("Yes"),
                    String::from("Maybe"),
                    String::from("No"),
                ],
            }),
            Box::new(Button {
                label: String::from("OK"),
            }),
        ],
    };

    screen.run();
  }
  ```
* If you try to add a instance of a type which does not implement `Draw` trait to `components` field, you will get a compile-time error.

## Trait Objects Perform Dynamic Dispatch
* The code that results from monomorphization (discussed [here](#performance-of-code-using-generics)) is doing **static dispatch**, which is when the compiler knows what method you’re calling at compile time.
* When we use trait objects, Rust uses *dynamic dispatch*. 
  * A **dynamic dispatch** is when the compiler can’t tell at compile time which method you’re calling. 
  * In dynamic dispatch cases, the compiler emits code that at runtime will figure out which method to call using the pointers inside the trait object. 
  * There is a runtime cost with such lookups that doesn’t occur with static dispatch. 

## Object Safety Is Required for Trait Objects
* You can only make *object-safe* traits into trait objects.
* A trait is **object safe** if all the methods defined in the trait have the following properties:
  * The return type is not `Self`.
    * The `Self` keyword is an alias for the concret type. 
    * If a trait method returns the concrete `Self` type, but a trait object forgets the exact type that `Self` is, there is no way the method can use the original concrete type.
  * There are no generic type parameters.
    * As we know, the generic type parameters are filled in with concrete type parameters when the trait is used.
    * This way, the concrete types become part of the type that implements the trait. 
    * When the type is forgotten through the use of a trait object, there is no way to know what types to fill in the generic type parameters with.
* For example, the following code will result in compile-time error:
  ```rust
  pub trait Clone {
    fn clone(&self) -> Self;
  }

  pub struct Screen {
    pub components: Vec<Box<dyn Clone>>,
  }
  ```
  Error:
  ```
  error[E0038]: the trait `Clone` cannot be made into an object
  ```

## Returning a Closure
* 

