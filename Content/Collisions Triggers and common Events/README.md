Unity provides various built-in **events and triggers** that help you interact with objects, handle physics, detect input, and respond to user actions. Here’s a breakdown of the most commonly used ones:

---

## **1. Collision and Trigger Events**  
These events detect when objects **collide** or **pass through each other**. They require **Collider** components and may need **Rigidbody** components to work.

### **Physics Collisions (Requires Rigidbody)**
| Event | Description |
|------|------------|
| `OnCollisionEnter(Collision collision)` | Called when the GameObject **collides** with another object. |
| `OnCollisionStay(Collision collision)` | Called every frame while two objects **stay in contact**. |
| `OnCollisionExit(Collision collision)` | Called when the object **stops colliding** with another object. |

🔹 **Example Usage:**
```csharp
void OnCollisionEnter(Collision collision)
{
    Debug.Log("Collided with: " + collision.gameObject.name);
}
```

---

### **Trigger Events (Pass-through Collisions)**
- Used for areas like checkpoints, damage zones, or interactive regions.
- **Objects must have "Is Trigger" enabled on their Collider.**

| Event | Description |
|------|------------|
| `OnTriggerEnter(Collider other)` | Called when an object **enters** a trigger zone. |
| `OnTriggerStay(Collider other)` | Called every frame while an object **stays** inside a trigger. |
| `OnTriggerExit(Collider other)` | Called when an object **leaves** the trigger zone. |

🔹 **Example Usage:**
```csharp
void OnTriggerEnter(Collider other)
{
    Debug.Log("Entered trigger: " + other.gameObject.name);
}
```

---

## **2. Mouse and Input Events**
These events handle user interaction using the **mouse or touch**.

| Event | Description |
|------|------------|
| `OnMouseDown()` | Called when the object is **clicked**. |
| `OnMouseUp()` | Called when the mouse button is **released** over the object. |
| `OnMouseEnter()` | Called when the mouse pointer **enters** the object’s Collider. |
| `OnMouseExit()` | Called when the mouse pointer **leaves** the object’s Collider. |
| `OnMouseOver()` | Called **every frame** while the mouse is over the object. |
| `OnMouseDrag()` | Called when the object is clicked and **dragged**. |

🔹 **Example Usage (Clickable Object):**
```csharp
void OnMouseDown()
{
    Debug.Log("Object Clicked: " + gameObject.name);
}
```

📌 **Note:** These require a **Collider** attached to the object to detect mouse interactions.

---

## **3. UI Events (Requires EventSystem & GraphicRaycaster)**
Unity UI (e.g., **Buttons, Images, Panels**) uses **EventSystem** to detect clicks and interactions.

| Event | Description |
|------|------------|
| `OnPointerClick(PointerEventData eventData)` | Called when a UI element is clicked. |
| `OnPointerEnter(PointerEventData eventData)` | Called when the pointer enters a UI element. |
| `OnPointerExit(PointerEventData eventData)` | Called when the pointer leaves a UI element. |
| `OnPointerDown(PointerEventData eventData)` | Called when a UI element is pressed down. |
| `OnPointerUp(PointerEventData eventData)` | Called when a UI element is released. |

🔹 **Example (Button Click Handling using `IPointerClickHandler`):**
```csharp
using UnityEngine;
using UnityEngine.EventSystems;

public class UIButtonClick : MonoBehaviour, IPointerClickHandler
{
    public void OnPointerClick(PointerEventData eventData)
    {
        Debug.Log("Button Clicked!");
    }
}
```

📌 **Alternative:** You can also assign functions in **Unity's Inspector** using the **Button** component.

---

## **4. Lifecycle Events (MonoBehaviour)**
These methods are automatically called at different stages of a GameObject’s lifetime.

| Event | Description |
|------|------------|
| `Awake()` | Called **before Start()**, used for initialization. |
| `Start()` | Called on the **first frame** the script runs. |
| `Update()` | Called **every frame**, used for logic like movement. |
| `FixedUpdate()` | Called **at fixed time intervals** (used for physics). |
| `LateUpdate()` | Called **after Update()**, useful for camera following. |
| `OnEnable()` | Called when the GameObject is enabled. |
| `OnDisable()` | Called when the GameObject is disabled. |
| `OnDestroy()` | Called before the object is **destroyed**. |

🔹 **Example Usage:**
```csharp
void Start()
{
    Debug.Log("Game Started!");
}

void Update()
{
    Debug.Log("Frame Updated!");
}
```

---

## **5. Animation Events**
These allow animations to **trigger functions** at specific points.

🔹 **How to Use:**
1. Open **Animation Window**.
2. Select an Animation Clip.
3. Click **Add Event** at a keyframe.
4. Assign a function in a script.

🔹 **Example:**
```csharp
public void PlaySound()
{
    Debug.Log("Playing sound effect!");
}
```
- The `PlaySound()` function will be called during the animation.

---

## **6. Scene and GameObject Events**
These handle **scene changes and object state changes**.

| Event | Description |
|------|------------|
| `OnApplicationQuit()` | Called when the game is **closing**. |
| `OnApplicationPause(bool pauseStatus)` | Called when the app is **paused/resumed**. |
| `OnSceneLoaded(Scene scene, LoadSceneMode mode)` | Called when a scene is **loaded**. |

🔹 **Example (Detect Scene Change):**
```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class SceneLoader : MonoBehaviour
{
    void OnEnable()
    {
        SceneManager.sceneLoaded += OnSceneLoaded;
    }

    void OnSceneLoaded(Scene scene, LoadSceneMode mode)
    {
        Debug.Log("Loaded Scene: " + scene.name);
    }
}
```

---

## **7. Input Handling (Keyboard & Touch)**
Unity provides **Input System** to detect keyboard, touch, or gamepad inputs.

| Event | Description |
|------|------------|
| `Input.GetKeyDown(KeyCode.X)` | Detects **key press** once. |
| `Input.GetKey(KeyCode.X)` | Detects **continuous key press**. |
| `Input.GetKeyUp(KeyCode.X)` | Detects when the key is **released**. |

🔹 **Example (Jump with Spacebar):**
```csharp
void Update()
{
    if (Input.GetKeyDown(KeyCode.Space))
    {
        Debug.Log("Jump!");
    }
}
```

---

### **📌 Summary**
| Category | Common Events |
|----------|--------------|
| **Collisions & Triggers** | `OnCollisionEnter()`, `OnTriggerEnter()` |
| **Mouse Events** | `OnMouseDown()`, `OnMouseEnter()` |
| **UI Events** | `OnPointerClick()`, `OnPointerEnter()` |
| **Lifecycle Events** | `Start()`, `Update()`, `FixedUpdate()` |
| **Scene & Object Events** | `OnApplicationQuit()`, `OnSceneLoaded()` |
| **Input Handling** | `Input.GetKeyDown()`, `Input.GetAxis()` |

---

These are the most commonly used events in Unity! 🚀🔥
