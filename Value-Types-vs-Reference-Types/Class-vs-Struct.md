# Class vs Struct - Choosing Reference vs Value Types

## 🎯 Quick Comparison

| Feature | Class | Struct |
|---------|-------|--------|
| **Type** | Reference type | Value type |
| **Storage** | Heap | Stack (usually) |
| **Assignment** | Reference copied | Value copied |
| **Inheritance** | Supports | Inherits from ValueType |
| **Null** | Can be null | Can be null? (nullable) |
| **Mutation** | Shared mutations visible | Independent copies |
| **Performance** | Heap allocation overhead | Faster, less GC pressure |
| **Use Case** | Complex objects, identity | Small, immutable data |

---

## 📌 What is a Class?

A **class** is a reference type that defines an object with identity. Multiple variables can reference the same object.

### Class Syntax

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    
    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }
    
    public void Greet()
    {
        Console.WriteLine($"Hello, I'm {Name}");
    }
}
```

### Class Characteristics

✅ **Reference semantics** - multiple variables point to same object
✅ **Identity** - objects can be compared by reference (`ReferenceEquals`)
✅ **Inheritance** - can inherit from other classes
✅ **Polymorphism** - supports virtual methods
✅ **Null support** - can be null
✅ **Garbage collected** - automatic memory management

---

## 📌 What is a Struct?

A **struct** is a value type that represents a lightweight data container. Each assignment creates a new copy.

### Struct Syntax

```csharp
public struct Point
{
    public int X { get; set; }
    public int Y { get; set; }
    
    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }
    
    public double DistanceFromOrigin()
    {
        return Math.Sqrt(X * X + Y * Y);
    }
}
```

### Struct Characteristics

✅ **Value semantics** - each variable has its own copy
✅ **No identity** - equality is based on values
✅ **Limited inheritance** - only from `object` or interfaces
✅ **Stack storage** - faster allocation/deallocation
✅ **Default constructor** - automatically available
✅ **Small size** - suitable for small data structures

---

## 🔍 Behavior Differences

### Example 1: Reference Semantics (Class)

```csharp
public class Account
{
    public string Owner { get; set; }
    public decimal Balance { get; set; }
}

// Using a class
var account1 = new Account { Owner = "Mostafa", Balance = 1000 };
var account2 = account1;

// Modify through account2
account2.Balance = 500;

Console.WriteLine(account1.Balance);  // Output: 500
Console.WriteLine(account2.Balance);  // Output: 500
// Both reference the same object!
```

**Why?** Class is a reference type - both variables point to the same object.

---

### Example 2: Value Semantics (Struct)

```csharp
public struct Point
{
    public int X { get; set; }
    public int Y { get; set; }
}

// Using a struct
var point1 = new Point { X = 10, Y = 20 };
var point2 = point1;

// Modify through point2
point2.X = 50;

Console.WriteLine(point1.X);  // Output: 10
Console.WriteLine(point2.X);  // Output: 50
// Each variable has its own copy!
```

**Why?** Struct is a value type - assignment copies the value.

---

### Example 3: Mutation Issues with Classes

```csharp
public class Temperature
{
    public double Celsius { get; set; }
}

var temp1 = new Temperature { Celsius = 20 };
var temps = new List<Temperature> { temp1 };

// Later, modify through temp1
temp1.Celsius = 30;

// The list contains the modified value!
Console.WriteLine(temps[0].Celsius);  // Output: 30
```

**Why?** The list stores a reference to the same object.

---

### Example 4: Struct Copying Issue

```csharp
public struct Person
{
    public string Name { get; set; }
}

var person1 = new Person { Name = "Mostafa" };
var list = new List<Person> { person1 };

// Retrieve from list and modify
var person2 = list[0];
person2.Name = "Ahmed";

