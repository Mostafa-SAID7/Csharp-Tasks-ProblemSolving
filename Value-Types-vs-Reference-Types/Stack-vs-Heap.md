# Stack vs Heap - Memory Allocation

## 🎯 Quick Overview

| Aspect | Stack | Heap |
|--------|-------|------|
| **Size** | Limited, small | Large, flexible |
| **Speed** | Very fast (LIFO) | Slower (dynamic allocation) |
| **Allocation** | Automatic (when variable declared) | Dynamic (when `new` used) |
| **Deallocation** | Automatic (when scope ends) | Garbage collected |
| **Thread Safety** | Each thread has own stack | Shared across threads |
| **Fragmentation** | No fragmentation | Can become fragmented |
| **What's stored** | Local variables, method calls | Objects, arrays, reference data |

---

## 📚 The Stack - LIFO Data Structure

### What is the Stack?

The **Stack** is a region of memory that operates on a Last-In-First-Out (LIFO) principle. It's used for storing local variables and method calls.

### How the Stack Works

Think of it like a stack of plates:
- You add plates to the top (push)
- You remove plates from the top (pop)
- When you pop, the most recently added plate is removed first

### Visual Representation

```
Method2 Active
┌─────────────────────┐
│ local var x = 5     │
│ local var y = 10    │
├─────────────────────┤
│ Method1 frame       │
│ int a = 100         │
│ int b = 200         │
├─────────────────────┤
│ Main method frame   │
│ int result = 0      │
└─────────────────────┘
     ↑ Stack Grows UP
     
When Method2 finishes, its frame is removed.
```

### Key Characteristics

1. **Automatic Allocation & Deallocation**
   - Memory is automatically allocated when variable is declared
   - Memory is automatically freed when variable goes out of scope

2. **Size Limited**
   - Typical stack size: 1-8 MB per thread
   - Exceeding causes **StackOverflowException**

3. **Very Fast**
   - No searching or garbage collection needed
   - Just move the stack pointer

4. **Thread Safety**
   - Each thread has its own stack
   - No contention between threads

---

## 💾 The Heap - Dynamic Memory

### What is the Heap?

The **Heap** is a region of memory used for dynamic allocation of objects and reference types. It's managed by the garbage collector in .NET.

### How the Heap Works

The heap is less organized than the stack. Memory can be allocated and freed in any order.

### Visual Representation

```
HEAP Memory Space
┌──────────────────────────────────┐
│ Employee Object                  │ ← referenced by 'emp1'
│ Name = "Mostafa"                 │
├──────────────────────────────────┤
│ [FREE SPACE]                     │
├──────────────────────────────────┤
│ string "Hello"                   │ ← referenced by 'greeting'
├──────────────────────────────────┤
│ Array [1,2,3,4,5]                │ ← referenced by 'numbers'
└──────────────────────────────────┘

Stack stores REFERENCES to these objects
┌─────────────────────┐
│ emp1 ──────────────→ (points to Employee on Heap)
│ greeting ──────────→ (points to string on Heap)
│ numbers ───────────→ (points to array on Heap)
└─────────────────────┘
```

### Key Characteristics

1. **Dynamic Allocation**
   - Memory allocated when object is created with `new`
   - Size determined at runtime

2. **Large Size**
   - Typically much larger than stack (100s of MB to GB)
   - Limited only by available RAM

3. **Slower Access**
   - Requires dereferencing (following the reference)
   - Garbage collection overhead

4. **Automatic Deallocation**
   - Garbage collector identifies unreferenced objects
   - Automatically frees memory

---

## 🔄 Practical Example: Stack vs Heap

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}

static void Main()
{
    // STACK: Local variable 'age' stores the value 25 directly
    int age = 25;
    
    // STACK: Local variable 'person' stores a reference
    // HEAP: Person object is created and stored
    Person person = new Person { Name = "Mostafa", Age = 25 };
    
    // STACK: Local variable 'numbers' stores a reference
    // HEAP: Array object is created
    int[] numbers = new int[5] { 1, 2, 3, 4, 5 };
    
    // STACK: 'name' stores a reference
    // HEAP: String object is created
    string name = "Hello";
}
// When Main ends, stack frame is cleaned up.
// Heap objects (Person, array, string) may still be referenced or eligible for GC.
```

---

## 🎯 Memory Layout Detail

### Value Type in Local Scope

```csharp
int x = 10;
int y = x;
```

```
STACK:
┌─────────────┐
│ y = 10      │ (copy of x)
│ x = 10      │ (original)
└─────────────┘
```

---

### Reference Type in Local Scope

```csharp
var emp1 = new Employee { Name = "Mostafa" };
var emp2 = emp1;
```

```
STACK:                          HEAP:
┌────────────────┐              ┌──────────────────┐
│ emp2 ─────────┐├─────────────→│ Employee object  │
│ emp1 ─────────┘│              │ Name = "Mostafa" │
└────────────────┘              └──────────────────┘

