# Fearless Concurrency
* Concurrent vs Parallel programming:
  * Concurrent: different parts of a program execute independently
  * Parallel: different parts of a program execute at the same time
* By leveraging ownership and type checking, many concurrency errors are compile-time errors in Rust rather than runtime errors.
  * This is called as **Fearless Concurrency** by Rust's marketing team.

# Threads in Rust
* An executed program’s code is run in a process, and the operating system manages multiple processes at once.
* Within your program, you can also have independent parts that run simultaneously. The features that run these independent parts are called **threads**.
* Two models of implementing threads by programming languages:
  * The model where a language calls the operating system APIs to create threads is sometimes called **1:1**, meaning one operating system thread per one language thread.
    * Also called as **Native Threads**.
  * Programming language-provided threads are known as **green threads**
    * Languages that use these green threads will execute them in the context of a different number of operating system threads. 
    * For this reason, the green-threaded model is called the **M:N** model: there are `M` green threads per `N` operating system threads, where `M` and `N` are not necessarily the same number.
* Each model has its own advantages and trade-offs, and the trade-off most important to Rust is *runtime* support.
  * **Runtime** refers to the code that is included by the language in every binary. 
  * Smaller runtimes have fewer features but have the advantage of resulting in smaller binaries, which make it easier to combine the language with other languages in more contexts.
* Rust need to have very small runtime, and so the Rust standard library only provides an implementation of 1:1 threading.
  * There are crates available to implement M:N threading (TODO: mention crate names)

## Create new threads with `spawn`
* Example:
  ```rust
  use std::thread;
  use std::time::Duration;

  fn main() {
      thread::spawn(|| {
          for i in 1..10 {
              println!("hi number {} from the spawned thread!", i);
              thread::sleep(Duration::from_millis(1));
          }
      });

      for i in 1..5 {
          println!("hi number {} from the main thread!", i);
          thread::sleep(Duration::from_millis(1));
      }
  }
  ```
  One possible output:
  ```
  hi number 1 from the main thread!
  hi number 1 from the spawned thread!
  hi number 2 from the main thread!
  hi number 2 from the spawned thread!
  hi number 3 from the main thread!
  hi number 3 from the spawned thread!
  hi number 4 from the main thread!
  hi number 4 from the spawned thread!
  hi number 
  ```
  * The new thread will be stopped when the main thread ends, whether or not it has finished running. 
    * So, as soon as main thread is done printing 4 messages along with delay, it stops and doesn't wait for spawned thread to complete.
  * The calls to `thread::sleep` force a thread to stop its execution for a short duration, allowing a different thread to run. 
  * The threads will probably take turns, but that isn’t guaranteed: it depends on how your operating system schedules the threads.

## Waiting for All Threads to Finish Using `join` Handles
* Example:
  ```rust
  use std::thread;
  use std::time::Duration;

  fn main() {
      let handle = thread::spawn(|| {
          for i in 1..10 {
              println!("hi number {} from the spawned thread!", i);
              thread::sleep(Duration::from_millis(1));
          }
      });

      for i in 1..5 {
          println!("hi number {} from the main thread!", i);
          thread::sleep(Duration::from_millis(1));
      }

      handle.join().unwrap();
  }
  ```
  * The return type of `thread::spawn` is `JoinHandle`. 
  * A `JoinHandle` is an owned value that, when we call the join method on it, will wait for its thread to finish.
  * Calling `join` on `handle` blocks the thread currently running until the thread represented by `handle` terminates.
  * **Blocking a thread** means that thread is prevented from performing work or exiting. 
  * The main thread and `handle` spawned thread keep on alternating (because of `thread::sleep`).
    * However, the main thread waits because of the call to `handle.join()` and does not end until the spawned thread is finished.
* If you put `handle.join()` before `for` loop in `main` function:
  ```rust
  //.. previous code

  // `handle.join()` moved here
  handle.join().unwrap();

  for i in 1..5 {
      println!("hi number {} from the main thread!", i);
      thread::sleep(Duration::from_millis(1));
  }
  ```
  The main thread will wait for the spawned thread to finish and then run its for loop, so the output won’t be interleaved anymore:
  ```
  hi number {1 till 9} from the spawned thread!
  hi number {1 till 4} from the main thread!
  ```
  * Messages clubbed together via `{1 till 9}`, meaning 9 messages were printed with numbers from 1 till 9.

