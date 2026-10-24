# Boxing vs Unboxing - Type Conversion

## 🎯 Quick Definition

| Operation | What Happens | Cost | When Used |
|-----------|-------------|------|-----------|
| **Boxing** | Value type → Reference type | Allocates heap memory | Passing to `object` |
| **Unboxing** | Reference type → Value type | Copies from heap to stack | Retrieving from `object` |
| **Implicit Boxing** | Automatic by compiler | Performance cost | `object x = 5;` |
| **Explicit Unboxing** | Requires casting | Can throw exception | `int x = (int)obj;` |

---

## 📌 What is Boxing?

**Boxing** is the process of converting a value type into a reference type (wrapping it in an object).

### How Boxing Works

1. **A new object is allocated on the heap**
2. **The value is copied into the object**
3. **A reference to the object is returned**

### Visual Representation

```
Value Type: int x = 10
STACK: [x = 10]

After Boxing:
STACK: [reference] ──→ HEAP: [boxed int object with value 10]
```

### Simple Example

```csharp
int value = 25;              // Value type on stack
object boxed = value;        // Boxing happens here!

// What the compiler does internally:
// 1. Allocate memory on heap for boxed int
// 2. Copy value 25 into the heap object
// 3. Store reference in 'boxed'
```

---

## 🎯 When Boxing Occurs (Implicit)

Boxing happens **automatically** (implicitly) in these scenarios:

### 1️⃣ Assigning Value Type to `object`

```csharp
int num = 5;
object obj = num;  // ✅ Boxing occurs automatically
```

### 2️⃣ Passing Value Type to Method Expecting `object`

```csharp
void PrintObject(object obj)
{
    Console.WriteLine(obj);
}

int age = 30;
PrintObject(age);  // ✅ Boxing occurs - age is wrapped in object
```

### 3️⃣ Passing Value Type to Method Expecting Interface

```csharp
void PrintValue(IComparable comparable)
{
    // ...
}

int num = 10;
PrintValue(num);  // ✅ Boxing occurs - int implements IComparable
```

### 4️⃣ Collections Without Generics (Pre-Generic Collections)

```csharp
ArrayList list = new ArrayList();
int value = 5;
list.Add(value);  // ✅ Boxing occurs - ArrayList expects object
```

### 5️⃣ String Formatting/Interpolation with Value Types

```csharp
int count = 5;
string message = $"Count: {count}";  // ❌ Modern boxing optimization
// Older: string.Format() would box the value
```

---

## 📌 What is Unboxing?

**Unboxing** is the process of converting a reference type (boxed value) back to its original value type.

### How Unboxing Works

1. **Checks if the object is actually a boxed value type**
2. **Copies the value from the heap object to the stack**
3. **Returns the value**

### Visual Representation

```
HEAP: [boxed int object with value 10]
            ↓ Unboxing
STACK: [value = 10]
```

### Simple Example

```csharp
object boxed = 25;           // Boxing: int wrapped in object
int value = (int)boxed;      // Unboxing: extract int from object

// What happens internally:
// 1. Check if boxed is actually an int
// 2. Copy the value (25) from heap to stack
// 3. Store in 'value'
```

---

## ⚠️ Unboxing Rules - Very Important!

### Rule 1: Type Must Match Exactly

```csharp
object boxed = 10;        // int is boxed

// ✅ Correct: Unbox to same type (int)
int value = (int)boxed;   // Works

// ❌ WRONG: Try to unbox to different type (long)
long value = (long)boxed; // ❌ InvalidCastException!
```

**Why?** The object remembers what type it was boxed as. You must unbox to the exact same type.

### Rule 2: Null Reference Handling

```csharp
object boxed = null;

// ✅ OK: Unboxing null to nullable type
int? value = (int?)boxed;  // value = null

// ❌ WRONG: Unboxing null to non-nullable type
int value = (int)boxed;    // ❌ NullReferenceException!
```

### Rule 3: Check Before Unboxing (Best Practice)

```csharp
object obj = 5;

// ✅ Safe: Use 'is' to check first
if (obj is int)
{
    int value = (int)obj;
    Console.WriteLine($"Unboxed: {value}");
}
else
{
    Console.WriteLine("Not an int!");
}

// ✅ Safe: Use 'as' for reference types (returns null if not match)
int? value2 = obj as int?;  // value2 = 5 or null
```

---

## 🔍 Detailed Examples

### Example 1: Complete Boxing & Unboxing Cycle

```csharp
// Step 1: Create a value type
int originalValue = 42;
Console.WriteLine($"Original: {originalValue}");  // Output: 42

// Step 2: Box the value
object boxed = originalValue;  // Allocates heap memory, copies value

// Step 3: Unbox the value
int unboxedValue = (int)boxed;  // Copies value back to stack
Console.WriteLine($"Unboxed: {unboxedValue}");  // Output: 42

// Step 4: Verify they're independent
unboxedValue = 100;
Console.WriteLine($"Original: {originalValue}");    // Output: 42 (unchanged)
Console.WriteLine($"Unboxed: {unboxedValue}");      // Output: 100
```

**Key Point:** Boxing creates a **copy**. Modifying the unboxed value doesn't affect the original.

---

### Example 2: The Type Mismatch Error

```csharp
// Scenario: Wrong unboxing type
object boxed = 10;  // int is boxed

try
{
    // ❌ Trying to unbox as long
    long value = (long)boxed;
}
catch (InvalidCastException ex)
{
    // Exception occurs here!
    Console.WriteLine($"Error: {ex.Message}");
    // Output: "Unable to cast object of type 'System.Int32' to type 'System.Int64'."
}
```

