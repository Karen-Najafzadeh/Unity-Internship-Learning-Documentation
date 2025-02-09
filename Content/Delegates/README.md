
# **C# Delegates in Unity – Complete Guide**

Delegates are an essential feature of C# that allow methods to be passed as parameters, stored as variables, and dynamically invoked. They play a critical role in **Unity** by enabling **event handling**, **callbacks**, and **custom event-driven programming**.

> **Why Use Delegates?**  
> Delegates enable you to write more modular and flexible code. For example, they allow different systems (such as UI, gameplay logic, or networking) to subscribe to events and react when something happens without tightly coupling those systems together.

---

## **What is a Delegate?**

A **delegate** is a type that safely encapsulates a method, or even multiple methods, that share the same signature (i.e., return type and parameters). Think of it as a type-safe function pointer.

### **Basic Syntax**

```csharp
// 1. Declare a delegate type that expects a method with a string parameter and a void return type.
public delegate void MyDelegate(string message);

// 2. Create a method that matches the delegate's signature.
public void PrintMessage(string msg) {
    Debug.Log(msg);
}

// 3. Assign the method to the delegate variable and invoke it.
MyDelegate myDel = PrintMessage;
myDel("Hello, Unity!"); // This prints "Hello, Unity!" in the Unity console.
```

*Explanation:*  
- **Step 1:** We declare a delegate named `MyDelegate` that can hold any method that takes a single `string` parameter and returns `void`.  
- **Step 2:** `PrintMessage` is defined to match the delegate's signature.  
- **Step 3:** We assign `PrintMessage` to a `MyDelegate` variable and then call it with a string.

---

## **Types of Delegates in Unity**

| **Delegate Type**          | **Description**                                                                 | **Example**                                                      |
|----------------------------|---------------------------------------------------------------------------------|------------------------------------------------------------------|
| **Single-cast Delegate**   | Holds a reference to a single method.                                           | `MyDelegate myDel = MethodA;`                                     |
| **Multi-cast Delegate**    | Holds multiple methods using `+=` and `-=`.                                      | `myDel += MethodB;`                                               |
| **Anonymous Delegate**     | Uses inline delegate syntax without a separate named method.                    | `myDel = delegate(string msg) { Debug.Log(msg); };`               |
| **Lambda Expressions**     | Provides a more concise syntax for anonymous methods.                         | `myDel = (msg) => Debug.Log(msg);`                                 |
| **Generic Delegates**      | Uses built-in generic delegates like `Func<>` and `Action<>` for various types.   | `Action<int> myAction = (x) => Debug.Log(x);`                      |
| **Event Delegates**        | Special delegates declared with the `event` keyword to support safe event handling. | `public event MyDelegate OnEventTriggered;`                    |

---

## **1️⃣ Single-Cast Delegates**

A single-cast delegate holds **only one method** at a time. They are simple and are ideal when you know exactly which method should be called.

```csharp
public delegate void SingleDelegate(string message);

public class SingleCastExample : MonoBehaviour {
    void Start() {
        // Assign a method to the delegate.
        SingleDelegate myDel = PrintMessage;
        // Invoke the delegate to call the method.
        myDel("Single-cast delegate called!");
    }

    void PrintMessage(string msg) {
        // Print the message to the Unity console.
        Debug.Log(msg);
    }
}
```

*Detailed Explanation:*  
- **Declaration:** `SingleDelegate` is declared to encapsulate methods that take a `string` parameter.
- **Usage:** In `Start()`, we assign the `PrintMessage` method to `myDel` and then invoke it with a custom message.

✅ **When to Use:** Use single-cast delegates when only one method needs to be invoked dynamically.

---

## **2️⃣ Multi-Cast Delegates**

A multi-cast delegate can hold references to **multiple methods**. This is useful for event systems where multiple subscribers should be notified.

```csharp
public delegate void MultiDelegate(string message);

public class MultiCastExample : MonoBehaviour {
    void Start() {
        // Assign the first method.
        MultiDelegate myDel = MethodA;
        // Add a second method to the invocation list.
        myDel += MethodB;
        
        // Both MethodA and MethodB are called.
        myDel("Multi-cast delegate called!");

        // Remove MethodA so only MethodB is called.
        myDel -= MethodA;
        myDel("Only MethodB should run now.");
    }

    void MethodA(string msg) { 
        Debug.Log("Method A: " + msg); 
    }
    
    void MethodB(string msg) { 
        Debug.Log("Method B: " + msg); 
    }
}
```

*Detailed Explanation:*  
- **Combining Methods:** The delegate `myDel` initially points to `MethodA` and then `MethodB` is added with the `+=` operator.
- **Invocation:** When invoked, both methods are executed in the order they were added.
- **Modification:** You can remove methods from the delegate with the `-=` operator.

