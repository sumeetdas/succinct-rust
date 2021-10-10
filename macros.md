# Rust Macros

## Procedural Macros
* Procedural macros allow you to run code at compile time that consumes and produces Rust syntax. 
  * You can sort of think of procedural macros as functions from an AST to another AST.
* Procedural macros must be defined in a crate with the crate type of `proc-macro`:
  ```
  [lib]
  proc-macro = true
  ```

### Function-like Procedural Macros
* Function-like macros define macros that look like function calls. 
* Similarly to `macro_rules!` macros, they’re more flexible than functions; for example, they can take an unknown number of arguments.
* Example:
  ```rust
  extern crate proc_macro;
  use proc_macro::TokenStream;
  
  #[proc_macro]
  pub fn make_answer(_item: TokenStream) -> TokenStream {
      "fn answer() -> u32 { 42 }".parse().unwrap()
  }
  ```
  * This creates a macro called `make_answer`.
  * Function name must be equal to the macro name we need to define.
  * To invoke this macro, use `make_answer!();`.
  * These macros are defined by a public function `pub fn` with `proc_macro` attribute and a signature of `(TokenStream) -> TokenStream`.
    * The input `TokenStream` is what's inside the delimitters `(` and `)` during macro invocation.
      * Everything in between `(` and `)` in `make_answer!()`.
    * The output `TokenStream` replaces the entire macro invocation.
* Usage of `make_answer` macro:
  ```rust
  extern crate proc_macro_examples;
  use proc_macro_examples::make_answer;

  make_answer!();

  fn main() {
      println!("{}", answer());
  }
  ```

### Derive macros
* Let's implement a derive macro which will implement a trait named `HelloMacro` to any struct: 
  ```rust
  pub trait HelloMacro {
    fn hello_macro();
  }

  #[derive(HelloMacro)]
  struct Pancakes;

  fn main() {
    Pancakes::hello_macro();
  }
  ```
* Implementation of `HelloMacro`:
  ```rust
  extern crate proc_macro;

  use proc_macro::TokenStream;
  use quote::quote;
  use syn;

  #[proc_macro_derive(HelloMacro)]
  pub fn hello_macro_derive(input: TokenStream) -> TokenStream {
      // Construct a representation of Rust code as a syntax tree that we can manipulate
      let ast = syn::parse(input).unwrap();

      // Build the trait implementation
      let name = &ast.ident;
      let gen = quote! {
        impl HelloMacro for #name {
            fn hello_macro() {
                println!("Hello, Macro! My name is {}!", stringify!(#name));
            }
        }
      };
      gen.into()
  }
  ```
  * The `quote!` macro lets us define the Rust code that we want to return. 
    * `quote!`'s `into` method converts intermediate Rust code into `TokenStream`.
    * The `quote!` macro also provides templating mechanics: we can enter `#name`, and `quote!` will replace it with the value in the variable `name`.
  * The `stringify!` macro used here is built into Rust. 
    * It takes a Rust expression, such as `1 + 2`, and at compile time turns the expression into a string literal, such as `"1 + 2"`.
    * Unlike `print!` and `format!` macros, it does not evaluate the expressions and then convert the result in string. Instead, it converts the expression into a string as is.
  * Type of `ast` is `DeriveInput` (docs [here](https://docs.rs/syn/1.0/syn/struct.DeriveInput.html)).

### Attribute macros
* These macros create attributes with parameters. Example: 
  ```rust
  #[show_streams(bar)]
  fn invoke2() {}
  ```
* Example to create an attribute macro `show_streams`:
  ```rust
  #[proc_macro_attribute]
  pub fn show_streams(attr: TokenStream, item: TokenStream) -> TokenStream {
      println!("attr: \"{}\"", attr.to_string());
      println!("item: \"{}\"", item.to_string());
      item
  }
  ```
  * We need attribute `#[proc_macro_attribute]` added to a  public function with the same name as our desired attribute macro name.
  * This function will have two parameters:
    * `attr: TokenStream`: Contains attributes inside our attribute macro. 
      * Example: for `#[show_streams(multiple => token)]`,  `attr` will contain `multiple => token`.
    * `item: TokenStream`: Contains the item on which we add this attribute macro.
      * For example, in below code:
        ```rust
        #[show_streams{bar}]
        fn invoke2() {}
        ```
        `item` will contain `fn invoke2() {}`.
      * As observed above, delimitters could be `{` and `}` as well.





