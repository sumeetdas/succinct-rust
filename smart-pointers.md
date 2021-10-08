# rust-smart-pointers-in-one-page
Everything about commonly used smart pointers in this one page.

# Smart Pointers
* A **pointer** is a general concept for a variable that contains an address in memory. 
  * This address refers to, or “points at,” some other data.
* The most common kind of pointer in Rust is a reference, which:
  * is indicated by the `&` symbol and borrow the value they point to. 
  * does not have any special capabilities other than referring to data. 
  * does not have any overhead.
* **Smart pointers** are data structures that not only act like a pointer but also have additional metadata and capabilities. 
  * Also found in C++ and other languages
  * Example - **Reference Counting** smart pointer, which enables you to have multiple owners of data by keeping track of the number of owners and, when no owners remain, cleaning up the data.
* References are pointers that only borrow data; in contrast, in many cases, smart pointers **own** the data they point to.
* `String` and `Vec<T>` are also examples of smart pointers because they own some memory and allow you to manipulate it
* Smart pointers are implemented as structs which additionally implement `Deref` and `Drop` traits:
  * The `Deref` trait allows an instance of the smart pointer struct to behave like a reference so you can write code that works with either references or smart pointers. 
  *  The `Drop` trait allows you to customize the code that is run when an instance of the smart pointer goes out of scope.

## Using `Box<T>` to Point to Data on the Heap
* Box is a smart pointer with type as `Box<T>`. 
  * It allows you to store data on the heap rather than the stack. 
  * What remains on the stack is the pointer to the heap data. Pointer type is `usize`. 
* Boxes don’t have performance overhead, other than storing their data on the heap instead of on the stack.
* The `Box<T>` type is a smart pointer because it implements:
  * the `Deref` trait, which allows `Box<T>` values to be treated like references. 
  * the `Drop` trait, which allows the heap data that the `Box<T>` is pointing to get cleaned up when the box value goes out of scope.

### Using a Box<T> to Store Data on the Heap
* Example:
  ```rust
  fn main() {
    let b = Box::new(5);
    println!("b = {}", b);
  }
  ```
  * Variable `b` stores the value of a `Box` that points to the value `5`, which is allocated on the heap. 
  * This program will print `b = 5`; in this case, we can access the data in the box similar to how we would if this data were on the stack. 
  * When a box goes out of scope, as b does at the end of main, it will be deallocated. 
  * The deallocation happens for the box (stored on the stack) and the data it points to (stored on the heap).

### Enabling Recursive Types with Boxes
* At compile time, Rust needs to know how much space a type takes up. 
* One type whose size can’t be known at compile time is a **recursive type**, where a value can have as part of itself another value of the same type. 
* Because this nesting of values could theoretically continue infinitely, Rust doesn’t know how much space a value of a recursive type needs. 
* However, boxes have a known size, so by inserting a box in a recursive type definition, you can have recursive types.
* Linked List is a recursive type:
  ```rust
  enum LinkedList {
    Node(i32, LinkedList),
    Nil
  }
  use crate::LinkedList::{Node, Nil};
  fn main() {
    let list = Node(1, Node(2, Node(3, Nil)));
  }
  ```
  Compiling this code would give an error:
  ```
    error[E0072]: recursive type `List` has infinite size
  --> src/main.rs:1:1
    |
  1 | enum LinkedList {
    | ^^^^^^^^^ recursive type has infinite size
  2 |     Node(i32, LinkedList),
    |               ---- recursive without indirection
    |
  ```
  * Rust can't figure out the space to allocate during compile time because `LinkedList`'s `Node` type contains `LinkedList` and this pattern could go on indefinitely. Hence, the error.
* To implement recursive types, use `Box<T>`:
  ```rust
  enum LinkedList {
    Node(i32, Box<LinkedList>),
    Nil
  }
  use crate::LinkedList::{Node, Nil};
  fn main() {
    let list = Node(1, Box::new(Node(2, Box::new(Node(3, Nil)))));
  }
  ```
  * This works ok because `Node` now contains value (of type `i32`) and a reference to `LinkedList` instance stored on heap.
    * This reference is of type `Box`, which internally stores the pointer data as `usize`.
  * Since Rust can now figure out the size of `Node` at compile time, the code compiles without any error.

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


