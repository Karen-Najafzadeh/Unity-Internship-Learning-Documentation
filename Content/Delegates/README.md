# **C# Delegates in Unity – Complete Guide**  
Delegates are an essential feature of C# that allows methods to be passed as parameters. They are heavily used in **Unity** for handling **events, callbacks, and custom event-driven programming**.

---

## **What is a Delegate?**  
A **delegate** is a type that holds references to methods with a specific signature. It allows methods to be **passed as parameters**, executed dynamically, and subscribed to events.

### **Basic Syntax**
```csharp
// Declare a delegate type
public delegate void MyDelegate(string message);

// Create a method that matches the delegate signature
public void PrintMessage(string msg) {
    Debug.Log(msg);
}

// Assign and invoke the delegate
MyDelegate myDel = PrintMessage;
myDel("Hello, Unity!");
```

---

## **Types of Delegates in Unity**
| **Delegate Type** | **Description** | **Example** |
|-------------------|---------------|------------|
| **Single-cast Delegate** | Holds a reference to a single method | `MyDelegate myDel = MethodA;` |
| **Multi-cast Delegate** | Holds multiple methods (using `+=` and `-=`) | `myDel += MethodB;` |
| **Anonymous Delegate** | Uses inline delegate syntax without a separate method | `myDel = delegate(string msg) { Debug.Log(msg); };` |
| **Lambda Expressions** | Shorter syntax for anonymous methods | `myDel = (msg) => Debug.Log(msg);` |
| **Generic Delegates** | Works with different types using `Func<>` and `Action<>` | `Action<int> myAction = (x) => Debug.Log(x);` |
| **Event Delegates** | Used for event handling (e.g., Unity Events) | `public event MyDelegate OnEventTriggered;` |

---

## **1️⃣ Single-Cast Delegates**
A single-cast delegate holds **only one method**.

```csharp
// Step 1: Declare a delegate
public delegate void SingleDelegate(string message);

public class SingleCastExample : MonoBehaviour {
    void Start() {
        // Step 2: Assign a method to the delegate
        SingleDelegate myDel = PrintMessage;
        
        // Step 3: Invoke the delegate
        myDel("Single-cast delegate called!");
    }

    void PrintMessage(string msg) {
        Debug.Log(msg);
    }
}
```
✅ **Best for calling one function dynamically**.

---

## **2️⃣ Multi-Cast Delegates**
A **multi-cast delegate** can reference multiple methods.  
Use `+=` to add methods and `-=` to remove them.

```csharp
public delegate void MultiDelegate(string message);

public class MultiCastExample : MonoBehaviour {
    void Start() {
        MultiDelegate myDel = MethodA;
        myDel += MethodB;  // Adds another method
        myDel("Multi-cast delegate called!");

        myDel -= MethodA;  // Removes MethodA
        myDel("Only MethodB should run now.");
    }

    void MethodA(string msg) { Debug.Log("Method A: " + msg); }
    void MethodB(string msg) { Debug.Log("Method B: " + msg); }
}
```
✅ **Best for event-driven programming** (e.g., triggering multiple callbacks).

---

## **3️⃣ Anonymous Delegates**
Instead of defining a separate method, you can use an **anonymous method**.

```csharp
public delegate void MyDelegate(string message);

public class AnonymousDelegateExample : MonoBehaviour {
    void Start() {
        // Define delegate inline
        MyDelegate myDel = delegate (string msg) {
            Debug.Log("Anonymous Delegate: " + msg);
        };

        myDel("Called with anonymous method!");
    }
}
```
✅ **Useful for short, one-time-use methods**.

---

## **4️⃣ Lambda Expressions**
A **lambda expression** is a shorter way to write anonymous methods.

```csharp
public delegate void MyDelegate(string message);

public class LambdaExample : MonoBehaviour {
    void Start() {
        // Using lambda expression
        MyDelegate myDel = (msg) => Debug.Log("Lambda: " + msg);
        myDel("Called with lambda!");
    }
}
```
✅ **Best for concise expressions and inline logic**.