Both references point to the SAME object
```

---

### Value Type as Class Field

```csharp
public class Account
{
    public int Balance { get; set; } // value type
}

var account = new Account { Balance = 1000 };
```

```
STACK:                          HEAP:
┌────────────────┐              ┌──────────────────┐
│ account ──────┐├─────────────→│ Account object   │
└────────────────┘              │ Balance = 1000   │
                                │ (stored on Heap) │
                                └──────────────────┘

The value type (Balance) is stored ON THE HEAP
because it's part of the object!
```

---

## ⚠️ The Myth: "Value Types on Stack, Reference Types on Heap"

### The Reality

**This rule is INCOMPLETE.** Here's what's actually true:

#### ✅ True Cases

1. **Local value type variables** are stored on stack
   ```csharp
   int x = 10;  // ✅ x is on stack
   ```

2. **Local reference type variables** store references on stack
   ```csharp
   object obj = new object();  // ✅ obj reference is on stack
                               // ✅ object itself is on heap
   ```

#### ❌ False Cases

3. **Value type as class field** is on HEAP (not stack!)
   ```csharp
   public class MyClass
   {
       public int MyInt { get; set; }  // ❌ This int is on HEAP!
   }
   ```

4. **Value type in array** is on HEAP
   ```csharp
   int[] numbers = new int[10];  // ❌ The integers are on HEAP!
   ```

---

## 🚀 Performance Implications

### Stack - Advantages
- ✅ Extremely fast allocation and deallocation
- ✅ No garbage collection overhead
- ✅ Better cache locality (sequential memory)
- ✅ Thread-safe (no shared resources)

### Heap - Advantages
- ✅ Can store large objects
- ✅ Objects can outlive the current method
- ✅ Flexible size allocation
- ✅ Shared between multiple references

---

## 🔍 Stack Overflow Example

```csharp
public class Program
{
    public static int RecursiveMethod(int n)
    {
        if (n == 0) return 0;
        
        // Each call creates a new stack frame
        return RecursiveMethod(n - 1) + 1;
    }
    
    static void Main()
    {
        // ❌ Will cause StackOverflowException for large n
        int result = RecursiveMethod(100000);
    }
}
```

Why?
- Each method call creates a new frame on stack
- Stack has limited size (~1 MB typically)
- Deep recursion exhausts the stack

---

## 🎯 Stack Trace Example

When an exception occurs, the stack trace shows the sequence of method calls:

```
System.StackOverflowException: Exception of type 'System.StackOverflowException' was thrown.
   at Program.RecursiveMethod(Int32 n)
   at Program.RecursiveMethod(Int32 n)  ← This repeats many times
   at Program.RecursiveMethod(Int32 n)
   at Program.Main()
```

This shows the **stack frame history** - which methods called which.

---

## 📋 Decision Tree: Stack or Heap?

```
Variable is:
├─ Local variable of value type? 
│  └─ STACK (int x = 5)
├─ Local variable of reference type?
│  └─ Reference on STACK, object on HEAP
├─ Field/property of a class?
│  └─ HEAP (stored with the object)
├─ Element in an array?
│  └─ HEAP (stored with the array)
└─ Field/property of a struct?
   └─ Depends on where the struct is stored
```

---

## 🧪 Quick Memory Test

For each line, where is the data stored?

```csharp
int age = 25;                              // age: STACK, value 25: STACK
string name = "Mostafa";                   // name: STACK, string object: HEAP
int[] numbers = new int[10];               // numbers: STACK, array: HEAP
Person person = new Person();              // person: STACK, Person object: HEAP

public class Account
{
    public int Balance { get; set; }       // Balance field: HEAP (part of Account)
}
```

---

## 📝 Summary

| Concept | Stack | Heap |
|---------|-------|------|
| **When used** | Local value type variables | Objects and reference types |
| **Allocation** | Automatic and fast | Slower, requires `new` |
| **Deallocation** | Automatic when out of scope | Garbage collection |
| **Size limit** | Limited (~1-8 MB) | Large, system-dependent |
| **Access speed** | Fastest | Slower |
| **Thread safety** | Safe (per-thread) | Requires synchronization |
| **Common issue** | StackOverflowException | OutOfMemoryException, GC pressure |

---

**Next:** Continue to [Boxing vs Unboxing](./Boxing-vs-Unboxing.md) to understand type conversion between value and reference types.
