# rust-in-one-page
I document all I know about Rust programming language in this one page.

## Variables
To define an immutable variable:
```rust
let x = 5;
```

To define a mutable variable, add `mut` after `let`
```rust
let mut x = 5;
```

As you can see, mentioning data type of variable is optional, as Rust compiler can infer data type from RHS value. If you want to specify data type, you can do so as:
```rust
let x: i32 = 5;
```

i32 here is data type for 32-bit integer values.

## Constants
* Constants are always immutable, so can't use `mut` with them.
* Constants always require data type annotation.
* Constants can be used in any scope, including global scope.
    * They remain valid in the scope they are defined in.
* Constants can only be assigned constant values, like string or numbers and not some values computed during runtime (like result of some function call)
* To define constant:
```rust
const PI:f32 = 3.14;
```
* By convention, constants are named as ALL_CAPS.

## Shadowing
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

## Data Types

### Scalar Types
* A scalar type represents a single value. 
* Rust has four primary scalar types: integers, floating-point numbers, Booleans, and characters.

#### Integer Type
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

#### Floating-Point Types 
* Two floating types: `f32` and `f64`
* Default is `f64`. E.g. in `let x = 2.0`, `x`'s data type is `f64`.

#### Boolean type
* Boolean type has two possible values - `true` or `false`.
* Booleans are one byte in size, and are specified by `bool` data type.

#### Character type
* Defined by `char`
* `char` literals are specified using single quotes
* Can save emojis into char variable: `let heart_eyed_cat = '😻';`
* Can save unicode as chars: `let digit_2 = '\u{0032}';`
* Rust’s char type is four bytes in size and represents a Unicode Scalar Value

### Compound Types
* Compound types can group multiple values into one type. 

#### Tuple Type
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

#### Array Type
* Unlike a tuple, every element of an array must have the same type.
* Arrays have fixed length.
* Eg: `let a = [1, 2, 3, 4, 5];`
* Arrays are stored on stack
* Specify array type as follows: `let a: [i32; 5] = [1, 2, 3, 4, 5];`
  * `i32, 5` means array elements are of type `i32` and array length is 5
* Create an array of 5 elements, all having same value 3: `let a = [3; 5];`
* Access array elements as: `let first = a[0];`
  
## Functions
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
  Result:
  ```
  Inside main()
  Inside da_func()
  ```
* You can define function with parameters as: `fn da_func(x:i32, y:i32) { ...}`. Note that data types for function parameters (like `x` and `y` here) is required.

### Expressions and Statements
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
  Result:
  ```
  x = 5, y = 4
  ```
  Notice the following:
    * Block returns a value (`x + 1`). Any statement which does not end with `;` is considered as return expression.
    * We reused variable `x` inside the block and assigned it `3`. This value will shadow the value `5` throughout the block. Once the block is over, `x`'s value becomes `5`.

### Function with return values

* Return types are defined as follows: `fn func() -> i32 {...}`
* In Rust, the return value of the function is synonymous with the value of the final expression in the block of the body of a function.
  ```rust
  fn five() -> i32 {
    5
  }
  ```
  This functions returns the value `5`.

## Comments
* Comments start with `//`
  ```rust
  // flag for whether to perform search op or not
  let search_flag = false;
  ```
* Comments can added at the end of the line:
  ```rust
  let search_flag = false; // comment
  ```

## Control flow

### if expression
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

### loop expression
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

### for loop
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

  Result:
  ```
  4 3 2 1
  ```
  Here, `1..4` creates an array with values between 1 and 4, and calling `rev` function reverses the order. Then use for loop to iterate through the array.

## Ownership

### Stack and Heap
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

### Ownership Rules
* Each value in Rust has a variable that’s called its owner.
* There can only be one owner at a time.
* When the owner goes out of scope, the value will be dropped.

### Variable Scope
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

### String Type
* String literals are of type `str` and are immutable.
* Strings of type `String` are mutable.
* Convert `str` to `String` via: `let string = String::from('hello');`
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

### Move
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

### Copy, Clone and Drop traits
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
  
### Ownership and Functions
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

### Return Values and Scope
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

### References and Borrowing
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
### Mutable references
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

### Dangling References
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

## The Slice Type
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

## Structs
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
### Field Init Shorthand
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
### Struct Update Syntax
  ```rust
  let user2 = User {
      email: String::from("another@example.com"),
      username: String::from("anotherusername567"),
      ..user1
  };
  ```
  * Rest of the values of `user2` would be used from `user1`.

### Tuple Structs
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

### Unit-Like Structs
  *  You can define structs that don’t have any fields.
  *  These are called unit-like structs because they behave similarly to (), the unit type. 
  *  Useful when you need to implement a trait but don’t have any data you want to store in it.

### Debug structs
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

### Methods
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

### Automatic referencing and dereferencing
  * When you call a method with `object.something()`, Rust automatically adds in `&`, `&mut`, or `*` so object matches the signature of the method. 
  * In other words, the following are the same:
    ```rust
    p1.distance(&p2);
    (&p1).distance(&p2);
    ```
  * This automatic referencing behavior works because methods have a clear receiver—the type of `self`. 
  * Given the receiver and name of a method, Rust can figure out definitively whether the method is reading (`&self`), mutating (`&mut self`), or consuming (`self`). 

### Associated functions
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

## Enums and Pattern Matching
* Rust’s enums are most similar to algebraic data types in functional languages, such as F#, OCaml, and Haskell. Good.

