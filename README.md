# Working with Rust Unsafe Code

> **Source:** [Day 44: Working with Rust Unsafe Code | by Tech Insights Hub | in Cubed - Freedium](https://freedium.cfd/https://blog.cubed.run/day-44-working-with-rust-unsafe-code-7c8ec1dcf76a)

Rust guarantees memory safety using strict compile-time checks and ownership rules. These mechanisms are key to preventing common bugs like data races and buffer overflows. However, there are instances where we must bypass these safety checks for performance reasons or when interacting with hardware directly. Today, we focus on using unsafe code blocks in Rust — detailing when and how to leverage them safely, code examples that illustrate their use, and an exercise to implement an unsafe Rust program.

## When and How to Use Unsafe Code Blocks in Rust

Rust's `unsafe` keyword allows developers to bypass certain safety checks imposed by the compiler. However, this flexibility must be approached cautiously. "_Unsafe_" code is used when we need to:

- **Access Raw Pointers**: In contrast to Rust's reference system, raw pointers are not subject to strict borrowing rules.
- **Call Unsafe Functions or Methods**: Some functions (often foreign functions via FFI — Foreign Function Interface) are marked as `unsafe` because they require a guarantee that the user code fulfills the function's preconditions.
- **Manipulate Mutable Static Variables**: Unlike regular variables, _mutable static_ variables may cause race conditions and require an `unsafe` block to access.
- **Implement Traits that Cannot Be Automatically Verified**: For example, `unsafe trait` implementations bypass certain safety checks.

## Using Unsafe Blocks Safely

When employing `unsafe` blocks, it is crucial to encapsulate and isolate the usage as much as possible. Adopting defensive programming patterns and thorough code reviews helps to ensure that such code does not introduce undefined behavior.

**Basic Syntax of Unsafe Code**

```rust
fn main() {
    let mut num = 5;

    // Raw pointers can only be dereferenced in unsafe blocks
    let r1 = &num as *const i32;
    let r2 = &mut num as *mut i32;
    unsafe {
        println!("r1 is: {}", *r1);
        println!("r2 is: {}", *r2);
    }
}
```

In this code example, `unsafe` blocks are used to dereference raw pointers, a behavior considered "_unsafe_" because the Rust compiler cannot guarantee memory safety for raw pointers.

## Code Examples for Working with Unsafe

**Example 1: Calling an Unsafe Function**

```rust
unsafe fn dangerous() {
    println!("This function is unsafe to call!");
}

fn main() {
    unsafe {
        dangerous();
    }
}
```

In this code, the function `dangerous()` is explicitly marked as `unsafe` and can only be called within an `unsafe` block. The compiler forces this separation to signal potential risks to the developer.

**Example 2: Dereferencing a Raw Pointer**

```rust
fn main() {
    let num = 42;
    let r = &num as *const i32;

    unsafe {
        println!("Dereferenced value: {}", *r);
    }
}
```

**Example 3: Working with Unsafe Traits**

```rust
unsafe trait UnsafeTrait {
    fn unsafe_method(&self);
}

struct MyStruct;
unsafe impl UnsafeTrait for MyStruct {
    fn unsafe_method(&self) {
        println!("Executing unsafe method.");
    }
}

fn main() {
    let my_struct = MyStruct;
    unsafe {
        my_struct.unsafe_method();
    }
}
```

**Example 4: Modifying Mutable Static Variables**

```rust
static mut COUNTER: u32 = 0;

fn increment_counter() {
    unsafe {
        COUNTER += 1;
        println!("Counter: {}", COUNTER);
    }
}

fn main() {
    increment_counter();
    increment_counter();
}
```

The use of _mutable static_ variables is inherently risky and requires special care to prevent data races.

## Exercise: Implement an Unsafe Rust Program

### Objective

Create a program that demonstrates the use of raw pointers, calls to `unsafe` functions, and the manipulation of _mutable static_ variables.

### Task Description

- Create a mutable static variable that represents a counter.
- Implement an `unsafe` function to increment the counter.
- Use raw pointers to read and print the counter value.

**Solution**

```rust
static mut GLOBAL_COUNTER: u32 = 0;

unsafe fn increment_global_counter() {
    GLOBAL_COUNTER += 1;
}

fn main() {
    for _ in 0..5 {
        unsafe {
            increment_global_counter();
            let counter_ptr = &GLOBAL_COUNTER as *const u32;
            println!("Counter Value: {}", *counter_ptr);
        }
    }
}
```

**Explanation**

- The code defines a _mutable static_ variable `GLOBAL_COUNTER` and an `unsafe` function to increment it.
- Within the `main` function, we use `unsafe` blocks to modify the counter and read its value via raw pointers.

## Best Practices When Using Unsafe Code

1. **Minimize the Use of Unsafe Blocks**: Isolate `unsafe` code as much as possible within smaller, well-tested components.
2. **Understand the Invariants**: Only use `unsafe` when you fully understand and can enforce the safety invariants.
3. **Code Review and Testing**: Ensure thorough code reviews and comprehensive testing to identify potential risks and undefined behaviors.
4. **Follow Documentation**: Clearly document why and how `unsafe` code is used to maintain safety guarantees across your codebase.

