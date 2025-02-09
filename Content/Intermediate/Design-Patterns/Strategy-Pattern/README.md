
# Strategy Design Pattern

## What is the Strategy Design Pattern?

The **Strategy Design Pattern** is a behavioral design pattern that defines a family of algorithms, encapsulates each one in a separate class, and makes them interchangeable. This pattern allows you to select the algorithm's behavior at runtime.

Instead of implementing multiple algorithms directly in a class using conditional statements, the Strategy Pattern delegates the responsibility to different strategy classes. This leads to cleaner, more modular, and maintainable code.

---

## When to Use the Strategy Design Pattern?

Use the Strategy Pattern when:  
1. **You need different behaviors for similar operations:** For example, different sorting algorithms, pathfinding strategies, or AI behaviors.  
2. **You want to eliminate complex conditionals:** Replace large `if-else` or `switch` statements with polymorphism.  
3. **Algorithms can change at runtime:** For example, switching difficulty levels in a game or dynamically adjusting player behavior.  
4. **You need to make the system extensible:** New algorithms can be added without modifying the existing code.

---

## Simple Example: Character Attack Behavior

### Problem:  
A character can attack using different strategies (e.g., melee attack, ranged attack). Each attack behavior should be interchangeable without modifying the character class.

### Implementation:

Let's start by makeing a base structure for our strategies:

```csharp
using UnityEngine;

// Strategy Interface
public interface IAttackStrategy
{
    void Attack();
}
```

Now let's design the strategies and implement what each strategy does:

```csharp

// Concrete Strategies
public class MeleeAttack : IAttackStrategy
{
    public void Attack()
    {
        Debug.Log("Performing a melee attack!");
    }
}

public class RangedAttack : IAttackStrategy
{
    public void Attack()
    {
        Debug.Log("Performing a ranged attack!");
    }
}
```

Now let's create the Character:


```csharp
// Context Class
public class Character : MonoBehaviour
{
    private IAttackStrategy _attackStrategy;

    public void SetAttackStrategy(IAttackStrategy strategy)
    {
        _attackStrategy = strategy;
    }

    public void PerformAttack()
    {
        if (_attackStrategy != null)
        {
            _attackStrategy.Attack();
        }
        else
        {
            Debug.Log("No attack strategy selected.");
        }
    }
}
```

Finally it's time to use the strategies we defined earlier

```csharp
// Usage
public class StrategyPatternExample : MonoBehaviour
{
    private void Start()
    {
        Character character = new Character();

        character.SetAttackStrategy(new MeleeAttack());
        character.PerformAttack();

        character.SetAttackStrategy(new RangedAttack());
        character.PerformAttack();
    }
}
```

### Usage
Create Two GameObjects and attach the Character and StrategyPatternExample scripts to them respectively.


### Output (Unity Console):
```
Performing a melee attack!
Performing a ranged attack!
```

---

## Robust and Practical Example: NPC Movement Behavior

### Problem:  
An NPC needs to move in different ways depending on its current behavior (e.g., patrolling, chasing, or idle). Each movement behavior can be implemented as a separate strategy, and the NPC can switch strategies dynamically.

### Implementation:

Create the base structure for strategies

```csharp
using UnityEngine;

// Strategy Interface
public interface IMovementStrategy
{
    void Move(Transform npcTransform);
}
```

Define the strategies:

```csharp
using UnityEngine;

// Concrete Strategies
public class PatrolMovement : IMovementStrategy
{
    private Vector3 _startPoint = new Vector3(-5, 0, 0);
    private Vector3 _endPoint = new Vector3(5, 0, 0);
    private float _speed = 2f;
    private bool _movingToEnd = true;

    public void Move(Transform npcTransform)
    {
        if (_movingToEnd)
        {
            npcTransform.position = Vector3.MoveTowards(npcTransform.position, _endPoint, _speed * Time.deltaTime);
            if (npcTransform.position == _endPoint)
            {
                _movingToEnd = false;
            }
        }
        else
        {
            npcTransform.position = Vector3.MoveTowards(npcTransform.position, _startPoint, _speed * Time.deltaTime);
            if (npcTransform.position == _startPoint)
            {
                _movingToEnd = true;
            }
        }
    }
}

public class ChaseMovement : IMovementStrategy
{
    private Transform _playerTransform;
    private float _speed = 3.5f;

    public ChaseMovement(Transform playerTransform)
    {
        _playerTransform = playerTransform;
    }

    public void Move(Transform npcTransform)
    {
        npcTransform.position = Vector3.MoveTowards(npcTransform.position, _playerTransform.position, _speed * Time.deltaTime);
    }
}

public class IdleMovement : IMovementStrategy
{
    public void Move(Transform npcTransform)
    {
        Debug.Log("NPC is idle.");
    }
}
```

Now let's create the NPC itself:

```csharp
using UnityEngine;

// Context Class
public class NPC : MonoBehaviour
{
    private IMovementStrategy _movementStrategy;

    public void SetMovementStrategy(IMovementStrategy strategy)
    {
        _movementStrategy = strategy;
    }

    private void Update()
    {
        _movementStrategy?.Move(transform);
    }
}
```
Just like that, simple. huh ?
Now let's create a class responsible for controlling the NPC:

```csharp
using UnityEngine;

// Usage
public class NPCController : MonoBehaviour
{
    [SerializeField] private NPC npc;
    [SerializeField] private Transform playerTransform;

    private void Start()
    {
        // Start with patrol movement
        npc.SetMovementStrategy(new PatrolMovement());
    }

    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.P))
        {
            npc.SetMovementStrategy(new PatrolMovement());
            Debug.Log("Switched to Patrol Movement.");
        }
        else if (Input.GetKeyDown(KeyCode.C))
        {
            npc.SetMovementStrategy(new ChaseMovement(playerTransform));
            Debug.Log("Switched to Chase Movement.");
        }
        else if (Input.GetKeyDown(KeyCode.I))
        {
            npc.SetMovementStrategy(new IdleMovement());
            Debug.Log("Switched to Idle Movement.");
        }
    }
}
```

### Usage:
1. Create an NPC GameObject in Unity and attach the `NPC` script to it.
2. Create a player GameObject and assign its `Transform` to the `NPCController` script.
3. Use the following keys to switch strategies:
   - `P`: Patrol Movement
   - `C`: Chase Movement (follows the player)
   - `I`: Idle Movement

### Output (Unity Console):
```
Switched to Patrol Movement.
Switched to Chase Movement.
Switched to Idle Movement.
```

---

## Advantages of the Strategy Pattern:
1. **Eliminates conditional logic:** No need for long `if-else` or `switch` statements to decide behavior.  
2. **Improves flexibility:** Easily swap out behaviors at runtime.  
3. **Promotes the Open/Closed Principle:** Adding new strategies doesn't require modifying existing code.  
4. **Encourages modularity:** Each strategy is encapsulated in its own class, making the code more manageable and testable.

---

contact me and let me know if you need explanations or refinements! 😊

---
# A big **QUESTION**:
### is it similar to the state pattern or am I getting it wrong?

You're absolutely right to notice some similarities between the **State** and **Strategy** design patterns they do share some concepts. However, they are used for different purposes and have key differences. Here's a comparison table to help clarify:

| **Aspect**              | **State Pattern**                                                                                       | **Strategy Pattern**                                                                                   |
|-------------------------|-------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| **Purpose**             | Manages the internal **state** of an object, allowing its behavior to change dynamically based on its state. | Encapsulates different **algorithms/behaviors** and allows the client to choose or switch between them. |
| **Focus**               | Focuses on the **state transitions** and the behavior changes that come with those transitions.         | Focuses on providing multiple **interchangeable algorithms or behaviors** for a task.                 |
| **Behavior Changes**    | The object itself often controls when to transition to another state.                                  | The client (or external code) chooses which behavior or strategy to use.                              |
| **Implementation**      | States are tightly coupled with the context (e.g., the context knows which state to transition to).     | Strategies are typically **independent** and don't interact with each other.                          |
| **Dynamic Switching**   | Switching between states happens based on rules defined by the **current state** or the context.        | Switching strategies is typically done **explicitly by the client**.                                  |
| **Common Use Cases**    | - Managing game AI states (e.g., idle, patrol, chase, attack)                                          | - Sorting algorithms (e.g., quicksort, mergesort)                                                     |
|                         | - Workflow processes (e.g., approval workflows)                                                       | - Movement behaviors (e.g., walking, flying, swimming)                                                |
|                         | - UI transitions (e.g., menu states)                                                                  | - Pricing strategies (e.g., discounts, premium pricing)                                               |
| **Object Behavior**     | Behavior depends on the **current state** of the object.                                               | Behavior depends on the **selected strategy** for the object.                                         |
| **Example Analogy**     | A vending machine that changes behavior based on its state (e.g., No Coin, Has Coin, Dispensing).      | A payment system that allows the user to pay with a credit card, PayPal, or cryptocurrency.           |
| **Code Dependency**     | States are aware of each other and may define the next state in their logic.                           | Strategies are independent and unaware of one another.                                                |
| **Granularity**         | Focuses on managing an object's behavior at a **macro level** (entire state lifecycle).                | Focuses on swapping **micro-level algorithms** or behaviors.                                          |

---

### Key Similarities:
1. **Both use encapsulation:** Both patterns encapsulate behaviors into separate classes, promoting modularity and clean code.  
2. **Both use composition:** Both patterns use composition instead of inheritance to allow dynamic behavior changes at runtime.  
3. **Both allow flexibility:** They provide the ability to change behavior dynamically during runtime, making systems more adaptable.  

---

### Key Differences:
1. **State Transition vs. Behavior Selection:**
   - In the **State Pattern**, transitions between states are managed automatically by the context or the current state.  
   - In the **Strategy Pattern**, the client explicitly chooses which strategy to use.  
2. **Purpose:**
   - The **State Pattern** is about managing the lifecycle or states of an object.  
   - The **Strategy Pattern** is about swapping algorithms or behaviors for a single task.  
3. **Coupling:**
   - States are often coupled to the context and may reference or dictate transitions to other states.  
   - Strategies are completely independent and don't interact with each other.

---

### When to Use Which?

- **State Pattern:** Use when an object's behavior depends on its state, and it needs to transition between different states automatically (e.g., AI states, workflow processes).  
- **Strategy Pattern:** Use when you want to swap algorithms or behaviors dynamically based on the situation or user choice (e.g., payment options, movement strategies).

---

Does this make it clearer? 😊

