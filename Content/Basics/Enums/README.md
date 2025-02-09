In C#, an **enum** (short for enumeration) is a distinct type that consists of a set of named constants. Under the hood, these constants are represented by numeric values (by default of type `int`, though you can specify other integral types). Enums are extremely useful when you want to represent a fixed set of related values in a type‑safe manner.

However, enums themselves only hold numeric data. If you need to associate extra information (like a string, float, or bool) with each enum value, you can’t directly store multiple types in the enum’s value. Instead, you can attach metadata to each enum member by using **custom attributes**. This way, you can "annotate" each member with extra information (such as a type name, description, or any other data) and then use reflection to read that information at runtime.

Below is a comprehensive tutorial covering:

1. **Basic enum usage.**
2. **Defining and using custom attributes on enum members.**
3. **An example where an enum is decorated with a custom attribute that indicates different types (e.g., `"string"`, `"int"`, `"float"`, `"bool"`).**
4. **How to retrieve and use the extra data via reflection.**

---

## **1. Basic Enum Usage**

A typical enum in C# might look like this:

```csharp
public enum Days
{
    Monday,    // implicitly 0
    Tuesday,   // implicitly 1
    Wednesday, // implicitly 2
    Thursday,  // implicitly 3
    Friday,    // implicitly 4
    Saturday,  // implicitly 5
    Sunday     // implicitly 6
}
```

You can also specify a different underlying type:

```csharp
public enum SmallNumbers : byte
{
    Zero,
    One,
    Two
}
```

Enums make your code more readable and reduce errors because you can use named values instead of "magic numbers."

---

## **2. Adding Metadata to Enums with Custom Attributes**

Since enums only store numeric values, you can’t directly assign each member a type such as string, int, etc. Instead, you can define a custom attribute that holds additional metadata. Let’s define an attribute that lets you specify a data type as a string.

### **Step 2.1: Define a Custom Attribute**

Create a custom attribute (for example, `DataTypeAttribute`) to annotate each enum member:

```csharp
using System;

[AttributeUsage(AttributeTargets.Field, Inherited = false, AllowMultiple = false)] // optional
public sealed class DataTypeAttribute : Attribute
{
    // A property to hold the type name (like "string", "int", "float", or "bool").
    public string DataType { get; }

    // Constructor that sets the data type.
    public DataTypeAttribute(string dataType)
    {
        DataType = dataType;
    }
}
```

**Explanation:**
- The `AttributeUsage` attribute restricts this custom attribute to be used on enum fields (or other members if desired). It's optional!
- The attribute stores a single piece of information: the data type as a string.

### **Step 2.2: Define an Enum Using the Custom Attribute**

Now define your enum and annotate each member with the extra type information:

```csharp
public enum Save
{
    [DataType("string")]
    Username,
    
    [DataType("int")]
    CurrentTutorial,
    
    [DataType("float")]
    XP,
    
    [DataType("bool")]
    HasVIP
}
```

In this example, each member of the `Save` enum is decorated with a `DataTypeAttribute` that indicates the associated data type.

---

## **3. Retrieving Attribute Data via Reflection**

Once you have attached metadata to your enum members, you can use reflection to read this data at runtime. This is useful, for example, when you need to know what type of data to expect or how to process a particular enum member.

---

## **4. When and Why to Use This Approach**

- **Type-Safe Options:** Enums provide a limited set of constant values, which is great for settings, modes, states, or options in your application.
- **Extra Metadata:** Sometimes, the enum name isn’t enough. For example, if you are saving data and need to know what type each setting should be, attaching a custom attribute (like the `DataTypeAttribute` shown above) gives you a way to define that metadata.
- **Reflection-Based Processing:** By using reflection, you can build generic systems that inspect your enums and decide how to process or convert data dynamically based on the metadata.

### **Use Case Example**

Imagine you have a save system where each save field in the `Save` enum is mapped to a data type. When loading data from a file or a database, you could use the enum’s metadata to decide how to cast or parse each value automatically.

---

## **Summary**

- **Enums** in C# are used to define a set of named constants and are, by default, backed by an integer type.
- **Custom Attributes** let you add extra metadata to each enum member. In our example, we defined a `DataTypeAttribute` that specifies a string representing a data type.
- **Reflection** can be used at runtime to read these custom attributes, enabling you to build flexible systems that behave differently depending on the metadata associated with enum values.
- This approach is useful for situations where your enum values represent keys or settings that require additional context—such as indicating whether a save field should be treated as a string, int, float, or bool.

By combining enums with custom attributes, you create a powerful, self-documenting way to manage constants and their associated metadata in your Unity projects.

Happy coding!