## Treating Smart Pointers Like Regular References with the `Deref` Trait
* Implementing the `Deref` trait allows you to customize the behavior of the **dereference operator**, `*`.
* This allows you to write code that operates on references and smart pointers alike.

### Following the Pointer to the Value with the Dereference Operator
* Example:
  ```rust
  fn main() {
    let x = 5;
    let y = &x;

    assert_eq!(5, x);
    assert_eq!(5, *y);
  }
  ``` 
  * `y` stores reference to `x`
  * If we want to make an assertion about the value in y, we have to use `*y` to follow the reference to the value it’s pointing to (hence **dereference**).
* Trying to do `assert_eq!(5, y);` would result in following error:
  ```
    error[E0277]: can't compare `{integer}` with `&{integer}`
  --> src/main.rs:6:5
    |
  6 |     assert_eq!(5, y);
    |     ^^^^^^^^^^^^^^^^^ no implementation for `{integer} == &{integer}`
    |
  ```

### Using `Box<T>` Like a Reference
* Example:
  ```rust
  fn main() {
    let x = 5;
    let y = Box::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
  }
  ```
  * `y` contains boxed `x` but can be deferenced (via `*y`) because `Box` implements `Deref` trait.

### Custom type and its `Deref` implementation
* We create the following struct tuple `Number`:
  ```rust
  struct Number<T>(T);

  impl<T> Number<T> {
      fn new(x: T) -> Number<T> {
          Number(x)
      }
  }
  ```
* We now implement `Deref` for `Number`:
  ```rust
  use std::ops::Deref;

  impl<T> Deref for Number<T> {
      type Target = T;

      fn deref(&self) -> &Self::Target {
          &self.0
      }
  }
  ```
  * The `type Target = T;` syntax defines an associated type for the Deref trait to use. 
    * Basically, `Target` defines the type of reference to be returned by `deref` method.
  * Returning `&self.0` would return reference to the value stored in `Number` instance via `*` operator.
* Behind the scene, `*y` evaluates to `*(y.deref())`.
  * `deref` method returns reference to the value.
  * `*` then does a plain dereference of the reference returned by `deref`.
* If the `deref` method returned the value directly instead of a reference to the value, the value would be moved out of `self`, which is not desirable.

### Deref coercions
* **Deref coercion** is a convenience that Rust performs on *arguments* to functions and methods. 
  * It works only on types that implement the `Deref` trait. 
  * It converts types implementing `Deref` into a reference to another type. 
* For example, deref coercion can convert `&String` to `&str` because `String` implements the `Deref` trait such that it returns `str`. 
* Deref coercion happens automatically when there's a mismatch between argument type and the parameter type defined in the function/method. 
  * E.g. calling a function `funk(&name);` where `name` is of type `String` even though `funk` function is defined as 
  `funk(val: &str) { /* code */ }`
* A sequence of calls to the deref method converts the type we provided into the type the parameter needs.
* Example:
  ```rust
  fn hello(name: &str) {
    println!("Hello, {}!", name);
  }

  fn main() {
    let m = Employee::new(String::from("Rust"));
    hello(&m);
  }
  ```
  * Because we implemented `Deref` trait for `Employee`, Rust an turn `&Employee<String>` into `&String`.
    * While implementing `Deref` trait, we set `Target` as `T`. 
    * Since `T` here is `String`, `deref` method returns reference of `String` type, i.e. `&String`
  * Rust calls deref again to turn the `&String` into `&str`, which matches the `hello` function’s definition.
* When the `Deref` trait is defined for the types involved, Rust will analyze the types and use `Deref::deref` as many times as necessary to get a reference to match the parameter’s type. 
* The number of times that `Deref::deref` needs to be inserted is resolved at <u>compile time</u>, so there is no runtime penalty for taking advantage of deref coercion!

