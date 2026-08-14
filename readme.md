# CS312 Operating Systems — Lab 1

## Overview

This lab covers:

1. Linux/terminal commands
2. `man`, pipes, and command-line tools
3. C compilation
4. Command-line arguments
5. Pointers and pass-by-value
6. Makefiles
7. Basic file descriptors and system-call concepts

---

# 1. Explore Your Computer from the Terminal

## (a) List Contents of Home Directory

```bash
ls
```

Long format:

```bash
ls -l
```

Include hidden files:

```bash
ls -la
```

Useful variants:

```bash
ls -lh      # human-readable file sizes
ls -lt      # sort by modification time
ls -ltr     # reverse time order
```

### What does `ls -l` show?

Example:

```text
-rw-r--r--  1 user staff  1024 Aug 14 12:30 file.txt
```

| Field | Meaning |
|---|---|
| `-rw-r--r--` | Permissions |
| `1` | Number of hard links |
| `user` | Owner |
| `staff` | Group |
| `1024` | Size in bytes |
| `Aug 14 12:30` | Modification time |
| `file.txt` | Filename |

### `man ls`

```bash
man ls
```

`man` means **manual**. It displays documentation for commands.

Exit with:

```text
q
```

---

# 2. List Files Sorted by Modification Time

The question asks for the latest modification at the bottom.

```bash
ls -ltr
```

Breakdown:

```text
-l    long format
-t    sort by modification time
-r    reverse the order
```

Normally:

```bash
ls -lt
```

shows newest first.

Therefore:

```bash
ls -ltr
```

shows oldest → newest.

---

# 3. CPU, RAM, and Storage Information

## CPU Information

```bash
lscpu
```

Important fields include:

```text
Model name
CPU MHz
CPU max MHz
CPU(s)
Core(s) per socket
Thread(s) per core
Architecture
```

You can also use:

```bash
cat /proc/cpuinfo
```

### CPU Clock Speed

For example:

```text
CPU max MHz: 4200
```

means the CPU can operate up to approximately **4.2 GHz** under the relevant conditions.

---

## RAM

```bash
free -h
```

Example:

```text
               total    used    free
Mem:            15Gi     5Gi     8Gi
```

`-h` means **human-readable**.

Another command:

```bash
cat /proc/meminfo
```

---

## Storage

```bash
df -h
```

Example:

```text
Filesystem      Size  Used Avail Use%
/dev/nvme0n1   500G  120G  380G  24%
```

`df` = disk filesystem information.

For disks:

```bash
lsblk
```

or:

```bash
sudo fdisk -l
```

### HDD vs SSD

```bash
lsblk -o NAME,ROTA,SIZE,TYPE
```

Generally:

```text
ROTA = 1  → rotational disk → HDD
ROTA = 0  → non-rotational → usually SSD
```

---

# 4. Linux Distribution and Kernel Version

## Distribution

```bash
cat /etc/os-release
```

Example:

```text
NAME="Ubuntu"
VERSION="24.04 LTS"
```

Another command:

```bash
lsb_release -a
```

## Kernel Version

```bash
uname -r
```

Example:

```text
6.8.0-40-generic
```

More complete information:

```bash
uname -a
```

### Important Distinction

**Linux distribution ≠ Linux kernel**

For example:

```text
Ubuntu
   ↓
Linux kernel
   ↓
6.8.x
```

Ubuntu is the distribution; Linux is the kernel.

---

# 5. Find IP Address and Test Internet

## IP Address

Modern Linux:

```bash
ip addr
```

Short form:

```bash
ip a
```

Look for something like:

```text
inet 192.168.1.25/24
```

You can also use:

```bash
hostname -I
```

## Test Internet Connectivity

```bash
ping google.com
```

Example:

```text
64 bytes from ...
time=20 ms
```

Stop with:

```text
Ctrl+C
```

### Important Distinction

```text
IP address
    ↓
Identifies your machine/interface on a network

ping
    ↓
Tests whether packets can reach another machine
```

---

# 6. Find Python Versions

Find Python executables:

```bash
which python
```

```bash
which python3
```

Check versions:

```bash
python --version
```

```bash
python3 --version
```

Find Python installations:

```bash
whereis python
```

You can also use:

```bash
which -a python
```

Example:

```text
/usr/bin/python3
/usr/local/bin/python3
```

### Which Python Runs?

The shell searches directories listed in:

```bash
echo $PATH
```

in order.

For example:

```text
/usr/local/bin:/usr/bin:/bin
```

If:

```text
/usr/local/bin/python
```

