# Command Design Pattern

## What is the Command Design Pattern?

The **Command Pattern** is a behavioral design pattern that encapsulates a request or action as an object, allowing you to:  
1. Parameterize objects with actions.  
2. Delay execution of a command.  
3. Queue commands for later execution.  
4. Maintain a history of executed commands for undo/redo functionality.

In simpler terms, the pattern turns a request into a standalone object that contains all the details of the request. This decouples the sender (who triggers the request) from the receiver (who executes the request).

---

## When to Use the Command Design Pattern?

Use the Command Pattern when:  
1. **You need undo/redo functionality:** For example, in text editors or level editors.  
2. **You want to queue requests:** For example, in AI task systems or job processing queues.  
3. **You need to decouple request execution from its invocation:** For example, triggering actions from UI buttons without directly tying them to the logic.  
4. **You want to log executed operations:** For example, to debug or replay user actions.  

---

### **Step-by-Step Walkthrough of the Command Pattern:**
### Simple Example: Light Switch
#### Problem:
We want to control a light using commands to turn it on and off.

#### 1. **Understanding the Command Pattern:**
   The **Command Pattern** is a way to turn a request (action) into a standalone object. This object can then be passed around, executed, undone, or queued for later. Instead of calling a method directly on an object (like in regular programming), you use command objects to execute methods or actions.

   **Core Concept:**  
   - **Command** = The object that represents the action.  
   - **Invoker** = The object that asks for the action to be executed.  
   - **Receiver** = The object that knows how to perform the actual action.

#### 2. **The Components of the Command Pattern:**

1. **Receiver:**  
   This is the object that actually knows how to perform the action. For example, in our light switch example, the `Light` class is the receiver because it knows how to turn the light on and off.

   ```csharp
   public class Light
   {
       public void TurnOn() { Debug.Log("The light is ON."); }
       public void TurnOff() { Debug.Log("The light is OFF."); }
   }
   ```

2. **Command Interface:**  
   This is the interface that defines the common method (`Execute()`) that every concrete command will implement. The `Undo()` method is optional, but it's common for commands that need to support undo functionality.

   ```csharp
   public interface ICommand
   {
       void Execute();
       void Undo();
   }
   ```

3. **Concrete Command Classes:**  
   These are the classes that implement the `ICommand` interface. Each concrete command knows how to call the methods on the receiver to perform the desired action. In our example, we have two concrete commands: `TurnOnLightCommand` and `TurnOffLightCommand`.

   ```csharp
   public class TurnOnLightCommand : ICommand
   {
       private Light _light;
       public TurnOnLightCommand(Light light) { _light = light; }
       public void Execute() { _light.TurnOn(); }
       public void Undo() { _light.TurnOff(); }
   }

   public class TurnOffLightCommand : ICommand
   {
       private Light _light;
       public TurnOffLightCommand(Light light) { _light = light; }
       public void Execute() { _light.TurnOff(); }
       public void Undo() { _light.TurnOn(); }
   }
   ```

4. **Invoker:**  
   The invoker is the object that calls the `Execute()` method of a command. It doesn't need to know how the action is executed or what the action is, just that it can call `Execute()`.

   ```csharp
   public class RemoteControl
   {
       private ICommand _command;
       public void SetCommand(ICommand command) { _command = command; }
       public void PressButton() { _command.Execute(); }
       public void PressUndo() { _command.Undo(); }
   }
   ```

5. **Client (Usage):**  
   The client creates the objects, sets the commands, and tells the invoker to execute them. In this case, the client is a `MonoBehaviour` script that binds everything together in Unity.

   ```csharp
   public class CommandPatternExample : MonoBehaviour
   {
       private void Start()
       {
           Light light = new Light();
           ICommand turnOnCommand = new TurnOnLightCommand(light);
           ICommand turnOffCommand = new TurnOffLightCommand(light);

           RemoteControl remote = new RemoteControl();

           // Turn on the light
           remote.SetCommand(turnOnCommand);
           remote.PressButton();

           // Undo the action
           remote.PressUndo();

           // Turn off the light
           remote.SetCommand(turnOffCommand);
           remote.PressButton();
       }
   }
   ```

   **Output:**
   ```
   The light is ON.
   The light is OFF.
   The light is OFF.
   The light is ON.
   ```

