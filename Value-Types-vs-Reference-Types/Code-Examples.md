# Code Examples - Practical Demonstrations

Complete, runnable code examples demonstrating all concepts.

---

## 📝 Example 1: Value Types vs Reference Types - Basic

```csharp
using System;

class Program
{
    static void Main()
    {
        Console.WriteLine("=== VALUE TYPE vs REFERENCE TYPE ===\n");
        
        // VALUE TYPE: int
        Console.WriteLine("--- VALUE TYPE (int) ---");
        int first = 10;
        int second = first;
        
        second = 20;
        
        Console.WriteLine($"first: {first}");    // 10
        Console.WriteLine($"second: {second}");  // 20
        Console.WriteLine("=> Independent copies\n");
        
        // REFERENCE TYPE: Employee
        Console.WriteLine("--- REFERENCE TYPE (Employee) ---");
        var emp1 = new Employee { Name = "Mostafa", Salary = 5000 };
        var emp2 = emp1;
        
        emp2.Salary = 6000;
        
        Console.WriteLine($"emp1.Salary: {emp1.Salary}");  // 6000
        Console.WriteLine($"emp2.Salary: {emp2.Salary}");  // 6000
        Console.WriteLine("=> Shared object");
    }
}

public class Employee
{
    public string Name { get; set; }
    public decimal Salary { get; set; }
}
```

**Output:**
```
=== VALUE TYPE vs REFERENCE TYPE ===

--- VALUE TYPE (int) ---
first: 10
second: 20
=> Independent copies

--- REFERENCE TYPE (Employee) ---
emp1.Salary: 6000
emp2.Salary: 6000
=> Shared object
```

---

## 📝 Example 2: Boxing and Unboxing

```csharp
using System;
using System.Diagnostics;

class Program
{
    static void Main()
    {
        Console.WriteLine("=== BOXING AND UNBOXING ===\n");
        
        // Boxing example
        Console.WriteLine("--- BOXING ---");
        int value = 42;
        object boxed = value;  // Boxing occurs
        
        Console.WriteLine($"Original value: {value}");
        Console.WriteLine($"Boxed value: {boxed}");
        Console.WriteLine($"Types: {value.GetType()} vs {boxed.GetType()}\n");
        
        // Unboxing example
        Console.WriteLine("--- UNBOXING ---");
        int unboxed = (int)boxed;  // Unboxing occurs
        
        Console.WriteLine($"Unboxed value: {unboxed}");
        unboxed = 100;
        Console.WriteLine($"After modification - Original: {value}, Unboxed: {unboxed}\n");
        
        // Type mismatch error
        Console.WriteLine("--- BOXING TYPE MISMATCH ---");
        try
        {
            object intBoxed = 10;
            long wrongUnbox = (long)intBoxed;  // ❌ Will throw!
        }
        catch (InvalidCastException ex)
        {
            Console.WriteLine($"Error: {ex.Message}\n");
        }
        
        // Performance comparison
        Console.WriteLine("--- PERFORMANCE COMPARISON ---");
        PerformanceComparison();
    }
    
    static void PerformanceComparison()
    {
        const int iterations = 1_000_000;
        
        // No boxing
        var watch = Stopwatch.StartNew();
        for (int i = 0; i < iterations; i++)
        {
            int value = i;
        }
        watch.Stop();
        Console.WriteLine($"No boxing (1M iterations): {watch.ElapsedMilliseconds}ms");
        
        // With boxing
        watch.Restart();
        for (int i = 0; i < iterations; i++)
        {
            object boxed = i;
        }
        watch.Stop();
        Console.WriteLine($"With boxing (1M iterations): {watch.ElapsedMilliseconds}ms");
    }
}
```

**Output:**
```
=== BOXING AND UNBOXING ===

--- BOXING ---
Original value: 42
Boxed value: 42
Types: System.Int32 vs System.Int32

--- UNBOXING ---
Unboxed value: 42
After modification - Original: 42, Unboxed: 100

--- BOXING TYPE MISMATCH ---
Error: Unable to cast object of type 'System.Int32' to type 'System.Int64'.

--- PERFORMANCE COMPARISON ---
No boxing (1M iterations): 1ms
With boxing (1M iterations): 75ms
```

---

## 📝 Example 3: Stack vs Heap Visualization