✅ **When to Use:** Multi-cast delegates are ideal for event-driven programming where multiple callbacks need to be executed.

---

Below is a more complex example of multi-cast delegates split into two separate scripts. In this example, a **Game Manager** script defines a multi-cast delegate to notify subscribers about score changes, and a **Subscriber** script listens for those changes and responds with multiple actions.

---

### **Script 1: MultiCastManager.cs**

This script defines a multi-cast delegate called `ScoreEventDelegate` that accepts a player's name and a score change. It then simulates two score change events (an increase and a decrease) at different times.

```csharp
using UnityEngine;
using System;

public class MultiCastManager : MonoBehaviour
{
    // Define a delegate type that takes a player's name and a score change.
    public delegate void ScoreEventDelegate(string playerName, int scoreChange);
    
    // Create a multi-cast delegate instance. Other scripts can add their methods to this delegate.
    public ScoreEventDelegate OnScoreChanged;

    void Start()
    {
        // Simulate a score increase after 2 seconds.
        Invoke("SimulateScoreIncrease", 2f);
        // Simulate a score decrease after 4 seconds.
        Invoke("SimulateScoreDecrease", 4f);
    }

    void SimulateScoreIncrease()
    {
        Debug.Log("MultiCastManager: Simulating score increase...");
        // Check if there are any subscribers before invoking the delegate.
        OnScoreChanged?.Invoke("Player1", 10);
    }

    void SimulateScoreDecrease()
    {
        Debug.Log("MultiCastManager: Simulating score decrease...");
        // Invoke the delegate for a score decrease event.
        OnScoreChanged?.Invoke("Player1", -5);
    }
}
```

**Explanation:**

- **Delegate Declaration:**  
  The delegate `ScoreEventDelegate` is declared to handle methods that receive a `string` (player name) and an `int` (score change).

- **Multi-Cast Delegate Instance:**  
  The `OnScoreChanged` variable can hold references to multiple methods. Any script can subscribe (using `+=`) or unsubscribe (using `-=`) to this delegate.

- **Simulated Events:**  
  Two simulated events are invoked with `Invoke()`. Each calls the delegate with different score changes.

---

### **Script 2: MultiCastSubscriber.cs**

This script subscribes two separate methods to the multi-cast delegate defined in the **MultiCastManager**. One method logs the score change to the console, and the other simulates a UI update.

```csharp
using UnityEngine;

public class MultiCastSubscriber : MonoBehaviour
{
    // Reference to the manager that holds the multi-cast delegate.
    public MultiCastManager manager;

    void OnEnable()
    {
        // Ensure the manager is assigned.
        if (manager != null)
        {
            // Subscribe two methods to the OnScoreChanged delegate.
            manager.OnScoreChanged += LogScoreChange;
            manager.OnScoreChanged += UpdateUI;
        }
        else
        {
            Debug.LogWarning("MultiCastSubscriber: Manager reference not set!");
        }
    }

    void OnDisable()
    {
        // Unsubscribe to avoid memory leaks or null reference exceptions.
        if (manager != null)
        {
            manager.OnScoreChanged -= LogScoreChange;
            manager.OnScoreChanged -= UpdateUI;
        }
    }

    // First subscriber method: Logs the score change details.
    void LogScoreChange(string playerName, int scoreChange)
    {
        Debug.Log($"LogScoreChange: {playerName}'s score changed by {scoreChange} points.");
    }

    // Second subscriber method: Simulates a UI update based on the score change.
    void UpdateUI(string playerName, int scoreChange)
    {
        Debug.Log($"UpdateUI: Updating UI for {playerName} with a score change of {scoreChange}.");
        // Here you could add code to update UI elements like text or progress bars.
    }
}
```

**Explanation:**

- **Manager Reference:**  
  The subscriber script holds a public reference to the `MultiCastManager`. Drag the GameObject with the **MultiCastManager** component into this field in the Inspector.

- **Subscription and Unsubscription:**  
  In `OnEnable()`, the script subscribes two methods (`LogScoreChange` and `UpdateUI`) to the `OnScoreChanged` delegate. In `OnDisable()`, it unsubscribes them to prevent memory leaks.

- **Subscriber Methods:**  
  - `LogScoreChange` writes a log message to the console with details about the score change.
  - `UpdateUI` simulates updating the game's UI. In a real-world scenario, this method could update text fields or other UI elements.

---

### **Usage:**

1. **Setup the Scene:**
   - Create an empty GameObject and attach the **MultiCastManager** script.
   - Create another GameObject and attach the **MultiCastSubscriber** script.
   - In the Inspector for the **MultiCastSubscriber**, assign the **MultiCastManager** GameObject to the `manager` field.

