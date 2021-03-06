Minilang
========

Minilang is an intentionally simple language that was designed as the
first assignment in an introduction to compilers class.  Students are
expected to write a scanner, parser, type checker and code generator
(usually generating C code).  The syntax of the language is described
in doc/grammar.ebnf and sample programs can be found under the
examples/ directory.

Minilang-rs
===========

Minilang-rs is my attempt at writing a Minilang compiler in Rust.
I've been interested in Rust for a few years now (I remember when it
was still implemented in OCaml, had a type state system and a garbage
collector), and I figured that a compiler would be a nice task to
finally learn the language.

Design
======

The crude flowchart below describes the overall flow of the compiler.


             (stdin) --> [scanner] --> (tokens) --> [parser] --+
                                                               |
    +-- (Symtable + Exprtable) <-- [type checker] <-- (AST) <--+
    |
    +--> [code generator] --> (stdout)

Modules
-------

Below is a list of the modules of Minilang-rs with a short description
of what they do.

- **error.rs**: contains the enum **Error**, a list of all the errors that
  can occur in the compiler.
- **pos.rs**: contains the struct **Pos** that tracks the line and column of
  tokens and AST nodes.
- **token.rs**: contains the enum **TokenType** and the struct **Token** that
  are generated by the scanner and consumed by the parser.
- **types.rs**: contains the enum **Type** used by the typechecking and code
  generation phases.
- **scanner.rs**: a hand-written scanner.  Reads the input from a String.
- **parser.rs**: a predictive, recursive-descent parser.
- **typecheck.rs**: type checks the program; produces a symbol table
  (map from variable names to types) and an expression table (map from
  expressions to types).  An expression table is used rather than
  writing a new AST that contains types or making the original AST
  parametrized.
- **cgen.rs**: takes the AST, symbol table and expression table and
  generates a C program.
- **main.rs**: connects all the phases together and reports errors when
  they occur.

Possible improvements
=====================

## Less copies

A number of phases create copies (.clone()) that may be
unneecessary. Learn more about lifetime management and see if they
could be replaced with references.

## More streams

The scanner reads from a String and the parser reads from a
Vec<Token>; maybe we could use stream-like structures instead to avoid
reading the whole file in order to find a parse error on the first
line for instance.

## Ugly `if let` pattern in type checker

The `if let` boilerplate in the `tc_*` type checking functions is
pretty ugly.  How could this be improved?  Create a new struct type
for the "content" of each AST node?  Seems to only shuffle the boiler
plate around.