exists, it is generally found before:

```text
/usr/bin/python
```

---

# 7. Basic Linux Commands

The lab asks you to study the `man` pages for `cp`, `mkdir`, `mv`, and `rm`.

## `cp` — Copy

```bash
cp file1.txt file2.txt
```

Copy directory:

```bash
cp -r dir1 dir2
```

---

## `mkdir` — Make Directory

```bash
mkdir test
```

Create nested directories:

```bash
mkdir -p a/b/c
```

---

## `mv` — Move/Rename

Rename:

```bash
mv old.txt new.txt
```

Move:

```bash
mv file.txt Documents/
```

---

## `rm` — Remove

```bash
rm file.txt
```

Remove directory recursively:

```bash
rm -r directory
```

Force removal:

```bash
rm -rf directory
```

> ⚠️ `rm -rf` is dangerous because it permanently deletes files/directories without asking.

---

# 8. `man` Pages and `wc`

Study:

```bash
man wc
```

`wc` = **word count**.

Run:

```bash
wc input.txt
```

Typical output:

```text
10  50  300 input.txt
```

The three numbers are:

```text
10   → lines
50   → words
300  → bytes
```

Explicit options:

```bash
wc -l input.txt     # lines
wc -w input.txt     # words
wc -c input.txt     # bytes
```

For two files:

```bash
wc input.txt input2.txt
```

This gives statistics for each file and a total.

---

# 9. Pipes

Example:

```bash
cat input2.txt | wc
```

## What Happens?

```text
input2.txt
    ↓
   cat
    ↓
stdout
    ↓
   pipe
    ↓
   wc
```

`|` is called a **pipe**.

The output of the command on the left becomes the input of the command on the right.

For example:

```bash
cat input2.txt | wc -l
```

means:

> Read `input2.txt`, send its contents through a pipe to `wc`, and count the lines.

This can be simplified to:

```bash
wc -l input2.txt
```

But the pipe is important because pipes are a fundamental Unix IPC mechanism.

---

# 10. C Programs and Compilation

The basic process is:

```text
C source code
     ↓
   gcc
     ↓
 executable
     ↓
 ./program
```

Example:

```c
#include <stdio.h>

int main() {
    printf("Hello World\n");
    return 0;
}
```

Compile:

```bash
gcc hello.c -o hello
```

Run:

```bash
./hello
```

Output:

```text
Hello World
```

---

# 11. GCC Options

The lab asks you to understand:

```text
-Wall
-o
-c
-S
```

## `-Wall`

Enable many useful compiler warnings:

```bash
gcc -Wall hello.c -o hello
```

Very useful for debugging.

---

## `-o`

Specify output filename:

```bash
gcc hello.c -o myprogram
```

Without `-o`, GCC normally creates:

```text
a.out
```

---

## `-c`

Compile but **do not link**:

```bash
gcc -c hello.c
```

Produces:

```text
hello.o
```

Pipeline:

```text
hello.c
  ↓
compiler
  ↓
hello.o
```

---

## `-S`

Generate assembly:

```bash
gcc -S hello.c
```

Produces:

```text
hello.s
```

So:

```text
hello.c
   ↓
gcc -S
   ↓
hello.s
```

---

# 12. Complete C Compilation Process

This is one of the most important concepts in the lab.

```text
hello.c
   │
   │ Preprocessing
   ↓
hello.i
   │
   │ Compilation
   ↓
hello.s
   │
   │ Assembly
   ↓
hello.o
   │
   │ Linking
   ↓
Executable
```

Commands:

### Preprocessing

```bash
gcc -E hello.c -o hello.i
```

### C → Assembly

```bash
gcc -S hello.c -o hello.s
```

### Assembly → Object File

```bash
gcc -c hello.c -o hello.o
```

### Linking

```bash
gcc hello.o -o hello
```

Therefore:

```text
.c
 ↓
.i
 ↓
.s
 ↓
.o
 ↓
executable
```

---

# 13. Word-Count C Program

A simple word-count program:

```c
#include <stdio.h>

int main(int argc, char *argv[]) {

    if (argc != 2) {
        printf("Usage: %s <input-file>\n", argv[0]);
        return 1;
    }

    FILE *fp = fopen(argv[1], "r");

    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    int c;
    int in_word = 0;
    int words = 0;

    while ((c = fgetc(fp)) != EOF) {

        if (c == ' ' || c == '\n' || c == '\t') {
            in_word = 0;
        }
        else if (!in_word) {
            words++;
            in_word = 1;
        }
    }

    fclose(fp);

    printf("Number of words: %d\n", words);

    return 0;
}
```