---

## **5️⃣ Generic Delegates (`Action<>` and `Func<>`)**
### **`Action<>` (Void Return)**
`Action<>` is a built-in delegate that takes **parameters** but returns **void**.

```csharp
public class ActionExample : MonoBehaviour {
    void Start() {
        Action<string> myAction = (msg) => Debug.Log("Action: " + msg);
        myAction("Hello from Action!");
    }
}
```
✅ **Use when you need a delegate that doesn't return a value.**

---

### **`Func<>` (Returns a Value)**
`Func<>` is a built-in delegate that **returns a value**.

```csharp
public class FuncExample : MonoBehaviour {
    void Start() {
        Func<int, int, int> add = (a, b) => a + b;
        // the first two parameters are used as inputs and the last parameter is the return value
        int result = add(3, 4);
        Debug.Log("Sum: " + result);
    }
}
```
✅ **Use when you need a delegate that returns a value.**

---

## **6️⃣ Event Delegates (`event` Keyword)**
Events are **special delegates** that **prevent direct assignment** and **enforce proper subscription**.

```csharp
public class EventExample : MonoBehaviour {
    public delegate void EventDelegate(string message);
    public event EventDelegate OnEventTriggered;  // Declare an event

    void Start() {
        OnEventTriggered += PrintMessage;  // Subscribe
        OnEventTriggered?.Invoke("Event triggered!");  // Fire event
    }

    void PrintMessage(string msg) {
        Debug.Log(msg);
    }
}
```
✅ **Use events when multiple scripts should respond to a trigger** (e.g., UI updates, player actions). **[See more about Events](../Events/)**

---

## **7️⃣ Unity-Specific Use Cases**
| **Use Case** | **Example** |
|-------------|------------|
| **Button Click Handling** | `button.onClick.AddListener(() => Debug.Log("Button Clicked!"));` |
| **Delegates with Coroutines** | `StartCoroutine(DelayedAction(() => Debug.Log("Executed!"), 2f));` |
| **Observer Pattern** | `OnHealthChanged += UpdateHealthUI;` |

---

## **8️⃣ Advanced Usage: Delegates with Coroutines**
You can pass a delegate to a **Coroutine** for dynamic execution.

```csharp
public class CoroutineDelegateExample : MonoBehaviour {
    void Start() {
        StartCoroutine(DelayedAction(() => Debug.Log("Action after 2 seconds!"), 2f));
    }

    IEnumerator DelayedAction(Action callback, float delay) {
        yield return new WaitForSeconds(delay);
        callback?.Invoke();
    }
}
```
✅ **Great for time-based events and flexible coroutines**.

---

# **🛠️ Summary Table: Delegates in Unity**
| **Type** | **Syntax** | **Use Case** |
|---------|----------|----------|
| **Basic Delegate** | `delegate void MyDelegate();` | Assign functions dynamically |
| **Multi-Cast Delegate** | `myDel += MethodA;` | Call multiple methods at once |
| **Anonymous Delegate** | `myDel = delegate { ... };` | Inline methods |
| **Lambda Expression** | `myDel = (x) => {...};` | Shorter syntax for anonymous methods |
| **Action<> Delegate** | `Action<int> myAction = (x) => {...};` | No return value |
| **Func<> Delegate** | `Func<int, string> myFunc = (x) => {...};` | Returns a value |
| **Event Delegate** | `public event MyDelegate OnEvent;` | Safe event handling |

---

# **Final Thoughts**
✅ **Delegates** are powerful tools in Unity for creating **dynamic, flexible, and modular code**.  
✅ **Events** provide **safe, encapsulated** event handling.  
✅ **Lambda expressions and `Action<>`/`Func<>`** make coding **concise and readable**.

Let me know if you want deeper explanations or practical examples! 🚀