## Using `move` Closures with Threads
* The `move` closure is often used alongside `thread::spawn` because it allows you to use data from one thread in another thread.
* The following code will give an error:
  ```rust
  use std::thread;

  fn main() {
      let v = vec![1, 2, 3];

      let handle = thread::spawn(|| {
          println!("Here's a vector: {:?}", v);
      });

      drop(v); 

      handle.join().unwrap();
  }
  ```
  Error:
  ```
    error[E0373]: closure may outlive the current function, but it borrows `v`, which is owned by the current function
  --> src/main.rs:6:32
    |
  6 |     let handle = thread::spawn(|| {
    |                                ^^ may outlive borrowed value `v`
  7 |         println!("Here's a vector: {:?}", v);
    |                                           - `v` is borrowed here
    |
  ```
  * Rust *infers* how to capture `v`, and because `println!` only needs a reference to `v`, the closure tries to borrow `v`. 
  * However, there’s a problem: Rust can’t tell how long the spawned thread will run, so it doesn’t know if the reference to `v` will always be valid.
  * To make this point clear, we added `drop(v)` right after `handle` thread.
    * The `handle` thread has a reference to `v` but the main thread drops `v` immmediately. 
* To be able to use environment data into `thread::spawn` closure, we use `move` keyword as follows:
  ```rust
  let handle = thread::spawn(move || {
      println!("Here's a vector: {:?}", v);
  });
  ```
  * By adding the move keyword before the closure, we force the closure to take ownership of the values it’s using rather than allowing Rust to infer that it should borrow the values. 
  * If we try to add `drop(v)` after spawning `handle` thread above, we would get following error:
    ```
      error[E0382]: use of moved value: `v`
      --> src/main.rs:10:10
      |
    4 |     let v = vec![1, 2, 3];
      |         - move occurs because `v` has type `Vec<i32>`, which does not implement the `Copy` trait
    5 | 
    6 |     let handle = thread::spawn(move || {
      |                                ------- value moved into closure here
    7 |         println!("Here's a vector: {:?}", v);
      |                                           - variable moved due to use in closure
    ...
    10|     drop(v); // oh no!
      |          ^ value used here after move

    ```
    * By moving ownership of `v` to spawned thread, Rust guarantees that main thread won't be able to use `v` and hence we can't run `drop(v);` after spawning `handle` thread.

# Using Message Passing to Transfer Data Between Threads
* One increasingly popular approach to ensuring safe concurrency is **message passing**, where threads or actors communicate by sending each other messages containing data.
* One major tool Rust has for accomplishing message-sending concurrency is the **channel**, a programming concept that Rust’s standard library provides an implementation of. 
* A channel in programming has two halves: a transmitter and a receiver. 
  * One part of your code calls methods on the **transmitter** with the data you want to send, and another part checks the **receiver** for arriving messages. 
  * A channel is said to be closed if <u>either</u> the transmitter or receiver half is dropped.
* Example:
  ```rust
  use std::sync::mpsc;

  fn main() {
      let (tx, rx) = mpsc::channel();
      thread::spawn(move || {
          let val = String::from("hi");
          // `send` returns Result<T, E> type
          tx.send(val);
      });

      let received = rx.recv().unwrap();
      println!("Got: {}", received);
  }
  ```
  Output of the above code: `Got: hi`

  * We create a new channel using the `mpsc::channel` function; `mpsc` stands for **multiple producer, single consumer**. 
    * In short, the way Rust’s standard library implements channels means a channel can have multiple sending ends that produce values but only one receiving end that consumes those values. 
    * The `mpsc::channel` function returns a tuple, the first element of which is the sending end and the second element is the receiving end. 
    * The abbreviations `tx` and `rx` are traditionally used in many fields for transmitter and receiver respectively, so we name our variables as such to indicate each end. 
    * We’re using a `let` statement with a pattern that destructures the tuples into `tx` and `rx`. 
  * We’re using `thread::spawn` to create a new thread and then using `move` to move `tx` into the closure so the spawned thread owns `tx`. 
    * The spawned thread needs to own the transmitting end of the channel to be able to send messages through the channel.
    * The transmitting end has a `send` method that takes the value we want to send. 
    * The `send` method returns a `Result<T, E>` type, so if the receiving end has already been dropped and there’s nowhere to send a value, the `send` operation will return an error.
      * Notice that we move the variable `val` to `send` method (`tx.send(val)`). 
      * So, we can't do like `println!("{}", val);` after invoking `send` method.
      * This is helpful because since `send` is sending `val`'s data to another thread, we don't want to accidentally drop or modify `val` as it can lead to errors due to inconsistent or nonexistent data.
  * The receiving end of a channel has two useful methods: `recv` and `try_recv`. 
    * `recv` method will block the main thread’s execution and wait until a value is sent down the channel. 
      * Once a value is sent, `recv` will return it in a `Result<T, E>`. 
      * When the sending end of the channel closes, `recv` will return an error to signal that no more values will be coming.
    * The `try_recv` method doesn’t block, but will instead return a `Result<T, E>` immediately: an `Ok` value holding a message if one is available and an `Err` value if there aren’t any messages this time. 
      * Using `try_recv` is useful if this thread has other work to do while waiting for messages: we could write a loop that calls `try_recv` every so often, handles a message if one is available, and otherwise does other work for a little while until checking again.