Compile:

```bash
gcc -Wall wordcount.c -o wordcount
```

Run:

```bash
./wordcount input.txt
```

---

# 14. `argc` and `argv`

If you run:

```bash
./wordcount input.txt
```

then:

```text
argc = 2

argv[0] = "./wordcount"
argv[1] = "input.txt"
```

If you run:

```bash
./wordcount input.txt another.txt
```

then:

```text
argc = 3

argv[0] = "./wordcount"
argv[1] = "input.txt"
argv[2] = "another.txt"
```

### Important

```c
argv[i]
```

is a **string**.

For example:

```bash
./printlog 10
```

means:

```c
argv[1] = "10"
```

not integer `10`.

You need conversion:

```c
atoi(argv[1])
```

or preferably:

```c
strtol()
strtod()
```

when proper error checking is required.

---

# 15. Accept an Input File as a Command-Line Argument

Basic pattern:

```c
int main(int argc, char *argv[])
```

Check arguments:

```c
if (argc != 2) {
    printf("Usage: %s <filename>\n", argv[0]);
    return 1;
}
```

Open file:

```c
FILE *fp = fopen(argv[1], "r");
```

Always check:

```c
if (fp == NULL) {
    perror("fopen");
    return 1;
}
```

The file may not exist or you may not have permission.

---

# 16. Why Normal `swap()` Does Not Work

Consider:

```c
void swap(int x, int y) {
    int temp = x;
    x = y;
    y = temp;
}
```

Call:

```c
int a = 10;
int b = 20;

swap(a, b);

printf("%d %d\n", a, b);
```

Output:

```text
10 20
```

## Why?

C uses **pass-by-value**.

When:

```c
swap(a, b);
```

is called, copies are created:

```text
main():

a = 10
b = 20

        ↓

swap():

x = 10
y = 20
```

Changing `x` and `y` only changes the copies.

When `swap()` returns, `x` and `y` disappear.

Therefore:

```text
a = 10
b = 20
```

remain unchanged.

---

# 17. Swap Using Pointers

Correct solution:

```c
#include <stdio.h>

void swap(int *px, int *py) {
    int temp = *px;
    *px = *py;
    *py = temp;
}

int main() {

    int a = 10;
    int b = 20;

    printf("Before: a = %d, b = %d\n", a, b);

    swap(&a, &b);

    printf("After:  a = %d, b = %d\n", a, b);

    return 0;
}
```

Output:

```text
Before: a = 10, b = 20
After:  a = 20, b = 10
```

## Important Pointer Symbols

### `&a`

Means:

> Address of `a`

### `int *p`

Means:

> `p` is a pointer to an integer.

### `*p`

Means:

> Value stored at the address contained in `p`.

Therefore:

```c
swap(&a, &b);
```

passes addresses.

Inside:

```c
*px = *py;
```

modifies the original variables.

### Memory Picture

```text
main()

a = 10
address = 0x100

b = 20
address = 0x200


swap(&a, &b)

px ──────────→ 0x100 → 10
py ──────────→ 0x200 → 20
```

So:

```c
*px = *py;
```

means:

```text
address 0x100 gets value 20
```

Thus `a` changes.

---

# 18. Makefile

A `Makefile` automates compilation.

Basic structure:

```make
target: prerequisites
	command
```

> The command must begin with a **TAB**, not spaces.

Example:

```make
CC = gcc
CFLAGS = -Wall

hw: hw.o helper.o
	$(CC) $(CFLAGS) -o hw hw.o helper.o

hw.o: hw.c
	$(CC) $(CFLAGS) -c hw.c

helper.o: helper.c
	$(CC) $(CFLAGS) -c helper.c

clean:
	rm -f hw.o helper.o hw
```

Run:

```bash
make
```

---

# 19. How `make` Works

Suppose:

```text
hw.c
helper.c
```

are source files.

The dependency graph is:

```text
hw.c
 ↓
hw.o ──────┐
           ├──→ hw
helper.o ──┘
 ↑
helper.c
```

The build process is:

```text
hw.c
 ↓
compiler
 ↓
hw.o

helper.c
 ↓
compiler
 ↓
helper.o

hw.o + helper.o
       ↓
     linker
       ↓
       hw
```

Run:

```bash
./hw
```

### Why use Make?

Without Make:

```bash
gcc -Wall -c hw.c
gcc -Wall -c helper.c
gcc -Wall -o hw hw.o helper.o
```

With Make:

```bash
make
```