#### 3. **What is Happening in the Command Pattern Example?**

- The **Receiver** (`Light`) knows how to perform the action (turning the light on or off).
- The **Command** (`TurnOnLightCommand` and `TurnOffLightCommand`) encapsulates the request, telling the receiver what to do (turn the light on/off).
- The **Invoker** (`RemoteControl`) doesn't know how the light works; it just calls `Execute()` on the command.
- The **Client** (`CommandPatternExample`) sets up the commands and presses the button to trigger actions.

#### 4. **Comparing Command Pattern to State and Strategy Patterns:**

Now, let's compare the **Command Pattern** with the **State** and **Strategy** Patterns. Here's a simplified way to look at them:

| **Aspect**              | **Command Pattern**                                                                                         | **State Pattern**                                                                                       | **Strategy Pattern**                                                                                   |
|-------------------------|------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| **Purpose**             | Encapsulates a request/action as an object, allowing it to be queued, undone, or executed later.           | Manages state transitions and changes the object's behavior dynamically based on its state.           | Encapsulates interchangeable algorithms or behaviors to provide flexibility and modularity.           |
| **Behavior Management** | The invoker asks the command to execute an action. The command knows how to call the receiver.              | The object switches its behavior depending on its current state.                                        | The object switches between different behaviors based on the context or choice.                        |
| **How It Works**        | The invoker calls `Execute()` on a command, which then calls a method on the receiver (e.g., turn the light on). | The object's behavior depends on its state, and the state manages transitions to other states.         | The object dynamically selects a behavior (algorithm) to use for a task.                              |
| **Use Cases**           | Undo/redo functionality, task queues, user input systems.                                                   | Game AI, workflow systems, vending machines.                                                          | Sorting algorithms, movement behaviors, payment strategies.                                            |
| **Decoupling**          | Decouples the sender (invoker) from the receiver (action performer).                                         | The state manages the behavior, while the context controls transitions.                                | The client selects the strategy, decoupling the strategy from the task it performs.                   |

---

### **Key Takeaways:**

1. **Command Pattern:**
   - Allows actions to be encapsulated as objects.
   - Invokes actions at a later time (or queues them).
   - Can support undo functionality.
   - Decouples the sender from the receiver.

2. **State Pattern:**
   - Changes an object's behavior based on its internal state.
   - States manage the transitions between one another.
   - Useful for scenarios where an object has multiple distinct states (e.g., game characters, workflows).

3. **Strategy Pattern:**
   - Allows switching between different algorithms or behaviors for a task.
   - The client explicitly selects which strategy to use.
   - Useful for scenarios where different algorithms can be applied to a task (e.g., sorting, movement types).

---

## Complex Example: Home Automation System

### Problem:
We want to control various devices in a home automation system using commands. Devices include lights, fans, and thermostats. We want to be able to turn devices on/off, set temperatures, and undo actions.

### Implementation:


### **First** we need to defin the recievers

```csharp
using System.Collections.Generic;
using UnityEngine;

// Receiver Classes
public class Light
{
    public void TurnOn() { Debug.Log("Light is ON."); }
    public void TurnOff() { Debug.Log("Light is OFF."); }
}

public class Fan
{
    public void TurnOn() { Debug.Log("Fan is ON."); }
    public void TurnOff() { Debug.Log("Fan is OFF."); }
}

public class Thermostat
{
    private int _temperature;
    public void SetTemperature(int temperature)
    {
        _temperature = temperature;
        Debug.Log($"Thermostat set to {_temperature} degrees.");
    }
}
```

### **Second** defining the Interface

