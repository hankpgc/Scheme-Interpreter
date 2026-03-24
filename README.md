[README.md](https://github.com/user-attachments/files/26199733/README.md)
# OurScheme Interpreter

A Scheme language interpreter implemented from scratch in C++ (~3,000 lines) as a Programming Languages course project. Supports a substantial subset of standard Scheme, including S-expression parsing, tree-walking evaluation, and a symbol table for variable binding.

---

## Features

- **Lexer (Scanner)**: Tokenizes raw input character by character, tracking line and column numbers for precise error reporting
- **Parser**: Recursive-descent parser that builds a token stream from S-expressions, handling nested lists, dotted pairs, and quote syntax sugar
- **S-expression Tree**: Translates token streams into binary cons-cell trees (`mleft` / `mright` pointer structure) representing all Scheme data
- **Evaluator**: Tree-walking evaluator that recursively evaluates S-expressions, dispatching to 74 built-in procedures
- **Symbol Table**: Global binding table supporting `define`, symbol lookup, redefinition, and `clean-environment`
- **Pretty Printer**: Recursive formatter that outputs S-expressions with correct indentation and dotted-pair notation

---

## Supported Procedures

| Category | Procedures |
|---|---|
| List operations | `cons`, `list`, `car`, `cdr` |
| Type predicates | `atom?`, `pair?`, `list?`, `null?`, `integer?`, `real?`, `number?`, `string?`, `boolean?`, `symbol?` |
| Arithmetic | `+`, `-`, `*`, `/` |
| Comparison | `>`, `>=`, `<`, `<=`, `=` |
| Logic | `and`, `or`, `not` |
| String | `string-append`, `string>?`, `string<?`, `string=?` |
| Equality | `eqv?`, `equal?` |
| Control flow | `if`, `cond`, `begin` |
| Environment | `define`, `clean-environment`, `exit` |
| Quote | `'` (quote syntax sugar) |

---

## Data Types

| Type | Examples |
|---|---|
| Integer | `42`, `-7`, `+100` |
| Float | `3.14`, `.5`, `1.` |
| String | `"hello world"` |
| Boolean | `#t`, `#f` (also `t`, `nil`) |
| Symbol | `x`, `my-var` |
| Nil / Empty list | `nil`, `'()`, `#f` |
| Pair / List | `(1 . 2)`, `(1 2 3)` |

---

## Build & Run

### Requirements
- C++ compiler (g++ recommended)

### Compile
```bash
g++ -o OurScheme Proj2_Final.cpp
```

### Run (interactive mode)
```bash
./OurScheme
```

### Run with input file
```bash
./OurScheme < input.txt
```

> **Note**: The program reads a test case number from the first line of input when `CALTEST` is set to `true` (default). If running interactively without a test number, set `#define CALTEST false` before compiling.

---

## Usage Example

```
Welcome to OurScheme!

> ( define x 10 )
x defined

> ( define y 20 )
y defined

> ( + x y )
30

> ( car '( 1 2 3 ) )
1

> ( cond ( ( > x 5 ) 'big ) ( else 'small ) )
big

> ( clean-environment )
environment cleaned

> ( exit )

Thanks for using OurScheme!
```

---

## Error Handling

The interpreter provides descriptive error messages with line and column information:

| Error | Example message |
|---|---|
| Unexpected token | `ERROR (unexpected token) : ')' expected when token at Line 3 Column 5 is >>abc<<` |
| Unbound symbol | `ERROR (unbound symbol) : foo` |
| Wrong argument count | `ERROR (incorrect number of arguments) : car` |
| Wrong argument type | `ERROR (car with incorrect argument type) : 3` |
| Non-list application | `ERROR (non-list) : ( 1 . 2 )` |
| Non-function application | `ERROR (attempt to apply non-function) : 42` |
| Division by zero | `ERROR (division by zero)` |
| Unclosed string | `ERROR (no closing quote) : END-OF-LINE encountered at Line 2 Column 7` |
| No more input | `ERROR (no more input) : END-OF-FILE encountered` |
| DEFINE format error | `ERROR (DEFINE format) : ...` |
| COND format error | `ERROR (COND format) : ...` |
| DEFINE outside top level | `ERROR (level of DEFINE)` |
| EXIT outside top level | `ERROR (level of EXIT)` |

---

## Project Structure

```
Proj2_Final.cpp       # All source code in a single file
│
├── Token class       # Lexer, Parser, Tree builder, Pretty Printer
│   ├── GetAToken()       - Lexical analysis / tokenizer
│   ├── IsSexp()          - Recursive-descent S-expression parser
│   ├── Translate()       - Quote expansion and .nil insertion
│   ├── BuildCons()       - Constructs cons-cell binary tree
│   ├── AddNode()         - Recursive tree node builder
│   └── PrettyPrint()     - Recursive output formatter
│
├── EvaluateSExp()    # Tree-walking evaluator
├── IsAFunction()     # Built-in function dispatcher
├── gSymbolTable      # Global symbol table (vector of BindingSymbol)
│
└── Built-in functions
    ├── Cons, List, Car, Cdr
    ├── Define, CleanEnvironment
    ├── IsAtom, IsPair, IsList, IsNull, IsInt, IsReal ...
    ├── Add, Minus, Mul, Div
    ├── And, Or, Not
    ├── If, Cond, Begin
    └── StringAppend, IsEeqv, IsEqual ...
```

---

## Implementation Notes

- **Quote handling**: The `'x` syntax is expanded into `(quote x)` during the `Translate()` phase before tree construction
- **Dotted pair normalization**: `(A B C)` is internally transformed to `(A . (B . (C . nil)))` during translation
- **define redefinition**: Re-defining an existing symbol updates its binding in-place in the symbol table
- **cond else**: The `else` keyword is treated as a special token only when it appears as the first element of the last `cond` clause; otherwise it behaves as a normal symbol
- **Procedure values**: Built-in functions can be bound to symbols (e.g., `(define f +)`) and applied via the `#<procedure name>` representation
