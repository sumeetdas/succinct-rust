# Succinct Rust
This repo contains my notes on Rust programming language.

# Variables
* To define an immutable variable:
  ```rust
  let x = 5;
  ```
* To define a mutable variable, add `mut` after `let`:
  ```rust
  let mut x = 5;
  ```
* As you can see, mentioning data type of variable is optional, as Rust compiler can infer data type from RHS value. 
* If you want to specify data type, you can do so as:
  ```rust
  let x: i32 = 5;
  ```
  * `i32` here is data type for 32-bit integer values.

# Constants
* To define constant:
```rust
const PI:f32 = 3.14;
```
* By convention, constants are named as ALL_CAPS.
* Constants are always immutable, so can't use `mut` with them.
* Constants always require data type annotation (e.g. `PI:f32`).
* Constants can be used in any scope, including global scope.
    * They remain valid in the scope they are defined in.
* Constants can only be assigned constant values, like string or numbers and not some values computed during runtime (like result of some function call)

# Shadowing
* You can reuse same variable name in Rust.
```rust
let x = 5;
let x = 2.0 * 5 as f32;
```
* In the example above, you use the same variable name `x` to create another immutable variable.
* Sometimes, this come as handy, as you don't have to think about creative variable names and re-use existing ones.
* This is not same as adding `mut` to `let` statement, as that won't allow you to assign data of different data type. So, the following would cause an error:
```rust
let mut x = 5;
x = 2.0 * 5 as f32; // error: expected integer, found `f32`
```

# Data Types

## Scalar Types
* A scalar type represents a single value. 
* Rust has four primary scalar types: integers, floating-point numbers, Booleans, and characters.

### Integer Type
* An integer is a number without a fractional component. 
* Example: u32. This type declaration indicates that the value it’s associated with should be an unsigned integer (signed integer types start with i, instead of u) that takes up 32 bits of space.
* | Length |	Signed |	Unsigned |
  |-|-|-|
  | 8-bit |	i8 |	u8 |
  | 16-bit | i16 | u16 |
  | 32-bit | i32 | u32 |
  | 64-bit | i64| u64 |
  |128-bit|	i128|	u128|
  |arch | isize|size |
* The isize and usize types depend on the kind of computer your program is running on: 
  * 64 bits if you’re on a 64-bit architecture and 
  * 32 bits if you’re on a 32-bit architecture.
* | Number literals |	Example |
  |-|-|
  | Decimal|98_222|
  |Hex|	0xff|
  |Octal|	0o77|
  |Binary|	0b1111_0000|
  |Byte (u8 only)|	b'A'|
* All number literals except the byte literal allow a type suffix, such as `57u8`, and _ as a visual separator, such as `1_000`
* Integer types default to `i32`

### Floating-Point Types 
* Two floating types: `f32` and `f64`
* Default is `f64`. E.g. in `let x = 2.0`, `x`'s data type is `f64`.

### Boolean type
* Boolean type has two possible values - `true` or `false`.
* Booleans are one byte in size, and are specified by `bool` data type.

### Character type
* Defined by `char`
* `char` literals are specified using single quotes
* Can save emojis into char variable: `let heart_eyed_cat = '😻';`
* Can save unicode as chars: `let digit_2 = '\u{0032}';`
* Rust’s char type is four bytes in size and represents a Unicode Scalar Value

## Compound Types
* Compound types can group multiple values into one type. 

### Tuple Type
* A tuple is a general way of grouping together a number of values with a **variety of types** into one compound type. 
* Tuples have a fixed length: once declared, they cannot grow or shrink in size.
* Example: `let tup: (i32, f64, u8) = (500, 6.4, 1);`
* Use pattern matching to get individual tuple values:
  ```rust
  let (x, y, z) = tup;
  println!("The value of y is: {}", y);
  ```
* `let (x, y, z) = tup;` is called **destructuring**, because it breakes tuple into multiple parts
* We can access a tuple element directly by using a period (.) followed by the index of the value we want to access. Eg: `let five_hundred = x.0;`

### Array Type
* Unlike a tuple, every element of an array must have the same type.
* Arrays have fixed length.
* Eg: `let a = [1, 2, 3, 4, 5];`
* Arrays are stored on stack
* Specify array type as follows: `let a: [i32; 5] = [1, 2, 3, 4, 5];`
  * `i32, 5` means array elements are of type `i32` and array length is 5
* Create an array of 5 elements, all having same value 3: `let a = [3; 5];`
* Access array elements as: `let first = a[0];`
  
# Functions
* `main` function (stored in `main.rs` file) is the entry point of Rust programs.
* `fn` keyword defines new functions
  ```rust
  fn main() {
    print!("Inside main()");
    da_func();
  }

  fn da_func() {
    print!("Inside da_func()");
  }
  ```
  Output:
  ```
  Inside main()
  Inside da_func()
  ```