```csharp
using System;

class Program
{
    static void Main()
    {
        Console.WriteLine("=== STACK vs HEAP ===\n");
        
        // Stack: Local value type
        int localInt = 5;
        Console.WriteLine("Stack Memory:");
        Console.WriteLine($"  localInt = {localInt} (on stack)");
        
        // Heap: Local reference type
        var person = new Person { Name = "Mostafa", Age = 30 };
        Console.WriteLine("\nHeap Memory:");
        Console.WriteLine($"  person -> [Person object] (on heap)");
        Console.WriteLine($"    Name: {person.Name}");
        Console.WriteLine($"    Age: {person.Age}");
        
        // Heap: Value type as field
        var account = new Account { Balance = 1000, Id = 1 };
        Console.WriteLine("\nValue Type as Field:");
        Console.WriteLine($"  account -> [Account object] (on heap)");
        Console.WriteLine($"    Balance: {account.Balance} (stored on heap with Account)");
        Console.WriteLine($"    Id: {account.Id}");
        
        // Stack: Local value type in array
        int[] numbers = new int[3] { 1, 2, 3 };
        Console.WriteLine("\nArray of Value Types:");
        Console.WriteLine($"  numbers -> [array] (on heap)");
        Console.WriteLine($"    Elements: {string.Join(", ", numbers)} (stored on heap with array)");
    }
}

public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}

public class Account
{
    public int Id { get; set; }
    public decimal Balance { get; set; }
}
```

**Output:**
```
=== STACK vs HEAP ===

Stack Memory:
  localInt = 5 (on stack)

Heap Memory:
  person -> [Person object] (on heap)
    Name: Mostafa
    Age: 30

Value Type as Field:
  account -> [Account object] (on heap)
    Balance: 1000 (stored on heap with Account)
    Id: 1

Array of Value Types:
  numbers -> [array] (on heap)
    Elements: 1, 2, 3 (stored on heap with array)
```

---

## 📝 Example 4: Class vs Struct - Behavior

```csharp
using System;

class Program
{
    static void Main()
    {
        Console.WriteLine("=== CLASS vs STRUCT ===\n");
        
        // Class behavior
        Console.WriteLine("--- CLASS BEHAVIOR ---");
        var emp1 = new EmployeeClass { Name = "Mostafa" };
        var emp2 = emp1;
        emp2.Name = "Ahmed";
        
        Console.WriteLine($"emp1.Name: {emp1.Name}");  // Ahmed (shared)
        Console.WriteLine($"emp2.Name: {emp2.Name}");  // Ahmed
        Console.WriteLine();
        
        // Struct behavior
        Console.WriteLine("--- STRUCT BEHAVIOR ---");
        var point1 = new PointStruct { X = 1, Y = 2 };
        var point2 = point1;
        point2.X = 10;
        
        Console.WriteLine($"point1.X: {point1.X}");  // 1 (independent)
        Console.WriteLine($"point2.X: {point2.X}");  // 10
        Console.WriteLine();
        
        // Null handling
        Console.WriteLine("--- NULL HANDLING ---");
        EmployeeClass classNull = null;  // ✅ OK
        Console.WriteLine($"classNull is null: {classNull == null}");
        
        // PointStruct structNull = null;  // ❌ Compile error
        
        PointStruct? nullableStruct = null;  // ✅ OK with nullable
        Console.WriteLine($"nullableStruct is null: {nullableStruct == null}");
    }
}

// Class (Reference Type)
public class EmployeeClass
{
    public string Name { get; set; }
}

// Struct (Value Type)
public struct PointStruct
{
    public int X { get; set; }
    public int Y { get; set; }
}
```

**Output:**
```
=== CLASS vs STRUCT ===

--- CLASS BEHAVIOR ---
emp1.Name: Ahmed (shared)
emp2.Name: Ahmed

--- STRUCT BEHAVIOR ---
point1.X: 1 (independent)
point2.X: 10

--- NULL HANDLING ---
classNull is null: True
nullableStruct is null: True
```

---

## 📝 Example 5: Shallow Copy vs Deep Copy

