# Pointer Basics

## 1. Pointer Concepts

A pointer is a variable that stores the memory address of another object.

### 1.1. Why Do We Use Pointers?

- **Modify original data directly**
- **Pass large amounts of data without copying**
- **Manage dynamically allocated memory**
- **Point to different objects during execution**

## 2. Pointer Declaration and Basic Syntax

### 2.1. Declaring and Initializing a Pointer

```cpp
int a = 10;
int* p = &a;
```

- `int a = 10;`
  - Declares an integer variable `a` and initializes it to `10`.
  - As a local variable, `a` is typically stored on the stack.

- `int* p = &a;`
  - Declares a pointer variable `p` that points to an `int`.
  - Stores the memory address of `a` in `p`.
  - As a local variable, `p` is also typically stored on the stack.

### 2.2. Pointer Size and Memory

- The size of a pointer depends on the target architecture and ABI.
  - **32-bit environment:** Typically 4 bytes.
  - **64-bit environment:** Typically 8 bytes.
- The size of a pointer does not depend on the size of the object it points to.
- Both `a` and `p` are typically stored on the stack when declared as local variables.
- `p` stores the address of `a`, allowing indirect access to its value.
- Declaring a pointer does not automatically allocate memory on the heap.

### 2.3. Accessing Values Through a Pointer

```cpp
int a = 10;
int* p = &a;

cout << a;   // 10
cout << &a;  // Memory address of a
cout << p;   // Memory address of a
cout << *p;  // 10
cout << &p;  // Memory address of p
```

- `a` : Value of `a`.
- `&a` : Memory address of `a`.
- `p` : Address stored in the pointer.
- `*p` : Value of the object pointed to by `p`.
- `&p` : Memory address of the pointer itself.