2. **Run the Game:**
   - When you play the scene, the **MultiCastManager** will simulate two events: a score increase and a score decrease.
   - The **MultiCastSubscriber** will receive these events and execute both `LogScoreChange` and `UpdateUI` methods, demonstrating the multi-cast delegate in action.

This example showcases how multi-cast delegates allow multiple subscriber methods to respond to a single event, keeping your code modular and flexible. Enjoy integrating multi-cast delegates into your Unity projects!

---

## **3️⃣ Anonymous Delegates**

Anonymous delegates allow you to define the delegate inline without creating a separate named method. They are great for quick, one-off operations.

```csharp
public delegate void MyDelegate(string message);

public class AnonymousDelegateExample : MonoBehaviour {
    void Start() {
        // Define the delegate inline using the anonymous delegate syntax.
        MyDelegate myDel = delegate (string msg) {
            Debug.Log("Anonymous Delegate: " + msg);
        };

        // Invoke the anonymous delegate.
        myDel("Called with anonymous method!");
    }
}
```

*Detailed Explanation:*  
- **Inline Definition:** The delegate's body is defined right where it is used.
- **Use Case:** This is especially useful for small callbacks or event handlers that don't need to be reused elsewhere.

✅ **When to Use:** Best for short, one-time-use methods where defining a separate method would be overkill.

---

## **4️⃣ Lambda Expressions**

Lambda expressions are a more concise way to write anonymous methods. They improve code readability by reducing boilerplate. They provide a concise way to represent anonymous methods using a clear, inline syntax. They’re particularly popular in Unity for tasks like event handling, LINQ queries, and callback methods. Below are several examples and explanations to help you understand and leverage lambda expressions effectively in your projects.


```csharp
public delegate void MyDelegate(string message);

public class LambdaExample : MonoBehaviour {
    void Start() {
        // Using a lambda expression to define the delegate inline.
        MyDelegate myDel = (msg) => Debug.Log("Lambda: " + msg);
        myDel("Called with lambda!");
    }
}
```

*Detailed Explanation:*  
- **Syntax:** Lambdas use the syntax `(parameters) => expression` or a block of code.
- **Readability:** They reduce code clutter, making the intent clear.

✅ **When to Use:** Best for concise expressions and inline logic, especially in event handlers or quick callbacks.


---

## **1. Basic Lambda Expression**

A basic lambda expression is often used when you need a simple, single-line function.

```csharp
using UnityEngine;
using System;

public class LambdaBasicExample : MonoBehaviour {
    void Start() {
        // Define a lambda expression that takes a string and logs it.
        Action<string> printMessage = (msg) => Debug.Log(msg);
        
        // Call the lambda expression
        printMessage("Hello from a basic lambda expression!");
    }
}
```

**Explanation:**

- **Syntax:**  
  `(msg) => Debug.Log(msg)`  
  - `msg` is the parameter.
  - `=>` separates the parameter list from the expression.
  - `Debug.Log(msg)` is the expression executed when the lambda is invoked.
  
- **Delegate Type:**  
  The `Action<string>` delegate represents a method that takes a `string` and returns `void`.

---

## **2. Lambda Expression with Multiple Parameters**

When your lambda needs to work with multiple inputs, simply include them in the parameter list.

```csharp
using UnityEngine;
using System;

public class LambdaMultiParameterExample : MonoBehaviour {
    void Start() {
        // Define a lambda expression that takes two integers and returns their sum.
        Func<int, int, int> add = (a, b) => a + b;
        
        // Call the lambda expression
        int result = add(5, 3);
        Debug.Log("The sum is: " + result);
    }
}
```

**Explanation:**

- **Delegate Type:**  
  `Func<int, int, int>` represents a method taking two integers and returning an integer.  
- **Lambda Body:**  
  `(a, b) => a + b` adds the two parameters and returns the result.

---

## **3. Multi-Line Lambda Expression**

For more complex operations, you can include multiple statements inside a lambda using curly braces `{ }`.

```csharp
using UnityEngine;
using System;

public class LambdaMultiLineExample : MonoBehaviour {
    void Start() {
        // Define a lambda expression with multiple statements.
        Action<string> processMessage = (msg) => {
            Debug.Log("Starting processing...");
            Debug.Log("Processing message: " + msg);
            Debug.Log("Finished processing.");
        };

        processMessage("Hello, multi-line lambda!");
    }
}
```

**Explanation:**

- **Curly Braces:**  
  When using `{ }`, you need an explicit `return` statement if the lambda returns a value (in the case of a `Func<>`). For `Action<>`, no return is needed.
- **Usage:**  
  Multi-line lambdas are useful when the operation cannot be written as a single expression.

---

## **4. Capturing Outer Variables (Closures)**

Lambda expressions can "capture" variables from their enclosing scope, which is useful for maintaining state or configuration without explicitly passing parameters.