Make checks timestamps and dependencies and rebuilds only what is necessary.

---

# 20. Extract `lab-code.tar.gz`

The archive is a compressed tar archive.

Extract:

```bash
tar -xzf lab-code.tar.gz
```

Meaning:

```text
-x  extract
-z  gzip
-f  file
```

List contents without extracting:

```bash
tar -tzf lab-code.tar.gz
```

After extraction:

```bash
cd lab-code
```

Inspect:

```bash
ls
```

Build:

```bash
make
```

Run the generated program:

```bash
./program_name
```

Study source:

```bash
less file.c
```

or:

```bash
cat file.c
```

---

# 21. `printlog` Program

The lab asks you to create:

```text
printlog x
```

which prints:

\[
\log_e(x) = \ln(x)
\]

The input must be valid and:

\[
x > 0
\]

## `printlog.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <math.h>

int main(int argc, char *argv[]) {

    if (argc != 2) {
        fprintf(stderr, "Usage: %s <positive-number>\n", argv[0]);
        return 1;
    }

    char *end;
    errno = 0;

    double x = strtod(argv[1], &end);

    if (errno != 0 || end == argv[1] || *end != '\0') {
        fprintf(stderr, "Error: invalid number '%s'\n", argv[1]);
        return 1;
    }

    if (x <= 0) {
        fprintf(stderr, "Error: x must be greater than 0\n");
        return 1;
    }

    printf("ln(%g) = %g\n", x, log(x));

    return 0;
}
```

Compile:

```bash
gcc -Wall printlog.c -o printlog -lm
```

### Why `-lm`?

`log()` is provided by the math library.

```text
-lm
 ↑
link with libm
```

Run:

```bash
./printlog 10
```

Output approximately:

```text
ln(10) = 2.30259
```

Run:

```bash
./printlog 1
```

Output:

```text
ln(1) = 0
```

Invalid input:

```bash
./printlog -5
```

Output:

```text
Error: x must be greater than 0
```

Invalid number:

```bash
./printlog abc
```

Output:

```text
Error: invalid number 'abc'
```

Missing argument:

```bash
./printlog
```

Output:

```text
Usage: ./printlog <positive-number>
```

---

# 22. Update the Makefile

A complete Makefile can be:

```make
CC = gcc
CFLAGS = -Wall

PROGRAMS = hw printlog

all: $(PROGRAMS)

hw: hw.o helper.o
	$(CC) $(CFLAGS) -o hw hw.o helper.o

hw.o: hw.c
	$(CC) $(CFLAGS) -c hw.c

helper.o: helper.c
	$(CC) $(CFLAGS) -c helper.c

printlog: printlog.o
	$(CC) $(CFLAGS) -o printlog printlog.o -lm

printlog.o: printlog.c
	$(CC) $(CFLAGS) -c printlog.c

clean:
	rm -f *.o $(PROGRAMS)
```

Build:

```bash
make
```

Test:

```bash
./printlog 10
```

Clean generated files:

```bash
make clean
```

---

# 23. Standard File Descriptors

Linux/Unix convention:

```text
0 → stdin
1 → stdout
2 → stderr
```

## stdin

Standard input:

```text
0
```

Usually the keyboard.

## stdout

Standard output:

```text
1
```

Usually the terminal.

## stderr

Standard error:

```text
2
```

Used for error messages.

---

# 24. Redirection

Redirect standard output:

```bash
./program > output.txt
```

Conceptually:

```text
stdout (1)
     ↓
output.txt
```

Redirect errors:

```bash
./program 2> error.txt
```

Conceptually:

```text
stderr (2)
     ↓
error.txt
```

Redirect both:

```bash
./program > output.txt 2> error.txt
```

---

# 25. Pipes and File Descriptors

Example:

```bash
cat file.txt | wc -l
```

Conceptually:

```text
cat
 │
 │ stdout (1)
 ↓
 PIPE
 ↓
wc
 │
 │ stdin (0)
```

This connects the standard output of one process to the standard input of another.

---

# 26. Shell and Operating System

The shell is a program that interprets commands.

For:

```bash
ls
```

the general idea is:

```text
User
  ↓
Shell
  ↓
Process / program
  ↓
System calls
  ↓
Kernel
  ↓
Hardware
```

The shell does not itself implement every command. Many commands are separate programs.

---

# 27. System Calls

A user program requests kernel services using **system calls**.

Examples:

```text
read()
write()
open()
close()
fork()
exec()
wait()
```

General flow:

```text
User Mode
    ↓
System Call
    ↓
