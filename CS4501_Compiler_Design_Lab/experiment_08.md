# Experiment 8 - Type checking

**Manual experiment number:** 8

## Program and test inputs

### `mismatch.txt`

```text
int a;
float b;
int c;
a = b + c;
```

### `typecheck.l`

```lex
%option noyywrap nodefault
%{
#include "typecheck.tab.h"
#include <stdlib.h>
#include <string.h>

static char *copy_text(const char *text) {
    size_t length = strlen(text) + 1;
    char *copy = (char *)malloc(length);
    if (copy == NULL) {
        fprintf(stderr, "Out of memory\n");
        exit(EXIT_FAILURE);
    }
    memcpy(copy, text, length);
    return copy;
}
%}

%%
"int"                           { return INT_TYPE; }
"float"                         { return FLOAT_TYPE; }
([0-9]+\.[0-9]+)|(\.[0-9]+)     { return FLOAT_LITERAL; }
[0-9]+                          { return INT_LITERAL; }
[a-zA-Z_][a-zA-Z0-9_]*          { yylval.text = copy_text(yytext); return ID; }
[=+\-*/;()]                      { return yytext[0]; }
[ \t\r\n]+                      { /* skip whitespace */ }
.                               { return yytext[0]; }
%%
```

### `typecheck.y`

```yacc
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int yylex(void);
int yyerror(const char *message);

enum value_type {
    TYPE_UNDEFINED = -2,
    TYPE_MISMATCH = -1,
    TYPE_INT = 1,
    TYPE_FLOAT = 2
};

struct symbol {
    char name[64];
    enum value_type type;
};

static struct symbol table[100];
static int symbol_count = 0;

static const char *type_name(enum value_type type) {
    switch (type) {
        case TYPE_INT: return "int";
        case TYPE_FLOAT: return "float";
        case TYPE_MISMATCH: return "mismatch";
        default: return "undefined";
    }
}

static enum value_type lookup_type(const char *name) {
    int i;
    for (i = 0; i < symbol_count; ++i) {
        if (strcmp(table[i].name, name) == 0) {
            return table[i].type;
        }
    }
    return TYPE_UNDEFINED;
}

static void declare_symbol(const char *name, enum value_type type) {
    if (lookup_type(name) != TYPE_UNDEFINED) {
        printf("Duplicate declaration: %s\n", name);
        return;
    }
    if (symbol_count >= (int)(sizeof table / sizeof table[0])) {
        fprintf(stderr, "Symbol table is full\n");
        exit(EXIT_FAILURE);
    }
    snprintf(table[symbol_count].name, sizeof table[symbol_count].name, "%s", name);
    table[symbol_count].type = type;
    ++symbol_count;
    printf("Declared %s as %s\n", name, type_name(type));
}

static enum value_type combine_types(enum value_type left, enum value_type right) {
    if (left == TYPE_UNDEFINED || right == TYPE_UNDEFINED) {
        return TYPE_UNDEFINED;
    }
    if (left == TYPE_MISMATCH || right == TYPE_MISMATCH || left != right) {
        return TYPE_MISMATCH;
    }
    return left;
}
%}

%define parse.error verbose
%union {
    char *text;
    int type;
}
%token <text> ID
%token INT_TYPE FLOAT_TYPE INT_LITERAL FLOAT_LITERAL
%type <type> expression
%left '+' '-'
%left '*' '/'
%start program

%%
program:
      %empty
    | program statement
    ;

statement:
      INT_TYPE ID ';' {
          declare_symbol($2, TYPE_INT);
          free($2);
      }
    | FLOAT_TYPE ID ';' {
          declare_symbol($2, TYPE_FLOAT);
          free($2);
      }
    | ID '=' expression ';' {
          enum value_type left_type = lookup_type($1);
          enum value_type right_type = (enum value_type)$3;
          if (left_type == TYPE_UNDEFINED) {
              printf("Undefined variable: %s\n", $1);
          } else if (right_type == TYPE_UNDEFINED) {
              printf("Type check failed for %s: expression contains undefined variable(s)\n", $1);
          } else if (right_type == TYPE_MISMATCH || left_type != right_type) {
              printf("Type mismatch in assignment to %s\n", $1);
          } else {
              printf("No type mismatch in expression: %s = ...\n", $1);
          }
          free($1);
      }
    ;

expression:
      ID {
          enum value_type type = lookup_type($1);
          if (type == TYPE_UNDEFINED) {
              printf("Undefined variable: %s\n", $1);
          }
          free($1);
          $$ = type;
      }
    | INT_LITERAL                  { $$ = TYPE_INT; }
    | FLOAT_LITERAL                { $$ = TYPE_FLOAT; }
    | '(' expression ')'           { $$ = $2; }
    | expression '+' expression   { $$ = combine_types($1, $3); }
    | expression '-' expression   { $$ = combine_types($1, $3); }
    | expression '*' expression   { $$ = combine_types($1, $3); }
    | expression '/' expression   { $$ = combine_types($1, $3); }
    ;
%%

int main(void) {
    printf("Enter declarations and expressions:\n");
    return yyparse() == 0 ? EXIT_SUCCESS : EXIT_FAILURE;
}

int yyerror(const char *message) {
    fprintf(stderr, "Syntax Error: %s\n", message);
    return 0;
}
```

### `undefined.txt`

```text
int a;
a = b + 1;
```

### `valid.txt`

```text
int a;
int b;
int c;
a = b * c;
```

## Executed output

```text
EXPERIMENT 8 - TYPE CHECKING
Execution engine: portable semantic runner

--- Valid types ---
$ typecheck < valid.txt
Enter declarations and expressions:
Declared a as int
Declared b as int
Declared c as int
No type mismatch in expression: a = ...
[exit status: 0]

--- Type mismatch ---
$ typecheck < mismatch.txt
Enter declarations and expressions:
Declared a as int
Declared b as float
Declared c as int
Type mismatch in assignment to a
[exit status: 0]

--- Undefined variable ---
$ typecheck < undefined.txt
Enter declarations and expressions:
Declared a as int
Undefined variable: b
Type check failed for a: expression contains undefined variable(s)
[exit status: 0]
```

## Execution provenance

The output above was generated by executing the repository portable harness, which mirrors the lexical, grammar, semantic-action, optimization, or code-generation behavior of the canonical Flex/Bison source shown above. Native Flex/Bison execution is available through the included Colab/workflow pipeline.