```csharp
using UnityEngine;
using System;

public class LambdaClosureExample : MonoBehaviour {
    void Start() {
        int multiplier = 2;  // This variable is captured by the lambda

        // Define a lambda that uses the captured variable 'multiplier'
        Action<int> multiplyAndLog = (num) => {
            int result = num * multiplier;
            Debug.Log($"{num} multiplied by {multiplier} equals {result}");
        };

        multiplyAndLog(5);
    }
}
```

**Explanation:**

- **Closures:**  
  The lambda captures the variable `multiplier` from its surrounding scope, so any changes to `multiplier` before the lambda is invoked would be reflected.
- **Usage:**  
  This pattern is common for configuration values, counters, or other stateful data in event handlers.

---

## **5. Using Lambdas for Event Handling**

Lambda expressions are widely used to attach simple event handlers without creating separate methods.

```csharp
using UnityEngine;
using UnityEngine.UI;

public class LambdaEventExample : MonoBehaviour {
    public Button myButton;

    void Start() {
        // Adding an inline lambda expression as an event listener for the button click.
        myButton.onClick.AddListener(() => {
            Debug.Log("Button clicked via lambda!");
        });
    }
}
```

**Explanation:**

- **Inline Definition:**  
  Instead of writing a separate method, the lambda expression is defined directly within the `AddListener` call.
- **Benefits:**  
  This keeps the code compact and localized to where it’s used, improving readability.

---

## **6. Lambdas with LINQ Queries**

Lambda expressions are integral to LINQ (Language Integrated Query) in C#, enabling powerful and concise data manipulation.

```csharp
using UnityEngine;
using System.Linq;
using System.Collections.Generic;

public class LambdaLINQExample : MonoBehaviour {
    void Start() {
        // Sample list of integers.
        List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

        // Use a lambda expression to filter the list for even numbers.
        List<int> evenNumbers = numbers.Where(num => num % 2 == 0).ToList();

        Debug.Log("Even Numbers: " + string.Join(", ", evenNumbers));
    }
}
```

**Explanation:**

- **LINQ Method:**  
  The `Where` method takes a lambda expression that returns `true` for elements to include.
- **Filtering:**  
  In this example, `num => num % 2 == 0` filters the list to only include even numbers.

---

## **7. Combining Lambdas with Delegates and Events**

Lambda expressions can also be combined with events to create dynamic and flexible event systems.

```csharp
using UnityEngine;
using System;

public class LambdaEventDelegateExample : MonoBehaviour {
    // Define an event using a built-in delegate type.
    public event Action<string> OnMessageReceived;

    void Start() {
        // Subscribe to the event with a lambda expression.
        OnMessageReceived += (msg) => {
            Debug.Log("Lambda Event Handler: " + msg);
        };

        // Fire the event.
        OnMessageReceived?.Invoke("Event triggered with lambda!");
    }
}
```

**Explanation:**

- **Event Subscription:**  
  The lambda `(msg) => { ... }` is subscribed to the `OnMessageReceived` event.
- **Null-Conditional Operator:**  
  The `?.Invoke()` syntax ensures the event is only called if there are subscribers, preventing a `NullReferenceException`.

---

## **Summary**

Lambda expressions in C# are a powerful tool for writing inline, concise functions. They are used extensively in Unity for:

- **Simplifying event handlers**
- **Streamlining LINQ queries**
- **Creating closures that capture external state**
- **Replacing verbose anonymous methods with compact syntax**

By incorporating lambda expressions into your Unity scripts, you can reduce boilerplate code and make your logic more readable and maintainable. Experiment with these examples in your projects to see how they can enhance your code!

---

## **5️⃣ Generic Delegates (`Action<>` and `Func<>`)**


Below is an in-depth guide on using C#’s built-in generic delegates: **`Action<>`** and **`Func<>`**. These delegates help you write more concise, expressive, and flexible code. You’ll find several examples along with explanations that illustrate various use cases in Unity.

---

# **Understanding Action<> and Func<>**

### **Action<>**
- **Purpose:** Represents a delegate (or method pointer) that returns **void**.
- **Usage:** When you want to execute a block of code (often a callback or event handler) that does not return a value.
- **Generic Signature:**  
  `Action<T1, T2, ..., TN>` where each `T` is a parameter type.  
  For example, `Action<string>` represents a delegate that takes a single string and returns nothing.

### **Func<>**
- **Purpose:** Represents a delegate that **returns a value**.
- **Usage:** When you need to perform computations or transformations that produce a result.
- **Generic Signature:**  
  `Func<T1, T2, ..., TN, TResult>`  
  The last type parameter is the return type, and any parameters before it are the input types.  
  For example, `Func<int, int, int>` represents a delegate that takes two integers and returns an integer.