```csharp
// Command Interface
public interface ICommand
{
    void Execute();
    void Undo();
}
```

### **Third** we should define the commands

```csharp
// Concrete Commands
public class TurnOnLightCommand : ICommand
{
    private Light _light;
    public TurnOnLightCommand(Light light) { _light = light; }
    public void Execute() { _light.TurnOn(); }
    public void Undo() { _light.TurnOff(); }
}

public class TurnOffLightCommand : ICommand
{
    private Light _light;
    public TurnOffLightCommand(Light light) { _light = light; }
    public void Execute() { _light.TurnOff(); }
    public void Undo() { _light.TurnOn(); }
}

public class TurnOnFanCommand : ICommand
{
    private Fan _fan;
    public TurnOnFanCommand(Fan fan) { _fan = fan; }
    public void Execute() { _fan.TurnOn(); }
    public void Undo() { _fan.TurnOff(); }
}

public class TurnOffFanCommand : ICommand
{
    private Fan _fan;
    public TurnOffFanCommand(Fan fan) { _fan = fan; }
    public void Execute() { _fan.TurnOff(); }
    public void Undo() { _fan.TurnOn(); }
}

public class SetThermostatCommand : ICommand
{
    private Thermostat _thermostat;
    private int _temperature;
    private int _previousTemperature;

    public SetThermostatCommand(Thermostat thermostat, int temperature)
    {
        _thermostat = thermostat;
        _temperature = temperature;
    }

    public void Execute()
    {
        _previousTemperature = _temperature; // Assume we can get the current temperature
        _thermostat.SetTemperature(_temperature);
    }

    public void Undo()
    {
        _thermostat.SetTemperature(_previousTemperature);
    }
}
```

### Now it's time to define the invoker:

```csharp
// Invoker Class
public class HomeAutomationController
{
    private Stack<ICommand> _commandHistory = new Stack<ICommand>();

    public void ExecuteCommand(ICommand command)
    {
        command.Execute();
        _commandHistory.Push(command);
    }

    public void UndoLastCommand()
    {
        if (_commandHistory.Count > 0)
        {
            ICommand lastCommand = _commandHistory.Pop();
            lastCommand.Undo();
        }
    }
}
```

### **Finally** we imlement the usage and invoke the commands

```csharp
// Client (Usage)
public class HomeAutomationExample : MonoBehaviour
{
    private void Start()
    {
        Light livingRoomLight = new Light();
        Fan bedroomFan = new Fan();
        Thermostat thermostat = new Thermostat();

        ICommand turnOnLivingRoomLight = new TurnOnLightCommand(livingRoomLight);
        ICommand turnOffLivingRoomLight = new TurnOffLightCommand(livingRoomLight);
        ICommand turnOnBedroomFan = new TurnOnFanCommand(bedroomFan);
        ICommand turnOffBedroomFan = new TurnOffFanCommand(bedroomFan);
        ICommand setThermostatTo22 = new SetThermostatCommand(thermostat, 22);

        HomeAutomationController controller = new HomeAutomationController();

        // Turn on the living room light
        controller.ExecuteCommand(turnOnLivingRoomLight);

        // Turn off the living room light
        controller.ExecuteCommand(turnOffLivingRoomLight);

        // Turn on the bedroom fan
        controller.ExecuteCommand(turnOnBedroomFan);

        // Set thermostat to 22 degrees
        controller.ExecuteCommand(setThermostatTo22);

        // Undo the last command (set thermostat)
        controller.UndoLastCommand();

        // Undo the previous command (turn on bedroom fan)
        controller.UndoLastCommand();
    }
}
```

### Output (Unity Console):
```
Light is ON.
Light is OFF.
Fan is ON.
Thermostat set to 22 degrees.
Thermostat set to 22 degrees.
Fan is OFF.
```

---

This complex example demonstrates how the Command Pattern can be used to manage a home automation system, allowing for actions to be executed and undone dynamically.
