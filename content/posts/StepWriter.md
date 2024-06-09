---
title: STEP - WRITER.
date: 2024-06-02
draft: false
hideMeta: false
tags: ["STEP Writer", "C++", "FTXUI", "CMake", "GoogleTest"]
ShowCodeCopyButtons: true
ShowWordCount: true
math: true

---
This Post was Last Updated on: June 8, 2024

[![Themes.png](https://github.com/BrianAnakPintar/step-writer/blob/main/Docs/ThemesStacked.png?raw=true)](https://github.com/BrianAnakPintar/step-writer/blob/main/Docs/ThemesStacked.png)

STEP-Writer is just my own take on writing a text editor, my hope is that as I continue working on this project it will eventually reach the point where I myself would use this application as my go to editor. Additionally, I wish to have this text editor work as a multi-user editor, this means that changes in the application is synced across various users.

This page ideally would be the final "documentation" page for this project. I will try to keep this page up to date with all the recent updates that I make to the repository.

### Acronyms.
As mentioned, my goal is to make this program good enough to be my go to editor.

Currently, STEP is an acronym that's supposed to be `Shared Text Editor Program`. But at this stage it's looking more like `Scuffed Text Editor Program`. Therefore, I will try my best, such that I can finally transition from `Scuffed` $\to$ `Shared`.

# Overview.
STEP-Writer is a TUI text editor written in C++. At its core, its my own version of neovim, a tool that I spend a lot of time playing with the `config` files. 

Unfortunately, I can't seem to connect neovim to my school's remote servers as I can't figure out a way to ssh within neovim nor was I able to install neovim in the remote servers. As the remote servers is important to ssh on

Here is some old demo of the application in action (Current one needs to fix highlighting).
[![Demo.png](https://github.com/BrianAnakPintar/step-writer/blob/main/Docs/Overview.gif?raw=true)](https://github.com/BrianAnakPintar/step-writer/blob/main/Docs/Overview.gif)

# Background:
While this project might not be the next VS Code killer nor would it replace neovim, I think there's some pedagogical values that can be attained working on this project. Moreover, since I am writing this myself I need not conform to any specific norms that some might have in their editors. This means that I am able to customize the crap out of my editor with either the weirdest keybindings that might not make sense to most but makes perfect sense to me.

# Features
Currently this application features some basic text editing functionalities such as

- Normal Mode (Vim-like navigation using `h` `j` `k` `l`)
- Insert Mode (`i`)
- Status bar
- File explorer
- Saving and Quitting (CTRL + P) only when file is modified.
- Syntax Highlighting (Kinda, I need to update the vectors everytime first.)

I haven't outlined the details of these features quite well. As I am still working on improving these. Currently, I'm planning to refactor the codebase as after making several of these features work, it's very surprising to see how much reading one's own code can make yourself reflect on its quality.

# Plans

Firstly, I have been working on various unit tests, I just got started on this so ideally once I finish writing these unit tests, I would have gotten a robust set of tests that I can be confident of when I start refactoring my horribly written program.

There are of course still various plans that I aim to make, the most important features that I aim to achieve quickly is a proper file explorer, file saving, syntax highlighting, a search feature and a working terminal.

Other plans that I wish to add but does not seem like it would matter as much at this point is to swap out the data structure I am using. This [post](https://coredumped.dev/2023/08/09/text-showdown-gap-buffers-vs-ropes/) seems to contain crucial information that I can implement in this project, I am heavily leaning towards using gap buffers but at this point it might be best to complete all other core features before working on this feature.

# Dev-logs?

Thus far I have encountered so many challenges and weird bugs and it has been really fun to overcome these obstacles such as heap and stack allocation bugs (bugs caused by how the stack frame works and it's interactions with using FTXUI), structuring proper code and how one might refactor.

I will probably start writing more about these stories cause I think they are worthwhile to talk about as it will help me in expressing my thoughts regarding similar issues whether it be to others or myself.

# Repository:
GitHub: https://github.com/BrianAnakPintar/step-writer/
{{< githubrepo 
    name="Step Writer" 
    description="GitHub Repository" 
>}}