## Sending Multiple Values and Seeing the Receiver Waiting
* Example:
  ```rust
  use std::sync::mpsc;
  use std::thread;
  use std::time::Duration;

  fn main() {
      let (tx, rx) = mpsc::channel();

      thread::spawn(move || {
          let vals = vec![
              String::from("hi"),
              String::from("from"),
              String::from("the"),
              String::from("thread"),
          ];

          for val in vals {
              tx.send(val).unwrap();
              thread::sleep(Duration::from_secs(1));
          }
      });

      for received in rx {
          println!("Got: {}", received);
      }
  }
  ```
  * In the main thread, we’re not calling the `recv` function explicitly anymore: instead, we’re treating `rx` as an iterator. 
  * For each value received, we’re printing it. 
  * When the channel is closed, iteration will end.
  * Output:
    ```
    Got: hi
    Got: from
    Got: the
    Got: thread
    ```

## Creating Multiple Producers by Cloning the Transmitter
* We can do so by cloning the transmitting half of the channel:
  ```rust
  let (tx, rx) = mpsc::channel();

  let tx1 = tx.clone();
  thread::spawn(move || {
      let vals = vec![
          String::from("hi"),
          String::from("from"),
          String::from("the"),
          String::from("thread"),
      ];

      for val in vals {
          tx1.send(val).unwrap();
          thread::sleep(Duration::from_secs(1));
      }
  });

  thread::spawn(move || {
      let vals = vec![
          String::from("more"),
          String::from("messages"),
          String::from("for"),
          String::from("you"),
      ];

      for val in vals {
          tx.send(val).unwrap();
          thread::sleep(Duration::from_secs(1));
      }
  });

  for received in rx {
      println!("Got: {}", received);
  }
  ```
  Output:
  ```
  Got: hi
  Got: more
  Got: from
  Got: messages
  ```
  * The output is non-deterministic, so the output might be different in your run.

# Shared-State Concurrency
* In a way, channels in any programming language are similar to single ownership, because once you transfer a value down a channel, you should no longer use that value. 
* Shared memory concurrency is like multiple ownership: multiple threads can access the same memory location at the same time.

## Using Mutexes to Allow Access to Data from One Thread at a Time
* **Mutex** is an abbreviation for <u>mutual exclusion</u>, as in, a mutex allows only one thread to access some data at any given time. 
* To access the data in a mutex, a thread must first signal that it wants access by asking to acquire the mutex’s lock. 
* The **lock** is a data structure that is part of the mutex that keeps track of who currently has exclusive access to the data. 
* Therefore, the mutex is described as guarding the data it holds via the locking system.
* Rules of working with a mutex:
  * You must attempt to acquire the lock before using the data.
  * When you’re done with the data that the mutex guards, you must unlock the data so other threads can acquire the lock.

## `Mutex<T>` API
* Example:
  ```rust
  use std::sync::Mutex;

  fn main() {
      let m = Mutex::new(5);

      {
          let mut num = m.lock().unwrap();
          *num = 6;
      }

      println!("m = {:?}", m);
  }
  ```
* We create a `Mutex<T>` using the associated function `new`.
* To access the data inside the mutex, we use the `lock` method to acquire the lock. 
  * This call will block the current thread so it can’t do any work until it’s our turn to have the lock.
* The call to `lock` returns a smart pointer called `MutexGuard`, **wrapped** in a `LockResult`.
* After we’ve acquired the lock, we can treat the return value, named `num` in this case, as a mutable reference to the data inside.
* The `MutexGuard` smart pointer implements `Deref` to point at our inner data (`*num = 6`).
* The smart pointer also has a `Drop` implementation that releases the lock automatically when a `MutexGuard` goes out of scope, which happens at the end of the inner scope.
* As a result, we don’t risk forgetting to release the lock and blocking the mutex from being used by other threads because the lock release happens automatically.
* `Mutex<T>` comes with the risk of **deadlocks**, which occur when an operation needs to lock two resources and two threads have each acquired one of the locks, causing them to wait for each other forever. (TODO: Need example)

