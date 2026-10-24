# Follow-up Questions - Interview Q&A

Quick answers to common follow-up questions. See linked documents for detailed explanations.

---

## 1️⃣ What is the difference between Stack and Heap?

**See:** [Stack vs Heap](./Stack-vs-Heap.md)

**Answer:** 
- **Stack:** LIFO memory for local value type variables. Fast, automatic cleanup, limited size (~1-8 MB).
- **Heap:** Dynamic memory for objects. Garbage collected, larger size, slower access.
- **Important:** The rule "value types on stack, reference types on heap" is incomplete. Value type fields in objects are stored on the heap.

---

## 2️⃣ What is boxing and unboxing?

**See:** [Boxing vs Unboxing](./Boxing-vs-Unboxing.md)

**Answer:**
- **Boxing:** Converting value type → reference type (allocates heap memory)
- **Unboxing:** Converting reference type → value type (copies from heap)
- **Performance:** Both are 50-100x slower than direct operations
- **Avoid by:** Using generics and `List<T>` instead of `ArrayList`

---

## 3️⃣ What is the difference between class and struct?

**See:** [Class vs Struct](./Class-vs-Struct.md)

**Answer:**
- **Class:** Reference type, identity-based, full inheritance, for complex objects
- **Struct:** Value type, value-based, limited inheritance, for small immutable values
- **Use class for:** Employees, Accounts, complex objects
- **Use struct for:** Point, Color, Vector, coordinate data

---

## 4️⃣ What happens when you pass an object to a method?

**Answer:**

**Value Type:**
```csharp
void Modify(int x) { x = 100; }
int original = 5;
Modify(original);
// original is still 5 (copy was modified, not original)
```

**Reference Type (without `ref`):**
```csharp
void Modify(Person p) { p.Name = "New"; }  // ✅ Changes visible
void Replace(Person p) { p = new Person(); }  // ❌ Doesn't affect original

var person = new Person { Name = "Old" };
Modify(person);      // Name is "New" (object modified)
Replace(person);     // Still original object (reassignment didn't work)
```

**With `ref` keyword:**
```csharp
void Replace(ref Person p) { p = new Person(); }  // ✅ Affects original
Replace(ref person);  // Now new object
```

---

## 5️⃣ What is the difference between shallow copy and deep copy?

**See:** [Shallow Copy vs Deep Copy](./Shallow-Copy-vs-Deep-Copy.md)

**Answer:**
- **Shallow Copy:** Copies top-level object only. Nested objects are still shared references.
- **Deep Copy:** Recursively copies all nested objects. Completely independent copies.
- **Use shallow:** For simple objects with primitives
- **Use deep:** For objects with mutable reference types

---

## 6️⃣ Are strings value types or reference types? Why are they immutable?

**Answer:**
- **Type:** Strings are reference types (stored on heap)
- **But:** They are immutable (cannot be changed after creation)
- **Why immutable:**
  - Safety: Multiple references can't see unexpected changes
  - Performance: Strings can be cached/interned
  - Thread safety: No synchronization needed
  
```csharp
string s = "Hello";
s = s + " World";  // Creates NEW string, doesn't modify original

// The string "Hello" is unchanged - this creates a new string
```

---

## 7️⃣ Can a reference type be passed by value?

**Answer:**
- **Always:** Reference types are "passed by value of the reference"
- **Meaning:** The reference itself is copied, not the object
- **Result:** Object mutations are visible, but reassignment is not

```csharp
void Modify(Person p)
{
    p.Name = "New";  // ✅ Visible (modifies object)
    p = new Person();  // ❌ Not visible (reassigns reference)
}

var person = new Person { Name = "Old" };
Modify(person);
Console.WriteLine(person.Name);  // Output: "New" (not object from inside)
```

Use `ref` keyword to pass by reference if you need reassignment to be visible.

---

## 8️⃣ What is the difference between `ref`, `out`, and `in`?

**Answer:**

| Keyword | Purpose | Must Initialize | Use Case |
|---------|---------|-----------------|----------|
| **ref** | Pass by reference | Yes | Modify value, swap values |
| **out** | Return multiple values | No | Return multiple values from method |
| **in** | Pass by reference (read-only) | Yes | Performance (avoid struct copy) |

**Examples:**

```csharp
// ref - modify and return
void Increment(ref int x) { x++; }
int num = 5;
Increment(ref num);  // num is now 6

// out - return multiple values
void GetCoordinates(out int x, out int y) { x = 10; y = 20; }
GetCoordinates(out int a, out int b);  // a=10, b=20

// in - pass large struct efficiently
void PrintPoint(in Point p) { /* read-only */ }
PrintPoint(in myPoint);  // No copy made
```

---

## 9️⃣ What's the difference between `null` and default value?

**Answer:**

**Value Types:**
- Default value: `0`, `false`, or zero-initialized struct
- Cannot be `null` (unless nullable like `int?`)
- Always have a valid default

**Reference Types:**
- Default value: `null`
- `null` means no object reference

```csharp
int number;      // Default: 0 (compile error if uninitialized local)
string text;     // Default: null (compile error if uninitialized local)

public class MyClass
{
    public int x;    // Field default: 0
    public string s; // Field default: null
}

int? nullable = null;  // ✅ Nullable value type can be null
```

---

## 🔟 Scenario-Based Questions

### "Should I use class or struct for a Point?"
**Answer:** Struct - it's small (2-3 ints), immutable, and frequently created. Avoid mutable structs as they cause confusion with copying.

### "This loop with ArrayList is slow. Why?"
**Answer:** Boxing. Each `int` is wrapped in an object (heap allocation). Use `List<int>` instead for type safety and no boxing.

### "Why does modifying the list item not work for my struct?"
```csharp
List<Point> points = new List<Point> { new Point { X = 1 } };
points[0].X = 10;  // ❌ Doesn't modify the list!
```
**Answer:** Structs are value types. Accessing returns a copy. Use a class if you need to modify items, or use a temporary variable and reassign.

### "My unboxing failed with InvalidCastException. Why?"
**Answer:** You tried to unbox to a different type. Unboxing must match the original boxed type exactly. Use `as` or `is` to check first.

---

## 📝 Interview Preparation Checklist

- [ ] Can explain value types vs reference types from first principles
- [ ] Understand Stack vs Heap (including the myths)
- [ ] Know when boxing occurs and why to avoid it
- [ ] Can choose between class and struct correctly
- [ ] Understand shallow vs deep copy implications
- [ ] Know `ref`, `out`, `in` differences
- [ ] Can explain null vs default values
- [ ] Have practical examples for each concept
- [ ] Can troubleshoot common scenarios

---

## 🎯 Key Takeaways

1. **Value types** = data copied on assignment
2. **Reference types** = reference copied, object shared
3. **Stack/Heap** = understanding is nuanced, not absolute rule
4. **Boxing** = avoid using generics
5. **Class vs Struct** = choose based on identity vs value semantics
6. **Performance matters** = be aware of copies and allocations

---

**See:** [Code-Examples.md](./Code-Examples.md) for runnable demonstrations of all concepts.