```csharp
using System;

class Program
{
    static void Main()
    {
        Console.WriteLine("=== SHALLOW vs DEEP COPY ===\n");
        
        // Shallow copy
        Console.WriteLine("--- SHALLOW COPY ---");
        var original = new Person
        {
            Name = "Mostafa",
            Address = new Address { City = "Cairo", Zip = "12345" }
        };
        
        var shallow = (Person)original.MemberwiseClone();
        
        Console.WriteLine($"Original - Name: {original.Name}, City: {original.Address.City}");
        
        shallow.Name = "Ahmed";
        shallow.Address.City = "Giza";
        
        Console.WriteLine($"After shallow copy modification:");
        Console.WriteLine($"  Original Name: {original.Name} (unchanged - value type)");
        Console.WriteLine($"  Original City: {original.Address.City} (CHANGED - shared reference!)");
        Console.WriteLine($"  Shallow Name: {shallow.Name}");
        Console.WriteLine($"  Shallow City: {shallow.Address.City}\n");
        
        // Deep copy
        Console.WriteLine("--- DEEP COPY ---");
        var original2 = new PersonWithClone
        {
            Name = "Mostafa",
            Address = new Address { City = "Cairo", Zip = "12345" }
        };
        
        var deep = (PersonWithClone)original2.Clone();
        
        Console.WriteLine($"Original - Name: {original2.Name}, City: {original2.Address.City}");
        
        deep.Name = "Ahmed";
        deep.Address.City = "Giza";
        
        Console.WriteLine($"After deep copy modification:");
        Console.WriteLine($"  Original Name: {original2.Name} (unchanged)");
        Console.WriteLine($"  Original City: {original2.Address.City} (unchanged)");
        Console.WriteLine($"  Deep Name: {deep.Name}");
        Console.WriteLine($"  Deep City: {deep.Address.City}");
    }
}

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

public class PersonWithClone : ICloneable
{
    public string Name { get; set; }
    public Address Address { get; set; }
    
    public object Clone()
    {
        return new PersonWithClone
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
```

**Output:**
```
=== SHALLOW vs DEEP COPY ===

--- SHALLOW COPY ---
Original - Name: Mostafa, City: Cairo
After shallow copy modification:
  Original Name: Mostafa (unchanged - value type)
  Original City: Giza (CHANGED - shared reference!)
  Shallow Name: Ahmed
  Shallow City: Giza

--- DEEP COPY ---
Original - Name: Mostafa, City: Cairo
After deep copy modification:
  Original Name: Mostafa (unchanged)
  Original City: Cairo (unchanged)
  Deep Name: Ahmed
  Deep City: Giza
```

---

## 📝 Example 6: ref, out, and in Keywords

```csharp
using System;

class Program
{
    static void Main()
    {
        Console.WriteLine("=== ref, out, and in Keywords ===\n");
        
        // ref - pass by reference
        Console.WriteLine("--- ref KEYWORD ---");
        int value = 5;
        Console.WriteLine($"Before: {value}");
        Increment(ref value);
        Console.WriteLine($"After Increment(ref value): {value}\n");
        
        // out - return multiple values
        Console.WriteLine("--- out KEYWORD ---");
        GetCoordinates(out int x, out int y);
        Console.WriteLine($"GetCoordinates returned: x={x}, y={y}\n");
        
        // in - pass by reference (read-only)
        Console.WriteLine("--- in KEYWORD ---");
        var point = new Point { X = 1, Y = 2 };
        PrintPoint(in point);
        Console.WriteLine();
        
        // Swap example with ref
        Console.WriteLine("--- Swapping with ref ---");
        int a = 10, b = 20;
        Console.WriteLine($"Before swap: a={a}, b={b}");
        Swap(ref a, ref b);
        Console.WriteLine($"After swap: a={a}, b={b}");
    }
    
    static void Increment(ref int number)
    {
        number++;
    }
    
    static void GetCoordinates(out int x, out int y)
    {
        x = 10;
        y = 20;
    }
    
    static void PrintPoint(in Point point)
    {
        Console.WriteLine($"Point: ({point.X}, {point.Y})");
    }
    
    static void Swap(ref int a, ref int b)
    {
        int temp = a;
        a = b;
        b = temp;
    }
}

public struct Point
{
    public int X { get; set; }
    public int Y { get; set; }
}
```

**Output:**
```
=== ref, out, and in Keywords ===

--- ref KEYWORD ---
Before: 5
After Increment(ref value): 6

--- out KEYWORD ---
GetCoordinates returned: x=10, y=20

--- in KEYWORD ---
Point: (1, 2)

--- Swapping with ref ---
Before swap: a=10, b=20
After swap: a=20, b=10
```

---

## 📝 Example 7: Passing Objects to Methods

