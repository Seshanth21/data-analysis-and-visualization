# Experiment 9 - Simple code optimization

**Manual experiment number:** 9

## Program and test inputs

### `input.txt`

```text
a = 2 + 4;
b = d * 1;
c = s * 2;
d = x + 0;
e = 18 / 3;
```

### `optimize.l`

```lex
%option noyywrap nodefault
%{
#include "optimize.tab.h"
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
[a-zA-Z_][a-zA-Z0-9_]*   { yylval.text = copy_text(yytext); return ID; }
[0-9]+                    { yylval.text = copy_text(yytext); return NUM; }
[=+\-*/;()]                { return yytext[0]; }
[ \t\r\n]+                { /* skip whitespace */ }
.                         { return yytext[0]; }
%%
```

### `optimize.y`

```yacc
%{
#include <errno.h>
#include <limits.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int yylex(void);
int yyerror(const char *message);

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

static char *format_binary(const char *left, char operator, const char *right) {
    size_t size = strlen(left) + strlen(right) + 6;
    char *result = (char *)malloc(size);
    if (result == NULL) {
        fprintf(stderr, "Out of memory\n");
        exit(EXIT_FAILURE);
    }
    snprintf(result, size, "%s %c %s", left, operator, right);
    return result;
}

static int parse_integer(const char *text, long long *value) {
    char *end = NULL;
    long long parsed;
    errno = 0;
    parsed = strtoll(text, &end, 10);
    if (errno != 0 || end == text || *end != '\0') {
        return 0;
    }
    *value = parsed;
    return 1;
}

static char *from_integer(long long value) {
    char buffer[64];
    snprintf(buffer, sizeof buffer, "%lld", value);
    return copy_text(buffer);
}

static char *optimize_binary(char *left, char operator, char *right) {
    long long left_value = 0;
    long long right_value = 0;
    int left_is_number = parse_integer(left, &left_value);
    int right_is_number = parse_integer(right, &right_value);
    char *result = NULL;

    if (left_is_number && right_is_number && !(operator == '/' && right_value == 0)) {
        long long folded = 0;
        switch (operator) {
            case '+': folded = left_value + right_value; break;
            case '-': folded = left_value - right_value; break;
            case '*': folded = left_value * right_value; break;
            case '/': folded = left_value / right_value; break;
            default: break;
        }
        result = from_integer(folded);
        printf("// Constant Folding: %s %c %s -> %s\n", left, operator, right, result);
    } else if (operator == '+' && strcmp(right, "0") == 0) {
        result = copy_text(left);
        printf("// Algebraic Simplification: x + 0 -> x\n");
    } else if (operator == '+' && strcmp(left, "0") == 0) {
        result = copy_text(right);
        printf("// Algebraic Simplification: 0 + x -> x\n");
    } else if (operator == '-' && strcmp(right, "0") == 0) {
        result = copy_text(left);
        printf("// Algebraic Simplification: x - 0 -> x\n");
    } else if (operator == '*' && (strcmp(right, "1") == 0 || strcmp(left, "1") == 0)) {
        result = copy_text(strcmp(right, "1") == 0 ? left : right);
        printf("// Algebraic Simplification: x * 1 -> x\n");
    } else if (operator == '*' && (strcmp(right, "0") == 0 || strcmp(left, "0") == 0)) {
        result = copy_text("0");
        printf("// Algebraic Simplification: x * 0 -> 0\n");
    } else if (operator == '/' && strcmp(right, "1") == 0) {
        result = copy_text(left);
        printf("// Algebraic Simplification: x / 1 -> x\n");
    } else if (operator == '*' && (strcmp(right, "2") == 0 || strcmp(left, "2") == 0)) {
        const char *value = strcmp(right, "2") == 0 ? left : right;
        result = format_binary(value, '+', value);
        printf("// Strength Reduction: x * 2 -> x + x\n");
    } else {
        result = format_binary(left, operator, right);
    }

    free(left);
    free(right);
    return result;
}
%}

%define parse.error verbose
%union {
    char *text;
}
%token <text> ID NUM
%type <text> expression
%left '+' '-'
%left '*' '/'
%start statement_list

%%
statement_list:
      %empty
    | statement_list statement
    ;

statement:
      ID '=' expression ';' {
          printf("%s = %s\n", $1, $3);
          free($1);
          free($3);
      }
    ;

expression:
      NUM                        { $$ = $1; }
    | ID                         { $$ = $1; }
    | '(' expression ')'         { $$ = $2; }
    | expression '+' expression { $$ = optimize_binary($1, '+', $3); }
    | expression '-' expression { $$ = optimize_binary($1, '-', $3); }
    | expression '*' expression { $$ = optimize_binary($1, '*', $3); }
    | expression '/' expression { $$ = optimize_binary($1, '/', $3); }
    ;
%%

int main(void) {
    printf("Enter Three Address Code statements (end with Ctrl+D):\n");
    return yyparse() == 0 ? EXIT_SUCCESS : EXIT_FAILURE;
}

int yyerror(const char *message) {
    fprintf(stderr, "Syntax Error: %s\n", message);
    return 0;
}
```

## Executed output

```text
EXPERIMENT 9 - CODE OPTIMIZATION
Execution engine: portable semantic runner

--- Optimization input ---
$ optimize < input.txt
Enter Three Address Code statements (end with Ctrl+D):
// Constant Folding: 2 + 4 -> 6
a = 6
// Algebraic Simplification: x * 1 -> x
b = d
// Strength Reduction: x * 2 -> x + x
c = s + s
// Algebraic Simplification: x + 0 -> x
d = x
// Constant Folding: 18 / 3 -> 6
e = 6
[exit status: 0]
```

## Execution provenance

The output above was generated by executing the repository portable harness, which mirrors the lexical, grammar, semantic-action, optimization, or code-generation behavior of the canonical Flex/Bison source shown above. Native Flex/Bison execution is available through the included Colab/workflow pipeline.
