# 给JavaScript开发者的rust指南

## 简介

People seem to like Rust a lot! But if you're coming from JavaScript, not
everything may make a lot of sense at first. But no problem; this guide is for
you!
看上去Rust很受开发者欢迎！如果你来自于JavaScript社区，起初并不是所有内容都很容易理解，这个指南就是为你准备的！

Because I think Rust and JavaScript are really similar in many ways; to the
point that if you know JS it's mostly a matter of getting the hang of some of
the nuances before you can more or less get the hang of Rust.
因为我们觉得Rust和JavaScript在某些方面非常像；如果你了解JS的话，再了解一些两者之间的细微差别，有助于你去更快地熟悉Rust。

## 快速开始

Alright. So you want to write Rust? Step one is to get yourself a working
environment. This means installing tools. Here's an overview of what you need
(more or less in-order):
如果你想写Rust，第一步就是搭建环境。按顺序安装下面的工具：

### Rustup
类似于Node的`nvm`，不过是官方维护的。它将帮助你安装和管理Rust编译器版本。

安装Rustup的同时也会安装一个编译器工具链，会给你带来三个命令行工具

- `$ rustup`: 运行`rustup`去管理你的编译器工具链
- `$ rustc`: Rust的编译器，你从来不需要手动调用它，因为：
- `$ cargo`: `cargo`类似于Node的`npm`，它负责安装依赖和编译，非常值得花时间去深入了解`cargo`!

### Cargo-edit

