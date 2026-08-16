+++
title = "Finding a Serial Key in a Binary and Patching It"
date = 2026-03-03
draft = false
tags = ["reversing"]
description = "Analyzing a simple crackme to recover the serial key and patch the binary to always show success."
+++

# Finding a Serial Key in a Binary and Patching It
Today we’ll analyze a simple crackme from **crackinglessons.com – Crackme #1**

## Objective
1. Identify the correct serial key.
2. Patch the binary so it always displays the **“Congrats!”** message when the *Check* button is clicked.

# Understanding the Program Behavior
When we run the program, it asks:

**“Please enter the serial key”**

If we enter an incorrect value, a message box appears:

**“Wrong serial key. Try again.”**

So our first goal is simple: find where that message is triggered and trace backward to locate the comparison logic.

# Locating the Failure Message
Using **x64dbg**, we search for the string:

```plaintext
"Wrong serial key. Try again."
```

This leads us to address:

```plaintext
00401159
```

Scrolling up from that location reveals the surrounding control flow. We can see two clear branches:

* One path pushes `"Congrats!"` and `"Well done!"`
* The other pushes `"Sorry"` and `"Wrong serial key. Try again."`

That means somewhere above this section, the program performs a comparison and decides which message to display.

Here is the critical portion:

```asm
; address    bytes           instruction
00401101     B9 D81A4100     mov ecx, crackme1.411AD8
00401106     8D45 D0         lea eax, [ebp-30]
00401110     8A10            mov dl, byte ptr [eax]
00401112     3A11            cmp dl, byte ptr [ecx]
00401114     75 1A           jne 401130
```

This is where things get interesting.

# Identifying the Hardcoded Serial
At address:

```asm
; address    bytes           instruction
00401101     B9 D81A4100     mov ecx, crackme1.411AD8
```

Looking at the memory at `411AD8`, we see:

```plaintext
"cr4ckingL3ssons"
```

That means:
- `ECX` → points to the hardcoded correct serial
- `EAX` → points to the user input buffer (`[ebp-30]`)

Immediately after that, we see:

```asm
mov dl, [eax]
cmp dl, [ecx]
jne fail
```

This is a classic byte-by-byte string comparison.


# Understanding the Comparison Loop

The program compares:

```plaintext
User input  <->  "cr4ckingL3ssons"
```

It does this two bytes at a time:

```asm
mov dl, [eax]
cmp dl, [ecx]
jne fail

test dl, dl
je success

mov dl, [eax+1]
cmp dl, [ecx+1]
jne fail

add eax, 2
add ecx, 2
jne compare_loop
```

What this means:

* If any character differs -> jump to failure
* If the null terminator is reached and all bytes matched -> success

This is like:

```c
strcmp(input, "cr4ckingL3ssons")
```

So the valid serial key is:

```plaintext
cr4ckingL3ssons
```

Entering this value results in:

> **Congrats!**
> **Well done!**

The crackme is now solved.

# Patching the Binary

Now for the second objective: make it always show “Congrats!” regardless of input.

We look at this conditional jump:

```asm
; address    bytes    instruction
00401137     85C0     test eax, eax
00401139     75 19    jne 401154
```

Interpretation:

* `eax == 0` → correct serial
* `eax != 0` → wrong serial

The `jne` instruction jumps to the failure message.

### Patch Strategy

We can modify:

```plaintext
75 19  (JNE)
```

Into:

```plaintext
90 90  (NOP NOP)
```

or change it to:

```plaintext
EB 19  (JMP)
```

This forces execution to always continue to the success block.

After patching and saving the binary, clicking **Check** will always display:

- **Congrats!**
- **Well done!**

Even with incorrect input.


