---
title: Implementing a Syntax Highlighter with Tree-Sitter.
date: 2024-06-08
draft: false
hideMeta: false
tags: ["STEP Writer", "C++"]
ShowCodeCopyButtons: true
ShowWordCount: true
math: true

---
![syntax_highlight](https://github.com/BrianAnakPintar/step-writer/blob/main/Docs/Demo1.png?raw=true)

One of the most important part of a text editor is having a **syntax highlighting** system.
As such, I began looking into different implementations of how one might achieve syntax highlighting.
I narrowed down how I would achieve this with either one of:

 - A Lexer (Using a pre-made library or build one myself).
 - An Abstract Syntax Tree (Using Tree Sitter).

In terms of a lexer, it would read the entire file, concattenated as a string and then it would start parsing from there. This means that when we make a change in our file, we would have to concattenate each row again and reparse the file, this method seems inefficient unless you are using a data structure that allows fast string concattenations such as a rope.

On the other hand, tree-sitter seems to be able to modify it's tree efficiently without having to read the entire text file over and over again. This seems really cool 

## Installing Tree-Sitter in C++.
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
## Understanding Tree-Sitter.
This is my first time interacting with Tree-Sitter, the [documentation](https://tree-sitter.github.io/tree-sitter/using-parsers) helped me get a basic understanding of what's going on.

Essentially there is a tree structure which describes the entire file, I found that the best way to learn this structure is by trying it at the [playground](https://tree-sitter.github.io/tree-sitter/playground), it's actually very interesting with how this is structured cause then I am able to obtain interesting information such as the parent node (i.e. this variable is inside a for loop, etc.)

From the Getting Started page, I was able to see that we can easily create an AST by feeding it the string of the source code.

Here's how I made my AST. Really makes my life easy.
```c++
const char *source_code = docString.c_str();
uint32_t source_code_len = charCount();
tree = ts_parser_parse_string(parser, NULL, source_code, source_code_len);
TSNode root = ts_tree_root_node(tree);
```

So when we have an AST, it contains a lot of valuable information. In my case, I just used the node type to appropriate color the keyword:
```c++
void Document::highlightSyntax(TSNode node) { 
    if (ts_node_is_null(node)) return;

    const char *c_node_type = ts_node_type(node);
    std::string node_type(c_node_type);

    TSPoint start_point = ts_node_start_point(node);
    TSPoint end_point = ts_node_end_point(node);

    if (node_type == "string_literal" || node_type == "system_lib_string" || node_type == "character_literal") {
        std::pair<uint32_t, uint32_t> idx_pair(start_point.column, end_point.column);
        highlight_item item = {idx_pair, highlight_type::String};
        rows[start_point.row].add_highlight_item(item);
    }
    ...
}
```

## A really inefficient way of updating the tree.
So I've been praising tree-sitter for being efficient, and this is mainly because technically we don't need to recreate an AST whenever we make an edit to our source code.
The trick for this is to use an existing function called `ts_tree_edit` where you would pass in your tree and a `TSInputEdit` which describes the edit you made.

However, I got stuck on this step as I couldn't find an example of how this was used. As such here's my buggy attempt of trying to use it. (pls ignore bad img quality)
![Demo.png](https://raw.githubusercontent.com/BrianAnakPintar/step-writer/refs/heads/main/Docs/BUGS.gif)

So as a **temporary solution**, I ended up recreating the tree whenever I would make an edit.
This in turn is probably a horrible decision that will bite me in the future as it is probably going to be extremely inefficient for very large source files.

Thus far, it seems like there's still no issue with performance but it might be cause I'm not really stress testing it much. I expect I will have to revisit this issue again in the future. But for now, this duct tape solution shall live in the codebase until I decide to revisit it again.