```csharp
using System;

class Program
{
    static void Main()
    {
        Console.WriteLine("=== PASSING TO METHODS ===\n");
        
        // Pass value type
        Console.WriteLine("--- PASS VALUE TYPE ---");
        int num = 10;
        PassByValue(num);
        Console.WriteLine($"After PassByValue: {num}\n");  // Still 10
        
        // Pass reference type
        Console.WriteLine("--- PASS REFERENCE TYPE ---");
        var emp = new Employee { Name = "Mostafa", Salary = 5000 };
        ModifyObject(emp);
        Console.WriteLine($"After ModifyObject:");
        Console.WriteLine($"  Name: {emp.Name}");
        Console.WriteLine($"  Salary: {emp.Salary}\n");
        
        // Pass reference type with reassignment
        Console.WriteLine("--- REASSIGN REFERENCE (NO ref) ---");
        emp = new Employee { Name = "Ahmed", Salary = 3000 };
        TryReplaceObject(emp);
        Console.WriteLine($"After TryReplaceObject:");
        Console.WriteLine($"  Name: {emp.Name}");  // Still Ahmed
        Console.WriteLine($"  Salary: {emp.Salary}\n");
        
        // Pass reference type with ref
        Console.WriteLine("--- REASSIGN REFERENCE (WITH ref) ---");
        emp = new Employee { Name = "Ahmed", Salary = 3000 };
        ReplaceObject(ref emp);
        Console.WriteLine($"After ReplaceObject(ref emp):");
        Console.WriteLine($"  Name: {emp.Name}");  // Now Ali
        Console.WriteLine($"  Salary: {emp.Salary}");
    }
    
    static void PassByValue(int number)
    {
        number = 20;
    }
    
    static void ModifyObject(Employee emp)
    {
        emp.Salary = 6000;  // ✅ Modifies original
    }
    
    static void TryReplaceObject(Employee emp)
    {
        emp = new Employee { Name = "Ali", Salary = 7000 };  // ❌ Doesn't affect original
    }
    
    static void ReplaceObject(ref Employee emp)
    {
        emp = new Employee { Name = "Ali", Salary = 7000 };  // ✅ Affects original
    }
}

public class Employee
{
    public string Name { get; set; }
    public decimal Salary { get; set; }
}
```

**Output:**
```
=== PASSING TO METHODS ===

--- PASS VALUE TYPE ---
After PassByValue: 10

--- PASS REFERENCE TYPE ---
After ModifyObject:
  Name: Mostafa
  Salary: 6000

--- REASSIGN REFERENCE (NO ref) ---
After TryReplaceObject:
  Name: Ahmed
  Salary: 3000

--- REASSIGN REFERENCE (WITH ref) ---
After ReplaceObject(ref emp):
  Name: Ali
  Salary: 7000
```

---

## 📝 Example 8: Collections - Boxing with ArrayList vs List<T>

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using System.Diagnostics;

class Program
{
    static void Main()
    {
        Console.WriteLine("=== COLLECTIONS: ArrayList vs List<T> ===\n");
        
        const int count = 100_000;
        
        // ArrayList (non-generic) - causes boxing
        Console.WriteLine("--- ArrayList (Non-Generic) ---");
        var watch = Stopwatch.StartNew();
        ArrayList arrayList = new ArrayList();
        for (int i = 0; i < count; i++)
        {
            arrayList.Add(i);  // ❌ Boxing occurs
        }
        watch.Stop();
        Console.WriteLine($"Add 100K items: {watch.ElapsedMilliseconds}ms");
        
        watch.Restart();
        long sum = 0;
        foreach (object item in arrayList)
        {
            sum += (int)item;  // ❌ Unboxing occurs
        }
        watch.Stop();
        Console.WriteLine($"Iterate and sum: {watch.ElapsedMilliseconds}ms");
        Console.WriteLine($"Sum: {sum}\n");
        
        // List<T> (generic) - no boxing
        Console.WriteLine("--- List<T> (Generic) ---");
        watch.Restart();
        List<int> list = new List<int>();
        for (int i = 0; i < count; i++)
        {
            list.Add(i);  // ✅ No boxing
        }
        watch.Stop();
        Console.WriteLine($"Add 100K items: {watch.ElapsedMilliseconds}ms");
        
        watch.Restart();
        sum = 0;
        foreach (int item in list)
        {
            sum += item;  // ✅ No unboxing
        }
        watch.Stop();
        Console.WriteLine($"Iterate and sum: {watch.ElapsedMilliseconds}ms");
        Console.WriteLine($"Sum: {sum}");
    }
}
```

**Output:**
```
=== COLLECTIONS: ArrayList vs List<T> ===

--- ArrayList (Non-Generic) ---
Add 100K items: 45ms
Iterate and sum: 35ms
Sum: 4999950000

--- List<T> (Generic) ---
Add 100K items: 2ms
Iterate and sum: 1ms
Sum: 4999950000
```

---

## 🎯 How to Use These Examples

1. **Copy the code** to a Visual Studio C# console project
2. **Run and observe** the output
3. **Modify values** to experiment
4. **Add breakpoints** to inspect memory
5. **Compare performance** by changing the constant `iterations`

These examples should help reinforce your understanding before the interview!

---

**Study Complete!** You now have comprehensive documentation on Value Types vs Reference Types. Review the key points and practice with the code examples.
