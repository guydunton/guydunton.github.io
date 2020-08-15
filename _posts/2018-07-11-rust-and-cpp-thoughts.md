---
title: Rust and C++ thoughts
---

I recently got into a conversation with someone about Rust, C++ and the direction that each language is taking. It prompted me to take a closer look at Rust and see why it's made such a large splash online.

As a programmer who writes C++ daily I'm used to it's edges and cruft. Mitigating the strange edge cases in C++ requires knowledge of best practices and it's a constant effort to make sure everyone on the team is aware of those.

Rust is a completely different language. Since it's new it feels much cleaner than C++. As a new starter the installation is simple; just go to the website and download the entire toolchain. Writing Rust feels like the stripped back version of C++ that people keep asking for. My favorite is how statements can often be converted into an expression; makes refactoring feel a lot smoother.

```rust
let val: Option<i32> = None;
match val { // <-- Match as a statement. Like if in C++
    Some(v) => println!("value {}:", v),
    None => println!("Nothing")
}

// Can be converted into:

let val: Option<i32> = Some(42);
let valueStr = match val { // <-- An expression used like a C++ turnery operator
    Some(v) => format!("{}", v),
    None => "None".to_owned()
};
```

The major issue that most people seem to have in Rust is the borrow checker getting in the way of code, however I've not really found it to be an issue. Providing you are used to being careful with copies and passing references you shouldn't have to many problems with Rust.

I've still got a long way to go before I feel more comfortable in Rust than in C++ but so far I wouldn't complain if low level programming was done in Rust rather than the slightly creaky C++.