---

### Example 3: Collections Example

```csharp
// Old way - ArrayList (requires boxing/unboxing)
ArrayList oldList = new ArrayList();

oldList.Add(5);      // ✅ Boxing: int → object
oldList.Add(10);     // ✅ Boxing: int → object
oldList.Add(15);     // ✅ Boxing: int → object

foreach (object item in oldList)
{
    int value = (int)item;  // ❌ Unboxing: object → int (slow!)
    Console.WriteLine(value);
}

// New way - List<T> (no boxing/unboxing)
List<int> newList = new List<int>();

newList.Add(5);      // ✅ No boxing
newList.Add(10);     // ✅ No boxing
newList.Add(15);     // ✅ No boxing

foreach (int item in newList)
{
    Console.WriteLine(item);  // ✅ Direct access, no unboxing
}
```

---

## 🚀 Performance Impact

### The Cost of Boxing/Unboxing

Boxing and unboxing have **significant performance costs**:

1. **Memory allocation** on heap
2. **Value copying** (small cost)
3. **Garbage collection** pressure
4. **CPU cache** misses (heap access is slower)

### Performance Comparison

```csharp
const int iterations = 1_000_000;

// Test 1: Without boxing (Fast)
var watch = Stopwatch.StartNew();
for (int i = 0; i < iterations; i++)
{
    int value = i;  // No boxing
}
watch.Stop();
Console.WriteLine($"No boxing: {watch.ElapsedMilliseconds}ms");  // ~1-2ms

// Test 2: With boxing (Slower)
watch.Restart();
for (int i = 0; i < iterations; i++)
{
    object boxed = i;  // Boxing!
}
watch.Stop();
Console.WriteLine($"With boxing: {watch.ElapsedMilliseconds}ms");  // ~50-100ms
// Boxing is 50-100x slower!
```

---

### Real-World Performance Issue

```csharp
// ❌ SLOW: Boxing in loop
ArrayList list = new ArrayList();
for (int i = 0; i < 100000; i++)
{
    list.Add(i);  // ✅ Boxes every int
}
// Total: 100,000 box operations + GC pressure

// ✅ FAST: No boxing
List<int> list = new List<int>();
for (int i = 0; i < 100000; i++)
{
    list.Add(i);  // ✅ No boxing
}
// Total: Allocation once, then direct storage
```

---

## 🛡️ Best Practices to Avoid Boxing

### 1️⃣ Use Generic Collections Instead of Non-Generic

```csharp
// ❌ Old way - boxing
ArrayList list = new ArrayList();
list.Add(5);

// ✅ New way - no boxing
List<int> list = new List<int>();
list.Add(5);
```

### 2️⃣ Use Generic Methods

```csharp
// ❌ Boxing happens
void ProcessValue(object value)
{
    int num = (int)value;
}

// ✅ No boxing
void ProcessValue<T>(T value) where T : struct
{
    // Generic method uses value type directly
}
```

### 3️⃣ Use Generic Interfaces

```csharp
// ❌ Forces boxing when working with value types
public interface IValue
{
    object GetValue();
}

// ✅ Better - generic
public interface IValue<T>
{
    T GetValue();
}
```

---

## 🔍 Boxing Detection with Reflection

How can you tell if boxing will occur?

```csharp
// If a value type is assigned to object, boxing occurs
int value = 5;
object boxed = value;  // Boxing!

// To detect:
bool isValueType = value.GetType().IsValueType;  // True
bool isBoxedObject = boxed is object && boxed.GetType().IsValueType;  // True

Console.WriteLine($"Value type: {isValueType}");      // True
Console.WriteLine($"Is boxed: {isBoxedObject}");      // True
```

---

## 📊 Boxing/Unboxing Decision Tree

```
Are you:
├─ Passing a value type to a method expecting object?
│  └─ Boxing will occur (⚠️ Consider generics)
├─ Retrieving from a non-generic collection?
│  └─ Unboxing will occur (⚠️ Use List<T> instead)
├─ Using IComparable, IEnumerable on value types?
│  └─ Boxing will occur (⚠️ Usually acceptable)
├─ Formatting or string interpolation?
│  └─ May box (⚠️ Modern C# optimizes this)
└─ Working with nullable types?
   └─ Can avoid boxing with IEquatable<T>
```

---

## 📋 Common Interview Questions

### Q1: "What's the difference between boxing and unboxing?"
**A:** Boxing converts a value type to a reference type (allocates heap memory). Unboxing converts it back to the value type (copies from heap to stack).

### Q2: "Why is boxing a performance concern?"
**A:** Boxing allocates heap memory, requires copying, and creates garbage collection pressure. In loops, boxing can reduce performance by 50-100x.

### Q3: "What error occurs if you unbox to the wrong type?"
**A:** `InvalidCastException` - the object remembers what type it was boxed as.

### Q4: "How do you avoid boxing?"
**A:** Use generics instead of `object`, use `List<T>` instead of `ArrayList`, and avoid non-generic collections.

---

## 📝 Summary Table

| Aspect | Boxing | Unboxing |
|--------|--------|----------|
| **Direction** | Value → Reference | Reference → Value |
| **Memory** | Allocates heap | Copies from heap |
| **Automatic** | Yes (implicit) | No (requires cast) |
| **Error Type** | None (implicit) | `InvalidCastException` if wrong type |
| **Performance** | Slow (~50-100x slower) | Slow but faster than boxing |
| **When to Avoid** | Use generics | Use proper types |

---

**Next:** Continue to [Class vs Struct](./Class-vs-Struct.md) to understand when to choose which type.
