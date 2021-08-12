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
* 