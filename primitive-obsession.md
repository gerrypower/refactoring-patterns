---
title: Primitive Obsession 
author: Jason Cheong-Kee-You
description: Summary of the intent, motivation and implementation of Martin Fowler's 'Primitive Obsession' refactoring
version:
  revision: 0.0.1
  date: 2019-07-26
---
# Primitive Obsession

### Intent

 Reduce the use of primitives by creating fundamental types useful for the domain

### Motivation to Eliminate the Smell

  When using primitives, simple operations can become duplicated throughout the code

### Applicability

  Use this

- when the only a subset of the primitive is supported (e.g. telephone numbers do not permit arbitrary strings)
- when natural language doesn't match the operations (e.g. "+=" vs "deposit")
- to replace strings, numbers, arrays, hashs

### Consequences

- Conditional logic related to primitives is encapsulated in TypeClass
- Reduce duplication by encapsulating behavior in methods
- Capture and communicate intent moving from raw conditional logic to named methods
- Mysterious literal values are replaced by constants, or in some cases, completely hidden in TypeClass

### Participants

- TypeClass: the new class that is created
- Callers: any code that instantiates and accesses the primitives

### Refactoring

  Replace Primitive with Object

  Also known as:

- Replace Data Value with Object
- Replace Type Code with Class

## Implementation

  from

    orders.filter(o => o.priority === "high" || o.priority === "rush")

  to

    orders.filter(o => o.priority.higherThan(new Priority("normal")))

### Structure
  - Consider encapsulate variable
  - Create a new class that wraps the primitive
  - Create methods to provide access to the primitive
  - Replace all instances with wrapped instance that returns the primitive
  - Consider each use of the primitive whether the behavior can be moved to a method on the new class