* You can define function with parameters as: `fn da_func(x:i32, y:i32) { ...}`. Note that data types for function parameters (like `x` and `y` here) is required.
* You can create functions inside a function:
  ```rust
  fn main() {
    fn factorial(num: i32) -> i32 {
      if num <= 1 {
        1
      }
      else {
        factorial(num - 1) + factorial(num - 2)
      }
    }

    print!("Factorial of 5: {}", factorial(5));
  }
  ```
  * Advantage of creating function instead of closures (called as lambdas in JS, Java and other languages) is that you can call these functions recursively (as `factorial` function above).
  * No performance penalty for using function inside function, as they are [static](https://www.reddit.com/r/rust/comments/22km6a/functions_inside_functions/cgntp3c/?utm_source=reddit&utm_medium=web2x&context=3) (TODO: need more explanation here).

## Expressions and Statements
* In Rust, everything to the RHS of `=` is considered to be an expression.
  * For example, in `let x = 6`, 6 is an expression.
  * Expressions evaluate to something and returns a value, while as statements don't.
  * `let x = 6` is a statement which do not return any value. So, you can't you something like `let y = (let x = 6)`.
* Examples of expressions:
  * Literals (like we have seen above)
  * Function calls (eg. `let x = some_func();`)
  * Blocks if they return a value. Eg:
  ```rust
  let x = 5;
  let y = {
    let x = 3;
    x + 1
  };
  print!("x = {}, y = {}", x, y);
  ``` 
  Output:
  ```
  x = 5, y = 4
  ```
  Notice the following:
    * Block returns a value (`x + 1`). Any statement which does not end with `;` is considered as return expression.
    * We reused variable `x` inside the block and assigned it `3`. This value will shadow the value `5` throughout the block. Once the block is over, `x`'s value becomes `5`.

## Function with return values

* Return types are defined as follows: `fn func() -> i32 {...}`
* In Rust, the return value of the function is synonymous with the value of the final expression in the block of the body of a function.
  ```rust
  fn five() -> i32 {
    5
  }
  ```
  This functions returns the value `5`.

## Function pointers
* You can also pass regular functions to functions, using `fn` type.
* The `fn` type is called a **function pointer**.
* Example:
  ```rust
  fn add_one(x: i32) -> i32 {
    x + 1
  }

  // `do_twice` function accepts a function pointer 
  // `fn(i32) -> i32` as first parameter.
  // This parameter will accept any function which has 
  // `i32` as a single parameter and the return type.
  fn do_twice(f: fn(i32) -> i32, arg: i32) -> i32 {
      f(arg) + f(arg)
  }

  fn main() {
      // `add_one` function is passed as argument to `do_twice`
      let answer = do_twice(add_one, 5);

      println!("The answer is: {}", answer);
  }
  ```
  Output:
  ```
  The answer is: 12
  ```

# Comments
* Comments start with `//`
  ```rust
  // flag for whether to perform search op or not
  let search_flag = false;
  ```
* Comments can added at the end of the line:
  ```rust
  let search_flag = false; // comment
  ```

# Control flow

## if expression
* In Rust, `if` expression can also return values:
  ```rust
  let x = if number > 8 {
    "greater than 8"
  }
  else if some_val == 8 {
    "number is 8"
  }
  else {
    "less than 8"
  }
  ```

## loop expression
* Functional-style loops which will iterate for a certain number of times till it reaches a condition where it will return with some value. Eg: 
  ```rust
  let mut counter = 0;
  let x = loop {
    counter += 1;
    if counter == 10 {
      break 3
    }
  }
  ```
  Here, once `counter` reaches 10, `loop` breaks and returns a value of 3 and gets stored in variable `x`.
* Without `break` expression, loop will go on forever.

## for loop
* To loop through collection, you can use `for .. in` statement:
  ```rust
  let a = [1, 2, 3, 4, 5];
  for elem in a.iter() {
    print!("elem = {}", elem);
  }
  ```
* Another example:
  ```rust
  for a in (1..4).rev() {
    print!("{} ",a);
  }
  ```
  Output:
  ```
  4 3 2 1
  ```
  Here, `1..4` creates an array with values between 1 and 4, and calling `rev` function reverses the order. Then use for loop to iterate through the array.

# Ownership

## Stack and Heap
*  Stack
   *  The stack stores values in the order it gets them and removes the values in the opposite order. 
   *  This is referred to as last in, first out. 
   *  Adding data is called *pushing onto the stack*, and removing data is called *popping off the stack*.
   *  All data stored on the stack must have a known, fixed size. 
*  Heap
   * Data with an unknown size at compile time or a size that might change must be stored on the heap instead.
   * The heap is less organized: when you put data on the heap, you request a certain amount of space. 
   * The memory allocator finds an empty spot in the heap that is big enough, marks it as being in use, and returns a pointer, which is the address of that location. 
   * This process is called allocating on the heap and is sometimes abbreviated as just *allocating*.  
 * Pushing values onto the stack is not considered allocating.
 * Because the pointer is a known, fixed size, you can store the pointer on the stack, but when you want the actual data, you must follow the pointer.
 * Pushing to the stack is faster than allocating on the heap because the allocator never has to search for a place to store new data; that location is always at the top of the stack.
 * Comparatively, allocating space on the heap requires more work, because the allocator must first find a big enough space to hold the data and then perform bookkeeping to prepare for the next allocation.
 * When your code calls a function, the values passed into the function (including, potentially, pointers to data on the heap) and the function’s local variables get pushed onto the stack. 
   * When the function is over, those values get popped off the stack.

## Ownership Rules
* Each value in Rust has a variable that’s called its owner.
* There can only be one owner at a time.
* When the owner goes out of scope, the value will be dropped.

## Variable Scope
* A scope is the range within a program for which an item is valid.
* Usually, the scope for a variable is defined in the pair of braces `{..}` inside of which the variable is defined. 
  * The variable is called **in scope**.
  * Once we exit the brace pair, the variable **goes out of scope** and the variable is no longer valid.
* Eg: 
  ```rust
  {      // s is not valid here, it’s not yet declared
        let s = "hello";   // s is valid from this point forward

        // do stuff with s
    }   // this scope is now over, and s is no longer valid
  ```

## String Type
* String literals are of type `str` and are immutable.
* Strings of type `String` are mutable.
* Convert `str` to `String` via: `let string = String::from("hello");`
* This string can then be mutated: `string.push_str(" world");`
* In the case of string literals (of type `str`):
  * We know the contents at compile time, so the text is hardcoded directly into the final executable. 
  * This is why string literals are fast and efficient. 
  * This is made possible due to immutability of `str` types.
* With the String type, in order to support a mutable, growable piece of text, we need to allocate an amount of memory on the heap, unknown at compile time, to hold the contents.
  * The memory must be requested from the memory allocator at runtime.
    * Calling `String::from` requests the required memory 
  * We need a way of returning this memory to the allocator when we’re done with our String.
    * In languages with a garbage collector (GC), the GC keeps track and cleans up memory that isn’t being used anymore, and we don’t need to think about it.
    * In other system level languages, we need to take care of deallocating memory.
    * In Rust, the memory is automatically returned once the variable that owns it goes out of scope.
    ```rust
    {
        let s = String::from("hello"); // s is valid from this point forward

        // do stuff with s
    }                                  // this scope is now over, and s is no
                                       // longer valid
    ``` 
    * When a variable goes out of scope (like at the end of closing brace above), Rust calls a `drop` function.
    * `drop` function is like *Resource Acquisition Is Initialization (RAII)* in C++, which is a pattern of deallocating resources at the end of an item’s lifetime

## Move
* Consider the following snippet:
  ```rust
  let x = 5;
  let y = x;
  ```
  * Since data stored in x is primitive type of fixed, known size, its copied and then stored in variable y.
* Now, in the following snippet:
  ```rust
  let s1 = String::from("hello");
  let s2 = s1;
  ```
  * Rust doesn't explicitly copies heap data because its expensive as opposed to copying primitive data type like integers.
  * Instead, Rust **moves** heap data from `s1` to `s2`. This would make `s1` invalid. If you try to access `s1` later:
  ```rust
  let s1 = String::from("hello");
  let s2 = s1;

  println!("{}, world!", s1);
  ```
  you would see the following error:
  ```
    error[E0382]: borrow of moved value: `s1`
  --> src/main.rs:5:28
    |
  2 |     let s1 = String::from("hello");
    |         -- move occurs because `s1` has type `String`, which does not implement the `Copy` trait
  3 |     let s2 = s1;
    |              -- value moved here
  4 | 
  5 |     println!("{}, world!", s1);
    |                            ^^ value borrowed here after move
  ```
  * If you notice, Rust moves the heap data because `String` does not implement `Copy` trait (`trait` is like `interface` in Java and C++). 
    * In the case the type implements `Copy`, Rust would copy the data and you would see two different copies of the same data.
    * Which would also imply that you could use `s1` later in the above code.
  * However, since `String` implements `Drop` trait, `s1` gets automatically invalidated when it goes out of scope.

## `Copy`, `Clone` and `Drop` traits
* If a type implements `Drop` trait, then the resource (data stored in a variable) is dropped or deleted once the associated variable goes out of scope.
* If a type implements `Copy` trait,then the resource gets copied to the other variable.
  * When you implement `Copy` for a type:
  ```rust
  #[derive(Copy)]
  struct Person {
    //...
  }
  ```
  then all the fields of the type must implement the `Copy` trait or the code won't compile.
  * `Copy` trait acts as a marker for compiler that says: "you can duplicate myself with a simple bytes copy".
  * Types that implement `Copy` trait:
    * Any scalar value:
      * Integer types like `u32`
      * Boolean type `bool`
      * Floating point types like `f32`
      * Character type, `char`
      * Tuples, only if the contain types that also implement `Copy`.
        * e.g. (i32, i32) implements `Copy`, but (i32, String) do not.
* Copy and Drop traits are mutually exclusive; if you add `Copy` trait to a type, then you can't add `Drop` trait, and vice-versa.
  * Its because resources which implement `Drop` trait could get dropped in the same scope. This won't allow you to use the resource in later part of the scope, even though you might expect otherwise.
  ```rust
  fn drop_copy_type<T>(T x)
  where
      T: Copy + Drop,
  {
      // The inner file descriptor is closed there:
      std::mem::drop(x);
  }

  fn main() {
      let mut file = File::open("foo.txt").unwrap();
      drop_copy_type(file);
      let mut contents = String::new();

      // Oops, this is unsafe!
      // We try to read an already closed file descriptor:
      file.read_to_string(&mut contents).unwrap();
  }
  ```
* If a type implements `Clone` trait, then you can call explicitly `s1.clone()` method to clone data stored in `s1` variable. In this case, you can also implement `Drop` trait to the same type.
  * In other words, `Clone` and `Drop` can co-exist.
  
## Ownership and Functions
* Passing a variable to a function will move or copy, just as assignment does. 
  ```rust
  fn main() {
    let s = String::from("hello");  // s comes into scope

    takes_ownership(s); // s's value moves into the function...
    // ... and so is no longer valid here
    
    let x = 5; // x comes into scope
    
    makes_copy(x);   // x would move into the function,
    // but i32 is Copy, so it's okay to still use x afterward

  } // Here, x goes out of scope, then s. 
  // But because s's value was moved, nothing special happens.

  fn takes_ownership(some_string: String) { 
      // some_string comes into scope

      println!("{}", some_string);
  } // Here, some_string goes out of scope and `drop` is called. 
  // The backing memory is freed.

  fn makes_copy(some_integer: i32) { 
      // some_integer comes into scope

      println!("{}", some_integer);
  } // Here, some_integer goes out of scope. 
  // Nothing special happens.
  ```

## Return Values and Scope
* Returning values can also transfer ownership.
  ```rust
  fn main() {
    let s1 = gives_ownership(); // gives_ownership moves its return value into s1

    let s2 = String::from("hello");  // s2 comes into scope

    let s3 = takes_and_gives_back(s2);  
    // s2 is moved into takes_and_gives_back, which also
    // moves its return value into s3

  } // Here, s3 goes out of scope and is dropped. 
  // s2 goes out of scope but was moved, so nothing happens. 
  // s1 goes out of scope and is dropped.

  fn gives_ownership() -> String {             
    // gives_ownership will move its return value into the 
    // function that calls it

    // some_string comes into scope
    let some_string = String::from("hello"); 

    some_string  // some_string is returned and
    // moves out to the calling function
  }

  // takes_and_gives_back will take a String and return one
  fn takes_and_gives_back(a_string: String) -> String { 
    // a_string comes into scope

    a_string  // a_string is returned and moves out to the
    // calling function
  }
  ```
* The ownership of a variable follows the same pattern every time: assigning a value to another variable **moves** it. 
* When a variable that includes data on the heap goes out of scope, the value will be cleaned up by `drop` *unless* the data has been moved to be owned by another variable.

## References and Borrowing
* Suppose you want to get length of a string. If you pass the variable into a function, then the ownership gets transferred to the function. To avoid this, you can return a tuple of length and the string itself.
  ```rust
  fn main() {
    let str = String::from("hello");
    let (len, str) = length(str);
    print!("len of {} is {}", str, len);
  }
  fn length(s: String) -> (i32, String) {
    (s.len(), s)
  }
  ```
* Instead of returning tuple like above, you could instead pass a `reference` of the variable to the function:
  ```rust
  fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
  }

  fn calculate_length(s: &String) -> usize {
      s.len()
  }
  ```
* References allow you to refer to some value without taking ownership of it.
* The `&s1` syntax lets us create a reference that refers to the value of `s1` but does not own it. 
  * Because it does not own it, the value it points to will not be dropped when the reference goes out of scope.
* The signature of the function uses `&` to indicate that the type of the parameter s is a reference.
  ```rust
  // s is a reference to a String
  fn calculate_length(s: &String) -> usize { 
    s.len()
  } // Here, s goes out of scope. But because it does not have ownership of what it refers to, nothing happens.
  ```
* We call having references as function parameters **borrowing**. 
  * Borrowed values can't be modified. So, modifying the string using reference parameters would cause an error:
  ```rust
  fn change(some_string: &String) {
    some_string.push_str(", world");
  }
  ```
  Error:
  ```
  error[E0596]: cannot borrow `*some_string` as mutable, as it is behind a `&` reference
    |
  7 | fn change(some_string: &String) {
    |                        ------- help: consider changing this to be a mutable reference: `&mut String`
  8 |     some_string.push_str(", world");
    |     ^^^^^^^^^^^ `some_string` is a `&` reference, so the data it refers to cannot be borrowed as mutable
  ```
## Mutable references
* You can make references as mutable by adding `mut` after `&`:
  ```rust
  fn change(some_string: &mut String) {
    some_string.push_str(", world");
  }
  ```
* You can have **only one mutable reference** to a particular piece of data <u>in a particular scope</u>. So, this will throw an error:
  ```rust
  let mut s = String::from("hello");

  let r1 = &mut s;
  let r2 = &mut s;

  println!("{}, {}", r1, r2);
  ```
  Error:
  ```
  error[E0499]: cannot borrow `s` as mutable more than once at a time
    |
  4 |     let r1 = &mut s;
    |              ------ first mutable borrow occurs here
  5 |     let r2 = &mut s;
    |              ^^^^^^ second mutable borrow occurs here
  6 | 
  7 |     println!("{}, {}", r1, r2);
    |                        -- first borrow later used here

  ```
  * Benefits of this *one mutable reference in a scope* restriction:
    * Rust can prevent data races at compile time.
    * A data race is similar to a race condition and happens when these three behaviors occur:
      * Two or more pointers access the same data at the same time.
      * At least one of the pointers is being used to write to the data.
      * There’s no mechanism being used to synchronize access to the data.
* You can create new mutable reference inside a pair of curly braces, but you still can't have two simulatenous mutable references in the same block:
  ```rust
  let mut s = String::from("hello");
  {
      let r1 = &mut s;
  } // r1 goes out of scope here, so we can make a new reference with no problems.

  let r2 = &mut s;
  ```
* You cannot create a mutable reference if you have one or more immutable references in the same scope, *and* use that immutable reference later:
  ```rust
  let mut s = String::from("hello");

  let r1 = &s; // no problem
  let r2 = &s; // no problem
  let r3 = &mut s; // BIG PROBLEM

  // immutable reference r1 and r2 used after creating
  // mutable reference r3
  println!("{}, {}, and {}", r1, r2, r3);
  ```
  Error:
  ```
    error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
  --> src/main.rs:6:14
    |
  4 |     let r1 = &s; // no problem
    |              -- immutable borrow occurs here
  5 |     let r2 = &s; // no problem
  6 |     let r3 = &mut s; // BIG PROBLEM
    |              ^^^^^^ mutable borrow occurs here
  7 | 
  8 |     println!("{}, {}, and {}", r1, r2, r3);
    |                       -- immutable borrow later used here
  ```
  * You can use a mutable reference along with immutable references only when you no longer reference immutable references in the rest of the scope:
  ```rust
  let mut s = String::from("hello");

  let r1 = &s; // no problem
  let r2 = &s; // no problem
  println!("{} and {}", r1, r2);
  // r1 and r2 are no longer used after this point

  let r3 = &mut s; // no problem
  println!("{}", r3);
  ```
  * This works because a reference’s scope starts from where it is introduced and continues through the last time that reference is used.

## Dangling References
* In languages with pointers, it’s easy to erroneously create a **dangling pointer**, a pointer that references a location in memory that may have been given to someone else by freeing some memory. 
* In Rust, by contrast, the compiler guarantees that references will never be dangling references.
  * If you have a reference to some data, the compiler will ensure that the data will not go out of scope before the reference to the data does.
* Example:
  ```rust
  fn main() {
    let reference_to_nothing = dangle();
  }

  fn dangle() -> &String {
      let s = String::from("hello");

      &s
  }
  ```
  Error thrown by compiler:
  ```
    |
  5 | fn dangle() -> &String {
    |                ^ expected named lifetime parameter
    |
    = help: this function's return type contains a borrowed value, but there is no value for it to be borrowed from
  help: consider using the `'static` lifetime
  ```
  * Relevant error message: `this function's return type contains a borrowed value, but there is no value for it to be borrowed from`
  * Here's `dangle` function with comments to explain the above error message:
  ```rust
  fn dangle() -> &String { // dangle returns a reference to a String

    let s = String::from("hello"); // s is a new String

    &s // we return a reference to the String, s
  } // Here, s goes out of scope, and is dropped. 
  // Its memory goes away. Danger!
  ```
  * As you can see, since the variable owning the `String` is dropped at the end of the function, `&s` would create a reference to invalid memory location.
    * Rust stops you from creating such references by throwing compile-time error with the above error message.

# The Slice Type
* The **slice** data type let you reference a contiguous sequence of elements in a collection rather than the whole collection.
  * You can't have ownership of this data type.
* A string slice is a reference to part of a String:
  ```rust
  let s = String::from("hello world");

  let hello = &s[0..5];
  let world = &s[6..11];
  ```
* Slice is defined as `[starting_index..ending_index]`:
  * `starting_index` is inclusive
  * `ending_index` is exclusive.
  * e.g. `&s[0..5]` above will result in "hello" (index 0 to index 4, 5 is excluded)
* Example of valid slice:
  ```rust
  let slice = &s[..2]; // 0 to 2
  let slice = &s[2..]; // 2 to len - 1
  let slice = &s[..]; // 0 to len - 1
  ```
* In memory, slice is stored as the reference of the starting point of the slice and number of elements in it.
  * For example, `&s[2..4]` slice is stored as reference to index 2 of String `s` and number of elements (2);
* String slice range indices must occur at valid UTF-8 character boundaries.
  * If not, then the program will panic (Rust's way of throwing unhandled runtime error)
* Suppose we want to return the first word in a string. If no space in string, then return the entire string. Using slice, we can do as follows:
  ```rust
  fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    // iter() returns each element.
    // enumerate() transforms each element into a tuple of
    // (index, reference of element)
    for (i, &item) in bytes.iter().enumerate() {
        // b' ' converts char ' ' into its byte equivalent
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
  }

  fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    println!("the first word is: {}", word);
  }
  ```
* Now, in the above `main` function, if you were to clear `s` you would run into error.
  ```rust
  fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // error!

    println!("the first word is: {}", word);
  }
  ```
  Error:
  ```
    error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
    --> src/main.rs:18:5
     |
  16 |     let word = first_word(&s);
     |                     -- immutable borrow occurs here
  17 | 
  18 |     s.clear(); // error!
     |     ^^^^^^^^^ mutable borrow occurs here
  19 | 
  20 |     println!("the first word is: {}", word);
     |                   ---- immutable borrow later used here

  ```
  * The error occurs because `word` contains immutable reference to the string and while its still in use, we call `s.clear()` which does mutable borrow.
  * If `first_word` returned the end index of the first word, then `word` would still contain the index even after `s` becomes empty, and that would create another bug (since we are trying to get 0 to non-zero index string out of an empty string)
* The above `first_word`'s signature could be written as `fn first_word(s: &str) -> &str`. This way, `first_word` function can accept both `&String` and `&str`.
* You can send slice of string `s` as arguments to `first_word`:
  ```rust
  fn main() {
    // String type
    let my_string = String::from("hello world");

    // first_word works on slices of `String`s
    let word = first_word(&my_string[..]);

    // str type 
    let my_string_literal = "hello world"; 

    // first_word works on slices of string literals
    let word = first_word(&my_string_literal[..]);

    // Because string literals *are* string slices already,
    // this works too, without the slice syntax!
    let word = first_word(my_string_literal);
  }
  ```
* Array slices are also valid:
  ```rust
  let a = [1, 2, 3, 4, 5];

  let slice = &a[1..3];

  assert_eq!(slice, &[2, 3]);
  ```
  * This `slice` has the type &[i32]. 
  * It works the same way as string slices do, by storing a reference to the first element and a length.

# Structs
* Structs are like class in Java and C++
* Example:
  ```rust
  struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
  }
  ```
  * Notice that comma at the end of `active` property? Yeah, that's allowed in Rust. In Java enums, that extra comma would have thrown a compilation error.
* Like tuple, you can have multiple data types. Unlike tuples, you can name individual data.
* Create an instance of struct `User`:
  ```rust
  let user1 = User {
      email: String::from("someone@example.com"),
      username: String::from("someusername123"),
      active: true,
      sign_in_count: 1,
  };
  ```
  * This would create an immutable reference of `User` instance.
* To create mutable reference, add `mut`:
  ```rust
  let mut user1 = User {
      email: String::from("someone@example.com"),
      username: String::from("someusername123"),
      active: true,
      sign_in_count: 1,
  };

  user1.email = String::from("anotheremail@example.com");
  ```
  * Note that the entire instance must be mutable; Rust doesn’t allow us to mark only certain fields as mutable.
* No default constructors in Rust for struct types. By convention, you can create functions called `new` that would return instance of the struct.
* Struct reference example:
  ```rust
  struct Rectangle {
    width: u32,
    height: u32,
  }

  fn main() {
      let rect1 = Rectangle {
          width: 30,
          height: 50,
      };

      println!(
          "The area of the rectangle is {} square pixels.",
          area(&rect1)
      );
  }

  fn area(rectangle: &Rectangle) -> u32 {
      rectangle.width * rectangle.height
  }
  ```
## Field Init Shorthand
  ```rust
  fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
  }
  ```
  * When Variable and Fields have same name, you can use just the property name as above.
## Struct Update Syntax
  ```rust
  let user2 = User {
      email: String::from("another@example.com"),
      username: String::from("anotherusername567"),
      ..user1
  };
  ```
  * Rest of the values of `user2` would be used from `user1`.

## Tuple Structs
  ```rust
  struct Color(i32, i32, i32);
  struct Point(i32, i32, i32);

  let black = Color(0, 0, 0);
  let origin = Point(0, 0, 0);
  ```
  * Useful when you want to name a tuple, and naming each field would be verbose or redundant.
  * Example:
  ```rust
  struct Dimension(u32, u32);

  fn main() {
    let rect1 = Dimension(30, 50);

    println!(
        "The area of the rectangle is {} square pixels.",
        area(rect1)
    );
  }

  fn area(dimensions: Dimension) -> u32 {
      dimensions.0 * dimensions.1
  }
  ```   

## Unit-Like Structs
  *  You can define structs that don’t have any fields.
  *  These are called unit-like structs because they behave similarly to (), the unit type. 
  *  Useful when you need to implement a trait but don’t have any data you want to store in it.

## Debug structs
* When you try to print a struct as below:
  ```rust
  struct Rectangle {
    width: u32,
    height: u32,
  }

  fn main() {
      let rect1 = Rectangle {
          width: 30,
          height: 50,
      };

      println!("rect1 is {}", rect1);
  }
  ```
  the program throws an error:
  ```
  error[E0277]: `Rectangle` doesn't implement `std::fmt::Display`
  ```
  * All primitive types implement `Display` trait because they can be shown in just one way. Structs do not.
* To print struct for debugging, you need to explicity use derived traits as `Debug` on a struct as follows:
  ```rust
  #[derive(Debug)]
  struct Rectangle {
      width: u32,
      height: u32,
  }

  fn main() {
      let rect1 = Rectangle {
          width: 30,
          height: 50,
      };

      println!("rect1 is {:?}", rect1);
  }
  ```
  Output: 
  ```
  rect1 is Rectangle { width: 30, height: 50 }
  ```
  * To pretty print struct, use `{:#?}` instead in `println!` macro.
    * Output: 
      ```
      rect1 is Rectangle {
          width: 30,
          height: 50,
      }
      ```

## Methods
* Methods are similar to functions, but are defined in structs and the first parameter is always 
  * `self` (take ownership) or 
  * `&self` (immutable borrow) or 
  * `&mut self` (mutable borrow).
* Example:
  ```rust
  #[derive(Debug)]
  struct Rectangle {
      width: u32,
      height: u32,
  }

  impl Rectangle {
      fn area(&self) -> u32 {
          self.width * self.height
      }
  }

  fn main() {
      let rect1 = Rectangle {
          width: 30,
          height: 50,
      };

      println!(
          "The area of the rectangle is {} square pixels.",
          rect1.area()
      );
  }
  ```
  * Methods are defined in `impl` (implementation) blocks for a given struct.
* Methods with paramters:
  * Example: You want to check whether one rectangle can hold the other
    ```rust
    impl Rectangle {
        fn can_hold(&self, other: &Rectangle) -> bool {
            self.width > other.width && self.height > other.height
        }
    }

    fn main() {
      let rect1 = Rectangle {
        width: 30,
        height: 50,
      };
      let rect2 = Rectangle {
          width: 10,
          height: 40,
      };
      println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    }
    ```
* A struct can have multiple `impl` blocks:
  ```rust
  impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
  }

  impl Rectangle {
      fn can_hold(&self, other: &Rectangle) -> bool {
          self.width > other.width && self.height > other.height
      }
  }
  ```

## Automatic referencing and dereferencing
  * When you call a method with `object.something()`, Rust automatically adds in `&`, `&mut`, or `*` so object matches the signature of the method. 
  * In other words, the following are the same:
    ```rust
    p1.distance(&p2);
    (&p1).distance(&p2);
    ```
  * This automatic referencing behavior works because methods have a clear receiver—the type of `self`. 
  * Given the receiver and name of a method, Rust can figure out definitively whether the method is reading (`&self`), mutating (`&mut self`), or consuming (`self`). 

## Associated functions
* These functions are part of structs but don't have `self` or its variants as first parameters.
* These are like static methods in Java.
* These are called **associated functions** because they’re associated with the struct. 
* They’re still functions, not methods, because they don’t have an instance of the struct to work with. 
* Associated functions are often used for constructors that will return a new instance of the struct. Example:
  ```rust
  impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
  }
  fn main() {
    let square = Rectangle::square(3);
  }
  ```
  * To call a associated function, we use the `::` syntax with the struct name.

# Enums and Pattern Matching
* Rust’s enums are most similar to **algebraic data types** in functional languages, such as F#, OCaml, and Haskell. 
  * Algebraic data types are made up of sum and product types (hence the word *algebraic*).
  * Product types
    * These are data types which contain one or more data of different types *at the same time*. 
    * Rust's tuple is one such example. Tuple allows data of different types to exist at the same time in one tuple instance.
  * Sum types
    * These types can be thought of union of different data types.
    * Example: `type c = a | b` in languages like OCaml allows us to identify instance of `a` or `b` as instance of `c`.
    * No equivalent type in Java AFAIK. This type is supported in Rust in the form of enums.
* Example of enum:
  ```rust
  enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
  }

  impl Message {
    fn call(&self) {
      //..
    }
  }

  fn main() {
    let m = Message::Write(String::from("hello"));
    m.call();
  }
  ```
  * In this example, enum `Message` is a sum type of the following types: `Quit`, `Move`, `Write`, `ChangeColor`.
    * Its like creating different structs for each of the above four types.
  * You can add methods to an enum via `impl` blocks (same as in structs)
  * Access enum types via `::` operator (since enum types are like associated functions in structs; enum `Message` serves as namespace for these four enum types).

## `Option` Enum
* Null values are PITA to deal with (myself coming from Java background, so I know).
* Rust has no concept of null values. Instead, we use `Option` enum:
  ```rust
  enum Option<T> {
    Some(T),
    None,
  }
  ```
  * T is like generic parameters in Java. T could be replaced by any type.
* `Option` enum is available in Rust programs by default (included in the prelude), so don't have to import it.
* Examples of using `Option` enum:
  ```rust
  let some_number = Some(5);
  let some_string = Some("a string");

  let absent_number: Option<i32> = None;
  ```
  * Notice that when assigning `None` to a variable, you need to specify the variable's data type. Rust couldn't have inferred the type `T` in `Option` enum otherwise.
* Why `Option` is better than `null`:
  ```rust
  let x: i8 = 5;
  let y: Option<i8> = Some(5);

  let sum = x + y;
  ```
  * In Rust, this would throw a <u>compilation error</u>, as `x` and `y` are of two different types (`x` is `i8` and `y` is `Option<i8>`)
  * In Java, similar scenario involving null values would be allowed during compile time only to see an error during runtime. Try this code out:
  ```java
  public static void main(String[] args) {
          Integer x = 0;
          Integer y = null;
          Integer z = x + y;
  }
  ```

## `match` control flow
* You can do pattern matching on enum types via `match` operator.
  * Somewhat similar to switch/case in Java, but more powerful as it can match types
* Example:
  ```rust
  enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
  }

  fn value_in_cents(coin: Coin) -> u8 {
      match coin {
          Coin::Penny => 1,
          Coin::Nickel => {
            println!("coin type is Nickel");
            5
          },
          Coin::Dime => 10,
          Coin::Quarter => 25,
      }
  }
  ```
  * This will match different enum types and then return value mapped to a given type. For example, if coin is of type `Penny`, `value_in_cents` will return 1.
  * Each of the lines in `match` block like `Coin::Penny => 1` is called a **match arm**.
    * `Coin::Penny` is called pattern
    * 1 is some code you need to execute or return (as in this case)
  * You could enclose multiple lines of code inside curly braces in a match arm, as done in case of `Coin::Nickel` above.
* `Option` matching example:
  ```rust
  fn plus_one(x: Option<i32>) -> Option<i32> {
      match x {
          None => None,
          Some(i) => Some(i + 1),
      }
  }

  let five = Some(5);
  let six = plus_one(five);
  let none = plus_one(None);
  ```
* Matches are **exhaustive**. You must include all possible values of the enum type or else the code won't compile.
  * The following code will throw an error:
    ```rust
    fn plus_one(x: Option<i32>) -> Option<i32> {
        match x {
            Some(i) => Some(i + 1),
        }
    }
    ```
    Error
    ```
    error[E0004]: non-exhaustive patterns: `None` not covered
    --> src/main.rs:3:15
        |
    3   |         match x {
        |               ^ pattern `None` not covered
        |
        = help: ensure that all possible cases are being handled, possibly by adding wildcards or more match arms
        = note: the matched value is of type `Option<i32>`
    ```
* In case if you don't want to add all possible enum types, you could instead use special pattern `_`:
  ```rust
  let num_to_str = match input_num {
      1 => "one",
      3 => "three",
      5 => "five",
      7 => "seven",
      _ => "default",
  };
  ``` 
  * For `input_num` values not equal to either 1, 3, 5, or 7, the `match` expression would return `"default"` and gets stored in `num_to_str` variable.

### Matching named variables
* Example:
  ```rust
  let x = Some(5);
  let y = 10;

  match x {
      Some(50) => println!("Got 50"),
      Some(y) => println!("Matched, y = {:?}", y),
      _ => println!("Default case, x = {:?}", x),
  }

  println!("at the end: x = {:?}, y = {:?}", x, y);
  ```
  Output:
  ```
  Matched, y = 5
  at the end: x = Some(5), y = 10
  ```
  * `y` gets shadowed inside `match` arm `Some(y)` and `y` value becomes 5.
  * If `x` value was `None`, the output would have been:
    ```
    Default case, x = None
    at the end: x = None, y = 10
    ```

### Multiple patterns in `match`
* Example:
  ```rust
  let x = 1;

  match x {
      1 | 2 => println!("one or two"),
      3 => println!("three"),
      _ => println!("anything"),
  }
  ```
  Output:
  ```
  one or two
  ```

### Matching Ranges of Values with `..=`
* Example with numeric range:
  ```rust
  let x = 5;

  match x {
      1..=5 => println!("one through five"),
      _ => println!("something else"),
  }
  ```
  If `x` is 1, 2, 3, 4, or 5, the first arm will match.
* Example with `char` range:
  ```rust
  let x = 'c';

  match x {
      'a'..='j' => println!("early ASCII letter"),
      'k'..='z' => println!("late ASCII letter"),
      _ => println!("something else"),
  }
  ```
  Output:
  ```
  early ASCII letter
  ```

### Destructuring structs
* Example:
  ```rust
  struct Point {
    x: i32,
    y: i32,
  }

  fn main() {
      let p = Point { x: 0, y: 7 };

      let Point { x, y } = p;
      assert_eq!(0, x);
      assert_eq!(7, y);
  }
  ```
* You could also create new variables when destructing structs:
  ```rust
  fn main() {
    let p = Point { x: 0, y: 7 };

    let Point { x: a, y: b } = p;
    assert_eq!(0, a);
    assert_eq!(7, b);
  }
  ```
  Here, two new variables `a` and `b` are created that match the values `x` and `y`.
* Destructuring structs in `match`:
  ```rust
  fn main() {
    let p = Point { x: 0, y: 7 };

    match p {
        Point { x, y: 0 } => println!("On the x axis at {}", x),
        Point { x: 0, y } => println!("On the y axis at {}", y),
        Point { x, y } => println!("On neither axis: ({}, {})", x, y),
    }
  }
  ```
  Output:
  ```
  On the y axis at 7
  ```

### Destructuring enums
* Example:
  ```rust
  enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
  }

  fn main() {
      let msg = Message::ChangeColor(0, 160, 255);

      match msg {
          Message::Quit => {
              println!("The Quit variant has no data to destructure.")
          }
          Message::Move { x, y } => {
              println!(
                  "Move in the x direction {} and in the y direction {}",
                  x, y
              );
          }
          Message::Write(text) => println!("Text message: {}", text),
          Message::ChangeColor(r, g, b) => println!(
              "Change the color to red {}, green {}, and blue {}",
              r, g, b
          ),
      }
  }
  ```
  Output:
  ```
  Change the color to red 0, green 160, and blue 255
  ```

## Destructuring Structs and Tuples
* Example:
  ```rust
  let ((feet, inches), Point { x, y }) = ((3, 10), Point { x: 3, y: -10 });
  ```
  This declares and assigns the following variables: `feet` = 3, `inches` = 10, `x` = 3, `y` = -10.

## Ignoring an Entire Value with `_`
* Example:
  ```rust
  fn foo(_: i32, y: i32) {
    println!("This code only uses the y parameter: {}", y);
  }

  fn main() {
      foo(3, 4);
  }
  ```
  Output:
  ```
  This code only uses the y parameter: 4
  ```
* This is useful in cases when implementing a trait when you need a certain type signature but the function body in your implementation doesn’t need one of the parameters.
  * The compiler will then not warn about unused function parameters, as it would if you used a name instead.

## Ignoring Parts of a Value with a Nested `_`
* Example:
  ```rust
  let numbers = (2, 4, 8, 16, 32);

  match numbers {
      (first, _, third, _, fifth) => {
          println!("Some numbers: {}, {}, {}", first, third, fifth)
      }
  }
  ```
  Output:
  ```
  Some numbers: 2, 8, 32
  ```

## Ignoring an Unused Variable by Starting Its Name with `_`
* Example:
  ```rust
  fn main() {
    let _x = 5;
    let y = 10;
  }
  ```
  * This will give us compile-time warning about not using `y`, but we don't get any warning for `_x`.
* There's a difference between `_x` and `_`; `let _x = value` still binds `value` to the variable `_x`. For example:
  ```rust
  let s = Some(String::from("Hello!"));

  if let Some(_s) = s {
      println!("found a string");
  }

  println!("{:?}", s);
  ```
  would result in compile-time error as value `String::from("Hello!")` would move to `_s`, thereby making `s` variable after `if let` expression invalid.
  * If you used `Some(_)` instead, the code will compile just fine.

## Ignoring Remaining Parts of a Value with `..`
* Example:
  ```rust
  struct Point {
      x: i32,
      y: i32,
      z: i32,
  }

  let origin = Point { x: 3, y: 0, z: 0 };

  match origin {
      Point { x, .. } => println!("x is {}", x),
  }
  ```
  Output:
  ```
  x is 3
  ```
* Another example:
  ```rust
  fn main() {
    let numbers = (2, 4, 8, 16, 32);

    match numbers {
        (first, .., last) => {
            println!("first: {}, last: {}", first, last);
        }
    }
  }
  ```
  Output:
  ```
  first: 2, last: 32
  ```
* Using `..` must be unambiguous. The following code will result in compile error:
  ```rust
  fn main() {
    let numbers = (2, 4, 8, 16, 32);

    match numbers {
        (.., second, ..) => {
            println!("Some numbers: {}", second)
        },
    }
  }
  ```
  Error:
  ```
    error: `..` can only be used once per tuple pattern
  --> src/main.rs:5:22
    |
  5 |         (.., second, ..) => {
    |          --          ^^ can only be used once per tuple pattern
    |          |
    |          previously used here
  ```

## Extra Conditionals with Match Guards
* A **match guard** is an additional `if` condition specified after the pattern in a match arm that must also match, along with the pattern matching, for that arm to be chosen.
* Example:
  ```rust
  let num = Some(4);

  match num {
      Some(x) if x < 5 => println!("less than five: {}", x),
      Some(x) => println!("{}", x),
      None => (),
  }
  ```
  Output:
  ```
  less than five: 4
  ```
* Another example:
  ```rust
  fn main() {
    let x = Some(5);
    let y = 5;

    match x {
        Some(50) => println!("Got 50"),
        Some(n) if n == y => println!("Matched, n = {}", n),
        _ => println!("Default case, x = {:?}", x),
    }

    println!("at the end: x = {:?}, y = {}", x, y);
  }
  ```
  Output:
  ```
  Matched, n = 5
  at the end: x = Some(5), y = 5
  ```
  * `Some(n) if n == y` `match` arm doesn't introduce a new variable `y` inside `match` scope, and thus can use outer `y` as match guard.
* Match guard precedence example:
  ```rust
  let x = 4;
  let y = false;

  match x {
      4 | 5 | 6 if y => println!("yes"),
      _ => println!("no"),
  }
  ```
  * The precedence is `4 | 5 | 6`, followed by the match guard `if y`.

## `@` bindings
* To capture matched values in a variable, use `@` bindigs:
  ```rust
  enum Message {
      Hello { id: i32 },
  }

  let msg = Message::Hello { id: 5 };

  match msg {
      Message::Hello {
          id: id_variable @ 3..=7,
      } => println!("Found an id in range: {}", id_variable),
      Message::Hello { id: 10..=12 } => {
          println!("Found an id in another range")
      }
      Message::Hello { id } => println!("Found some other id: {}", id),
  }
  ```
  * In the above code, for some reason Rust does not store the matched value `5` inside `id` variable when it checks whether the value of `id` field after destructuring `msg` falls in `3..=7` range.
  * To store the value matched in a given arm, use `@` binding: `id: id_variable @ 3..=7`
  * `id_variable @` tells Rust to create a new variable named `id_variable` storing the matched value (here `5`).

## Patterns that Bind to Values
* Match arms can bind to the parts of the values that match the pattern.
* Example:
  ```rust
  #[derive(Debug)] 
  enum UsState {
    Alabama,
    Alaska,
    // --snip--
  }

  enum Coin {
      Penny,
      Nickel,
      Dime,
      Quarter(UsState),
  }

  fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        }
    }
  }
  ```
  * If we were to call `value_in_cents(Coin::Quarter(UsState::Alaska))`, coin would be `Coin::Quarter(UsState::Alaska)`. 
  * When we compare that value with each of the match arms, none of them match until we reach `Coin::Quarter(state)`. 
  * At that point, the binding for `state` will be the value `UsState::Alaska`. 

## `if let` syntax
* If you want to execute some code just for one match arm and ignore the rest, then you can use `if let` syntax.
* Example: You have a match expression like this:
  ```rust
  let num_to_str = match input_num {
      1 => "one",
      3 => "three",
      5 => "five",
      7 => "seven",
      _ => "default",
  };
  ```
  * If you just want to deal with pattern `7` and ignore the rest, you could use `if let`:
    ```rust
    let num_to_str = if let 7 = input_num {
      "seven"
    }
    else {
      "default"
    }
    ```
* Another example:
  ```rust
  fn func(favorite_color: Option<&str>, is_tuesday: bool,
  age: Result<u8, _>) {
    if let Some(color) = favorite_color {
        println!("Using your favorite color, {}, as the background", color);
    } else if let Ok(age) = age {
        if age > 30 {
            println!("Using purple as the background color");
        } else {
            println!("Using orange as the background color");
        }
    } else {
        println!("Using blue as the background color");
    }
  }
  ```
  * If `favorite_color` is `Some("red")`, then the output would be `Using your favorite color, red, as the background`.
  * If `favorite_color` is `None` and `age` is `Ok(34)`, then the output would be `Using orange as the background color`
  * Else the output would be `Using blue as the background color`.

## `while let` expression
* Example:
  ```rust
  let mut stack = Vec::new();

  stack.push(1);
  stack.push(2);
  stack.push(3);

  while let Some(top) = stack.pop() {
      println!("{}", top);
  }
  ```
  Output:
  ```
  3
  2
  1
  ```
  * The loop will run while `stack.pop()` returns `Some(val)`.
  * The loop stops when `stack.pop()` returns `None`.

## Pattern matching in `for` loop
* Example:
  ```rust
  let v = vec!['a', 'b', 'c'];

  for (index, value) in v.iter().enumerate() {
      println!("{} is at index {}", value, index);
  }
  ```
  Output:
  ```
  a is at index 0
  b is at index 1
  c is at index 2
  ```

## Pattern matching in `let` statements
* Example:
  ```rust
  let (x, y, z) = (1, 2, 3);
  ```
  This would assign x = 1, y = 2, z = 3.
* Following would result in error:
  ```rust
  let (x, y) = (1, 2, 3);
  ```
  Output:
  ```
  error[E0308]: mismatched types
  ```

## Pattern matching in Function Parameters
* Example:
  ```rust
  fn print_coordinates(&(x, y): &(i32, i32)) {
    println!("Current location: ({}, {})", x, y);
  }

  fn main() {
      let point = (3, 5);
      print_coordinates(&point);
  }
  ```
  Output:
  ```
  Current location: (3, 5)
  ```

## Pattern refutability
* Patterns come in two forms: refutable and irrefutable.
* Patterns that will match for any possible value passed are **irrefutable**.
  * Example: `x` in statement `let x = 5`, because `x` matches anything and therefore *cannot* fail to match.
* Patterns that can fail to match for some possible value are **refutable**.
  * Example: `Some(x)` in `if let Some(x) = a_value` expression, because if the value of `a_value` is `None`, then the `Some(x)` pattern won't match.
* Function parameters, `let` statements, and `for` loops can only accept irrefutable patterns, because the program cannot do anything meaningful when values don’t match.
  * So, the following code won't compile:
    ```rust
    let Some(x) = some_option_value;
    ```
    as `Some(x) = ..` is a refutable pattern and `let` requires irrefutable patterns. So, `let` requires that `None = ` pattern to also be covered.
  * Alternative, use `match` with `let` for above:
    ```rust
    let x = match {
      Some(val) => val,
      None => DEFAULT_VALUE
    };
    ```
    
* The `if let` and `while let` expressions accept refutable and irrefutable patterns, but the compiler warns against irrefutable patterns.
  * This is because they are conditionals, so by definition they’re intended to handle possible failure.
  * Example:
    ```rust
    if let Some(x) = some_option_value {
        println!("{}", x);
    }
    ```
  * You could do something like:
    ```rust
    if let x = 5 {
        println!("{}", x);
    };
    ```
    but compiler would give you a warning:
    ```
    warning: irrefutable `if let` pattern
    ```
    because using irrefutable pattern with `if let` is useless.
* Pattern refutability explains why `match` arms must use refutable patterns, except for the last arm, which should match any remaining values with an irrefutable pattern. 


# Packages, Crates and Modules

## Packages and Crates
* A **crate** is a binary or library. 
* The **crate root** is a source file that the Rust compiler starts from and makes up the root module of your crate.
* A **package** is one or more crates that provide a set of functionality. 
  * A package contains a **Cargo.toml** file that describes how to build those crates.
  * Rules of package:
    * Must contain at least one crate (library or binary)
    * Must contain zero or one library.
    * Can contain several binary crates.
    * Can contain both library and binary crates
* Create a new package using `cargo new` command:
  ```bash
  $ cargo new my-project
     Created binary (application) `my-project` package
  $ ls my-project
  Cargo.toml
  src
  $ ls my-project/src
  main.rs
  ```
  * `Cargo.toml` is created, giving us a package.
  * No `src/main.rs` is mentioned in the contents of `Cargo.toml` file. 
    * This is because Rust follows a convention that `src/main.rs` is the crate root of a <u>binary crate</u> with the <u>same name</u> as the package.
  * If the package directory contains `src/lib.rs`, that becomes the crate root of a <u>library crate</u> with the <u>same name</u> as the package.
* Cargo passes the crate root files to `rustc` to build the library or binary.
* If a package contains `src/main.rs` and `src/lib.rs`, it has two crates: a library and a binary, both with the same name as the package. 
* A package can have multiple binary crates by placing files in the `src/bin` directory: each file will be a separate binary crate.
* Crates provide namespace to functions that we create, thereby resolving any name conflicts.
  * For example, we create a `struct Rng` and then we import a crate `rand` which also has a struct named `Rng`
  * Since crates create namespaces, there won't be any conflict.
  * In our crate, it refers to the struct `Rng` that we defined. 
  * We would access the `Rng` trait from the `rand` crate as `rand::Rng`.

## Modules
* **Modules** let us organize code within a crate into groups for readability and easy reuse. 
* Modules also control the *privacy* of items, which is whether an item: 
  * can be used by outside code (**public**) or 
  * is an internal implementation detail and not available for outside use (**private**).
* For example, create a new library named `restaurant` by running `cargo new --lib restaurant`, and then add the following code to `src/lib.rs` file:
  ```rust
  mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
  }
  ```
  * We defined a module via `mod` keyword followed by the name of the module (e.g. `mod front_of_house`)
  * Module can contain other modules, structs, enums, etc.
* Module name follows snake case convention (e.g. `snake_case`)
* By using modules, we can group related definitions together and name why they’re related.
* `src/main.rs` and `src/lib.rs` are called crate roots because the contents of either of these two files form a module named `crate` at the <u>root</u> of the crate’s module structure, known as the **module tree**.
  * For example, module tree of above `restaurant` library:
    ```
    crate
    └── front_of_house
        ├── hosting
        │   ├── add_to_waitlist
        │   └── seat_at_table
        └── serving
            ├── take_order
            ├── serve_order
            └── take_payment
    ```
  * If module A is contained inside module B, we say that module A is the **child** of module B and that module B is the **parent** of module A. 
    * `front_of_house` is parent of `hosting`, and `hosting` is child of `front_of_house`.
  * Those which are defined in the same module are called **siblings**.
    * `hosting` and `serving` are siblings.
  * Notice that the entire module tree is rooted under the *implicit module* named `crate`.

## Paths for Referring to an Item in the Module Tree
* To show Rust where to find an item in a module tree, we use a **path** in the same way we use a path when <u>navigating a filesystem</u>. 
* A path can take two forms:
  * An **absolute path** starts from a crate root by using a crate name or a literal crate.
  * A **relative path** starts from the *current module* and uses `self`, `super`, or an identifier in the current module.
* Both absolute and relative paths are followed by one or more identifiers separated by double colons (`::`).
* Example:
  ```rust
  mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
  }

  pub fn eat_at_restaurant() {
      // Absolute path
      crate::front_of_house::hosting::add_to_waitlist();

      // Relative path
      front_of_house::hosting::add_to_waitlist();
  }
  ```
* Running the above code will give error:
  ```
  error[E0603]: module `hosting` is private
  --> src/lib.rs:9:28
    |
  9 |     crate::front_of_house::hosting::add_to_waitlist();
    |                            ^^^^^^^ private module
    |
  note: the module `hosting` is defined here
  --> src/lib.rs:2:5
    |
  2 |     mod hosting {
    |     ^^^^^^^^^^^

  ```
  * This is because everything in a module is *private* by default.
* Modules define Rust's privacy boundary.
  * Modules are how encapsulation is supported in Rust - hide implementation details and expose only what's needed.
  * All items in a module like structs, functions, etc. are private by default.
  * Items in a parent module can’t use the private items <u>inside child modules</u>, but items in child modules can use the items in their ancestor modules. 
    * The reason is that child modules wrap and hide their implementation details, but the child modules can see <u>the context</u> in which they’re defined.

## Exposing Paths with the pub Keyword
* To provide access to inner members of a module, use `pub` keyword. Example:
  ```rust
  mod front_of_house {
    pub mod hosting {
        fn add_to_waitlist() {}
    }
  }

  pub fn eat_at_restaurant() {
      crate::front_of_house::hosting::add_to_waitlist();
  }
  ```
* The above example still won't compile as `add_to_waitlist` is not declared as `pub`.
  * Changing `fn add_to_waitlist()` to `pub fn add_to_waitlist()` will compile the code.

## Starting Relative Paths with super
* You can access parent module contents via `super` keyword
  * Its like doing `cd ..` in bash to go up to parent directory.
* Example:
  ```rust
  fn serve_order() {}

  mod back_of_house {
      fn fix_incorrect_order() {
          cook_order();
          super::serve_order();
      }

      fn cook_order() {}
  }
  ```
  * Here, `super` will take compiler to search for `serve_order` function in parent module of `back_of_house`. In this case, it does find the function and hence code compiles.

## Making Structs and Enums Public
* When you make structs public in a module, the struct's fields and methods are still private.
* #RustPattern: If you want to make a struct field immutable, just leave it as private and provide a getter method (or accessor) to get the field value:
  ```rust
  mod company {
    pub struct Employee {
        name: String
    }
    impl Employee {
        pub fn new(name: String) -> Employee {
            Employee {
                name
            }
        }
        pub fn get_name(&self) -> &String {
            &self.name
        }
    }
  }

  fn main() {
    let employee = company::Employee::new(String::from("Jethalal"));
    // cannot create instance of Employee here as name is private. So below code will result in error
    /*
    let employee = company::Employee {
      name: String::from("Jethalal")
    };
    */

    // trying to set employee name would throw error, as name is not visible
    // employee.name = "Ramesh";
    println!("name: {}", employee.get_name());
  }
  ```
* When you make enums public, all its variants (or enum types) become public. Example:
  ```rust
  mod back_of_house {
    pub enum Appetizer {
        Soup,
        Salad,
    }
  }

  pub fn eat_at_restaurant() {
      let order1 = back_of_house::Appetizer::Soup;
      let order2 = back_of_house::Appetizer::Salad;
  }
  ```

## `use` keyword
* We can bring a path into a scope once and then call the items in that path as if they’re local items with the `use` keyword. Example:
  ```rust
  mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
  }

  use crate::front_of_house::hosting;

  pub fn eat_at_restaurant() {
      hosting::add_to_waitlist();
      hosting::add_to_waitlist();
      hosting::add_to_waitlist();
  }
  ``` 
  * By adding `use crate::front_of_house::hosting` in the crate root, `hosting` is now a valid name in that scope, just as though the `hosting` module had been defined in the crate root. 
  * Paths brought into scope with `use` also check privacy, like any other paths.
* You can also bring an item into scope with use and a relative path:
  ```rust
  mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
  }

  use self::front_of_house::hosting;

  pub fn eat_at_restaurant() {
      hosting::add_to_waitlist();
      hosting::add_to_waitlist();
      hosting::add_to_waitlist();
  }
  ```
* You could have specified `use self::front_of_house::hosting::add_to_waitlist` and directly use `add_to_waitlist` function:
  ```rust
  use crate::front_of_house::hosting::add_to_waitlist;

  pub fn eat_at_restaurant() {
      add_to_waitlist();
  }
  ```
  * However, its unclear where the `add_to_wishlist` function is located (same module or different).
    * This can get more confusing when source code size increase.
  * So, the idiomatic way to bring functions from other module is to bring just the parent module path to the scope (`use self::front_of_house::hosting`) and then call the function (`hosting::add_to_waitlist()`).
* Idiomatic way of bringing structs, enums and other items is to specify its full path:
  ```rust
  use std::collections::HashMap;

  fn main() {
      let mut map = HashMap::new();
      map.insert(1, 2);
  }
  ```
  * Here, we specify the full path 
* If you're bringing two structs of same name but from different modules, then bring just the parent modules into scope:
  ```rust
  use std::fmt;
  use std::io;

  fn function1() -> fmt::Result {
      // --snip--
  }

  fn function2() -> io::Result<()> {
      // --snip--
  }
  ```
  * Here, we are using `Result` struct from two different modules by bringing parent modules `std::fmt` and `std::io` into scope.  
  * If we brought `Result` via complete path like `use std::fmt::Result`, then Rust wouldn't know which `Result` struct to use.
  * If you still want to use complete path, then alias the other structs via `as` keyword:
    ```rust
    use std::fmt::Result;
    use std::io::Result as IoResult;

    fn function1() -> Result {
        // --snip--
    }

    fn function2() -> IoResult<()> {
        // --snip--
    }
    ```

## Using External Packages
* When you add a dependency via `Cargo.toml`:
  ```
  [dependencies]
  algorithms = "0.1.1"
  ```
  you tell Cargo to download the `algorithm` package and its dependencies and make this external package available to our project.
* We can `use` the `algorithm` crate in any Rust project file as follows:
  ```rust
  use algorithms;

  fn main() {
      let factorial_of_5 = algorithms::factorial(5);
  }
  ```
* The standard library (`std`) is also a crate that’s external to our package. 
  * Because the standard library is shipped with the Rust language, we don’t need to change Cargo.toml to include `std`.
  * But we do need to refer to it with `use` to bring items from there into our package’s scope:
    ```rust
    use std::collections::HashMap;
    ```
    * This is an absolute path starting with `std`, the name of the standard library crate.

## Nested paths
* If we're using multiple items in the same crate or module, we can use nested paths. For example, we can write this:
  ```rust
  // --snip--
  use std::cmp::Ordering;
  use std::io;
  // --snip--
  ```
  as
  ```rust
  // --snip--
  use std::{cmp::Ordering, io};
  // --snip--
  ```
* If two `use` statements share a subpath like below:
  ```rust
  use std::io;
  use std::io::Write;
  ```
  we can write it as:
  ```rust
  use std::io::{self, Write};
  ```

## Glob operator
* If we want to bring *all* public items defined in a path into scope, we can specify that path followed by `*`, the glob operator:
  ```rust
  use std::collections::*;
  ```
* This use statement brings all public items defined in `std::collections` into the current scope. 
* Caution - Glob can make it harder to tell 
  * what names are in scope
  * where a name used in your program was defined
* Its often used when testing to bring everything under test into the `tests` module.

## Separating Modules into Different Files
* You've got this code:
  ```rust
  mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
  }

  use self::front_of_house::hosting;

  pub fn eat_at_restaurant() {
      hosting::add_to_waitlist();
      hosting::add_to_waitlist();
      hosting::add_to_waitlist();
  }
  ```
  and you want to split it into different files for each module.
* In Rust, modules can be stored in `<module-name>.rs` or create a directory called `<module-name>` and then store it in `<module-name>/mod.rs`.
* So, our file structure looks like:
  * `src/lib.rs`:
    ```rust
    mod front_of_house;

    use crate::front_of_house::hosting;

    pub fn eat_at_restaurant() {
        hosting::add_to_waitlist();
        hosting::add_to_waitlist();
        hosting::add_to_waitlist();
    }
    ```
    * `mod` followed by `<module-name>` and a semicolon `;` (e.g. `mod front_of_house;`) tells Rust to load that module file into scope.
    * There won't be any change to `use` statement with the refactoring of code into multiple files. 
  * `src/front_of_house/mod.rs`
    ```rust
    pub mod hosting;
    ```
  * `src/front_of_house/hosting.rs`
    ```rust
    pub fn add_to_waitlist() {}
    ```

# Collections
* Collections are data structures that contain multiple values.
* The data is stored on the heap, which means the amount of data does not need to be known at compile time and can grow or shrink as the program runs.
* Types of collections:
  * A *vector* allows you to store a variable number of values next to each other.
  * A *string* is a collection of characters. 
  * A *hash map* allows you to associate a value with a particular key. It’s a particular implementation of the more general data structure called a map.

## Vectors
* Vector, or `Vec<T>`, allows you to store more than one value in a single data structure that puts all the values next to each other in memory. 
* Vectors can only store values of the same type.
* Create a new vector:
  ```rust
  // immutable vector of integers
  let v: Vec<i32> = Vec::new();
  // immutable vector of integers 1,2 and 3
  let v = vec![1,2,3];
  ```
* Updating a vector:
  ```rust
  let mut v = Vec::new();

  v.push(5);
  v.push(6);
  v.push(7);
  v.push(8);
  ```
  * To push elements to a vector, add `mut` while declaring the vector.
* Dropping a vector drops its elements:
  ```rust
  {
      let v = vec![1, 2, 3, 4];

      // do stuff with v
  } // <- v goes out of scope and is freed here
  ```
* Reading elements of vectors:
  ```rust
  let v = vec![1, 2, 3, 4, 5];

  let third: &i32 = &v[2];
  println!("The third element is {}", third);

  match v.get(2) {
      Some(third) => println!("The third element is {}", third),
      None => println!("There is no third element."),
  }
  ```
  * Index in vector starts from 0.
  * `&v[100]` in above example would result in `panic` and thus crashing the program.
  * `v.get(100)` would return `None` and need to handle it accordingly.
* You cannot have mutable and immutable references in the same scope:
  ```rust
  let mut v = vec![1, 2, 3, 4, 5];

  let first = &v[0];

  v.push(6);

  println!("The first element is: {}", first);
  ```
  Error:
  ```
    error[E0502]: cannot borrow `v` as mutable because it is also borrowed as immutable
  --> src/main.rs:6:5
    |
  4 |     let first = &v[0];
    |                  - immutable borrow occurs here
  5 | 
  6 |     v.push(6);
    |     ^^^^^^^^^ mutable borrow occurs here
  7 | 
  8 |     println!("The first element is: {}", first);
    |                 ----- immutable borrow later used here
  ```
  * vector's `push` method can result in copying elements to new memory space, and thus `first` will point to invalid memory location.
  * Borrowing rules helps in avoiding this issue.
* Iterating over the values in vector:
  ```rust
  let v = vec![100, 32, 57];
  for i in &v {
      println!("{}", i);
  }
  ```
* To mutate vector values while iterating:
  ```rust
  let mut v = vec![100, 32, 57];
  for i in &mut v {
      *i += 50;
  }
  ```
  * To change the value that the mutable reference refers to, we have to use the dereference operator (`*`) to get to the value in `i` before we can use the `+=` operator.
* Vector of enums:
  ```rust
  enum SpreadsheetCell {
      Int(i32),
      Float(f64),
      Text(String),
  }

  let row = vec![
      SpreadsheetCell::Int(3),
      SpreadsheetCell::Text(String::from("blue")),
      SpreadsheetCell::Float(10.12),
  ];
  ```

# Strings
* Rust has only one string type in the core language, which is the string slice `str` that is usually seen in its borrowed form `&str`. 
* **String slices** - references to some UTF-8 encoded string data stored elsewhere
  * For example: String literals are stored in the program’s binary and are therefore string slices.
* The `String` type, which is provided by Rust’s standard library rather than coded into the core language, is a growable, mutable, owned, UTF-8 encoded string type.
  * Strings in Rust usually refer to `String` type and `&str` string slice type.
* Creating a new `String`:
  ```rust
  let mut s = String::new();
  ```
* Convert `str` literals to `String`:
  ```rust
  let s = "initial contents".to_string();
  // or
  let s = String::from("initial contents");
  ```
* Strings are UTF-8 encoded, so we can include any properly encoded data in them:
  ```rust
  let hello = String::from("नमस्ते");
  ```
* We can grow a String by using the push_str method to append a string slice:
  ```rust
  let mut s1 = String::from("foo");
  let s2 = "bar";
  s1.push_str(s2);
  println!("s2 is {}", s2);
  ```
  * Since `push_str` function takes a string slice, the calling function doesn't lose ownership of `s2` and can thus print its value.
* To append a character, use `push` function:
  ```rust
  let mut s = String::from("lo");
  s.push('l');
  ```
  * `s` will contain `lol`

## Concatenation with `+` operator
* Example:
  ```rust
  let s1 = String::from("Hello, ");
  let s2 = String::from("world!");
  let s3 = s1 + &s2; // note s1 has been moved here and can no longer be used
  ```
* `+` operator uses the `add` method:
  ```rust
  fn add(self, s: &str) -> String {
  ```
* After `s3`, `s2` remains valid as `s2` was borrowed. However, `s1` becomes invalid since ownership was moved to `add` function.
* Notice `&s2` is of type `&String`, but `add` accepts `&str` as second parameter. This works because Rust uses a **deref coercion** which turns `&s2` into `&s2[..]`.
* `let s3 = s1 + &s2;` isn't making new strings, but rather:
  * takes ownership of s1
  * appends a copy of the contents of s2
  * and then returns ownership of the result, thus making it more efficient than copying.

## Concatenation with `format!` macro
* Example:
  ```rust
  let s1 = String::from("tic");
  let s2 = String::from("tac");
  let s3 = String::from("toe");

  let s = format!("{}-{}-{}", s1, s2, s3);
  ```
* The format!` macro works in the same way as `println!`, but instead of printing the output to the screen, it returns a String with the contents. 
* The version of the code using `format!` is much easier to read and doesn’t take ownership of any of its parameters.

## Indexing into strings
* In Rust, indexing into strings will throw an error:
  ```rust
  let s1 = String::from("hello");
  let h = s1[0];
  ```
  Error:
  ```
    error[E0277]: the type `String` cannot be indexed by `{integer}`
  --> src/main.rs:3:13
    |
  3 |     let h = s1[0];
    |             ^^^^^ `String` cannot be indexed by `{integer}`
    |
    = help: the trait `Index<{integer}>` is not implemented for `String`
  ```
* This is because Rust doesn't support indexing of strings. This is because of how `String`s are implemented in Rust.
* Internal representation of `String`s
  * A `String` is a wrapper over a `Vec<u8>`
  * Example:
    ```rust
    let hello = String::from("Hola");
    ```
  * Here, `hello.len()` will be 4, which means the vector storing the string "Hola" is 4 bytes long. 
    * Each of these letters takes 1 byte when encoded in UTF-8. 
  * However, for this string:
    ```rust
    let hello = String::from("Здравствуйте");
    ```
    vector is 24 bytes long, not 12. This is because Cyrillic letters take 2 bytes of storage.
  * Doing `&hello[0]` will return first byte value stored, not the character `З` (which is capital Cyrillic letter Ze).
  * To avoid returning an unexpected value and causing bugs that might not be discovered immediately, Rust doesn’t compile code which does indexing like `&hello[0]`.
* Another point about UTF-8 is that there are actually three relevant ways to look at strings from Rust’s perspective: 
  * as bytes. 
    * e.g. Hindi word `नमस्ते` as bytes:
      ```
      [224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164, 224, 165, 135]
      ```
  * scalar values
    * e.g. Hindi word `नमस्ते` as Unicode scalar values:
      ```
      ['न', 'म', 'स', '्', 'त', 'े']
      ```
    * The fourth and sixth are not letters: they’re diacritics that don’t make sense on their own.
  * grapheme clusters (the closest thing to what we would call letters).
    * e.g. Hindi word `नमस्ते` as grapheme clusters:
      ```
      ["न", "म", "स्", "ते"]  
      ```
* Another reason against indexing into strings is indexing operations are expected to always take constant time (O(1)).
  *  But it isn’t possible to guarantee that performance with a String, because Rust would have to walk through the contents from the beginning to the index to determine how many valid characters there were.
* <u>Slicing strings</u> is allowed but should be used with caution.
  * For example, the following code will panic:
    ```rust
    let hello = "Здравствуйте";
    let s = &hello[0..1];
    ```
    Error:
    ```
    thread 'main' panicked at 'byte index 1 is not a char boundary; it is inside 'З' (bytes 0..2) of `Здравствуйте`', src/main.rs:4:14
    ```
  * Using `&hello[0..3]` works OK as Cyrillic characters are stored as 2 bytes.

## Methods for Iterating over `String`s
* To iterate over Unicode scalar values:
  ```rust
  for c in "नमस्ते".chars() {
    println!("{}", c);
  }
  ```
  Output:
  ```
  न
  म
  स
   ्
  त
   े
  ```
* To iterate over bytes:
  ```rust
  for b in "नमस्ते".bytes() {
    println!("{}", b);
  }
  ```
  Output:
  ```
  224
  164
  // --snip--
  165
  135
  ```
* Rust standard library doesn't provide grapheme cluster iteration functionality as it is complex.

# HashMaps
* The type `HashMap<K, V>` stores a mapping of keys of type `K` to values of type `V`. 
* It does this via a *hashing function*, which determines how it places these keys and values into memory. 
* Creating a new hash map:
  ```rust
  use std::collections::HashMap;

  let mut scores = HashMap::new();

  scores.insert(String::from("Blue"), 10);
  scores.insert(String::from("Yellow"), 50);
  ```
* Hash maps store their data on the heap.
* Hash maps are homogeneous: all of the keys must have the same type, and all of the values must have the same type.
* Creating hash map using `zip` and `collect` functions:
  ```rust
  use std::collections::HashMap;

  let teams = vec![String::from("Blue"), String::from("Yellow")];
  let initial_scores = vec![10, 50];

  let mut scores: HashMap<_, _> =
      teams.into_iter().zip(initial_scores.into_iter()).collect();
  ```
  * The type annotation `HashMap<_, _>` is needed here because it’s possible to `collect` into many different data structures and Rust doesn’t know which you want unless you specify.
    * For the parameters for the key and value types, however, we use underscores, and Rust can infer the types that the hash map contains based on the types of the data in the vectors. 
    * Here, key type would be `String` and the value type will be `i32`
  * `zip` method is used to create a vector of tuples where team is paired with initial score (e.g. “Blue” is paired with 10).
  * `collect` method to turns vector of tuples into a hash map.

## Hash Maps and Ownership
* For types that implement the `Copy` trait, like `i32`, the values are copied into the hash map. 
* For owned values like `String`, the values will be moved and the hash map will be the owner of those values.
* Example:
  ```rust
  use std::collections::HashMap;

  let field_name = String::from("Favorite color");
  let field_value = String::from("Blue");

  let mut map = HashMap::new();
  map.insert(field_name, field_value);
  // field_name and field_value are invalid at this point, try using them and
  // see what compiler error you get!
  ```
* If we insert references to values into the hash map, the values won’t be moved into the hash map. 
  * The values that the references point to must be valid for at least as long as the hash map is valid.

## Accessing Values in a Hash Map
* Example:
  ```rust
  use std::collections::HashMap;

  let mut scores = HashMap::new();

  scores.insert(String::from("Blue"), 10);
  scores.insert(String::from("Yellow"), 50);

  let team_name = String::from("Blue");
  let score = scores.get(&team_name);
  ```
  * Here, `score` will have the value that’s associated with the Blue team, and the result will be `Some(&10)`.
  * The result is wrapped in `Some` because get returns an `Option<&V>`; if there’s no value for that key in the hash map, get will return `None`.
* Iterate over key/value pair:
  ```rust
  use std::collections::HashMap;

  let mut scores = HashMap::new();

  scores.insert(String::from("Blue"), 10);
  scores.insert(String::from("Yellow"), 50);

  for (key, value) in &scores {
      println!("{}: {}", key, value);
  }
  ```
  Output:
  ```
  Yellow: 50
  Blue: 10
  ```

## Updating a Hash Map
* Overwriting a value:
  * If we insert a key and a value into a hash map and then insert that same key with a different value, the value associated with that key will be replaced.
  * Example:
    ```rust
    use std::collections::HashMap;

    let mut scores = HashMap::new();

    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Blue"), 25);

    println!("{:?}", scores);
    ```
    Output:
    ```
    {"Blue": 25}
    ```
* Only Inserting a value if key has no value
  * Can be done via `entry` followed by `or_insert` methods:
    ```rust
    use std::collections::HashMap;

    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);

    scores.entry(String::from("Yellow")).or_insert(50);
    scores.entry(String::from("Blue")).or_insert(50);

    println!("{:?}", scores);
    ```
  * The return value of the `entry` method is an enum called `Entry` that represents a value that might or might not exist.
  * If the key exists, the `or_insert` method on `Entry`:
    * will return a mutable reference to the value for the corresponding `Entry` key, *or else*
    * inserts the parameter as the new value for this key and returns a mutable reference to the new value. 
  * Output of above code:
    ```
    {"Yellow": 50, "Blue": 10}
    ```
    * The first call to entry will insert the key for the Yellow team with the value 50 because the Yellow team doesn’t have a value already.
    * The second call to entry will not change the hash map because the Blue team already has the value 10.

## Updating a Value Based on the Old Value
* Example:
  ```rust
  use std::collections::HashMap;

  let text = "hello world wonderful world";

  let mut map = HashMap::new();

  for word in text.split_whitespace() {
      let count = map.entry(word).or_insert(0);
      *count += 1;
  }

  println!("{:?}", map);
  ```
  Output:
  ```
  {"world": 2, "hello": 1, "wonderful": 1}
  ```
  * `or_insert` method returns mutable reference to value for the key.
  * To mutate the reference, use dereference operator (`*`) (e.g. `*count += 1` above)

## Hashing Functions
* By default, `HashMap` uses a hashing function called **SipHash** that can provide resistance to Denial of Service (DoS) attacks involving hash tables.
* You can change hash function by specifying a different *hasher*.
  * A **hasher** is a type that implements the `BuildHasher` trait.

# Error Handling in Rust
* There are two ways of handling errors in Rust:
  * The `panic!` macro signals that your program is in a state it can’t handle and lets you tell the process to stop instead of trying to proceed with invalid or incorrect values.
  * The `Result` enum uses Rust’s type system to indicate that operations might fail in a way that your code could recover from.

## Unrecoverable Errors with panic!
* When the panic! macro executes, your program will print a failure message, unwind and clean up the stack, and then quit.
* This most commonly occurs when a bug of some kind has been detected and it’s not clear to the programmer how to handle the error.
* Example:
  ```rust
  fn main() {
    panic!("crash and burn");
  }
  ```
  Error:
  ```
  thread 'main' panicked at 'crash and burn', src/main.rs:2:5
  note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
  ```
  * The call to panic! causes the error message contained in the last two lines. 
  * The first line shows our panic message (`'crash and burn'`) and the place in our source code where the panic occurred: `src/main.rs:2:5` indicates that it’s the second line, fifth character of our `src/main.rs` file.

## Using a `panic!` backtrace
* Consider the following code:
  ```rust
  fn main() {
    let v = vec![1, 2, 3];

    v[99];
  }
  ```
  * Here, we’re attempting to access the 100th element of our vector (which is at index 99 because indexing starts at zero), but it has only 3 elements. 
    * In this situation, Rust will panic. 
* In C, attempting to read beyond the end of a data structure is undefined behavior. 
  * You might get whatever is at the location in memory that would correspond to that element in the data structure, even though the memory doesn’t belong to that structure.
  * This is called a **buffer overread** and can lead to security vulnerabilities if an attacker is able to manipulate the index in such a way as to read data they shouldn’t be allowed to that is stored after the data structure.
* To protect from such vulnerabilities, Rust panics and crashes the program:
  ```
  thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 99', src/main.rs:4:5
  note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
  ```
* Notice that the second line above tells us that we can set the `RUST_BACKTRACE` environment variable to get a backtrace of exactly what happened to cause the error.
* A **backtrace** is a list of all the functions that have been called to get to this point. 
  * Backtraces in Rust work as they do in other languages: the key to reading the backtrace is to start from the top and read until you see files you wrote. 
* To get a backtrace, include `RUST_BACKTRACE=1` in `cargo run` command:
  ```bash
  $ RUST_BACKTRACE=1 cargo run
  thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 99', src/main.rs:4:5
  stack backtrace:
    0: rust_begin_unwind
             at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/std/src/panicking.rs:483
    1: core::panicking::panic_fmt
              at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/core/src/panicking.rs:85
    2: core::panicking::panic_bounds_check
              at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/core/src/panicking.rs:62
    3: <usize as core::slice::index::SliceIndex<[T]>>::index
              at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/core/src/slice/index.rs:255
    4: core::slice::index::<impl core::ops::index::Index<I> for [T]>::index
              at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/core/src/slice/index.rs:15
    5: <alloc::vec::Vec<T> as core::ops::index::Index<I>>::index
              at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/alloc/src/vec.rs:1982
    6: panic::main
              at ./src/main.rs:4
    7: core::ops::function::FnOnce::call_once
              at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/core/src/ops/function.rs:227
    # rest of the backtrace
  ```
  * The source code information you see above (e.g. `at ./src/main.rs:4`) are called **debug symbols**.
  * Debug symbols are not present if you do `cargo build --release` or `cargo run --release`.

## Unwinding the Stack or Aborting in Response to a Panic
* By default, when a panic occurs, the program starts unwinding, which means Rust walks back up the stack and cleans up the data from each function it encounters. 
  * But this walking back and cleanup is a lot of work. 
* The alternative is to immediately abort, which ends the program without cleaning up. 
  * Memory that the program was using will then need to be cleaned up by the operating system. 
  * If in your project you need to make the <u>resulting binary</u> as small as possible, you can switch from unwinding to aborting upon a panic by adding `panic = 'abort'` to the appropriate `[profile]` sections in your `Cargo.toml` file. 
  * For example, if you want to abort on panic in *release* mode, add this:
    ```
    [profile.release]
    panic = 'abort'
    ```

## Recoverable errors with `Result`
* `Result` enum:
  ```rust
  enum Result<T, E> {
    Ok(T),
    Err(E),
  }
  ```
  * `T` represents the type of the value that will be returned in a success case within the `Ok` variant, and 
  * `E` represents the type of the error that will be returned in a failure case within the `Err` variant.
* Usage of `Result` enum:
  ```rust
  use std::fs::File;

  fn main() {
      let f = File::open("hello.txt");

      let f = match f {
          Ok(file) => file,
          Err(error) => panic!("Problem opening the file: {:?}", error),
      };
  }
  ```
  * `Result` don't need to be imported via `use` as its brought into scope via prelude.
  * When the result is `Ok`, return the inner file value out of the `Ok` variant, and we then assign that file handle value to the variable `f`.
  * The other arm of the match handles the case where we get an `Err` value from `File::open`. In this example, we’ve chosen to call the `panic!` macro.
    * For example, the program will panic when `hello.txt` file does not exist.

## Matching with different errors
* Example:
  ```rust
  use std::fs::File;
  use std::io::ErrorKind;

  fn main() {
      match File::open("hello.txt") {
          Ok(file) => file,
          Err(error) => match error.kind() {
              ErrorKind::NotFound => match File::create("hello.txt") {
                  Ok(fc) => fc,
                  Err(e) => panic!("Problem creating the file: {:?}", e),
              },
              // capture all other errors in `other_error` variable
              other_error => panic!("Problem opening the file: {:?}", other_error)
          },
      };
  }
  ```
  * The type of the value that `File::open` returns inside the `Err` variant is `io::Error`, which is a struct provided by the standard library. 
  * This struct has a method kind that we can call to get an `io::ErrorKind` value. 
  * The enum `io::ErrorKind` is provided by the standard library and has variants representing the different kinds of errors that might result from an io operation. 
  * The variant we want to use is `ErrorKind::NotFound`, which indicates the file we’re trying to open doesn’t exist yet. 
  * So we match on `File::open("hello.txt")`, but we also have an inner match on `error.kind()`.
* Using `unwrap_or_else` of `Result` enum to open or create file:
  ```rust
  use std::fs::File;
  use std::io::ErrorKind;

  fn main() {
      File::open("hello.txt").unwrap_or_else(|error| match error.kind() {
          ErrorKind::NotFound => File::create("hello.txt")
              .unwrap_or_else(|error| panic!("Problem creating the file: {:?}", error),
          other_error => panic!("Problem opening the file: {:?}", other_error)
        }
      );
  }
  ```
  * This is more functional way of writing code, which removed one match expression and makes code more readable.

## Shortcuts for Panic on Error: unwrap and expect
* Example:
  ```rust
  use std::fs::File;

  fn main() {
      let f = File::open("hello.txt").unwrap();
  }
  ```
  * If `hello.txt` is present, then `File` object is assigned to `f`.
    * If not, program panics and crashes with following error:
      ```
      thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Error {
      repr: Os { code: 2, message: "No such file or directory" } }',
      src/libcore/result.rs:906:4
      ```
* You could customize panic message via `expect` method:
  ```rust
  use std::fs::File;

  fn main() {
      let f = File::open("hello.txt").expect("Failed to open hello.txt");
  }
  ```
  Output:
  ```
  thread 'main' panicked at 'Failed to open hello.txt: Error { repr: Os { code:
  2, message: "No such file or directory" } }', src/libcore/result.rs:906:4
  ```
* **Pro tip**: Don't ever use `unwrap` or `expect`, because that would make the program crash. Instead, use `unwrap_or_else` method to handle errors in code itself.

## Propagating Errors using `?` operator
* Example:
  ```rust
  use std::fs::File;
  use std::io;
  use std::io::Read;

  fn read_username_from_file() -> Result<String, io::Error> {
      let mut f = match File::open("hello.txt") {
          Ok(file) => file,
          Err(e) => Err(e),
      };

      let mut s = String::new();

      match f.read_to_string(&mut s) {
          Ok(_) => Ok(s),
          Err(e) => Err(e),
      }
  }
  ```
* You can write this code consicely using `?` operator:
  ```rust
  use std::fs::File;
  use std::io;
  use std::io::Read;

  fn read_username_from_file() -> Result<String, io::Error> {
      let mut s = String::new();

      File::open("hello.txt")?.read_to_string(&mut s)?;

      Ok(s)
  }
  ```
  * The `?` at the end of the `File::open` call will return the value inside an `Ok`. 
  * If an error occurs, the `?` operator will return early out of the whole function and give any `Err` value to the calling code. 
  * The same thing applies to the `?` at the end of the `read_to_string` call.
* You can use `?` operator in `main` function as follows:
  ```rust
  use std::error::Error;
  use std::fs::File;

  fn main() -> Result<(), Box<dyn Error>> {
      let f = File::open("hello.txt")?;

      Ok(())
  }
  ```
  * The `Box<dyn Error>` type is called a [**trait object**](advanced.md#trait-objects).
    * Here, it means “any kind of error.”

# Generic Data Types
* Generics are abstract stand-ins for concrete types or other properties (TODO: Need better definition).
  * Similar to Java Generics
* Generics help you to avoid code duplication by using same data structure or function for different data types.

## Generics in Function definitions
* Example:
  ```rust
  fn largest<T: std::cmp::PartialOrd + Copy>(list: &[T]) -> T {
    let mut largest = list[0];

    for &item in list {
        if item > largest {
            largest = item;
        }
    }

    largest
  }

  fn main() {
      let number_list = vec![34, 50, 25, 100, 65];

      let result = largest(&number_list);
      println!("The largest number is {}", result);

      let char_list = vec!['y', 'm', 'a', 'q'];

      let result = largest(&char_list);
      println!("The largest char is {}", result);
  }
  ```
* If you replace `T: std::cmp::PartialOrd + Copy` with just `T`, you would get the following error:
  ```
      error[E0369]: binary operation `>` cannot be applied to type `T`
    --> src/main.rs:5:17
      |
    5 |         if item > largest {
      |            ---- ^ ------- T
      |            |
      |            T
      |
    help: consider restricting type parameter `T`
      |
    1 | fn largest<T: std::cmp::PartialOrd>(list: &[T]) -> T {
      |             ^^^^^^^^^^^^^^^^^^^^^^

  ```
  * This is because `>` needs the generic type `T` to implement `std::cmp::PartialOrd` trait.
  * Also, `let mut largest = list[0]` requires `T` to implement `Copy` trait so that `list[0]` value gets copied over to `largest` or else move will be attempted. 
    * However, we can't do a move here as we can't move out of a reference, and `list` is a reference to an array.
* `i32` and `char` implement the `std::cmp::PartialOrd` and `Copy` trait.

## Generics in Struct and Method definitions
* Example:
  ```rust
  struct Point<T> {
    x: T,
    y: T,
  }

  impl<T> Point<T> {
      fn x(&self) -> &T {
          &self.x
      }
  }

  fn main() {
      let p = Point { x: 5, y: 10 };

      println!("p.x = {}", p.x());
  }
  ```
* If you try this:
  ```rust
  fn main() {
      let p = Point { x: 5, y: 10.5 };

      println!("p.x = {}", p.x());
  }
  ```
  you would get the following error:
  ```
    error[E0308]: mismatched types
  --> src/main.rs:7:38
    |
  7 |     let p = Point { x: 5, y: 4.0 };
    |                              ^^^ expected integer, found floating-point number
  ```
* To support different types for `Point`'s `x` and `y` fields, use different generic types:
  ```rust
  struct Point<T, U> {
    x: T,
    y: U,
  }
  ```
  This struct would support something like 
  ```rust
  let p = Point { 
    x: 5, 
    y: 4.0 
  };
  ```

## Enum with Generic Types
* Example with single generic type:
  ```rust
  enum Option<T> {
    Some(T),
    None,
  }
  ```
* Example with multiple generic types:
  ```rust
  enum Result<T, E> {
    Ok(T),
    Err(E),
  }
  ```

## Performance of Code Using Generics
* Rust implements generics in such a way that your code doesn’t run any slower using generic types than it would with concrete types.
* Rust accomplishes this by performing monomorphization of the code that is using generics at compile time. 
* **Monomorphization** is the process of turning generic code into specific code by filling in the concrete types that are used when compiled.
* Example:
  ```rust
  let integer = Some(5);
  let float = Some(5.0);
  ```
  * When Rust compiles this code, it performs monomorphization. 
  * During that process, the compiler reads the values that have been used in `Option<T>` instances and identifies two kinds of `Option<T>`: one is `i32` and the other is `f64`. 
  * As such, it expands the generic definition of `Option<T>` into `Option_i32` and `Option_f64`, thereby replacing the generic definition with the specific ones.
* Example of monomorphized `Option<T>` code:
  ```rust
  enum Option_i32 {
    Some(i32),
    None,
  }

  enum Option_f64 {
      Some(f64),
      None,
  }

  fn main() {
      let integer = Option_i32::Some(5);
      let float = Option_f64::Some(5.0);
  }
  ```
* Because Rust compiles generic code into code that specifies the type in each instance, we pay no runtime cost for using generics. 
* When the code runs, it performs just as it would if we had duplicated each definition by hand. 
* The process of monomorphization makes Rust’s generics extremely efficient at runtime.

# Traits
* A **trait** is used to define shared behavior in an abstract way. 
  * A type’s behavior consists of the methods we can call on that type. 
  * Different types share the same behavior if we can call the same methods on all of those types. 
  * Trait definitions represents this shared behavior in the form of a group of method signatures.
* Example of a trait:
  ```rust
  pub trait Summary {
    fn summarize(&self) -> String;
  }
  ```
  Any type implementing the trait `Summary` would enable a client to call `summarize` method on that type's instance.
* Example of trait implementation on a type:
  ```rust
  pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
  }

  impl Summary for NewsArticle {
      fn summarize(&self) -> String {
          format!("{}, by {} ({})", self.headline, self.author, self.location)
      }
  }

  pub struct Tweet {
      pub username: String,
      pub content: String,
      pub reply: bool,
      pub retweet: bool,
  }

  impl Summary for Tweet {
      fn summarize(&self) -> String {
          format!("{}: {}", self.username, self.content)
      }
  }
  ```
  * Trait implementations are done via `impl <type_name> for <trait_name> { .. }` (e.g. `impl Summary for Tweet { .. }`)
* Calling trait method of `Summary` for `Tweet` type:
  ```rust
  let tweet = Tweet {
      username: String::from("megan_sparkle"),
      content: String::from(
          "I love England!",
      ),
      reply: false,
      retweet: false,
  };

  println!("1 new tweet: {}", tweet.summarize());
  ```
  Output:
  ```
  1 new tweet: megan_sparkle: I love England!
  ```
* If the `Summary` trait was defined in *another module* called `aggregate`, then you would need to bring the trait into scope via `use aggregate::Summary;`

## Coherence / Orphan rule
* We can implement a trait on a type only if either the trait or the type is local to our crate.
  * Here, either `Summary` trait need to be local or `Tweet` struct need to be local.
  * We can’t implement the `Display` trait on `Vec<T>` within our crate, because `Display` and `Vec<T>` are defined in the standard library and aren’t local to our crate.
* This restriction is part of a property of programs called **coherence**, and more specifically the **orphan rule**, so named because the parent type is not present. 
* This rule ensures that other people’s code can’t break your code and vice versa. 
* Without the rule, two crates could implement the same trait for the same type, and Rust wouldn’t know which implementation to use.

## Default Implementations
* Example:
  ```rust
  pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
  }

  // to use default implementation, use empty block
  impl Summary for NewsArticle {}

  fn main () {
    let article = NewsArticle {
        headline: String::from("Headline"),
        location: String::from("USA"),
        author: String::from("Ice"),
        content: String::from("Some content"),
    };

    println!("New article available! {}", article.summarize());
  }
  ```
  * Since we implemented `Summary` trait for `NewsArticle` type without overriding `summarize` method, the output would be: `New article available! (Read more...)`
    * This is because we defined default behavior of `summarize` method to return `Read more...` string.
* We can call other trait methods inside default implementation of a trait method:
  ```rust
  pub trait Summary {
    fn summarize_author(&self) -> String;

    fn summarize(&self) -> String {
        format!("(Read more from {}...)", self.summarize_author())
    }
  }

  impl Summary for Tweet {
    fn summarize_author(&self) -> String {
        format!("@{}", self.username)
    }
  }

  fn main() {
    let tweet = Tweet {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        retweet: false,
    };

    println!("1 new tweet: {}", tweet.summarize());
  }
  ```
  Output:
  ```
  1 new tweet: (Read more from @horse_ebooks...)
  ```
  * Here, `summarize` method of `Summary` trait calls `summarize_author` method of the same trait. 
  * Since `summarize_author` method doesn't have an implementation, structs implementing the trait need to provide an implementation for it (as is done in `impl Summary for Tweet` block).

## Trait Bound Syntax
* Example:
  ```rust
  pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
  }
  ```
  * Any type implementing `Summary` trait can be passed as an argument to the `notify` function.
  * You can make this more concise via `&impl`:
    ```rust
    pub fn notify(item: &impl Summary) {
      println!("Breaking news! {}", item.summarize());
    }
    ```
## Specifying multiple trait bounds via `+` syntax:
* Example:
  ```rust
  pub fn notify<T: Summary + Display>(item: &T) {
  ```
  * With the two trait bounds specified, the body of notify can call `summarize` and use `{}` to format item.

## Clearer trait bounds using `where` clause
* When you have multiple trait bounds like this:
  ```rust
  fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {
  ```
  you can make it more clearer by using `where` clause:
  ```rust
  fn some_function<T, U>(t: &T, u: &U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
  {
  ```

## Returning Types that Implement Traits
* Example:
  ```rust
  fn returns_summarizable() -> impl Summary {
    Tweet {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        retweet: false,
    }
  }
  ```
  * This is useful in case of Closures and iterators.
    * They create types that only the compiler knows or types that are very long to specify. 
    * The `impl <Trait>` syntax lets you concisely specify that a function returns some type that implements the `Iterator` trait without needing to write out a very long type.

  * This doesn't work if your function returns multiple types implementing the same trait. So, following code <u>won't compile</u>:
    ```rust
    fn returns_summarizable(switch: bool) -> impl Summary {
        // both NewsArticle and Tweet implements Summary
        if switch {
            NewsArticle {
                //..
            }
        } else {
            Tweet {
                //..
            }
        }
    }
    ```

## Using Trait Bounds to Conditionally Implement Methods
* Example:
  ```rust
  struct Pair<T> {
      x: T,
      y: T,
  }

  impl<T: Display + PartialOrd> Pair<T> {
      fn cmp_display(&self) {
          if self.x >= self.y {
              println!("The largest member is x = {}", self.x);
          } else {
              println!("The largest member is y = {}", self.y);
          }
      }
  }
  ```
  * Here, `cmp_display` method is implemented only for those generic types which implements both `Display` and `PartialOrd` trait.
* Blanked Implementations:
  * We can also conditionally implement a trait for any type that implements another trait.
  * Implementations of a trait on any type that satisfies the trait bounds are called **blanket implementations** and are extensively used in the Rust standard library.
  * Example:
    ```rust
    impl<T: Display> ToString for T {
    // --snip--
    }
    ```
    * `ToString` trait is implemented for all types which implement `Display` trait.
    * Since `i32` implements `Display`, we can do this: 
    `3.to_string()`

## Associated Types
* **Associated types** specifies placeholder types in trait definitions. E.g. `type SomeType;`
* The implementor of a trait will specify the concrete type to be used using `type SomeType = i32;` 
* That way, we can define a trait that uses some types without needing to know exactly what those types are until the trait is implemented.
* Example - The `Iterator` trait:
  ```rust
  pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;
  }
  ```
  * The type `Item` is a placeholder type
  * The `next` method’s definition shows that it will return values of type `Option<Self::Item>`. 
  * Implementors of the `Iterator` trait will specify the concrete type for `Item` and the `next` method will return an `Option` containing a value of that concrete type.
* Example of `Iterator` trait implementor:
  ```rust
  impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        // --snip--
    }
  }
  ```

## Default Generic Type Parameters
* When we use generic type parameters, we can specify a default concrete type for the generic type. 
* This eliminates the need for implementors of the trait to specify a concrete type if the default type works. 
* The syntax for specifying a default type for a generic type is `<PlaceholderGenericType=ConcreteType>` when declaring the generic type.
* Example - The `Add` trait in `std::ops`:
  ```rust
  trait Add<Rhs=Self> {
      type Output;

      fn add(self, rhs: Rhs) -> Self::Output;
  }
  ```
  * `type Output;` is associated type, referenced by `Self::Output`.
  * `Rhs=Self` syntax is called **default type parameters**.
  *  The `Rhs` generic type parameter (short for “right hand side”) defines the type of the rhs parameter in the add method. 
  *  If we don’t specify a concrete type for `Rhs` when we implement the `Add` trait, the type of `Rhs` will default to `Self`, which will be the type we’re implementing `Add` on.
     *  Example: if we implement `Add` for `Point` struct as
        ```rust
        impl Add for Point {
          //..
        }
        ``` 
        where we don't provide the value of `Rhs` type, then 
        *  `Rhs` will equate to `Self`, and 
        *  `Self` in this case will equate to `Point`.

## Operator Overloading
* **Operator overloading** is customizing the behavior of an operator (such as `+`) in particular situations.
* Suppose you have a `Point` struct defined as:
  ```rust
  struct Point {
    x: i32,
    y: i32,
  }
  ```
  and you want to perform addition of two `Point` instances using `+` operator:
  ```rust
  assert_eq!(
        Point { x: 1, y: 0 } + Point { x: 2, y: 3 },
        Point { x: 3, y: 3 }
    );
  ```
* You can do so by overloading the `+` operator for `Point` struct. This can be done by implementing `Add` trait (discussed above)  for `Point`:
  ```rust
  impl Add for Point {
    type Output = Point;

    fn add(self, other: Point) -> Point {
        Point {
            x: self.x + other.x,
            y: self.y + other.y,
        }
    }
  }
  ```
* Since `Rhs` type's default is `Self` (equal to `Point` here) and associated type `Output` is set as `Point` in the trait implementation, the above code compiles OK.
* For the sake of completeness, here's how to overload `+` operator when RHS is of different type:
  ```rust
  struct Millimeters(u32);
  struct Meters(u32);

  impl Add<Meters> for Millimeters {
      type Output = Millimeters;

      fn add(self, other: Meters) -> Millimeters {
          Millimeters(self.0 + (other.0 * 1000))
      }
  }
  ```
  * With this, you can perform the following:
    ```rust
    assert_eq!(
      Millimeters(1000) + Meters(1),
      Millimeters(2000)
    );
    ```

## Fully Qualified Syntax for methods
* Suppose you have a struct `Game` which implements traits `GameStop` and `Amazon`. 
  * Both `GameStop` and `Amazon` traits have `price` method.
  * `Game` struct also implements its own `price` method.
* Code:
  ```rust
  trait GameStop {
    pub price(&self) -> u32;
  }

  trait Amazon {
    pub price(&self) -> u32;
  }
  
  struct Game;
  impl Game {
    pub price(&self) -> u32 { 100 }
  }

  impl GameStop for Game {
    pub price(&self) -> u32 { 200 }
  }

  impl Amazon for Game {
    pub price(&self) -> u32 { 150 }
  }
  ```
* Calling `game.price()` method, where `game` is an instance of `Game` would return `100`.
  * Rust calls the struct method if it is defined, ignoring trait implementation methods.
* To call trait implementation methods, use `<TRAIT_NAME>::<METHOD_NAME>(..)` syntax:
  ```rust
  fn main() {
    let game = Game{};
    println!("Game price: {}", game.price());
    println!("Game price: {}", Game::price(&game));
    println!("GameStop price: {}", GameStop::price(&game));
    println!("Amazon price: {}", Amazon::price(&game));
  }
  ```
  Output:
  ```
  Game price: 100
  Game price: 100
  GameStop price: 200
  Amazon price: 150
  ```
  * As seen above, `game.price()` can also be written as `Game::price(&game)`.

## Fully Qualified Syntax for Associated Functions
* Associated functions don't have `self` or its variants (like `&self`) as a first parameter.
* So, if a trait implementation and struct implementation has the same associated function, then use `<STRUCT_NAME as TRAIT_NAME>::<FUNCTION_NAME>(...)` syntax:
  ```rust
  trait Premium {
    pub price() -> u32;
  }
  struct Cabbage;
  impl Cabbage {
    pub price() -> u32 { 20 }
  }

  impl Premium for Cabbage {
    pub price() -> u32 { 40 }
  }

  fn main() {
    println!("Cabbage price: {}", Cabbage::price());
    println!("Premium Cabbage price: {}", <Cabbage as Premium>::price());
  }
  ```
  Output:
  ```
  Cabbage price: 20
  Premium Cabbage price: 40
  ```

# Closures
* Rust’s closures are anonymous functions you can save in a variable or pass as arguments to other functions. 
* You can create the closure in one place and then call the closure to evaluate it in a different context. 
* Unlike functions, closures can capture values from the scope in which they’re defined.

## Capturing the Environment with Closures
* Closures can capture their environment and access variables from the scope in which they’re defined.
* Example:
  ```rust
  fn main() {
    let x = 4;

    let equal_to_x = |z| z == x;

    let y = 4;

    assert!(equal_to_x(y));
  }
  ```
  * Even though `x` is not one of the parameters of `equal_to_x`, the `equal_to_x` closure is allowed to use the `x` variable that’s defined in the same scope that `equal_to_x` is defined in.
* Functions don't capture the environment:
  ```rust
  fn main() {
    let x = 4;

    fn equal_to_x(z: i32) -> bool {
        z == x
    }

    let y = 4;

    assert!(equal_to_x(y));
  }
  ```
  Error:
  ```
    error[E0434]: can't capture dynamic environment in a fn item
  --> src/main.rs:5:14
    |
  5 |         z == x
    |              ^
    |
    = help: use the `|| { ... }` closure form instead
  ```
* Closures have memory overhead, since they need memory to store the values from its parent scope for use in closure body.
  * Functions are not allowed to capture its parent scope values, so they don't incur this overhead.
* Closures can capture values from their environment in three ways, which directly map to the three ways a function can take a parameter: <u>taking ownership</u>, <u>borrowing mutably</u>, and <u>borrowing immutably</u>. These are encoded in the three `Fn` traits as follows:
  * `FnOnce` consumes the variables it captures from its *enclosing scope*, known as the closure’s **environment**. 
    * To consume the captured variables, the closure must take ownership of these variables and <u>move</u> them into the closure when it is defined. 
    * The `Once` part of the name represents the fact that the closure can’t take ownership of the same variables more than once, so it can be called only once.
  * `FnMut` can change the environment because it mutably borrows values.
  * `Fn` borrows values from the environment immutably.
* When you create a closure, Rust infers which trait to use based on *how* the closure uses the values from the environment. 
* All closures implement `FnOnce` because they can all be called at least once. 
* Closures that don’t move the captured variables also implement `FnMut`
* Closures that don’t need mutable access to the captured variables implement `Fn`. 
  * Example:
    ```rust
    let x = 4;

    let equal_to_x = |z| z == x;
    ```
    * Here, closure borrows `x` immutably, so `equal_to_x` has `Fn` trait.
* If you want to force the closure to take ownership of the values it uses in the environment, you can use the `move` keyword before the parameter list. 
  * This technique is mostly useful when passing a closure to a new thread to move the data so it’s owned by the new thread.
  * `move` closures may still implement `Fn` or `FnMut`, even though they capture variables by move. 
    * This is because the traits implemented by a closure type are determined by *what* the closure does with captured values, not how it captures them (as is specified by `move` keyword).
  * Example:
    ```rust
    fn main() {
      let x = vec![1, 2, 3];

      let equal_to_x = move |z| z == x;

      println!("can't use x here: {:?}", x);

      let y = vec![1, 2, 3];

      assert!(equal_to_x(y));
    }
    ```
    We get the following error:
    ```
        error[E0382]: borrow of moved value: `x`
    --> src/main.rs:6:40
      |
    2 |     let x = vec![1, 2, 3];
      |         - move occurs because `x` has type `Vec<i32>`, which does not implement the `Copy` trait
    3 | 
    4 |     let equal_to_x = move |z| z == x;
      |                      --------      - variable moved due to use in closure
      |                      |
      |                      value moved into closure here
    5 | 
    6 |     println!("can't use x here: {:?}", x);
      |                                        ^ value borrowed here after move
    ```
    * The `x` value is moved into the closure when the closure is defined, because we added the `move` keyword. 
    * The closure then has ownership of `x`, and `main` isn’t allowed to use `x` anymore in the `println!` statement. 
    * Removing `println!` will fix this example.

