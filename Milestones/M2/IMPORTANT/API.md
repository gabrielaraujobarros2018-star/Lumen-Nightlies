# API Development Guide

## Purpose

This document teaches **public and internal API development** for operating systems and low-level platforms, with emphasis on **discipline, stability, and correctness** rather than language complexity.

The rules here apply to kernels, system services, HALs, and core libraries.

---

## 1. What an API Is (OS Context)

An API is a **contract between subsystems**.

In an OS, an API exists to:

* Isolate components
* Prevent misuse
* Enable refactoring without breakage
* Enforce invariants

An API is **not** just a set of functions.

---

## 2. Public API vs Internal API

### Public API

Used by:

* Applications
* External developers
* Third-party modules

Characteristics:

* Strong backward compatibility
* Stable ABI
* Extensive validation
* Long deprecation cycles
* Thorough documentation

Examples:

* POSIX syscalls
* Android SDK
* libc

---

### Internal API

Used by:

* Kernel subsystems
* System services
* Drivers
* Platform components

Characteristics:

* Limited audience
* Controlled usage
* Faster evolution
* Still versioned and disciplined

Examples:

* Scheduler interfaces
* VFS ↔ filesystem drivers
* Power manager ↔ device drivers

---

## 3. Core API Design Principles

### 3.1 APIs Are Interfaces, Not Implementations

Headers define **what is allowed**, not **how it works**.

Rules:

* No internal structs in headers
* No global state exposure
* No implementation leakage

---

### 3.2 Opaque Types

APIs must hide internal data structures.

Effects:

* Prevents tight coupling
* Allows internal redesigns
* Enforces correct usage

This applies to both public and internal APIs.

---

### 3.3 Minimal Surface Area

APIs should be intentionally small.

Guidelines:

* Prefer multiple simple functions over one complex function
* Avoid flags unless strictly required
* Avoid optional parameters

Small APIs are:

* Easier to reason about
* Harder to misuse
* More stable long-term

---

## 4. Layering and Dependency Rules

Correct layering prevents architectural decay.

Recommended structure:

```
core/
api/        ← stable headers only
services/
drivers/
arch/
```

Rules:

* API headers never include implementation headers
* Lower layers never depend on higher layers
* Drivers depend only on API headers

Breaking these rules guarantees long-term instability.

---

## 5. Error Handling

APIs must fail explicitly.

Rules:

* No silent failures
* No implicit behavior
* No hidden retries

Use:

* Explicit error codes
* Defined failure conditions
* Predictable return values

This is mandatory for kernel and system-level APIs.

---

## 6. API Versioning

Even internal APIs must be versioned.

Reasons:

* Subsystem rewrites
* Module compatibility
* Backports
* Long-term maintenance

Versioning strategies:

* Numeric API version constants
* Versioned operation tables
* Feature flags

Never assume an API will stay unchanged.

---

## 7. Function Tables (Operations Interfaces)

Function tables decouple callers from implementations.

Benefits:

* Multiple implementations
* Hot-swappable subsystems
* Easier testing and mocking
* Cleaner abstractions

Used heavily in:

* Kernels
* Drivers
* Filesystems
* HALs

---

## 8. Documentation Rules

API documentation must describe **constraints**, not narratives.

Document:

* Required locks
* Thread safety
* Execution context (IRQ-safe, sleepable, etc.)
* Ownership and lifetime rules

Avoid:

* Describing internal logic
* Repeating function names

Good documentation prevents misuse more effectively than comments.

---

## 9. Stability Expectations

### Public APIs

* Never break without deprecation
* ABI stability is mandatory
* Behavior changes must be documented

### Internal APIs

* May evolve
* Must remain consistent within a release
* Breaking changes must be deliberate

Internal does not mean careless.

---

## 10. Security and Safety

APIs must assume:

* Callers make mistakes
* Inputs may be invalid
* States may be inconsistent

Rules:

* Validate inputs
* Enforce invariants
* Fail fast and predictably

Security starts at API boundaries.

---

## 11. What Skill Level Is Required

Advanced language features are not required.

Required skills:

* Basic to intermediate C/C++
* Clear understanding of pointers and memory
* Discipline in design
* Respect for boundaries

Professional API quality comes from structure, not syntax.

---

## 12. Core Philosophy

APIs exist to protect the system from itself.

A correct API:

* Prevents incorrect usage
* Survives refactors
* Enforces architectural boundaries

This philosophy applies equally to public and internal APIs.