## Sharing a Mutex<T> Between Multiple Threads
* We'll write code to spin up 10 threads and have them each increment a counter value by 1, so the counter goes from 0 to 10. 
* We'll start with this code:
  ```rust
  use std::sync::Mutex;
  use std::thread;

  fn main() {
      let counter = Mutex::new(0);
      let mut handles = vec![];

      for _ in 0..10 {
          let handle = thread::spawn(move || {
              let mut num = counter.lock().unwrap();

              *num += 1;
          });
          handles.push(handle);
      }

      for handle in handles {
          handle.join().unwrap();
      }

      println!("Result: {}", *counter.lock().unwrap());
  }
  ```
  * This results in compilation error because `counter` is moved to `thread` closure in the first iteration of `for` loop, so can't move it again to another thread in remaining iterations.
* Reference counter pointer `Rc<T>` is used to give a value to multiple owners. Let's try to fix the above code using `Rc<T>`:
  ```rust
  fn main() {
    let counter = Rc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Rc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();

            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
  }
  ```
  * This also results in compilation error as `Rc<T>` is not-thread safe.
  * The compilation error log contains the text: 
    ```
    the trait `Send` is not implemented for `Rc<Mutex<i32>>`
    ```
  * `Rc<T>` doesn't implement `Send` trait, which is one of the traits that ensures the types can be used in concurrent situations.

## Atomic Reference Counting with `Arc<T>`
* `Arc<T>` is a type like `Rc<T>` that is safe to use in concurrent situations. 
* The `A` stands for atomic, meaning it’s an atomically reference counted type. 
* Atomics work like primitive types but are safe to share across threads.
* The reason all primitives are not atomic by default is because thread safety comes with a performance penalty.
  * You don't want that penalty when doing operations within a single thread.
* We can now fix the above code using `Arc<T>`:
  ```rust
  use std::sync::{Arc, Mutex};
  use std::thread;

  fn main() {
      let counter = Arc::new(Mutex::new(0));
      let mut handles = vec![];

      for _ in 0..10 {
          let counter = Arc::clone(&counter);
          let handle = thread::spawn(move || {
              let mut num = counter.lock().unwrap();

              *num += 1;
          });
          handles.push(handle);
      }

      for handle in handles {
          handle.join().unwrap();
      }

      println!("Result: {}", *counter.lock().unwrap());
  }
  ```
  * Output:
    ```
    Result: 10
    ```

# Extend Concurrency using `Sync` and `Send` Traits
* Two concurrency concepts are embedded in the Rust language: the `std::marker` traits `Sync` and `Send`.

## Transfer Ownership Between Threads with `Send`
* The `Send` marker trait indicates that ownership of values of the type implementing `Send` can be transferred between threads.
* Almost every Rust type is `Send`, but there are some exceptions, like `Rc<T>` which does not implement `Send`.
  * Why? Because when we clone an `Rc<T>` value and tried to transfer ownership of the clone to another thread, both threads might update the reference count at the same time.
  * That's why `Rc<T>` is implemented for use in single-threaded situations where you don’t want to pay the thread-safe performance penalty.
  * When trying to use `Rc<T>` in threads, we get the following error: `the trait Send is not implemented for Rc<Mutex<i32>>`
* `Arc<T>` implements `Send` trait, and hence can be used in threads.
* Any type **composed** entirely of `Send` types is automatically marked as `Send` as well.

## Allowing Access from Multiple Threads with `Sync`
* The `Sync` marker trait indicates that the immutable reference `&T` of the type `T` implementing `Sync` can be safely sent to multiple threads.
  * Here, **safely** means that the reference to the type can be shared safely between threads without worrying about any data race issue.
  * Types like `Rc` which allows interior mutability even with immutable reference `&Rc<T>` cannot be safely shared between the threads and hence does not implement `Sync` trait.
* The `Sync` trait is automatically implemented by the compiler if the type and its constituent types implement `Sync` trait.
* Types implementing `Sync`:
  * Primitive types
  * Types composed of `Sync` types
  * The smart pointer `Mutex<T>`
* Types **not** implementing `Sync`:
  * The smart pointer `Rc<T>`
  * The `RefCell<T>` type and the family of related `Cell<T>` types
    * The implementation of borrow checking that `RefCell<T>` does at runtime is not thread-safe.
