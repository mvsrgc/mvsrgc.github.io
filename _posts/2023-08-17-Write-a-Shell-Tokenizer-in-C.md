---
title: Write a Shell Tokenizer in C (Part 1: Data Structures)
date: 2023-08-17 00:00:00
categories: [Programming, C]
tags: [c, tokenizer, shell]
---

# Write a Shell Tokenizer in C (Part 1: Data Structures)

Note: this post explains how I built a tokenizer for a shell in C. My needs were simple,
and I don't claim to be an expert on language design, grammar or shells. However, this
code has served me well. It is adapted from the Crafting Interpreters book, where
the original code was written in Java.

## What is tokenization?

A tokenizer takes in a raw string and groups series of characters in the string based
on predefined rules.

For example, when you enter `echo "Hello!" && ls -lha 12*.pdf`, the tokenizer's job is to take
that raw string ("echo "Hello!" && ls -lha 12*.pdf") and group it into tokens. The example
string could be grouped like this:

![Tokens]({{ '/assets/tokens.png' | relative_url }})

It's important to know what kind of tokens you want to support, because
your lexer will have to know how to process each one.

## Data Structures

Here's how I defined my Lexeme data structure in `tokenize.h`:
```c
typedef enum {
    STRING,
    NUMBER,
    LESS,
    GREATER,
    GREATERGREATER,
    SEMICOLON,
    OPEN_PARENS,
    CLOSE_PARENS,
    PIPE,
    WORD
    // Add more as needed.
} Lexeme;
```

Great! We know which tokens we want our shell to support. Now we need to
talk about *lexemes* and *literals* so we can actually define our Token
struct.

In language theory, a *lexeme* is an abstract unit that refers to
a basic lexical unit of your language. 
The Lexeme enum actually refers to all the different lexemes that our language supports.

As I said, lexemes by themselves are an abstract concept, they don't have
or hold any value. When we want to talk about a lexeme that can hold a value,
for example, the `STRING` lexeme can hold the value "Hello!", we say that "Hello!"
is a literal of the `STRING` lexeme.

A lexeme can be seen as a class, say, `Plane`, and the literal
is an actual instance of the `Plane` class, it holds data.

So, now that the difference between a *lexeme* and a *literal* is clear,
we can define our Token struct in `tokenize.h`:
```c
typedef struct {
    TokenType type;
    char *literal;
    double value;
    int position;
} Token;
```

A token has a type (lexeme), a literal, a value (which is used when the
lexeme is NUMBER, and finally, a position in the input string.

I decided to use a char *literal that can represent both string literals
and number literals as strings, and a double value which is used to represent
type-casted number values in the case of a string that holds a number.

Finally, when we parse our input string, we want to know a few things.
1. What's the list of tokens that we've parsed so far?
2. How many tokens are there in total?
3. What character position are we currently looking at?
4. What character position does the token we're parsing start at?

We need to differentiate 3 and 4 to account for tokens that span multiple characters,
if we're done processing ">>", the start character is the first >, and the current character
is the second >.

I chose to define a TokenizerState struct because I prefer writing functions that are easily
testable, and having most functions of our lexer take a TokenizerState as an argument
allows for easier testing than the way the author of Crafting Interpreters did in his book (using global state in the class.)

Here's the TokenizerState struct in `tokenize.h`:
```c
typedef struct {
    Token *tokens;
    size_t numTokens;
    size_t capacity;
    char *source;
    size_t current;
    size_t source_length;
    size_t start;
} TokenizerState;
```

TokenizerState allows us to keep track of points 1 through 4, and it has a couple
of extra members that are needed because we're using C (like the capacity
of the tokens array.).

In part 2, we will look at the actual algorithm used to tokenize an input string for a shell.
