# **Queues in C# and Unity – Complete Guide**  
A **Queue** in C# is a **FIFO (First-In, First-Out)** data structure. It is useful in Unity for handling event queues, AI decision-making, task management, and coroutine sequencing.

---

## **1️⃣ What is a Queue?**  
A **Queue** operates like a line:  
- The first element added is the first one removed (**FIFO**).
- You `Enqueue()` items and `Dequeue()` them when needed.

### **Basic Syntax**
```csharp
Queue<int> myQueue = new Queue<int>();  // Create a queue
myQueue.Enqueue(10);  // Add item
int firstItem = myQueue.Dequeue();  // Remove and get item
```

---

## **2️⃣ Queue vs. Other Data Structures**
| **Data Structure** | **Order** | **Usage** |
|--------------------|-----------|-----------|
| **Queue<T>** | First-In, First-Out (FIFO) | Handling tasks, AI behavior, background jobs |
| **Stack<T>** | Last-In, First-Out (LIFO) | Undo/Redo, function calls |
| **List<T>** | Index-based | Storing and iterating items dynamically |
| **Dictionary<TKey, TValue>** | Key-value pairs | Fast lookups by key |

---

## **3️⃣ Basic Queue Operations**
| **Operation** | **Description** | **Example** |
|--------------|----------------|-------------|
| **Enqueue(item)** | Adds an item to the queue | `queue.Enqueue(5);` |
| **Dequeue()** | Removes and returns the front item | `int x = queue.Dequeue();` |
| **Peek()** | Returns the front item **without removing it** | `int x = queue.Peek();` |
| **Count** | Returns the number of elements | `int size = queue.Count;` |
| **Contains(item)** | Checks if an item exists | `bool exists = queue.Contains(10);` |
| **Clear()** | Removes all elements | `queue.Clear();` |

---

## **4️⃣ Implementing a Queue in Unity**
### **Example: Queue for Enemy Spawn System**
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemySpawner : MonoBehaviour {
    Queue<GameObject> enemyQueue = new Queue<GameObject>();
    public GameObject enemyPrefab;

    void Start() {
        // Enqueue enemies
        for (int i = 0; i < 5; i++) {
            GameObject enemy = Instantiate(enemyPrefab);
            enemy.SetActive(false);  // Initially inactive
            enemyQueue.Enqueue(enemy);
        }

        // Spawn enemies with delay
        StartCoroutine(SpawnEnemies());
    }

    IEnumerator SpawnEnemies() {
        while (enemyQueue.Count > 0) {
            GameObject enemy = enemyQueue.Dequeue();  // Get first enemy
            enemy.SetActive(true);
            enemy.transform.position = new Vector3(Random.Range(-5, 5), 0, 0);
            yield return new WaitForSeconds(2f);  // Wait before next spawn
        }
    }
}
```
✅ **Best for handling tasks that should be executed sequentially**.

---

## **5️⃣ Using Queues for Coroutine Management**
Unity coroutines can use queues to **control execution order**.

### **Example: Coroutine Queue System**
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CoroutineQueueManager : MonoBehaviour {
    Queue<IEnumerator> coroutineQueue = new Queue<IEnumerator>();
    bool isProcessing = false;

    void Start() {
        // Enqueue coroutines
        EnqueueCoroutine(TaskOne());
        EnqueueCoroutine(TaskTwo());
        EnqueueCoroutine(TaskThree());
    }

    void EnqueueCoroutine(IEnumerator coroutine) {
        coroutineQueue.Enqueue(coroutine);
        if (!isProcessing) StartCoroutine(ProcessQueue());
    }

    IEnumerator ProcessQueue() {
        isProcessing = true;
        while (coroutineQueue.Count > 0) {
            yield return StartCoroutine(coroutineQueue.Dequeue());
        }
        isProcessing = false;
    }

    IEnumerator TaskOne() {
        Debug.Log("Task 1 started");
        yield return new WaitForSeconds(2);
        Debug.Log("Task 1 completed");
    }

    IEnumerator TaskTwo() {
        Debug.Log("Task 2 started");
        yield return new WaitForSeconds(1);
        Debug.Log("Task 2 completed");
    }

    IEnumerator TaskThree() {
        Debug.Log("Task 3 started");
        yield return new WaitForSeconds(3);
        Debug.Log("Task 3 completed");
    }
}
```
✅ **Ensures coroutines run in order without overlapping**.

