# Unity Events: A Comprehensive Guide

Unity events are a way to communicate between scripts in Unity, allowing scripts to send messages or trigger actions without directly referencing each other. They are essential for creating modular and reusable code. Unity provides built-in events as well as the ability to create custom events.

## What Are Unity Events?
Unity events allow communication between different scripts or objects in a decoupled manner. Instead of directly calling methods on other scripts, you use events to broadcast messages, and other scripts can subscribe to those messages.

Generally, in the Event concept, we have **Publishers** (which have the event) and **Subscribers** (which listen to the event and, when triggered, do something).

The Publisher has no idea how many listeners are there and doesn't know they even exist. When the time is right, it just fires off the event and notifies every listener that the event has been triggered.

### Why Use Unity Events?
- **Decoupling:** Scripts don't need direct references to each other.
- **Flexibility:** You can change behavior at runtime.
- **Reusability:** Events make scripts more modular and reusable.

---

### How to use an event?

Generally, to be able to use events, we have to implement 4 steps:
  - Declare the event
  - Trigger the event
  - Subscribe to the event
  - Define what to do when the event is triggered

Normally, the first two steps are implemented in the publisher script while the other two are implemented in the listener script.

### 1 & 2. Declare & Trigger the event:

First of all, we need to use the **System** namespace. We define the event by the **event** keyword and make it public to be accessed by other scripts. Normally the name of the event starts with an **On**. You don't have to name it this way, but it's just a convention that helps others and yourself to read the code easily.

```csharp
using System;
using UnityEngine;

public class PublisherClass : SingletonClass<PublisherClass> {

  // Here we declare the event.
  public event EventHandler OnSpacePressed;

  // Here we're gonna trigger the event when the space button is pressed.
  private void Update(){
    if (Input.GetKeyDown(KeyCode.Space)){
      OnSpacePressed?.Invoke(this, EventArgs.Empty);
      // The "this" argument is the sender, which is this script.
      // And the second argument is the additional data that we want to send to the listeners,
      // which in this case, is nothing.
    }
  }
}
```

