---
layout: article
title: The Life & Death of Rust Catch
permalink: /articles/life-and-death-of-rust-catch/
image: /assets/life_and_death_of_rust_catch/baseball_ball_glove.jpg
excerpt: The history of a Rust testing framework. From beginning to end.
excerpt: Perhaps it's a right of passage to create a unit testing library. In this article I track the history of the unit testing library I wrote in Rust. From it's initial inception to it's ultimate end.
tags: rust,testing,unit testing
order: 2
---

# The Life & Death of Rust Catch

This is the story of how I ended up writing a Rust unit testing framework and why you shouldn't use it.

> Alternatively, if you'd prefer the **tl;dr**:
>
> - Language extensions can limit your ability to use tools
> - Remember to use abstractions, even in tests
> - Write clear, concise names for tests

## The Beginning

Over Christmas 2018 I was working on a Gameboy Emulator using Rust ([Repo link](https://www.github.com/guydunton/rust-gb)). I was writing all code with unit tests and I found myself getting frustrated how bad my tests were. For example:

```rust
#[test]
fn load8_instructions() {
    {
        // LD (HL-) A
        let mut gb = Gameboy::new(vec![0x32, 0x00]);
        gb.set_register_16(RegisterLabel16::HL, 0x0001);
        gb.set_register_8(RegisterLabel8::A, 0x01);
        let cycles = gb.step_once();

        assert_eq!(gb.get_register_16(RegisterLabel16::HL), 0x0000);
        assert_eq!(gb.get_memory_at(1), 0x01);
        assert_eq!(gb.get_register_16(RegisterLabel16::ProgramCounter), 0x0001);
        assert_eq!(cycles, 8);
    }

    // a local function to test a register
    let ld8_test = |byte_code, register| {
        let mut gb = Gameboy::new(vec![byte_code, 0x01]);
        let _ = gb.step_once();
        assert_eq!(gb.get_register_8(register), 0x01);
        assert_eq!(gb.get_register_16(RegisterLabel16::ProgramCounter), 0x02);
    };

    // LD C d8
    ld8_test(0x0E, RegisterLabel8::C);

    // LD D d8
    ld8_test(0x16, RegisterLabel8::D);

    // This test went on for another 40 LOC btw ...
}
```

There are several things that I don't like with this:

1. The name `load8_instructions` is not descriptive.
2. I've tried to create an abstraction (with _ld8_test_) to make test writing easier but hadn't applied it consistently.
3. When the test fails you don't get anything helpful to track down what went wrong. Instead you get something like the following:

```
---- gameboy::tests::load8_test::load8_test::load8_instructions stdout ----
thread 'gameboy::tests::load8_test::load8_test::load8_instructions' panicked at 'assertion failed: `(left == right)`
left: `0`,
right: `1`', src/gameboy/tests/load8_test.rs:25:13
```

I felt that if I had a more opinionated testing framework I would be forced to write tests which were more more specific with clearer error messages. I had a quick look around but couldn't find anything that would solve all my problems so I set out to write my own testing framework. After all, _how hard could it be?_

## What is Rust Catch

Rust Catch was the testing framework that I came up with. It's based on the C++ testing framework [Catch 2](https://github.com/catchorg/Catch2) and it allowed me to write tests like this:

```rust
tests! {
    // Tests get a nice name
    test("vec capacity change appropriately") {

        // This variable is re-initialized for each section
        let mut vec = vec![1, 2, 3];

        section("push increases capacity") {
            vec.push(4);
            assert_eq!(vec.capacity(), 4);
        }

        section("pop leaves capacity same size") {
            vec.pop();
            assert_eq!(vec.capacity(), 3);
        }
    }
}
```

This solves many of the problems from the previous example:

1. Tests can have a descriptive name
2. There is no special assertion syntax
3. We can share code between testing without using [test fixtures](https://github.com/junit-team/junit4/wiki/test-fixtures). Just familiar in-line code.

> **Editor Note**
>
> You might be thinking "Now, Guy, couldn't you have achieved this by just writing better tests?"
>
> Of course you're right but why do something simple when you can spend weeks on something super duper complicated? :D

## How does it work

Rust Catch doesn't use familiar Rust syntax and so to get it to work involves diving into a relatively new Rust feature: [**Procedural Macros**](https://doc.rust-lang.org/reference/procedural-macros.html#function-like-procedural-macros). These macros essentially re-write the code within "`test! { ... }`" into the standard rust tests. For example:

```rust
tests! {
    test("Some interesting test title") {
        assert_eq!(1, 1);
    }
}
```

becomes:

```rust
#[test]
fn Some_interesting_test_title() {
    assert_eq!(1, 1);
}
```

For more information about how this works see the [repo readme](https://github.com/guydunton/rust-catch/blob/master/README.md#solution).

By using procedural macros I was able to implement everything I wanted Rust catch to do. I could write my test names as simple strings which encouraged my to make them focused on the feature I wanted to test and I could share code between tests without having to introduce abstractions. Everything seemed great...

![Me proud of my new testing framework]({{ "/assets/life_and_death_of_rust_catch/prospector.jpg" }}){: .center-image}

## The Beginning of the End

I ended up using Rust Catch for 2 projects. The first was a gameboy emulator and the second was a ray tracer. I managed to write some really nice tests for both projects but my love for the framework started to wane. There were some things which frustrated me with the library.

### Compile speed

The compile speed became annoying. To use procedural macros it's common to use the library [Syn](https://github.com/dtolnay/syn). Syn parses code sent to the macro into queryable objects. It's a great library but because Rust Catch uses so many modules from Syn compile times end up quite long.

It 7 seconds to clean build Rust catch which isn't necessarily the end of the world but when other issues cropped up I began to re-evaluate whether this was worth it.

### I wasn't using sections

Sections were an interesting feature from Rust Catch. They allow you to re-use code between tests without having to do anything special. In my opinion they are really intuitive and superior to [fixtures from JUnit](https://github.com/junit-team/junit4/wiki/Test-fixtures) or any _beforeEach_/_afterEach_ functions common in many [Javascript frameworks](https://jestjs.io/docs/en/setup-teardown#repeating-setup-for-many-tests).

The main problem was that I didn't really use them. When writing the Rust ray tracer (which I wrote after the emulator) I found I didn't use sections at all. I noticed this and went back to look at my use of sections in Rust-GB and found this:

```rust
test("Flag tests") {
    // Would copying and pasting this line have been so bad?
    let mut gb = Gameboy::new(vec![0xFE, 0x03]);

    section("Z flag is set if result is 0") {
        gb.set_register_8(RegisterLabel8::A, 0x03);
        let _ = gb.step_once();

        assert_eq!(gb.get_flag(Flags::Z), true);
    }

    section("Set the C flag if the value is greater than 0") {
        gb.set_register_8(RegisterLabel8::A, 0x01);
        gb.step_once();
        assert_eq!(gb.get_flag(Flags::C), true);
    }
}
```

It turns out that I'd really used sections to split tests and not to share code at all. I was starting to see that I wasn't getting much value from my framework as I'd hoped.

### Moving from RLS (Rust language server) to Rust Analyzer

The final nail in the coffin for Rust Catch came when I switched from the "[Rust](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust)" extension in VS Code to "[rust-analyzer](https://marketplace.visualstudio.com/items?itemName=matklad.rust-analyzer)". Rust Analyzer gives far superior auto-complete and I found myself really appreciating how helpful it was to be able to hit Ctrl+Space and get suggestions.

Unfortunately the one place I couldn't get suggestions was within my tests. Rust analyzer doesn't give any autocomplete within procedural macros and I found myself switching back to regular tests in order to have that IDE experience.

![Sad looking autocomplete]({{ "/assets/life_and_death_of_rust_catch/macro-autocomplete.png" }}){: .center-image }

## Back to regular tests

There are things I will take forward from Rust Catch even as I leave the code behind.

### 1. Test names are important.

The main thing I enjoyed from Rust Catch was being able to specify my test names as strings. It allowed my to be specific about what my tests were doing, making for much smaller, focused tests.

Going forwards I'm going to keep writing my tests with descriptive names I will just be converting the names into proper functions. For example:

```rust
test("colors can be multiplied together") {}

// Becomes

#[test]
fn colors_can_be_multiplied_together() {}
```

### 2. Fixtures aren't important, abstractions are.

I never really used sections. I've realized that when something is so annoying to write that I don't want to do it multiple times you should probably put it into an abstraction to reduce boilerplate.

It turns out it's ok to write code to make writing your tests easier. In the end you might find that abstraction useful in your application code as well.

### 3. Simplicity is still important in tests

Just because it's possible to extend & twist a language to get it to do your bidding (_Mwa hahaha_) doesn't mean you should. The cost will be things like complexity, compile times and tools interop. I eventually found that the cost of my language extension wasn't worth it.

## Conclusion

So that's it. Rust Catch won't be updated, I've removed it from my ray tracer and will be removing it from my emulator. While it's a little sad that the project wasn't more successful I'm pleased that it's taught me to write better tests.

Thank you for taking the time to read this.

![Goodbye Rust Catch]({{ "/assets/life_and_death_of_rust_catch/baseball.jpg" }}){: .center-image }

## Links

- [Link to Rust Catch repo](https://github.com/guydunton/rust-catch)
- [Link to the gameboy emulator tests](https://github.com/guydunton/rust-gb/tree/master/src/gameboy/tests)
- [Link to the ray tracer](https://github.com/guydunton/rtracer)
