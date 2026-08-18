# Source alignment and necessary corrections

The attached CS4501 manual defines 10 experiments and supplies sample FLEX/BISON programs. The executable versions in this repository use those programs as the specification, but several minimal corrections were required for modern toolchains and for the examples to behave as described.

| Experiment | Manual alignment | Reproducibility correction |
|---:|---|---|
| 1 | Recognizes identifiers, constants, comments and operators and prints a symbol table. | Added `%option noyywrap`, safe bounded symbol insertion, decimal constants, multi-character operators, and reliable block-comment matching. |
| 2 | Recognizes preprocessor directives, headers, keywords, identifiers, numbers, operators and delimiters. | Added file-open validation, comments, decimal numbers, multi-character operators, and `%option noyywrap`. |
| 3 | FLEX tokens plus BISON grammar for `+ - * /`, parentheses and unary minus. | Added a complete start rule, clean newline/EOF handling, prototypes, deterministic exit codes, and verbose parser diagnostics. |
| 4 | A variable must start with a letter and continue with letters or digits. | Retained the letter/digit token grammar while adding a complete start rule and deterministic valid/invalid exit behavior. |
| 5 | Recognizes `if`, `if-else`, `else-if`, `while`, `for`, and `switch-case`. | The manual grammar does not accept its own sample block `{ y = 10; }` because it lacks blocks and assignment statements. The repository adds blocks, assignments, expressions, `break`, dangling-`else` precedence, and complete `for`/`switch` rules. |
| 6 | Calculator for `+ - * /`. | Replaced the global `#define YYSTYPE` pattern with a typed BISON `%union`, corrected the statement grammar, added parentheses/unary minus and division-by-zero handling. |
| 7 | Generates temporary variables for arithmetic expressions and emits final assignment. | Added typed semantic values, safe dynamic strings, parentheses, deterministic memory handling, and semicolon-based input. |
| 8 | Builds a symbol table and checks declaration/assignment types. | Added typed semantic values, integer and floating literals, duplicate declaration checks, undefined-variable propagation, and deterministic diagnostics. |
| 9 | Performs constant folding, algebraic simplification and strength reduction. | Added safe integer parsing, dynamic output strings, division-by-zero protection, symmetric simplifications, parentheses and multiple statements. |
| 10 | Translates TAC assignments to 8086-style instructions. | Restricted each right-hand side to true three-address form (`operand` or `operand op operand`), added numeric operands, and emitted deterministic `MOV/ADD/SUB/MUL/DIV` sequences. |

These corrections are explicitly documented rather than silently presented as verbatim manual code.