The SingletonClass we inherited from is a class that I made that enables us to use the singleton pattern. For more info and to see the code, [Click here](https://github.com/Karen-Najafzadeh/Unity-Internship-Learning-Documentation/tree/main/Design-Patterns/Singletone-Pattern#making-it-easier).

The **?** and **.Invoke()** after the event are for checking if the event is not null and only then, triggers the event.

### 3 & 4. Subscribe to the event & define the behavior:

When we are done with the declaration and triggering, it's time to listen to the event and define what it does when triggered.

To subscribe and listen to an event, we have to get a reference to the event. This is where the Singleton Pattern will come to our aid.
By accessing the instance of the **PublisherClass**, we can tell the **SubscriberClass** to listen to the event.

Then, we tell the **SubscriberClass** what to do when the event is triggered. This is done by the following code:

```csharp
using System;
using UnityEngine;

public class SubscriberClass: MonoBehaviour{

  private void Start(){
    // Here we subscribe to the event 
    PublisherClass.instance.OnSpacePressed += DoThisWhenTriggered;
  }

  // And finally, we define what to do when the event is triggered:
  private void DoThisWhenTriggered(object sender, EventArgs e){
    Debug.Log("Space button pressed");
  }
}
```

### Unsubscribe the event:

Just like the way we subscribed to the event, we can also undo the subscription by using **-=** instead of **+=**. Just like this code:
```csharp
PublisherClass.instance.OnSpacePressed -= DoThisWhenTriggered;
```

### EventArgs, A way to send more info:
What if we want to notify the listeners and also send them some info too? To do so, we can use EventArgs. Look at the example below:

For example, each time that the space button is pressed, we want to notify the listeners and also send them the amount of times we've pressed the space button so far.

To do so, we need to cache an integer representing the count and increase it whenever the event is triggered.

To pass in data, we also need to create a new class and use it to send the data (In this example, **OnSpacePressedEventArgs**).

```csharp
using System;
using UnityEngine;

public class PublisherClass : SingletonClass<PublisherClass> {

  // Here we declare the event. Notice the changes we made.
  public event EventHandler<OnSpacePressedEventArgs> OnSpacePressed;
  
  // The class responsible to send the data:
  public class OnSpacePressedEventArgs : EventArgs {
    public int spaceCount;
  }

  // Let's declare an int for the count:
  private int count;

  // Here we're gonna trigger the event when the space button is pressed.
  private void Update(){
    if (Input.GetKeyDown(KeyCode.Space)){
      // Increase the count
      count++;
      OnSpacePressed?.Invoke(this, new OnSpacePressedEventArgs { spaceCount = count });
    }
  }
}
```

And in the subscriber, we can access the data sent, like this:

```csharp
using System;
using UnityEngine;

public class SubscriberClass: MonoBehaviour{

  private void Start(){
    // Here we subscribe to the event 
    PublisherClass.instance.OnSpacePressed += DoThisWhenTriggered;
  }

  // And finally, we define what to do when the event is triggered:
  // Modify the signature to receive our new type: 
  private void DoThisWhenTriggered(object sender, PublisherClass.OnSpacePressedEventArgs data){
    Debug.Log($"Space button pressed {data.spaceCount} times so far");
  }
}
```

### Custom Event Handlers:
The **EventHandler** type that we used is just a delegate. If it doesn't meet your needs, you can also define your own delegates.

Declaration in the PublisherClass:
```csharp
public event MyDelegateEventHandler OnSomethingHappened;
public delegate void MyDelegateEventHandler(float f);
// A delegate that returns void and has a float parameter.
```
Triggering:

```csharp
OnSomethingHappened?.Invoke(5.5f);
```
Subscription in the SubscriberClass:

```csharp
PublisherClass.instance.OnSomethingHappened += DoThis;
```
Defining what to do:

```csharp
private void DoThis(float f){
  Debug.Log($"This is the data that is sent to the subscriber class from the publisher class: {f}");
}
```

### Take Action:

Actions are yet another useful type of delegates. We can achieve the same with taking these Actions into the battlefield too. It's quite easy, I'll show you how:

Declaration:
```csharp
public event Action<bool, int> OnActionEvent;
// You can give it any parameters you want.
```

Triggering:
```csharp
OnActionEvent?.Invoke(true, 12);
```

Subscription:

We subscribe the same way:
```csharp
PublisherClass.instance.OnActionEvent += DoThat;
```

Definition of the job to be done:

```csharp
private void DoThat(bool arg1, int arg2){
  Debug.Log($"First argument is {arg1} and the second one is {arg2}");
}
```

### UnityEvents

Another way to do the same is to use the built-in UnityEvents inside the **UnityEngine.Events** namespace. Here is how to do so:

```csharp
using UnityEngine;
using UnityEngine.Events;

public class PublisherClass : MonoBehaviour {
    // notice that we are not using the event keyword here
    public UnityEvent OnSpacePressed;

    private void Update(){
    if (Input.GetKeyDown(KeyCode.Space)){
        OnSpacePressed?.Invoke();
        }
    }
}
```
By doing this, we get a field in the PublisherClass GameObject's Inspector. We can then assign the **``SubscriberClass``** GameObject to this field and choose the method that will be called when the event is triggered.

Alternatively, you can do this by code, just like the example bellow:

```csharp

public class SubscriberClass : MonoBehaviour {

    // It's a method that is executed when the gameobject with this script attached to, is enabled.
    private void OnEnable(){
        PublisherClass.Instance.OnSpacePressed.AddListener(DoThisWhenTriggered);
    }

    // Quite the opposite thing
    private void OnDisable(){
        PublisherClass.Instance.OnSpacePressed.RemoveListener(DoThisWhenTriggered);
    }

    private void DoThisWhenTriggered(){
        Debug.Log("Space button pressed");
    }
}
```

Unity events are a powerful tool for decoupling your code and enabling flexible interactions between GameObjects and scripts. Whether you use built-in Unity events, UnityEvent, or C# delegates, you can design systems that are easy to extend and maintain.
