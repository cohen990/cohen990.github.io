---
layout: post
title: "Setting Up IntelliJ For Rust"
date: 2017-11-26 16:25:06
description: Instructions on how to set up IntelliJ for rust as an IDE conducive to TDD
tags: 
- TDD
- rust
- IntelliJ
- environments
---
# Setting up IntelliJ for rust

## My Environment

*   Fedora 26
*   Virtual Box 5.2.0r118431
*   High Sierra (host OS) v10.13.1

## Basic Setup

1.  [invent the universe](https://www.youtube.com/watch?v=7s664NsLeFM), followed by computers and the internet
2.  install rust [https://www.rust-lang.org/en-US/install.html](https://www.rust-lang.org/en-US/install.html)
    *   this will give you cargo, rustc, rustup etc
    *   follow instructions for default installation
    *   remember to set up your current shell (instructions at end of installation), if it doesn't work, just reboot your machine
3.  cd to parent directory you want for your project
4.  `cargo new my-project`
5.  install IntelliJ [https://www.jetbrains.com/idea/](https://www.jetbrains.com/idea/)
6.  install "Rust" plugin from repositories
7.  open project in IntelliJ by opening the project directory
    *   intellij may complain about not finding a cargo project
    *   try to attach it to the `cargo.toml` in your project root directory
    *   may not help - IntelliJ tooling is not as good as it will be eventually

There you are - you now have a functional rust environment. With code completion, code generation, syntax highlighting, suggestions and a test runner. Everything you need to get started.  

## Advanced Setup

If you want your environment to do more, here are some of the tools I use to improve the work flow in rust and IntelliJ  

### Format on save ([rustfmt](https://crates.io/crates/rustfmt/))

1.  `cargo install rustfmt`
2.  Install the "Save Actions" plugin in IntelliJ
3.  Configure Save Actions like this
    *   You want to activate save actions only shortcut - IntelliJ does a lot of autosaving which will result in your code being reformated while you are still typing it if you use activate on save
    *   If you use the VIM emulator, `:wa` will not trigger the reformatting - you must use the save shortcut
4.  (Optional) Rebind the default `ctrl+shift+s` shortcut to `ctrl+s` for more convenience

### Clippy linting hinting ([clippy](https://crates.io/crates/clippy))

![picture of clippy from microsoft word](http://images.dailytech.com/nimage/19706_Clippy3.jpg)

Clippy is... clippy but for rust. It will make suggestions about improvements to be made to your code. The only difference between clippy and rust's clippy is that rust's clippy is actually useful.  

1.  `rustup toolchain install nightly`
2.  `rustup run nightly cargo install clippy`
3.  cd to root directory of your project
4.  `rustup run nightly cargo clippy`

### Watch tasks ([cargo-watch](https://crates.io/crates/cargo-watch))

When I'm writing code - I hate having to flip to the terminal to compile and run the tests and ensure it's all good. I like to use watch tasks to continuously run my tests. Rust has an option for this.  

1.  `cargo install cargo-watch`
2.  cd to root directory of your project
3.  `cargo watch -x test`

### Integrating watch and clippy into IntelliJ

This is all well and good, but the watch tasks and clippy aren't integrated into IntelliJ, they're run separately on a terminal, and only one at a time! Well it's possible to bring them into your IDE so everything is in the same place and running together.  

1.  Ensure the "Terminal" plugin is installed and enabled in IntelliJ
2.  open the terminal in IntelliJ
3.  `cargo watch -s "rustup run nightly cargo clippy && cargo test"`

## Done!

Now you have a rust environment capable of  

*   syntax highlighting
*   code completion
*   code generation
*   error highlighting
*   linting suggestions
*   format on save
*   continuous testing