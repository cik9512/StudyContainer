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

## 3. Pointers and Function Parameters

### 3.1. Pass by Value

When an argument is passed by value, the function receives a copy of the original value.

Changes made to the parameter do not affect the original variable.

```cpp
void change(int x)
{
    x = 20;
}

int main()
{
    int a = 10;
    change(a);

    // a = 10
}
```

- `int x` : A separate parameter initialized with a copy of `a`.
- Modifying `x` does not change `a`.

### 3.2. Pass by Reference

When an argument is passed by reference, the parameter becomes an alias for the original object.

Changes made through the reference affect the original object.

```cpp
void change(int& x)
{
    x = 20;
}

int main()
{
    int a = 10;
    change(a);

    // a = 20
}
```

- `int& x` : Declares a reference parameter.
- `x` refers to the original variable `a`.
- Modifying `x` directly modifies `a`.
- The `&` in `int& x` declares a reference type.

### 3.3. Passing a Pointer by Value

A pointer can also be passed to a function by value.

The function receives a copy of the address stored in the pointer.

```cpp
void change(int* p)
{
    *p = 30;
}

int main()
{
    int a = 10;
    change(&a);

    // a = 30
}
```

- `int* p` : Declares a pointer parameter.
- `&a` : Gets the memory address of `a`.
- `*p` : Accesses the object pointed to by `p`.
- Modifying `*p` changes the original object.
- Reassigning `p` does not change the caller's pointer.

### 3.4. Passing a Pointer by Reference

A pointer can be passed by reference when the function needs to modify the caller's pointer itself.

```cpp
void changePointer(int*& ptr, int& target)
{
    ptr = &target;
}

int main()
{
    int a = 10;
    int b = 20;

    int* p = &a;

    changePointer(p, b);

    // p now points to b
    // *p = 20
}
```

- `int*& ptr` : Declares a reference to a pointer to an integer.
- `ptr` is an alias for the caller's pointer `p`.
- Assigning a new address to `ptr` changes the original pointer `p`.
- `*ptr` accesses the integer pointed to by `p`.

### 3.5. The Different Meanings of `&`

The meaning of `&` depends on where it is used.

```cpp
int a = 10;

int* p = &a; // Address-of operator

int& r = a;  // Reference declaration
```

- `&a` : Gets the address of `a`.
- `int& r` : Declares `r` as a reference to an integer.
- These expressions use the same symbol but have different meanings.

### 3.6. Comparison

| Parameter | Meaning | Can modify the original integer? | Can reassign the caller's pointer? |
|---|---|---|---|
| `int x` | Pass an integer by value | No | N/A |
| `int& x` | Pass an integer by reference | Yes | N/A |
| `int* p` | Pass a pointer by value | Yes, through `*p` | No |
| `int*& p` | Pass a pointer by reference | Yes, through `*p` | Yes |
| `const int& x` | Pass a read-only reference | No, through `x` | N/A |

### 3.7. Key Takeaways

- **Pass by value:** The function receives a copy.
- **Pass by reference:** The function accesses the original object through an alias.
- **Pass a pointer by value:** The address is copied, but the pointed-to object can still be modified.
- **Pass a pointer by reference:** The function can change which object the caller's pointer points to.
- **Use `const T&` for read-only access to large objects when copying is unnecessary.**
