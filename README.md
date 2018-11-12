# Overview
This repository is a collection of HDL related tools.

**NOTE**: These tools are currently only recommended to be used by people interested in contributing to their development as they are still immature.

I am interested in collaboration with other people especially regarding semantic analysis of VHDL.

[![Join the chat at https://gitter.im/rust_hdl/Lobby](https://badges.gitter.im/rust_hdl/Lobby.svg)](https://gitter.im/rust_hdl/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://travis-ci.org/kraigher/rust_hdl.svg?branch=master)](https://travis-ci.org/kraigher/rust_hdl)

## Why Rust?
The tools within this repository have been written in the Rust programming language, thus the name `rust_hdl`. 
The Rust programming language was choosen over Python, C or C++ as the best suited language to write the tools in.
The main advantages are; 
- Excellent performance
- Strong modern type system
- Good libraries and tools
- Strong community and momentum

To install rust and its build tool cargo: https://rustup.rs/

# Projects
# VHDL Parser
[![vhdl parser crate](https://img.shields.io/crates/v/vhdl_parser.svg)](https://crates.io/crates/vhdl_parser)
### Goals
- This project aims to provide a fully featured open source VHDL parser that is easy to integrate into other tools.
- A design goal of the parser is to be able to recover from errors such that it is useful for building a language server. 
- Comments will be part of the AST to support document generation.
- Separate parsing from semantic analysis to allow code formatting on non-semantically correct code.

### Current status
- Almost all of VHDL-2008 is parsed, enough to parse the most popular VHDL projects on GitHub:
   - https://github.com/VUnit/vunit
   - https://github.com/OSVVM/OSVVM
   - https://github.com/UVVM/UVVM
   - https://github.com/VLSI-EDA/PoC
   - https://github.com/FPHDL/fphdl/
   - https://github.com/inforichland/freezing-spice (Contains one file with actual syntax error)
   - https://github.com/sergeykhbr/riscv_vhdl
   - https://github.com/fabioperez/space-invaders-vhdl
   - https://github.com/BG2BKK/img_process_vhdl (Contains one file with actual syntax error)
   - https://github.com/kevinpt/vhdl-extras
   - https://github.com/xesscorp/VHDL_Lib
- Good performance, can parse 440k lines of code (all repos above) in 500 ms on my laptop which is 45.5 MB/s of parsing throughput.
- Provides nice error messages such as:
```
error: Expected 'use', 'type', 'subtype', 'shared', 'constant', 'signal', 'variable', 'file', 'component', 'attribute', 'alias', 'impure', 'function', 'procedure', 'package' or 'for'
   --> example.vhd:19
    |
17  |  package pkg2 is
18  |    constant foo : natural := 22;
19 -->   error
    |    ~~~~~
20  |  end package;
21  |  
```

- The parser is a using hand written recursive descent since VHDL is not suitable for parser generators.
- Error recovery is still very rudimentary.
- No semantic analysis is done yet.
- Comments not part of AST yet.

## Trying it out
The VHDL parser has a command line demonstrator which will parse a list of files and print information about the parse results. The command line tool currently only serves as a demonstrator and has no intended usability at this point.

### Example output
```console
> cd rust_hdl/
> cargo build --release
> cargo run --bin vhdl_parser uart_rx.vhd uart_tx.vhd
Results from uart_rx.vhd
entity uart_rx
  with 1 generics
  with 6 ports
  with 6 concurrent statements
architecture a of uart_rx
  with 1 declarations
  with 2 concurrent statements

Results from uart_tx.vhd
entity uart_tx
  with 1 generics
  with 5 ports
  with 6 concurrent statements
architecture a of uart_tx
  with 1 declarations
  with 2 concurrent statements
```

## VHDL Language Server
[![vhdl ls crate](https://img.shields.io/crates/v/vhdl_ls.svg)](https://crates.io/crates/vhdl_ls)
### Goals
- A complete VHDL language server protocol implementation with diagnostics, navigate to symbol, find all references etc.

### Status
- Basic diagnosics based on parse errors and warnings.
- Only full document sync

## Trying it out
The language server has a command line binary `vhdl_ls` which implements a stdio based language server. 

### Building
```console
> cd rust_hdl
> cargo build --release
```
### Use in emacs
Using the language server in `emacs` with the `eglot` language server package it is enough to simply add the following line to your `.emacs.el`:
```elisp
(require 'eglot)
(add-to-list 'eglot-server-programs
             '(vhdl-mode . ("<PATH_TO_RUST_HDL>/target/release/vhdl_ls")))
```