---

# **1. Using Action<>: Basic Examples**

## **Example 1: A Simple Logging Action**

In this example, we define an `Action<string>` to log a message.

```csharp
using UnityEngine;
using System;

public class ActionBasicExample : MonoBehaviour {
    void Start() {
        // Define an Action that takes a string parameter.
        Action<string> logMessage = (message) => Debug.Log("Action Log: " + message);
        
        // Invoke the action.
        logMessage("Hello from Action!");
    }
}
```

**Explanation:**
- **Lambda Expression:** `(message) => Debug.Log("Action Log: " + message)` creates an inline anonymous function.
- **Delegate Invocation:** When `logMessage` is called, it executes the code inside the lambda.

---

## **Example 2: Action with Multiple Parameters**

You can also use `Action<>` with more than one parameter. For example, let’s create an action that logs a player's score.

```csharp
using UnityEngine;
using System;

public class ActionMultipleParametersExample : MonoBehaviour {
    void Start() {
        // Define an Action that takes a string and an int.
        Action<string, int> displayScore = (playerName, score) => {
            Debug.Log($"Player {playerName} scored {score} points!");
        };
        
        // Invoke the action.
        displayScore("Player1", 50);
    }
}
```

**Explanation:**
- **Multiple Parameters:** The lambda `(playerName, score) => { ... }` accepts two parameters.
- **String Interpolation:** The message is formatted to display both parameters.

---


# **2. Using Func<>: Basic Examples**

## **Example 1: Simple Arithmetic Function**

Here we create a `Func<int, int, int>` to perform addition.

```csharp
using UnityEngine;
using System;

public class FuncBasicExample : MonoBehaviour {
    void Start() {
        // Define a Func that takes two ints and returns their sum.
        Func<int, int, int> add = (a, b) => a + b;
        
        // Invoke the function.
        int result = add(3, 4);
        Debug.Log("Result of addition: " + result);
    }
}
```

**Explanation:**
- **Lambda Expression:** `(a, b) => a + b` succinctly adds the two parameters.
- **Return Value:** The result of the addition is captured in the `result` variable.

---

## **Example 2: Func with Complex Logic**

If your operation requires multiple steps, you can use a multi-line lambda with `Func<>`. For instance, calculating a discounted price.

```csharp
using UnityEngine;
using System;

public class FuncMultiLineExample : MonoBehaviour {
    void Start() {
        // Define a Func that calculates a discounted price.
        Func<float, float, float> calculateDiscount = (originalPrice, discountPercentage) => {
            float discountAmount = originalPrice * discountPercentage / 100f;
            float finalPrice = originalPrice - discountAmount;
            return finalPrice;
        };
        
        // Invoke the function.
        float finalPrice = calculateDiscount(200f, 15f);
        Debug.Log("Final Price after discount: " + finalPrice);
    }
}
```

**Explanation:**
- **Multi-Line Lambda:** Curly braces `{ }` are used to execute several statements.
- **Return Statement:** The lambda explicitly returns `finalPrice`.

---

## **Example 3: Func in Data Processing**

`Func<>` is especially powerful in data processing or filtering scenarios. Here’s an example using LINQ to filter and transform a list of numbers.

```csharp
using UnityEngine;
using System;
using System.Linq;
using System.Collections.Generic;

public class FuncLINQExample : MonoBehaviour {
    void Start() {
        // Create a list of integers.
        List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
        
        // Define a Func that squares a number.
        Func<int, int> square = x => x * x;
        
        // Use LINQ with the Func to transform the list.
        List<int> squaredNumbers = numbers.Select(square).ToList();
        
        Debug.Log("Squared Numbers: " + string.Join(", ", squaredNumbers));
    }
}
```

**Explanation:**
- **Transform Function:** The `square` function defined by `Func<int, int>` takes an integer and returns its square.
- **LINQ Integration:** The `Select` method applies the `square` function to each element in the list.

---

# **3. Combining Action<> and Func<> with Closures**

Both `Action<>` and `Func<>` support closures. They can capture variables from their surrounding scope, which is useful for maintaining state without extra parameters.

```csharp
using UnityEngine;
using System;

public class ClosureExample : MonoBehaviour {
    void Start() {
        int factor = 3;
        
        // Func that multiplies its input by an external variable 'factor'.
        Func<int, int> multiply = (num) => num * factor;
        
        int multipliedValue = multiply(10);
        Debug.Log("10 multiplied by factor (" + factor + ") equals: " + multipliedValue);
        
        // Action that logs a message including the captured variable.
        Action<string> logWithFactor = (message) => {
            Debug.Log(message + " with factor: " + factor);
        };
        
        logWithFactor("The factor is applied in this message");
    }
}
```