[`cargo-edit`](https://github.com/killercup/cargo-edit) provides essential
extensions to `cargo`. In particular: it allows you to run `cargo add` which
works somewhat similar to `npm install`.
[`cargo-edit`](https://github.com/killercup/cargo-edit) 扩展了`cargo`。运行`cargo add`类似于`npm install`

重要：`cargo install`类似于`npm install -g`。`cargo add`只会向你的`Cargo.toml`文件（Rust的`package.json`文件）添加依赖的正确版本。运行`cargo build`或`cargo check`时才会下载和编译依赖。


你可以运行下面的命令去安装`cargo-edit`:
```sh
$ cargo install cargo-edit
```

### Rust fmt

[`rustfmt`](https://github.com/rust-lang/rustfmt) is Rust's version of
[`prettier`](https://github.com/prettier/prettier). Everyone uses it, and even
if the default config might take some getting used to, it's what everyone uses.
[`rustfmt`](https://github.com/rust-lang/rustfmt) 是Rust版本的
[`prettier`](https://github.com/prettier/prettier). 大家都使用它，甚至一些默认配置都使用它

It's a binary component that hooks into the compiler, so it needs to be
installed with `rustup`:
这是一个二进制组件，hook进编译器，所以需要使用`rustup`来安装它

```sh
$ rustup component add rustfmt
```

This should take a few seconds on a fast connection. Whenever you update your
rust version, `rustfmt` will also be updated.
当你更新rust版本时，`rustfmt`也会更新。

Important commands are:
重要的命令：

```sh
$ cargo fmt                   // runs rustfmt to format your code
$ cargo fmt -- --check        // do a dry-run, outputting a diff of changes that would be made
$ cargo fmt -- --edition=2018 // pass this flag if you're doing stuff with async/await
```

```sh
$ cargo fmt                   // 运行rustfmt来格式化代码
$ cargo fmt -- --check        // do a dry-run, outputting a diff of changes that would be made
$ cargo fmt -- --edition=2018 // pass this flag if you're doing stuff with async/await
```

### Clippy

[clippy](https://github.com/rust-lang/rust-clippy) is a "linting" tool for Rust,
similar to [`standard`](https://github.com/standard/standard)'s style lints.
`cargo fmt` takes care of formatting. `rustc` takes care of correctness. But
`clippy` is in charge of helping you write "idiomatic" Rust.

This doesn't mean every lint in Clippy is perfect. But when you're getting
started it can be suuuper helpful to run!

### Editors

Rust has two language-server implementations:
[`rls`](https://github.com/rust-lang/rls) and
[`rust-analyzer`](https://github.com/rust-analyzer/rust-analyzer). Try using
`rust-analyzer` first, as it's a far superior experience. But if it doesn't work
for your setup, it's good to be aware of (the much older, kind of unmaintained)
`rls`.

You'll have to figure out for yourself how you want to set this up. But if
you're undecided, I've heard VSCode is generally straight forward.

### Testing

Cargo ships with a `cargo test` command, which will run both doctests and files
under `test/`. The Rust book has [a whole chapter dedicated to
testing](https://doc.rust-lang.org/1.30.0/book/second-edition/ch11-00-testing.html)
you should read on this. But it's good to know this is provided for you out of
the box!

### Creating new projects

You can create new projects using `cargo new` or `cargo init`. `new` creates a
new directory, `init` outputs files in the current directory. It's pretty basic,
but it's useful to get started with. If you want to write a library you can pass
either command the `--lib` flag. By default you'll create binaries (applications
with a `main` function that can be run).

There's also the newer
[`cargo-generate`](https://github.com/ashleygwilliams/cargo-generate) project.
This is a more powerful version of the built-in `cargo` commands, and allows you
to pick from templates. You may not need this if you're just messing around, but
it's probably good to be aware of.

### Publishing

`cargo publish` works like `npm publish`. The central repository is called
[crates.io](https://crates.io) and is very similar to NPM. Importantly it's not
owned by a scummy for-profit company, but is instead part of the Rust project.

If you've built something nice in Rust, consider going ahead and publishing it
to Crates.io. All that's needed is a GitHub account to sign up, and you're good
to go!

### Documentation

Most docs in JS seem to either be written in a README.md, or as part of some
special website. In Rust documentation is generated automatically using `rustdoc`.

You can run `rustdoc` through `$ cargo doc`. Every package on crates.io also has
documentation generated for you on [docs.rs](https://docs.rs). It's even
versioned, so you can check out older documentation too. For example: you can
find `async-std`'s docs under [docs.rs/async-std](https://docs.rs/async-std).

Writing docs in Rust is by using "doc comments" (`///` instead of the regular
`//` comments). You'll see a bunch in the rest of this guide. Important
documentation commands are:

```sh
$ cargo doc           // generate docs
$ cargo doc --open    // generate docs and then open them
$ rustup doc --std    // open the stdlib docs offline
$ rustup doc --book   // open the "Rust Programming Language" offline
```

### Cargo-watch

It can sometimes be tedious to run `cargo check` after every change. Which is
why [`cargo-watch`](https://github.com/passcod/cargo-watch) exists. You can
install it by running:

```sh
$ cargo install cargo-watch
```

Important `cargo-watch` commands are:

```sh
$ cargo watch             // Run "cargo check" on every change
$ cargo watch -x "test"   // Run "cargo test" on every change
```

## Terminology

Before we continue, let's establish some quick terminology:

- __Struct:__ like an "object" in JS. It can both be a data-only type. But can
    also work like a "class" with methods (both inherent and static).
- __Vec:__ like a JS "array".
- __Slice:__ like a ["view" into a `TypedArray` in
    JS](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays).
    You can't grow them, but you can create a "view of a view"
    (slice of a slice).
- __Traits:__ essentially the answer to the question: "what if a class could
    inherit from multiple other classes". Traits are also referred to as
    "mixins" in other languages. They don't allocate any data, but only provide
    methods and type definitions related to those methods.

## Thinking in Rust

Aside of the obvious type system stuff, I think there are a few core differences
between Rust and JS:

### Object-Oriented everything
In Rust *everything* is object-oriented. Imports are always done through
namespaces, and namespaces kind of behave like structs.

```rust
// println comes from the "std" namespace -- print something to the screen.
std::println!("hello world");

// Call the "new" method from the `HashMap` type from the `std::collections`
// namespace
let hashmap = std::collections::HashMap::new();
```

### Classes & Structs
Structs don't have "constructor" methods the way JS do; instead you define a
method that returns `Self` (which is a shorthand for the name of the struct).

```js
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}
```

```rust
pub struct Rectangle {
    height: usize,
    width: usize,
}

impl Rectangle {
    /// Create a new instance.
    pub fn new(height: usize, width: usize) -> Self {
        Self { height, width }
    }
}
```

### Expressions!
Everything is an expression. Which is to say: blocks suddenly have a lot more
meaning, and you can do some fun substitutions.

These are all equivalent:
```rust
let y = 1 + 1;
let x = y * y;
if x == 4 {
    println!("hello world");
}
```

```rust
// If we omit the `;` from a statement, it becomes the return value of the
// block it's in.
let x = {
    let y = 1 + 1;
    y * y
};
if x == 4 {
    println!("hello world");
}
```


```rust
// Expressions means that you can inline almost anything. Don't actually do this
// please.
if {
    let y = 1 + 1;
    y * y
} == 4 {
    println!("hello world");
}
```

### Know your `self`
There are 3 kinds of self: `self`, `&self`, `&mut self`.

```rust
pub struct Rectangle {
    height: usize,
    width: usize,
}

impl Rectangle {
    pub fn new(height: usize, width: usize) -> Self {
        Self { heigh, width }
    }

    /// Get the height
    ///
    /// We want to reference the value as read-only,
    /// so we use `&self` for shared access.
    pub fn height(&self) -> usize {
        self.height
    }

    /// Set the height
    ///
    /// We want to reference the value as "writable", so we use
    /// `&mut self` for exclusive access.
    pub fn set_height(&mut self, height: usize) -> usize {
        self.height = height
    }

    /// Get the height + width as a tuple.
    ///
    /// We want to "consume" the current struct, and return its internal parts.
    /// So instead of taking a reference, we take an owned value `self` after
    /// which the struct can no longer be used, and return a tuple (anonymous
    /// struct) containing its internals.
    pub fn parts(self) -> (usize, usize) {
        (self.height, self.width)
    }
}
```

This is the core of everything around the borrow checker. If you have exclusive
access to a variable, nobody else can have access to that variable too and you
can mutate it. If you have shared access to a variable, others may too, but
you're not allowed to update the value. That's how data races are prevented!

There's some escape hatches using `RefCell`, `Mutex` and other things to get
around this; but they apply clever tricks internally to uphold the same
guarantees at runtime rather than compile-time. Less efficient, but same rules!

That's it! Everything else is basically an application of these rules.

### Options
Instead of using `opts` or default values, most things use builders instead.
Kind of the way [`superagent`](https://github.com/visionmedia/superagent) works:

```js
let opts = {
  method: 'GET',
  headers: {
    'X-API-Key': 'foobar',
    'accept': 'json'
  }
};

try {
  let res = await fetch('/api/pet', opts);
} catch(err) {
  throw err
}
```

```js
superagent.post('/api/pet')
  .set('X-API-Key', 'foobar')
  .set('accept', 'json')
  .end((err, res) => {
    // Calling the end function will send the request
  });

```

```rust
let res = surf::post("/api/pet")
    .set_header("X-API-Key", "foobar")
    .set_header("accept", "json")
    .await?; // Calling .await will send the request
```

Internally builders generally take `self`, and return `self` as the output so
you can chain the methods together.

### `?` and `.await`

In Rust:
- `?` is for error handling; it says: "if it's an error, return the error, if
    it's not an error, return the value".
- `.await` is like `await` but you can chain it just like any other property.

Speaking of `.await` though. Do yourself a big favor and start with
_synchronous_ Rust first. Starting with async Rust is going full throttle on
hard mode, and I can guarantee it's going to be confusing.

Start easy. Do synchronous Rust for a bit. Skip HTTP. Don't do timeouts. Try,
uhh, other things first. Loops, and printing stuff to the console. Maybe even
borrowing if you're feeling cheeky.

## Outro

Hopefully this is somewhat useful for JS peeps looking at Rust. There's a lot
more that should be written here, but hopefully this is somewhat helpful!

## License
[MIT](./LICENSE-MIT) OR [Apache-2.0](./LICENSE-APACHE)
