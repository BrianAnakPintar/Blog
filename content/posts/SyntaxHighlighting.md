---
title: Implementing a Syntax Highlighter with Tree-Sitter in C++.
date: 2024-06-08
draft: true 
hideMeta: false
tags: ["STEP Writer", "C++", "FTXUI", "CMake", "GoogleTest"]
ShowCodeCopyButtons: true
ShowWordCount: true
math: true

---
One of the most important part of a text editor is having a **syntax highlighting** system.
As such, I began looking into different implementations of how one might achieve syntax highlighting.
I narrowed down how I would achieve this with either one of:

 - A Lexer (Using a pre-made library or build one myself).
 - An Abstract Syntax Tree (Using Tree Sitter).

In terms of a lexer, it would read the entire file, concattenated as a string and then it would start parsing from there. This means that when we make a change in our file, we would have to concattenate each row again and reparse the file, this method seems inefficient unless you are using a data structure that allows fast string concattenations such as a rope.

On the other hand, tree-sitter seems to be able to modify it's tree efficiently without having to read the entire text file over and over again. This seems really cool 

## Chapter 1. Installing Tree-Sitter in C++.
Trying to install tree sitter was quite a maze for me when I tried incorporating it into my C++ project as I was used to doing FetchContent to install it. This added with my at the time limited knowledge of CMake I ended up spending hours installing this library. Thankfully, I managed to figure things out by downloading the library and liking it using CMake.

In case anyone is also stuck in this process here's what I did:

1. Create a `third-party` directory and cloned the [tree-sitter](https://github.com/tree-sitter/tree-sitter) and [tree-sitter-cpp](https://github.com/tree-sitter/tree-sitter-cpp) repository there.
2. For each of the libaries, go into their respective directory and run `make`
3. Finally, add the following code into your `CMakeLists.txt` and include them into your project library.

```txt
# Add Tree-sitter library
add_library(ts-lib STATIC
    third-party/tree-sitter/lib/src/lib.c
)

# Include directories for Tree-sitter
target_include_directories(ts-lib PUBLIC
    third-party/tree-sitter/lib/include
    third-party/tree-sitter/lib/src
)

# Add Tree-sitter C++ grammar
add_library(ts-cpp STATIC
    third-party/tree-sitter-cpp/src/parser.c
    third-party/tree-sitter-cpp/src/scanner.c
)

# Include directories for Tree-sitter C++ grammar
target_include_directories(ts-cpp PUBLIC
    third-party/tree-sitter-cpp/src
)

```
## Chapter 2. Understanding Tree-Sitter.
This is my first time interacting with Tree-Sitter, the [documentation](https://tree-sitter.github.io/tree-sitter/using-parsers) helped me get a basic understanding of what's going on.

Essentially there is a tree structure which describes the entire file, I found that the best way to learn this structure is by trying it at the [playground](https://tree-sitter.github.io/tree-sitter/playground), it's actually very interesting with how this is structured cause then I am able to obtain interesting information such as the parent node (i.e. this variable is inside a for loop, etc.)

# TODO: Finish this post...