**Explanation:**
- **Closure:** Both lambda expressions capture the variable `factor` from the enclosing scope.
- **Usage:** This pattern is common in event handlers or callbacks where external state is needed.

---

# **Summary**

- **Action<>**  
  - **Use When:** You need to execute a block of code without returning a value.
  - **Examples:** Logging messages, handling button clicks, processing events.
  
- **Func<>**  
  - **Use When:** You need to compute a value or transform input data.
  - **Examples:** Arithmetic operations, data transformations, filtering with LINQ.

By incorporating **`Action<>`** and **`Func<>`** into your Unity projects, you can write cleaner, more maintainable code with less boilerplate. Experiment with these examples and adapt them to your specific project needs to take full advantage of C#’s powerful delegate features!

---

## **6️⃣ Event Delegates (`event` Keyword)**

Events in C# are special delegates that provide **encapsulation** and **safety**. They prevent external classes from overwriting delegate references, ensuring that only subscription (using `+=` and `-=`) is allowed.

```csharp
public class EventExample : MonoBehaviour {
    // Step 1: Declare a delegate type for events.
    public delegate void EventDelegate(string message);
    // Step 2: Declare an event based on the delegate.
    public event EventDelegate OnEventTriggered;

    void Start() {
        // Step 3: Subscribe a method to the event.
        OnEventTriggered += PrintMessage;

        // Step 4: Safely invoke the event (using the null-conditional operator).
        OnEventTriggered?.Invoke("Event triggered!");
    }

    void PrintMessage(string msg) {
        Debug.Log("Event received: " + msg);
    }
}
```

*Detailed Explanation:*  
- **Event Declaration:** The `event` keyword ensures that the delegate can only be invoked from within the declaring class.
- **Subscription:** Other classes can subscribe or unsubscribe using `+=` and `-=`.
- **Invocation Safety:** The `?.Invoke()` syntax ensures that the event is only called if there are subscribers.

✅ **When to Use:** Use events to implement publish/subscribe patterns where multiple systems need to respond to certain actions.

---

## **7️⃣ Unity-Specific Use Cases**

Delegates and events are widely used in Unity for various tasks. Here are some practical examples:

### **Button Click Handling**

For UI elements like buttons, Unity provides its own event system. However, you can also use delegates to add custom behavior.

```csharp
using UnityEngine.UI;

public class ButtonClickExample : MonoBehaviour {
    public Button myButton;

    void Start() {
        // Adding a listener with a lambda expression.
        myButton.onClick.AddListener(() => {
            Debug.Log("Button Clicked!");
        });
    }
}
```

### **Delegates with Coroutines**

You can pass a delegate to a coroutine for delayed or time-based execution.

```csharp
using System;
using System.Collections;
using UnityEngine;

public class CoroutineDelegateExample : MonoBehaviour {
    void Start() {
        // Start a coroutine that will execute the provided delegate after a delay.
        StartCoroutine(DelayedAction(() => Debug.Log("Action after 2 seconds!"), 2f));
    }

    IEnumerator DelayedAction(Action callback, float delay) {
        // Wait for the specified delay.
        yield return new WaitForSeconds(delay);
        // Invoke the callback if it's not null.
        callback?.Invoke();
    }
}
```

### **Observer Pattern**

Implementing the observer pattern is straightforward with delegates and events. For example, you might want different UI elements to update when the player's health changes.

```csharp
public class Player : MonoBehaviour {
    // Define an event to notify subscribers when health changes.
    public event Action<int> OnHealthChanged;
    private int health = 100;

    // Method to simulate taking damage.
    public void TakeDamage(int damage) {
        health -= damage;
        Debug.Log("Player Health: " + health);
        // Notify all subscribers about the health change.
        OnHealthChanged?.Invoke(health);
    }
}

public class HealthUI : MonoBehaviour {
    public Player player;

    void OnEnable() {
        // Subscribe to the player's health change event.
        player.OnHealthChanged += UpdateHealthUI;
    }

    void OnDisable() {
        // Unsubscribe to prevent memory leaks.
        player.OnHealthChanged -= UpdateHealthUI;
    }

    void UpdateHealthUI(int currentHealth) {
        // Update the UI accordingly.
        Debug.Log("Updating Health UI: " + currentHealth);
    }
}
```

*Detailed Explanation:*  
- **Player Class:** The `Player` class exposes an event `OnHealthChanged` that other classes (like UI controllers) can subscribe to.
- **HealthUI Class:** The UI element subscribes to this event and updates when the player's health changes.

✅ **When to Use:** Use these patterns for decoupling game logic from UI updates, animations, sound effects, etc.

---

## **8️⃣ Advanced Usage: Combining Delegates with Coroutines**

Delegates can be passed into coroutines for flexible timing and execution of actions.

