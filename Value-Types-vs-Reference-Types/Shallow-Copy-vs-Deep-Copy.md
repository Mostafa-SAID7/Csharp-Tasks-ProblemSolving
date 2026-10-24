# Shallow Copy vs Deep Copy - Object Copying Strategies

## 🎯 Quick Definition

| Aspect | Shallow Copy | Deep Copy |
|--------|--------------|-----------|
| **What's Copied** | Top-level object only | Everything including nested objects |
| **References** | Still point to original objects | Point to new objects |
| **Mutations** | Affect original nested objects | Don't affect anything |
| **Memory** | Less memory used | More memory used |
| **Speed** | Fast | Slower |
| **Risk** | Unintended mutations | Safe but expensive |
| **Use Case** | Simple objects with primitives | Complex objects with references |

---

## 📌 What is a Shallow Copy?

A **shallow copy** creates a new object but copies only the **references** to nested objects, not the objects themselves.

### Visual Representation

```
Original Object:
┌─────────────────────────────┐
│ Person                      │
│ Name: "Mostafa" (value)    │
│ Address: ─────┐            │
└────────────┼──┘            │
             │               │
             v               │
        ┌──────────────┐     │
        │ Address      │     │
        │ City: "Cairo"│◄────┘
        │ Zip: "12345" │
        └──────────────┘

Shallow Copy:
┌─────────────────────────────┐       Original Address
│ Person (NEW)                │       ┌──────────────┐
│ Name: "Mostafa" (COPIED)    │       │ Address      │
│ Address: ─────┐            │───────→│ City: "Cairo"│
└────────────┼──┘            │       │ Zip: "12345" │
             └────────────────────────┘
                (SAME REFERENCE)

Both references point to the SAME Address object!
```

### Simple Example

```csharp
public class Address
{
    public string City { get; set; }
    public string Zip { get; set; }
}

public class Person
{
    public string Name { get; set; }
    public Address Address { get; set; }
}

// Original
var original = new Person
{
    Name = "Mostafa",
    Address = new Address { City = "Cairo", Zip = "12345" }
};

// Shallow copy - using MemberwiseClone()
var shallow = (Person)original.MemberwiseClone();

// Modify through the shallow copy
shallow.Name = "Ahmed";
shallow.Address.City = "Giza";

Console.WriteLine(original.Name);              // Output: Mostafa (independent)
Console.WriteLine(original.Address.City);      // Output: Giza (AFFECTED!)
Console.WriteLine(shallow.Name);               // Output: Ahmed
Console.WriteLine(shallow.Address.City);       // Output: Giza
```

**Key Point:** The `Address` reference was copied, so both objects share the same `Address`.

---

## 📌 What is a Deep Copy?

A **deep copy** creates a new object and recursively copies **all nested objects** as well.

### Visual Representation

```
Deep Copy:
┌─────────────────────────────┐       NEW Address (copy)
│ Person (NEW)                │       ┌──────────────┐
│ Name: "Mostafa" (COPIED)    │───┐   │ Address (NEW)│
│ Address: ─────┐            │   └──→│ City: "Cairo"│
└────────────┼──┘            │       │ Zip: "12345" │
             │               │       └──────────────┘
             │ (DIFFERENT    │
             │  REFERENCE)   │
             │               │
             v               │
        Original Address     │
        ┌──────────────┐     │
        │ Address      │     │
        │ City: "Cairo"│◄────┘
        │ Zip: "12345" │
        └──────────────┘

Both have SEPARATE Address objects!
```

### Simple Example

```csharp
public class Address
{
    public string City { get; set; }
    public string Zip { get; set; }
}

public class Person : ICloneable
{
    public string Name { get; set; }
    public Address Address { get; set; }
    
    // Deep copy implementation
    public object Clone()
    {
        return new Person
        {
            Name = this.Name,  // Copy value
            Address = new Address  // Create NEW Address
            {
                City = this.Address.City,
                Zip = this.Address.Zip
            }
        };
    }
}

// Original
var original = new Person
{
    Name = "Mostafa",
    Address = new Address { City = "Cairo", Zip = "12345" }
};

// Deep copy
var deep = (Person)original.Clone();

// Modify through the deep copy
deep.Name = "Ahmed";
deep.Address.City = "Giza";

Console.WriteLine(original.Name);              // Output: Mostafa
Console.WriteLine(original.Address.City);      // Output: Cairo (UNAFFECTED!)
Console.WriteLine(deep.Name);                  // Output: Ahmed
Console.WriteLine(deep.Address.City);          // Output: Giza
```

**Key Point:** A completely new `Address` was created, so the original is unaffected.

---

## 🔍 Detailed Comparison Examples

### Example 1: Array Shallow Copy

```csharp
// Original array
int[] original = new int[] { 1, 2, 3 };

// Shallow copy - creates new array but points to same elements
// (doesn't apply to int array as ints are value types)
int[] shallow = (int[])original.Clone();

shallow[0] = 100;

Console.WriteLine(original[0]);  // Output: 1 (independent for value types)
Console.WriteLine(shallow[0]);   // Output: 100
```