// Original in list is unchanged
Console.WriteLine(list[0].Name);      // Output: Mostafa
Console.WriteLine(person2.Name);      // Output: Ahmed
```

**Why?** Retrieving from the list returns a copy of the struct.

---

## 🎯 When to Use Class

### Use a Class When:

1. **Object has identity** - objects are distinct even with same values
   ```csharp
   public class Employee
   {
       public string Name { get; set; }
       public int EmployeeId { get; set; }
       // Identity matters - two employees with same name are different
   }
   ```

2. **Object is mutable** - state changes frequently
   ```csharp
   public class BankAccount
   {
       public decimal Balance { get; set; }
       public void Deposit(decimal amount) { /* ... */ }
       // State changes, all references should see updates
   }
   ```

3. **Inheritance is needed** - object extends behavior
   ```csharp
   public class Vehicle { }
   public class Car : Vehicle { }
   public class Truck : Vehicle { }
   ```

4. **Object is large** - copying is expensive
   ```csharp
   public class Document
   {
       public string Content { get; set; }  // Could be MB
       // Copying MB of data is inefficient
   }
   ```

5. **Polymorphism is used** - virtual methods override
   ```csharp
   public class Shape
   {
       public virtual void Draw() { }
   }
   public class Circle : Shape
   {
       public override void Draw() { }
   }
   ```

---

## 🎯 When to Use Struct

### Use a Struct When:

1. **Represents a single value** - like a number
   ```csharp
   public struct Color
   {
       public byte R { get; }
       public byte G { get; }
       public byte B { get; }
       // Represents a single concept: a color
   }
   ```

2. **Small data container** - only a few fields
   ```csharp
   public struct Point2D
   {
       public double X { get; set; }
       public double Y { get; set; }
       // Small, frequently used
   }
   ```

3. **Should be immutable** - data doesn't change
   ```csharp
   public readonly struct DateRange
   {
       public DateTime Start { get; }
       public DateTime End { get; }
       // Once created, doesn't change
   }
   ```

4. **Lightweight and frequently created** - performance matters
   ```csharp
   // Used in tight loops - millions per frame in games
   public struct Vector3
   {
       public float X { get; set; }
       public float Y { get; set; }
       public float Z { get; set; }
   }
   ```

5. **No inheritance needed** - simple data container
   ```csharp
   public struct Pair<T>
   {
       public T First { get; set; }
       public T Second { get; set; }
   }
   ```

---

## ⚠️ Struct Pitfalls

### Pitfall 1: Unintended Copies

```csharp
public struct Student
{
    public string Name { get; set; }
}

var students = new List<Student>();
students.Add(new Student { Name = "Mostafa" });

// ❌ This gets a COPY from the list
var student = students[0];
student.Name = "Ahmed";  // Modifies the copy, not the list!

Console.WriteLine(students[0].Name);  // Output: Mostafa (unchanged)
```

**Better:** Use a reference type (class) if you need shared mutations.

---

### Pitfall 2: Boxing with Structs

```csharp
public struct Point
{
    public int X { get; set; }
}

var point = new Point { X = 10 };
object boxed = point;  // ❌ Boxing occurs!

// Unbox and modify
Point unboxed = (Point)boxed;
unboxed.X = 20;

// The original is unchanged
Console.WriteLine(point.X);      // Output: 10
Console.WriteLine(unboxed.X);    // Output: 20
```

**Avoid:** Don't assign structs to `object` or collections of `object`.

---

### Pitfall 3: Default Constructor Limitation

```csharp
public struct Point
{
    public int X { get; set; }
    public int Y { get; set; }
    
    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }
    
    // ❌ You CANNOT override the default (no-arg) constructor
    // public Point() { } // ❌ Compile error!
}

// Default constructor always exists:
var defaultPoint = new Point();  // ✅ X = 0, Y = 0
```

**Note:** In C# 10+, you can define a parameterless constructor.

---

### Pitfall 4: Mutable Struct (Anti-pattern)

```csharp
// ❌ AVOID: Mutable struct
public struct MutablePoint
{
    public int X { get; set; }
    public int Y { get; set; }
}

var points = new List<MutablePoint>
{
    new MutablePoint { X = 1, Y = 2 }
};

// ❌ This doesn't modify the list - it modifies a copy!
points[0].X = 10;

Console.WriteLine(points[0].X);  // Output: 1 (unchanged)
```

**Better:** Use `readonly struct` or make it immutable.

---

## ✅ Modern Best Practices

### Use `readonly struct` for Immutable Value Types

```csharp
// ✅ GOOD: Readonly struct prevents accidental mutation
public readonly struct ImmutablePoint
{
    public int X { get; }
    public int Y { get; }
    
    public ImmutablePoint(int x, int y)
    {
        X = x;
        Y = y;
    }
}