### Deref coercions and Mutability
* Similar to how you use the `Deref` trait to override the `*` operator on immutable references, you can use the `DerefMut` trait to override the `*` operator on *mutable references*.
* Rust does deref coercion when it finds types and trait implementations in three cases:
  * From `&T` to `&U` when `T: Deref<Target=U>`
    * It states that if you have a `&T`, and `T` implements `Deref` to some type `U`, you can get a `&U` transparently.
  * From `&mut T` to `&mut U` when `T: DerefMut<Target=U>`
    * It states that the same deref coercion happens for mutable references.
  * From `&mut T` to `&U` when `T: Deref<Target=U>`
    * Rust will also coerce a mutable reference to an immutable one. 
      * Because of the borrowing rules, if you have a mutable reference, that mutable reference must be the only reference to that data (otherwise, the program wouldn’t compile). 
      * Converting one mutable reference to one immutable reference will never break the borrowing rules. 
    * But the reverse is not possible: immutable references will never coerce to mutable references. 
      * Converting an immutable reference to a mutable reference would require that the initial immutable reference is the only immutable reference to that data, but the borrowing rules don’t guarantee that. 
      * Therefore, Rust can’t make the assumption that converting an immutable reference to a mutable reference is possible.

## Running Code on Cleanup with the Drop Trait
* `Drop` trait is the second trait to implement smart pointer pattern.
  * It lets you customize what happens when a value is about to go out of scope.
* You can provide an implementation for the `Drop` trait on any type, and the code you specify can be used to release resources like files or network connections. 
* Rust automatically frees memory when a resource implementing `Drop` trait is no longer in use; no special cleanup code required.
* Example:
  ```rust
  struct CustomSmartPointer {
    data: String,
  }

  impl Drop for CustomSmartPointer {
      fn drop(&mut self) {
          println!("Dropping CustomSmartPointer with data `{}`!", self.data);
      }
  }

  fn main() {
      let c = CustomSmartPointer {
          data: String::from("my stuff"),
      };
      let d = CustomSmartPointer {
          data: String::from("other stuff"),
      };
      println!("CustomSmartPointers created.");
  }
  ```
  Output:
  ```
  CustomSmartPointers created.
  Dropping CustomSmartPointer with data `other stuff`!
  Dropping CustomSmartPointer with data `my stuff`!
  ```
  * The body of the `drop` function is where you would place any logic that you wanted to run when an instance of your type goes out of scope. 
  * Rust automatically called `drop` for us when our instances went out of scope, calling the code we specified. 
  * Variables are dropped in the reverse order of their creation, so `c` was dropped first and then `d`.
* We don't have to worry about problems resulting from accidentally cleaning up values still in use: the ownership system that makes sure references are always valid also *ensures* that drop gets called only once when the value is no longer being used.

### Dropping a Value Early with `std::mem::drop`
* Suppose you want to drop a value manually before end of scope. You can't call `drop` method manually:
  ```rust
  fn main() {
    let c = CustomSmartPointer {
        data: String::from("some data"),
    };
    println!("CustomSmartPointer created.");
    c.drop();
    println!("CustomSmartPointer dropped before the end of main.");
  }
  ```
  Doing so would throw the following error:
  ```
    error[E0040]: explicit use of destructor method
    --> src/main.rs:16:7
    |
  16 |     c.drop();
    |     --^^^^--
    |     | |
    |     | explicit destructor calls not allowed
    |     help: consider using `drop` function: `drop(c)`

  ```
  * This error message states that we’re not allowed to explicitly call `drop`. 
  * The error message uses the term **destructor**, which is the general programming term for a function that cleans up an instance. 
    * The drop function in Rust is one particular destructor.
  * Rust doesn’t let us call `drop` explicitly because Rust would still automatically call `drop` on the value at the end of main. 
    * This would be a **double free** error because Rust would be trying to clean up the same value twice.
* To force a value to be dropped before end of scope, use `std::mem::drop`:
  ```rust
  fn main() {
    let c = CustomSmartPointer {
        data: String::from("some data"),
    };
    println!("CustomSmartPointer created.");
    drop(c);
    println!("CustomSmartPointer dropped before the end of main.");
  }
  ```
  Output:
  ```
  CustomSmartPointer created.
  Dropping CustomSmartPointer with data `some data`!
  CustomSmartPointer dropped before the end of main.
  ```
  * Second message `Dropping CustomSmartPointer..` tells us that `c` was dropped before end of scope of `main` function.