---

## **6️⃣ Priority Queue (Sorted Processing)**
A **Priority Queue** sorts elements so that the most important ones get processed first. Unity does not have a built-in priority queue, but you can use a `SortedList` or `SortedDictionary`.

### **Example: AI Decision Queue (Highest Priority First)**
```csharp
using System.Collections.Generic;
using UnityEngine;

public class PriorityQueue<T> {
    private List<(int priority, T item)> queue = new List<(int, T)>();

    public void Enqueue(T item, int priority) {
        queue.Add((priority, item));
        queue.Sort((a, b) => b.priority.CompareTo(a.priority));  // Highest priority first
    }

    public T Dequeue() {
        if (queue.Count == 0) return default;
        T item = queue[0].item;
        queue.RemoveAt(0);
        return item;
    }

    public int Count => queue.Count;
}

public class AIController : MonoBehaviour {
    PriorityQueue<string> aiTasks = new PriorityQueue<string>();

    void Start() {
        aiTasks.Enqueue("Attack", 2);
        aiTasks.Enqueue("Defend", 3);
        aiTasks.Enqueue("Retreat", 1);

        while (aiTasks.Count > 0) {
            Debug.Log("Executing: " + aiTasks.Dequeue());
        }
    }
}
```
✅ **Great for AI behavior (e.g., strongest enemies act first).**

---

## **7️⃣ Circular Queue (Looping Behavior)**
A **Circular Queue** wraps around when it reaches the end.

### **Example: Looping Through Player Abilities**
```csharp
using System.Collections.Generic;
using UnityEngine;

public class CircularQueue<T> {
    private Queue<T> queue = new Queue<T>();

    public void Enqueue(T item) => queue.Enqueue(item);

    public T GetNext() {
        T item = queue.Dequeue();
        queue.Enqueue(item);  // Move to back
        return item;
    }
}

public class AbilityCycler : MonoBehaviour {
    CircularQueue<string> abilities = new CircularQueue<string>();

    void Start() {
        abilities.Enqueue("Fireball");
        abilities.Enqueue("Ice Blast");
        abilities.Enqueue("Lightning Strike");

        Debug.Log("Next Ability: " + abilities.GetNext());  // Fireball
        Debug.Log("Next Ability: " + abilities.GetNext());  // Ice Blast
        Debug.Log("Next Ability: " + abilities.GetNext());  // Lightning Strike
        Debug.Log("Next Ability: " + abilities.GetNext());  // Fireball again
    }
}
```
✅ **Perfect for ability rotations, animations, or cyclic UI elements.**

---

## **8️⃣ Comparison of Queue Variants**
| **Queue Type** | **Behavior** | **Use Case** |
|--------------|-----------|-----------|
| **Queue<T>** | FIFO (First-In, First-Out) | Task management, enemy spawns |
| **Priority Queue** | Sorted by priority | AI decision-making, event processing |
| **Circular Queue** | Wraps around | Ability rotations, repeating cycles |

---

## **🛠️ Summary Table: Queue Operations**
| **Operation** | **Description** | **Syntax** |
|--------------|----------------|------------|
| **Enqueue(item)** | Adds an item to the queue | `queue.Enqueue(5);` |
| **Dequeue()** | Removes and returns the front item | `int x = queue.Dequeue();` |
| **Peek()** | Returns the front item **without removing** | `int x = queue.Peek();` |
| **Count** | Returns the number of elements | `int size = queue.Count;` |
| **Contains(item)** | Checks if an item exists | `bool exists = queue.Contains(10);` |
| **Clear()** | Removes all elements | `queue.Clear();` |

---

# **Final Thoughts**
✅ **Queues are essential for sequential task execution in Unity**.  
✅ **FIFO ensures a smooth, ordered processing flow**.  
✅ **Priority Queues optimize AI and event handling**.  
✅ **Coroutine Queues prevent execution overlap**.  