```csharp
using System;
using System.Collections;
using UnityEngine;

public class AdvancedCoroutineExample : MonoBehaviour {
    void Start() {
        // Execute a delegate after a 3-second delay.
        StartCoroutine(ExecuteAfterDelay(() => {
            Debug.Log("Executed after delay using a delegate!");
        }, 3f));
    }

    IEnumerator ExecuteAfterDelay(Action action, float delay) {
        // Provide detailed logging to track the coroutine's progress.
        Debug.Log("Waiting for " + delay + " seconds...");
        yield return new WaitForSeconds(delay);
        Debug.Log("Time elapsed. Executing action.");
        action?.Invoke();
    }
}
```

*Detailed Explanation:*  
- **Flexibility:** This pattern allows you to define what should happen after a delay without hardcoding the action.
- **Debugging:** Additional logs help trace execution during development.

✅ **When to Use:** Ideal for scenarios where the action to be performed after a delay might change based on game state or user interactions.




---

### Callbacks

Below are examples that demonstrates the use of a callback using the built-in `Action<>` delegate and lambda Epressions. In this scenario, one script (the **DataFetcher**) simulates an asynchronous operation (like fetching data) and then calls a callback once the operation is complete. Another script (the **DataConsumer**) initiates the data fetch and handles the result via the callback.

---

# Action<> callback

### **Script 1: DataFetcher.cs**

This script simulates an asynchronous operation using a coroutine. The method `FetchData` takes a callback of type `Action<string>` that is invoked with the "fetched" data after a delay.

```csharp
using UnityEngine;
using System;
using System.Collections;

public class DataFetcher : MonoBehaviour
{
    /// <summary>
    /// Simulates an asynchronous data fetch operation.
    /// </summary>
    /// <param name="onComplete">Callback that is invoked when data is fetched.</param>
    public void FetchData(Action<string> onComplete)
    {
        StartCoroutine(FetchDataCoroutine(onComplete));
    }

    /// <summary>
    /// A coroutine that waits for 2 seconds and then calls the callback.
    /// </summary>
    /// <param name="onComplete">The callback action to invoke after fetching data.</param>
    /// <returns></returns>
    private IEnumerator FetchDataCoroutine(Action<string> onComplete)
    {
        // Simulate a delay (e.g., waiting for a server response)
        Debug.Log("DataFetcher: Starting data fetch...");
        yield return new WaitForSeconds(2f);

        // Simulate fetched data
        string fetchedData = "Fetched data from server!";
        Debug.Log("DataFetcher: Data fetch complete.");

        // Call the callback with the fetched data.
        onComplete?.Invoke(fetchedData);
    }
}
```

---

### **Script 2: DataConsumer.cs**

This script subscribes to the callback from the **DataFetcher**. It starts the data fetch process in its `Start()` method and handles the result in the callback method `OnDataFetched`.

```csharp
using UnityEngine;

public class DataConsumer : MonoBehaviour
{
    // Reference to the DataFetcher component.
    public DataFetcher fetcher;

    void Start()
    {
        if (fetcher != null)
        {
            // Initiate data fetching and provide a callback to handle the result.
            fetcher.FetchData(OnDataFetched);
        }
        else
        {
            Debug.LogError("DataConsumer: DataFetcher reference is not set!");
        }
    }

    /// <summary>
    /// Callback method that handles the fetched data.
    /// </summary>
    /// <param name="data">The data fetched by the DataFetcher.</param>
    private void OnDataFetched(string data)
    {
        Debug.Log("DataConsumer: Received data - " + data);
        // Process the data as needed.
    }
}
```

---

### **How to Use This Example in Unity**

1. **Setup the Scene:**
   - Create an empty GameObject in your Unity scene and attach the **DataFetcher** script to it.
   - Create another GameObject and attach the **DataConsumer** script.
   - In the **DataConsumer** component (in the Inspector), assign the GameObject containing **DataFetcher** to the `fetcher` field.

2. **Run the Scene:**
   - When you play the scene, the **DataConsumer** script calls `FetchData` on the **DataFetcher**.
   - The **DataFetcher** simulates a data fetch by waiting 2 seconds before invoking the callback with the "fetched" data.
   - The **DataConsumer**’s callback method (`OnDataFetched`) is then executed, displaying the fetched data in the console.

This example shows how you can use a callback to decouple the data-fetching logic from the data-processing logic, making your code more modular and easier to manage. Enjoy integrating callbacks into your Unity projects!


# Lambda callback

Below is an example that demonstrates how to use lambda expressions as callbacks. In this scenario, one script (the **DataFetcherLambda**) simulates an asynchronous operation (such as fetching data) and then calls a callback. The callback is defined inline as a lambda expression in another script (the **DataConsumerLambda**).

---

### **Script 1: DataFetcherLambda.cs**