// Compiler prevents mutation:
// var point = new ImmutablePoint(1, 2);
// point.X = 10;  // ❌ Compile error!
```

---

### Use `record` for Immutable Reference Types

```csharp
// ✅ GOOD: Record is ideal for immutable data classes
public record Person(string Name, int Age);

var person1 = new Person("Mostafa", 30);
var person2 = new Person("Mostafa", 30);

Console.WriteLine(person1 == person2);  // Output: True (value equality)

// With expressions:
var person3 = person1 with { Name = "Ahmed" };  // Creates new record
```

---

### Use `record struct` for Small Value Types (C# 10+)

```csharp
// ✅ GOOD: Record struct combines benefits
public record struct Point(int X, int Y);

var p1 = new Point(1, 2);
var p2 = p1 with { X = 10 };

Console.WriteLine(p1 == p2);  // False (different X values)
```

---

## 🔍 Inheritance Differences

### Classes Support Full Inheritance

```csharp
public class Animal { }
public class Dog : Animal { }  // ✅ Works

public interface IMovable { void Move(); }
public class Car : IMovable { /* ... */ }  // ✅ Works
```

---

### Structs Have Limited Inheritance

```csharp
public struct Point { }

// ❌ Cannot inherit from other structs
// public struct Point3D : Point { }  // Compile error!

// ✅ Can inherit from interfaces
public struct Point : IEquatable<Point>, IComparable<Point> { /* ... */ }

// ✅ Implicitly inherits from ValueType and object
// All structs are derived from System.ValueType
```

---

## 📊 Decision Tree

```
What are you modeling?

├─ Discrete entity with identity?
│  └─ USE CLASS (Person, Employee, Account)
│
├─ Small value (coordinates, color, date)?
│  └─ USE STRUCT (Point, Color, DateTime)
│
├─ Immutable data container?
│  ├─ Simple value? → USE readonly struct
│  └─ Reference type? → USE record
│
├─ Need inheritance?
│  ├─ Must support? → USE CLASS
│  └─ Not needed? → USE STRUCT
│
├─ Performance critical?
│  ├─ Large object? → USE CLASS (less copying)
│  └─ Small, frequent? → USE STRUCT (less GC)
│
└─ Uncertain?
   └─ DEFAULT TO CLASS (more flexible)
```

---

## 📋 Common Interview Questions

### Q1: "When should I use a struct instead of a class?"
**A:** Use a struct for small, immutable value types like `Point`, `Color`, or `DateTime`. Use a class for larger objects with identity like `Person` or `Account`.

### Q2: "What's the main difference between class and struct?"
**A:** Classes are reference types (multiple variables can reference the same object); structs are value types (each variable has its own copy).

### Q3: "What's the boxing issue with structs?"
**A:** When you assign a struct to `object`, boxing occurs - the struct is copied to the heap. Unboxing extracts it back. This creates performance overhead.

### Q4: "Why should structs be immutable?"
**A:** Because structs are copied when assigned or passed, making them mutable leads to confusing behavior where modifications only affect copies.

### Q5: "Should I make my struct readonly?"
**A:** Yes, it's a best practice. `readonly struct` prevents accidental mutations and helps you write safer code.

---

## 📝 Summary Table

| Aspect | Class | Struct |
|--------|-------|--------|
| **Type Category** | Reference | Value |
| **Size** | Large or unbounded | Small and bounded |
| **Allocation** | Heap | Stack (usually) |
| **Copying** | Reference copied | Value copied |
| **Mutation** | Shared | Independent copies |
| **Inheritance** | Full support | Limited to interfaces |
| **Default State** | `null` | Default values (0, false, etc.) |
| **Performance** | GC pressure | Less GC, but copy overhead |
| **Use For** | Complex objects, identity | Lightweight values |
| **Example** | `Person`, `Account` | `Point`, `Color`, `DateTime` |

---

**Next:** Continue to [Shallow Copy vs Deep Copy](./Shallow-Copy-vs-Deep-Copy.md) to understand object copying strategies.
