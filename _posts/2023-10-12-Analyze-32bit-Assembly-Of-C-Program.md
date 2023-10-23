---
title: Analyzing 32-bit assembly of a simple C program
date: 2023-10-12 00:00:00
categories: [Programming]
tags: [c, assembly]
---

We'll be analyzing a simple C program that demonstrates a few important concepts for reverse-engineering/exploiting 32 bit binaries.

```c
#include <stdio.h>
#include <string.h>

void func(int x) {
    int a = 0;
    int b = x;
}

int main(void) {
    func(10);
}
```

Using [Godbolt compiler explorer](https://godbolt.org) with x86-64 gcc 13.2 and the `-m32` flag, we generate 32-bit x86 code.

```assembly
func:
        push    ebp
        mov     ebp, esp
        sub     esp, 16
        mov     DWORD PTR [ebp-4], 0
        mov     eax, DWORD PTR [ebp+8]
        mov     DWORD PTR [ebp-8], eax
        nop
        leave
        ret
main:
        push    ebp
        mov     ebp, esp
        push    10
        call    func
        add     esp, 4
        mov     eax, 0
        leave
        ret
```

The `sub esp, 16` instruction adds 16 bytes of free space to the stack frame (by subtracting 16 bytes from esp). This reserves space for local variables
that might be declared in the function. Note: the fact that 16 bytes are reserved is compiler-specific and can change depending on optimization levels, compilers, etc.

Then `mov DWORD PTR [ebp-4], 0` is placing the value `0` into the memory address at `ebp-4`. This is because 32 bits (4 bytes) are needed to represent the decimal value `0` which is assigned to `a`.

The `mov eax, DWORD PTR [ebp+8]` instruction is a tiny bit more complicated. First, why is it referencing `ebp+8`? That's because when a function is called with arguments, the arguments that are passed
to it are the first thing stored on it's stack frame, followed by the return address. On x86_64 32-bit architectures, the return address is stored at `ebp+4`. Since this is a 32-bit binary, `ebp+8` will reference the next value on the stack, which is the argument (`10`) passed to `func()`.

So, the argument at `ebp+8` first has to be moved to a general-purpose register (`eax` in this case) which is done by the `mov eax, DWORD PTR [ebp+8]` instruction.
Why? Because direct memory-to-memory transfers are not supported in the x86 architecture. Without the mov into `eax`, the instruction would look like this: `mov DWORD PTR [ebp-8], DWORD PTR [ebp+8]`, which is invalid.

In conclusion, we delved into the operations of stack frames, register manipulations, and specific assembly instructions. Understanding how the `sub esp, 16` creates stack space for local variables, or how arguments are passed to functions via `ebp+8`, helps in decoding more complex binaries later on. Moreover, we demonstrated why certain instructions are necessary, such as moving values to general-purpose registers before performing operations.