This script simulates an asynchronous data-fetch operation using a coroutine. It defines a `FetchData` method that accepts a callback of type `Action<string>`. Once the simulated fetch is complete, it invokes the callback with the fetched data.

```csharp
using UnityEngine;
using System;
using System.Collections;

public class DataFetcherLambda : MonoBehaviour
{
    /// <summary>
    /// Simulates an asynchronous data fetch operation.
    /// </summary>
    /// <param name="onComplete">Callback that is invoked with the fetched data.</param>
    public void FetchData(Action<string> onComplete)
    {
        StartCoroutine(FetchDataCoroutine(onComplete));
    }

    /// <summary>
    /// A coroutine that simulates a delay (e.g., a network request) and then calls the callback.
    /// </summary>
    /// <param name="onComplete">The callback action to invoke after fetching data.</param>
    /// <returns></returns>
    private IEnumerator FetchDataCoroutine(Action<string> onComplete)
    {
        Debug.Log("DataFetcherLambda: Starting data fetch...");
        // Simulate a delay for the data fetch (e.g., waiting for a server response)
        yield return new WaitForSeconds(2f);

        // Simulated fetched data
        string fetchedData = "Data fetched using a lambda callback!";
        Debug.Log("DataFetcherLambda: Data fetch complete.");

        // Invoke the callback with the fetched data
        onComplete?.Invoke(fetchedData);
    }
}
```

---

### **Script 2: DataConsumerLambda.cs**

This script demonstrates how to initiate the data fetch and handle the result using an inline lambda expression as the callback. Instead of defining a separate callback method, we pass a lambda directly when calling `FetchData`.

```csharp
using UnityEngine;

public class DataConsumerLambda : MonoBehaviour
{
    // Reference to the DataFetcherLambda component.
    public DataFetcherLambda fetcher;

    void Start()
    {
        if (fetcher != null)
        {
            // Call FetchData and provide an inline lambda expression as the callback.
            fetcher.FetchData((data) =>
            {
                // This code is executed when the data fetch is complete.
                Debug.Log("DataConsumerLambda: Received data - " + data);
                
                // You can add additional processing for the fetched data here.
            });
        }
        else
        {
            Debug.LogError("DataConsumerLambda: DataFetcherLambda reference is not set!");
        }
    }
}
```

---

### **How to Use This Example in Unity**

1. **Scene Setup:**
   - Create an empty GameObject and attach the **DataFetcherLambda** script.
   - Create another GameObject and attach the **DataConsumerLambda** script.
   - In the Inspector for the **DataConsumerLambda** GameObject, assign the GameObject containing **DataFetcherLambda** to the `fetcher` field.

2. **Run the Scene:**
   - When you press Play, the **DataConsumerLambda** script calls `FetchData` on **DataFetcherLambda**.
   - The **DataFetcherLambda** script simulates a 2-second delay and then invokes the callback (defined as a lambda) with the fetched data.
   - The lambda expression in **DataConsumerLambda** logs the fetched data to the console.

This example demonstrates how lambda expressions can simplify callback implementations by allowing you to define the callback logic inline, keeping your code more concise and readable. Enjoy using lambda callbacks in your Unity projects!


---

# **🛠️ Summary Table: Delegates in Unity**

| **Type**                  | **Syntax Example**                                    | **Typical Use Case**                             |
|---------------------------|-------------------------------------------------------|--------------------------------------------------|
| **Basic Delegate**        | `public delegate void MyDelegate(string msg);`       | Dynamically assign and invoke methods.           |
| **Multi-Cast Delegate**   | `myDel += AnotherMethod;`                             | Notify multiple subscribers (e.g., event system).|
| **Anonymous Delegate**    | `myDel = delegate(string msg) { Debug.Log(msg); };`   | Quick, inline implementations without extra methods. |
| **Lambda Expression**     | `myDel = (msg) => Debug.Log(msg);`                    | Concise syntax for inline delegate logic.        |
| **Action<> Delegate**     | `Action<int> myAction = (x) => Debug.Log(x);`         | When no return value is needed.                  |
| **Func<> Delegate**       | `Func<int, int, int> add = (a, b) => a + b;`          | When processing input and returning a value.     |
| **Event Delegate**        | `public event MyDelegate OnEvent;`                   | Safe publish/subscribe pattern for events.       |

---

# **Final Thoughts**

- **Delegates** are powerful tools that help you write **dynamic, flexible, and modular code** in Unity.
- **Events** built on delegates provide a safe way to implement the publish/subscribe model, keeping your code decoupled.
- **Anonymous methods, lambda expressions, and generic delegates** (`Action<>` and `Func<>`) simplify your code, making it more concise and readable.

Feel free to expand or modify this guide further as you explore more complex delegate and event-driven programming scenarios in your Unity projects! 🚀