**Note:** For arrays of value types, shallow copy is sufficient.

---

### Example 2: Array of Objects Shallow Copy

```csharp
public class Book
{
    public string Title { get; set; }
}

// Original array of objects
Book[] original = new Book[]
{
    new Book { Title = "C# Guide" },
    new Book { Title = "Design Patterns" }
};

// Shallow copy - creates new array but references SAME books
Book[] shallow = (Book[])original.Clone();

// Modify through shallow copy
shallow[0].Title = "C# Advanced";

Console.WriteLine(original[0].Title);  // Output: C# Advanced (AFFECTED!)
Console.WriteLine(shallow[0].Title);   // Output: C# Advanced
```

**Problem:** The books themselves are not copied, only the array.

---

### Example 3: Dictionary Shallow Copy

```csharp
public class Employee
{
    public string Name { get; set; }
}

// Original dictionary
var original = new Dictionary<string, Employee>
{
    { "emp1", new Employee { Name = "Mostafa" } }
};

// Shallow copy
var shallow = new Dictionary<string, Employee>(original);

// Modify employee through shallow copy
shallow["emp1"].Name = "Ahmed";

Console.WriteLine(original["emp1"].Name);  // Output: Ahmed (AFFECTED!)
Console.WriteLine(shallow["emp1"].Name);   // Output: Ahmed
```

**Problem:** The `Employee` objects are not copied.

---

## 🔨 Methods for Creating Copies

### Method 1: MemberwiseClone() - Shallow Copy

```csharp
public class Person
{
    public string Name { get; set; }
    public Address Address { get; set; }
}

var original = new Person { /* ... */ };

// Built-in shallow copy method
var shallow = (Person)original.MemberwiseClone();
```

**Characteristics:**
- ✅ Built-in, no implementation needed
- ✅ Copies all fields
- ❌ Only shallow (references copied)
- ✅ Works for any object (protected method)

---

### Method 2: ICloneable Interface - Deep Copy

```csharp
public class Person : ICloneable
{
    public string Name { get; set; }
    public Address Address { get; set; }
    
    public object Clone()
    {
        // Manually create new objects recursively
        return new Person
        {
            Name = this.Name,
            Address = new Address
            {
                City = this.Address.City,
                Zip = this.Address.Zip
            }
        };
    }
}

var original = new Person { /* ... */ };
var deep = (Person)original.Clone();
```

**Characteristics:**
- ✅ Full control over what's copied
- ✅ Type-safe when implemented explicitly
- ❌ Must be implemented manually
- ❌ `ICloneable` interface is not generic (returns object)

---

### Method 3: Copy Constructor - Deep Copy

```csharp
public class Person
{
    public string Name { get; set; }
    public Address Address { get; set; }
    
    // Copy constructor
    public Person(Person other)
    {
        Name = other.Name;
        Address = new Address
        {
            City = other.Address.City,
            Zip = other.Address.Zip
        };
    }
}

var original = new Person { /* ... */ };
var deep = new Person(original);  // Type-safe, clear intent
```

**Characteristics:**
- ✅ Type-safe, no casting needed
- ✅ Clear intent
- ✅ Works with inheritance
- ❌ Must be implemented manually

---

### Method 4: Manual Property Copying - Deep Copy

```csharp
public class Person
{
    public string Name { get; set; }
    public Address Address { get; set; }
}

var original = new Person { /* ... */ };

// Manual deep copy
var deep = new Person
{
    Name = original.Name,
    Address = new Address
    {
        City = original.Address.City,
        Zip = original.Address.Zip
    }
};
```

**Characteristics:**
- ✅ Explicit and clear
- ✅ Full control
- ❌ Verbose
- ❌ Tedious for large objects

---

### Method 5: Serialization (JSON) - Deep Copy

```csharp
using System.Text.Json;

public class Person
{
    public string Name { get; set; }
    public Address Address { get; set; }
}

var original = new Person { /* ... */ };

// Deep copy via JSON serialization
string json = JsonSerializer.Serialize(original);
var deep = JsonSerializer.Deserialize<Person>(json);
```

**Characteristics:**
- ✅ Simple one-liner
- ✅ Works for complex objects
- ❌ Performance overhead (serialization)
- ❌ Requires serialization support
- ❌ Loses non-serializable data

---

### Method 6: Reflection-Based Deep Copy

```csharp
using System.Reflection;

public static T DeepCopy<T>(T source) where T : new()
{
    T copy = new T();
    PropertyInfo[] properties = source.GetType().GetProperties();
    
    foreach (PropertyInfo property in properties)
    {
        if (property.CanRead && property.CanWrite)
        {
            object value = property.GetValue(source);
            
            // Recursively copy if object
            if (value != null && value.GetType().IsClass)
            {
                value = DeepCopy(value);
            }
            
            property.SetValue(copy, value);
        }
    }
    
    return copy;
}

var original = new Person { /* ... */ };
var deep = DeepCopy(original);
```

