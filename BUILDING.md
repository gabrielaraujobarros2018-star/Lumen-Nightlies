# Makefile Guide for Lumen before Stable 1.0

This document explains how the Make-based build system works in this project. It is written to remove confusion and make the build process fully understandable.

---

# Why We Use Make

This project contains a large number of source files and multiple build stages (bootloader, kernel, userland components, etc.). Running GCC manually is not scalable at this size.

Make provides:

* Dependency tracking
* Incremental builds
* Deterministic linking
* Controlled compilation flags
* Scalable architecture

Make is not "magic". It simply describes what depends on what.

---

# Core Concepts

## 1. Targets

A target is something we want to build.

Example:

```
kernel.elf: boot.o main.o mm.o
```

This means:

> To build `kernel.elf`, we need `boot.o`, `main.o`, and `mm.o`.

---

## 2. Rules

A rule has the structure:

```
target: dependencies
	command
```

Important: The indentation before `command` MUST be a real TAB character.

---

## 3. Automatic Variables

Inside a rule, Make provides shorthand variables:

* `$@` → the target name
* `$^` → all dependencies
* `$<` → the first dependency

Example:

```
$(TARGET): $(OBJS)
	$(CC) $(LDFLAGS) -o $@ $^
```

This expands to:

```
gcc <ldflags> -o kernel.elf boot.o main.o mm.o
```

If automatic variables are confusing, you may replace them with explicit variables:

```
$(CC) $(LDFLAGS) -o $(TARGET) $(OBJS)
```

Both are correct.

---

# Minimal Build Example

```
CC = gcc
CFLAGS = -ffreestanding -O2 -Wall -Wextra
LDFLAGS = -T linker.ld -nostdlib

SRCS = boot.c main.c mm.c
OBJS = $(SRCS:.c=.o)
TARGET = kernel.elf

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(LDFLAGS) -o $(TARGET) $(OBJS)

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJS) $(TARGET)
```

---

# Pattern Rules

This rule:

```
%.o: %.c
```

Means:

> Any `.o` file depends on its matching `.c` file.

Example:

```
main.o depends on main.c
```

---

# Recommended Flags for Low-Level Builds

For kernel or freestanding builds:

```
CFLAGS += -ffreestanding -fno-stack-protector -nostdlib
```

For dependency tracking:

```
CFLAGS += -MMD -MP
```

Then include generated dependency files:

```
-include $(OBJS:.o=.d)
```

---

# Debugging Make

To see full commands:

```
make V=1
```

To debug dependency decisions:

```
make --debug=b
```

To print variable values:

```
$(info OBJS = $(OBJS))
```

---

# Project Structure Recommendation

For large projects, avoid a single giant Makefile.

Recommended structure:

```
/boot
/kernel
/lib
/userland
/tools
```

Each directory contains its own Makefile.

The root Makefile includes or invokes them.

---

# Philosophy

Make is declarative, not procedural.

You do not tell Make "how to build step by step".

You describe:

> What depends on what.

Make figures out what must be rebuilt.

---

# Final Notes

* Avoid unnecessary complexity.
* Prefer explicit variables if shorthand feels unclear.
* Keep rules readable.
* Separate compile flags from link flags.
* Document non-obvious behavior.

Make is old, but it is powerful and predictable when understood correctly.

End of document.
