
# State Design Pattern

## What is the State Design Pattern?

The **State Design Pattern** is a behavioral design pattern that allows an object to alter its behavior when its internal state changes. It encapsulates the state-specific behavior into separate classes, enabling the object to delegate behavior based on its current state.  

This pattern is particularly useful when an object has multiple states, and its behavior depends on its state. Rather than using conditional statements (like `if-else` or `switch-case`) to handle state transitions, the State Pattern provides a cleaner, more modular approach by leveraging polymorphism.

---

## When to Use the State Design Pattern?

Use the State Pattern when:  
1. **Objects have dynamic behavior:** The object's behavior needs to change based on its current state.  
2. **State transitions are frequent:** Managing transitions through conditionals becomes error-prone or hard to maintain.  
3. **Modularity is required:** You want to separate state-specific behavior into independent classes for better maintainability and scalability.  
4. **Avoiding conditionals:** You aim to replace complex conditional logic with cleaner, polymorphic behavior.

---

## Simple Example: Implementing a Traffic Light

### Problem:  
We need to model a traffic light system that can change its behavior based on its current state (Red, Yellow, Green).  


---

## Example: Traffic Light

### Implementation:
---
First of all we need to define the states, so we use an interface as a base structure for our states

```csharp
using UnityEngine;

// State Interface
public interface ITrafficLightState
{
    void Handle(TrafficLight trafficLight);
}
```

Now, it's time to define the states, for instance, we have three states, Red, Green and Yellow. Here we define what each state should do.

```csharp

// Concrete States
public class RedState : ITrafficLightState
{
    public void Handle(TrafficLight trafficLight)
    {
        Debug.Log("Red Light - STOP!");
        trafficLight.SetState(new GreenState());
    }
}

public class GreenState : ITrafficLightState
{
    public void Handle(TrafficLight trafficLight)
    {
        Debug.Log("Green Light - GO!");
        trafficLight.SetState(new YellowState());
    }
}

public class YellowState : ITrafficLightState
{
    public void Handle(TrafficLight trafficLight)
    {
        Debug.Log("Yellow Light - Slow Down!");
        trafficLight.SetState(new RedState());
    }
}
```

Now it's time to implement the Trafficlight Class, and use the state pattern we designed earlier.
```csharp
// Context Class
public class TrafficLight : MonoBehaviour
{
    private ITrafficLightState _currentState;

    private void Start()
    {
        // Initialize with RedState
        _currentState = new RedState();
        StartCoroutine(SimulateTrafficLight());
    }

    public void SetState(ITrafficLightState state)
    {
        _currentState = state;
    }

    public void Request()
    {
        _currentState.Handle(this);
    }

    private System.Collections.IEnumerator SimulateTrafficLight()
    {
        while (true)
        {
            yield return new WaitForSeconds(2f); // Simulate a delay between state changes
            Request();
        }
    }
}
```

### Output:
```
Red Light - STOP!  
Green Light - GO!  
Yellow Light - Slow Down!  
Red Light - STOP!  
Green Light - GO!  
Yellow Light - Slow Down!
.
.
.

```

### How to Use:
1. Create a new GameObject in Unity.
2. Attach the `TrafficLight` script to the GameObject.
3. Run the game to see the states transition, logged in the Unity Console.



---

## Practical Example: Vending Machine

### Problem:  
A vending machine transitions between different states:  
1. **No Coin State** - Awaiting a coin.  
2. **Has Coin State** - Awaiting product selection.  
3. **Dispensing State** - Dispensing the selected product.  

Each state has its own behavior. For instance, without a coin, the machine shouldn't allow product selection. Or when has a coin, it should be wating for product selection and ...

### Implementation:
---
First we need to design the base structure for our states:
```csharp
// State Interface
public interface IVendingMachineState
{
    void InsertCoin(VendingMachine context);
    void SelectProduct(VendingMachine context);
    void DispenseProduct(VendingMachine context);
}
```

Now it's time to implement each state:

```csharp
// Concrete States
public class NoCoinState : IVendingMachineState
{
    public void InsertCoin(VendingMachine vendingMachine)
    {
        Debug.Log("Coin inserted.");
        vendingMachine.SetState(new HasCoinState());
    }

    public void SelectProduct(VendingMachine vendingMachine)
    {
        Debug.Log("Insert a coin first.");
    }

    public void DispenseProduct(VendingMachine vendingMachine)
    {
        Debug.Log("Insert a coin first.");
    }
}

public class HasCoinState : IVendingMachineState
{
    public void InsertCoin(VendingMachine vendingMachine)
    {
        Debug.Log("Coin already inserted.");
    }

    public void SelectProduct(VendingMachine vendingMachine)
    {
        Debug.Log("Product selected.");
        vendingMachine.SetState(new DispensingState());
    }

    public void DispenseProduct(VendingMachine vendingMachine)
    {
        Debug.Log("Select a product first.");
    }
}

public class DispensingState : IVendingMachineState
{
    public void InsertCoin(VendingMachine vendingMachine)
    {
        Debug.Log("Please wait, dispensing in progress.");
    }

    public void SelectProduct(VendingMachine vendingMachine)
    {
        Debug.Log("Please wait, dispensing in progress.");
    }

    public void DispenseProduct(VendingMachine vendingMachine)
    {
        Debug.Log("Dispensing product...");
        vendingMachine.SetState(new NoCoinState());
    }
}

```
Time to use the states:


```csharp
// Context Class
public class VendingMachine : MonoBehaviour
{
    private IVendingMachineState _currentState;

    private void Start()
    {
        _currentState = new NoCoinState(); // Initial state
    }

    public void SetState(IVendingMachineState state)
    {
        _currentState = state;
    }

    public void InsertCoin()
    {
        _currentState.InsertCoin(this);
    }

    public void SelectProduct()
    {
        _currentState.SelectProduct(this);
    }

    public void DispenseProduct()
    {
        _currentState.DispenseProduct(this);
    }

    private void Update()
    {
        // Simulate input for testing (use proper input handling in real projects)
        if (Input.GetKeyDown(KeyCode.C))
        {
            InsertCoin();
        }
        else if (Input.GetKeyDown(KeyCode.S))
        {
            SelectProduct();
        }
        else if (Input.GetKeyDown(KeyCode.D))
        {
            DispenseProduct();
        }
    }
}
```

### How to use:
1. Create a new GameObject in Unity.
2. Attach the VendingMachine script to the GameObject.
3. Run the game and press the following keys to test:
    - C: Insert Coin
    - S: Select Product
    - D: Dispense Product

### Output:
```
Insert a coin first.  
Coin inserted.  
Product selected.  
Dispensing product...  
Insert a coin first.
.
.
.
```

---

## Advantages of the State Pattern:
1. **Encapsulation of state-specific behavior:** Each state has its own class, reducing complexity.  
2. **Ease of extension:** Adding new states is straightforward.  
3. **Code maintainability:** Removes clutter from conditional statements and centralizes logic in state classes.

---

