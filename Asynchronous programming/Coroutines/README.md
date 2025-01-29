### **Coroutines in Unity**  
A **Coroutine** in Unity is a function that can pause execution and return control to Unity, then continue execution from the same point later. This is useful for handling time-dependent events like animations, waiting for user input, or delaying execution without blocking the main thread.  

#### **Basic Coroutine Example:**  
```csharp
IEnumerator MyCoroutine() {
    Debug.Log("Start");
    yield return new WaitForSeconds(2);
    Debug.Log("After 2 seconds");
}
```
To start a coroutine, use:  
```csharp
StartCoroutine(MyCoroutine());
```
To stop it:  
```csharp
StopCoroutine(MyCoroutine());
```
or  
```csharp
StopAllCoroutines();
```

#### **Coroutine with Parameters Example:**  
```csharp
IEnumerator MyCoroutineWithParams(float waitTime) {
    Debug.Log("Start");
    yield return new WaitForSeconds(waitTime);
    Debug.Log("After " + waitTime + " seconds");
}
```
To start this coroutine, use:  
```csharp
StartCoroutine(MyCoroutineWithParams(3f));
```

---

## **Recursive Coroutines**  
A **recursive coroutine** is a coroutine that calls itself within its execution, often used for tasks like polling or continuous behavior without blocking the main thread.

### **Example: Recursive Coroutine**
```csharp
IEnumerator RecursiveCoroutine(int count) {
    if (count <= 0) yield break;
    
    Debug.Log("Count: " + count);
    yield return new WaitForSeconds(1);

    StartCoroutine(RecursiveCoroutine(count - 1));
}
```
Here, the coroutine calls itself after each `WaitForSeconds`, creating a recursive behavior.

### **Alternative Approach (Yielding on Itself)**
```csharp
IEnumerator RecursiveCoroutine() {
    Debug.Log("Repeating...");
    yield return new WaitForSeconds(1);
    yield return StartCoroutine(RecursiveCoroutine());  // Calls itself recursively
}
```
This method waits for itself to complete before starting again.

---

## **Types of `yield return` in Unity**  
Unity provides different `yield return` options that allow coroutines to pause execution in various ways:

### 1. **`yield return null`** (Next Frame)  
Pauses the coroutine and resumes in the next frame.  
```csharp
IEnumerator WaitForNextFrame() {
    Debug.Log("Before waiting");
    yield return null; // Waits for the next frame
    Debug.Log("After waiting");
}
```

### 2. **`yield return new WaitForSeconds(float seconds)`** (Fixed Delay)  
Waits for a specified time before resuming.  
```csharp
IEnumerator WaitForSecondsExample() {
    Debug.Log("Waiting...");
    yield return new WaitForSeconds(2); // Waits for 2 seconds
    Debug.Log("Done!");
}
```

### 3. **`yield return new WaitForSecondsRealtime(float seconds)`** (Unscaled Time)  
Same as `WaitForSeconds` but ignores `Time.timeScale` (useful when the game is paused).  
```csharp
IEnumerator WaitForRealTime() {
    Debug.Log("Waiting...");
    yield return new WaitForSecondsRealtime(2); // Waits 2 real-time seconds
    Debug.Log("Done!");
}
```

### 4. **`yield return new WaitUntil(Func<bool>)`** (Wait Until Condition is True)  
Waits until a specified condition is met before continuing.  
```csharp
IEnumerator WaitUntilExample() {
    yield return new WaitUntil(() => Input.GetKeyDown(KeyCode.Space));  
    Debug.Log("Space key pressed!");
}
```

### 5. **`yield return new WaitWhile(Func<bool>)`** (Wait While Condition is True)  
Pauses execution while a condition is **true** and continues when it becomes **false**.  
```csharp
IEnumerator WaitWhileExample() {
    yield return new WaitWhile(() => Time.time < 10);  // Wait while time is less than 10 seconds
    Debug.Log("10 seconds passed!");
}
```

### 6. **`yield return StartCoroutine(OtherCoroutine())`** (Wait for Another Coroutine)  
Pauses the current coroutine until another coroutine completes.  
```csharp
IEnumerator TaskA() {
    Debug.Log("Task A started");
    yield return new WaitForSeconds(2);
    Debug.Log("Task A finished");
}

IEnumerator TaskB() {
    Debug.Log("Task B started");
    yield return StartCoroutine(TaskA());  // Waits for TaskA to finish
    Debug.Log("Task B finished");
}
```

### 7. **`yield break`** (Stop Coroutine)  
Ends a coroutine immediately.  
```csharp
IEnumerator StopCoroutineExample(bool shouldStop) {
    Debug.Log("Started");
    if (shouldStop) yield break;  // Exit immediately
    Debug.Log("This won't run if shouldStop is true");
}
```

---

### **When to Use Coroutines?**
✅ **Good for:**
- Timed delays (e.g., waiting a few seconds before an event).
- Smooth transitions (e.g., fading UI elements).
- Sequencing multiple tasks over time.

❌ **Avoid for:**
- Continuous logic that should be in `Update()`.
- Performance-heavy operations (consider using async/threads).
- Physics-based operations (use `FixedUpdate` instead).

### **Common Pitfalls**
- **Memory Leaks:** Ensure coroutines are stopped when no longer needed.
- **Complexity:** Overusing coroutines can make code harder to follow.

### **See Also:**
Not pleased with coroutines? Try **[Tasks](https://github.com/Karen-Najafzadeh/Unity-Internship-Learning-Documentation/tree/main/Asynchronous%20programming/Tasks)** for more complex asynchronous operations.