**Characteristics:**
- ✅ Generic and reusable
- ❌ Slow (reflection overhead)
- ❌ Complex implementation
- ❌ Potential errors

---

## 🚀 Performance Impact

### Shallow Copy Performance

```csharp
const int iterations = 100_000;

var original = new Person
{
    Name = "Mostafa",
    Address = new Address { City = "Cairo", Zip = "12345" }
};

var watch = Stopwatch.StartNew();
for (int i = 0; i < iterations; i++)
{
    var copy = (Person)original.MemberwiseClone();  // Fast!
}
watch.Stop();
Console.WriteLine($"Shallow copy: {watch.ElapsedMilliseconds}ms");  // ~5-10ms
```

---

### Deep Copy Performance

```csharp
var watch = Stopwatch.StartNew();
for (int i = 0; i < iterations; i++)
{
    var copy = (Person)original.Clone();  // Slower
}
watch.Stop();
Console.WriteLine($"Deep copy: {watch.ElapsedMilliseconds}ms");  // ~50-100ms
// Much slower due to creating new nested objects!
```

---

## 🧠 When to Use Each

### Use Shallow Copy When:

```csharp
✅ Object contains only value types and strings
public class Point
{
    public int X { get; set; }
    public int Y { get; set; }
    // All value types - shallow copy is sufficient
}

✅ You explicitly want shared nested objects
var list1 = new List<string> { "a", "b" };
var list2 = new List<string>(list1);  // Shallow copy
// Both lists share the same strings (usually fine)

✅ Performance is critical and risk is acceptable
foreach (var item in largeCollection)
{
    var shallow = (Item)item.MemberwiseClone();  // Fast
}
```

---

### Use Deep Copy When:

```csharp
✅ Object contains mutable reference types
public class Company
{
    public string Name { get; set; }
    public List<Employee> Employees { get; set; }  // Mutable
    // Deep copy needed!
}

✅ You need complete independence
var original = GetConfiguration();
var backup = original.DeepCopy();  // Completely independent

✅ Modifying copy should not affect original
var document = database.GetDocument();
var edit = document.DeepCopy();  // Modify safely
```

---

## 📋 Decision Tree

```
Do you need to copy an object?

├─ Does object contain only primitives/strings?
│  └─ Use SHALLOW COPY (fast, simple)
│
├─ Does object contain mutable reference types?
│  └─ Use DEEP COPY (safe)
│
├─ Is performance critical?
│  ├─ Shallow copy acceptable? → Use SHALLOW COPY
│  └─ Need deep copy? → Use COPY CONSTRUCTOR (faster than reflection)
│
├─ How complex is the object?
│  ├─ Simple? → Use COPY CONSTRUCTOR
│  ├─ Complex? → Use JSON SERIALIZATION or REFLECTION
│  └─ Very complex? → Use JSON SERIALIZATION
│
└─ What's your priority?
   ├─ Type safety → COPY CONSTRUCTOR
   ├─ Simplicity → JSON SERIALIZATION
   ├─ Performance → SHALLOW COPY or COPY CONSTRUCTOR
   └─ Flexibility → REFLECTION-BASED
```

---

## 🔍 Common Interview Questions

### Q1: "What's the difference between shallow and deep copy?"
**A:** Shallow copy copies only references to nested objects; deep copy recursively copies all nested objects. Shallow copy is faster but modifying nested objects affects both copies.

### Q2: "When should I use shallow copy vs deep copy?"
**A:** Use shallow copy for simple objects with primitives. Use deep copy when the object contains mutable reference types and you need complete independence.

### Q3: "What's the best way to implement deep copy?"
**A:** Copy constructor is best for type safety and clarity. JSON serialization is good for complex objects. Reflection is generic but slower.

### Q4: "Why is MemberwiseClone() not enough?"
**A:** `MemberwiseClone()` is a shallow copy - it doesn't copy nested objects, only their references. Modifying nested objects affects the original.

### Q5: "Can I use foreach to create deep copies?"
**A:** No, simple iteration only calls the method. You need specific copy logic for each type.

---

## 📝 Summary Table

| Method | Type | Speed | Safety | Complexity | Use Case |
|--------|------|-------|--------|-----------|----------|
| MemberwiseClone() | Shallow | Very fast | Low | Low | Simple objects |
| ICloneable | Deep | Medium | High | High | Standard interface |
| Copy Constructor | Deep | Fast | Very High | Low | Recommended |
| Manual Assignment | Deep | Fast | Very High | Medium | Small objects |
| JSON Serialization | Deep | Slow | High | Low | Complex objects |
| Reflection | Deep | Very Slow | Medium | High | Generic scenarios |

---

**Next:** Continue to [Follow-up Questions](./Follow-up-Questions.md) to review common interview questions.