Kernel Mode
    ↓
Kernel performs operation
    ↓
User Mode
```

System calls are the main interface between user programs and the OS kernel.

---

# 28. Important Viva Questions

## Q1. What is the difference between `ls -lt` and `ls -ltr`?

```text
ls -lt
```

Newest files first.

```text
ls -ltr
```

Oldest files first, so the latest file is at the bottom.

---

## Q2. What does `-Wall` do?

It enables many useful compiler warnings in GCC.

```bash
gcc -Wall program.c -o program
```

---

## Q3. What does `-c` do?

It compiles source code into an object file without performing the final linking step.

```bash
gcc -c program.c
```

Produces:

```text
program.o
```

---

## Q4. What does `-S` do?

It generates assembly code.

```bash
gcc -S program.c
```

Produces:

```text
program.s
```

---

## Q5. What is a pipe?

A pipe connects the standard output of one process to the standard input of another.

```bash
cat file.txt | wc
```

---

## Q6. Why doesn't normal `swap()` work?

Because C passes function arguments by value.

The function receives copies.

---

## Q7. How does pointer-based `swap()` work?

It passes the addresses of the variables:

```c
swap(&a, &b);
```

The function receives pointers:

```c
void swap(int *a, int *b)
```

and dereferences them:

```c
*a
*b
```

to modify the original variables.

---

## Q8. What are `argc` and `argv`?

`argc` = number of command-line arguments.

`argv` = array of strings containing those arguments.

For:

```bash
./program hello 10
```

```text
argc = 3

argv[0] = "./program"
argv[1] = "hello"
argv[2] = "10"
```

---

## Q9. Why is `-lm` needed?

Because functions such as `log()` are provided by the math library.

```bash
gcc printlog.c -o printlog -lm
```

---

## Q10. What is a Makefile?

A file containing rules that describe how programs should be built.

Example:

```make
program: program.o
	gcc -o program program.o
```

---

# 29. Essential Commands — Quick Revision

```bash
# Files/directories
ls
ls -la
ls -ltr
pwd
cd
mkdir
cp
mv
rm

# Documentation
man ls
man wc

# File contents
cat
less

# Word count
wc
wc -l
wc -w
wc -c

# Networking
ip addr
hostname -I
ping google.com

# Hardware
lscpu
free -h
df -h
lsblk

# OS/kernel
uname -a
uname -r
cat /etc/os-release

# Python
which python
which python3
python --version
python3 --version
echo $PATH

# GCC
gcc file.c -o file
gcc -Wall file.c -o file
gcc -E file.c
gcc -S file.c
gcc -c file.c

# Run
./program
./program arg1 arg2

# Archive
tar -xzf file.tar.gz
tar -tzf file.tar.gz

# Make
make
make clean
```

---

# 30. Final Lab Cheat Sheet

## Linux

```text
ls       → list files
cd       → change directory
pwd      → current directory
mkdir    → create directory
cp       → copy
mv       → move/rename
rm       → remove
man      → manual
cat      → display file
less     → view file
```

## Pipes

```text
A | B
```

means:

```text
stdout(A) → stdin(B)
```

## File Descriptors

```text
0 → stdin
1 → stdout
2 → stderr
```

## C Arguments

```c
int main(int argc, char *argv[])
```

For:

```bash
./program 10
```

```text
argc = 2
argv[0] = "./program"
argv[1] = "10"
```

## Pointers

```text
&a → address of a
*p → value at address p
int *p → pointer to int
```

## C Compilation

```text
.c → preprocessing → .i
.i → compilation   → .s
.s → assembly      → .o
.o → linking       → executable
```

## GCC

```text
-Wall → warnings
-o    → output filename
-c    → compile, don't link
-S    → generate assembly
-E    → preprocessing only
-lm   → link math library
```

## Make

```text
target: dependencies
	TAB command
```

Example:

```make
program: program.o
	gcc -o program program.o
```

## OS Architecture

```text
User Program
     ↓
System Call
     ↓
Kernel
     ↓
Hardware
```

## Most Important Topics to Prepare for Viva

1. **Linux commands and their options**
2. **Pipes**
3. **stdin/stdout/stderr**
4. **File descriptors**
5. **C compilation stages**
6. **GCC options: `-Wall`, `-c`, `-S`, `-E`, `-o`, `-lm`**
7. **`argc` and `argv`**
8. **Pass-by-value**
9. **Pointers and dereferencing**
10. **`Makefile` and dependency graphs**
11. **System calls**
12. **User mode vs kernel mode**
