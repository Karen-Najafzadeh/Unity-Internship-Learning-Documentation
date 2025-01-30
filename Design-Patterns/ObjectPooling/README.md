
# Object Pooling in Unity 🚀  

**Object Pooling** is a design pattern used to improve performance by reusing objects instead of creating and destroying them repeatedly. This is especially useful in Unity for optimizing frequently instantiated objects like bullets, enemies, or particle effects.

---

## 📝 Why Use Object Pooling?  

- **Improves Performance**: Reduces memory allocation and garbage collection.  
- **Optimizes Gameplay**: Prevents frame drops during intense scenes.  
- **Reusable Objects**: Efficiently manages resources.

---

## 💡 How It Works  

1. **Pool Creation**: A pool of pre-instantiated objects is created at runtime.  
2. **Reuse Objects**: Objects are pulled from the pool when needed.  
3. **Return to Pool**: After use, objects are deactivated and returned to the pool for future reuse.

---

## ⚡ Benefits of Object Pooling  

- Eliminates **runtime spikes** caused by frequent instantiation and destruction.  
- Helps manage large numbers of game objects efficiently.  
- Keeps gameplay **smooth and responsive**.

---

Feel free to integrate object pooling into your Unity project and watch your performance improve!  

--- 

# how to create an object pool

in the example below, we are using a simple way of creating object pools for different types of objects using the built-in unity namespace of **UnityEngine.Pool** 

## explanation:
- first of all we are gonna use the needed namespaces:
~~~csharp
using UnityEngine;
using UnityEngine.Pool;
~~~

- second, we have to have a refrence to the gameobject's prefab that we want to instantiate:
~~~csharp
[SerializeField] private GameObject axePrefab;
// or as many types of objects you need like, other types of projectiles or enemies etc.
~~~

- Third, we need to initialize the pool for each of our object types:
there are a few parameters that we need to provide:

~~~csharp
axePool = new ObjectPool<Axe>(Create, OnGet, OnRelease, OnDestroy, true, 20, 40);
// or as many types of objects you need like, other types of projectiles or enemies etc.
~~~
###### true:
 This parameter is a boolean value that typically indicates whether the pool should expand automatically when it runs out of objects. If set to true, the pool will create new objects as needed. If set to false, the pool will not create new objects and will return null or throw an exception when it runs out of
###### 20:
 This parameter represents the initial size of the pool. It specifies the number of objects that will be created and added to the pool when it is first initialized.
###### 40:
 This parameter represents the maximum size of the pool. It specifies the maximum number of objects that the pool can hold. If the pool reaches this limit and the boolean parameter is set to false, no more objects will be created.

Other parameters will be described in the example

- Finally, we need to define some methods, to handle the senarios where an objec is created, destroyed, sent back to the pool etc.


## Example:

here is a compelete example of how to create an object pool for two different weapons, like throwing axes and arrows, with in-line explanations:
we use singletone pattern for defining the pool manager so it can be used where ever needed in the project. in order to do so, we are inheriting the pool manager class from a class called **SingleToneClass** that makes the provided class as a singletone class.

  

~~~csharp
using UnityEngine;
using UnityEngine.Pool;

public class PoolManager : SingleToneClass<PoolManager>  {

    /// <summary>
    /// Prefab for the Axe object.
    /// </summary>
    [SerializeField] private GameObject axePrefab;

    /// <summary>
    /// Prefab for the Arrow object.
    /// </summary>
    [SerializeField] private GameObject arrowPrefab;

    /// <summary>
    /// Object pool for Axe objects.
    /// </summary>
    public ObjectPool<Axe> axePool;

    /// <summary>
    /// Object pool for Arrow objects.
    /// </summary>
    public ObjectPool<Arrow> arrowPool;

    /// <summary>
    /// Initializes the object pools for Axe and Arrow objects.
    /// </summary>
    private void Start(){
        axePool = new ObjectPool<Axe>(CreateAxe, OnGetAxe, OnReleaseAxe, OnDestroyAxe, true, 20, 40);
        arrowPool = new ObjectPool<Arrow>(CreateArrow, OnGetArrow, OnReleaseArrow, OnDestroyArrow, true, 5, 10);
    }

    /// <summary>
    /// Creates a new Axe object.
    /// </summary>
    /// <returns>A new Axe object.</returns>
    private Axe CreateAxe() {
        Axe axe = Instantiate(axePrefab).GetComponent<Axe>();
        axe.gameObject.SetActive(false);
        return axe;
    }

    /// <summary>
    /// Creates a new Arrow object.
    /// </summary>
    /// <returns>A new Arrow object.</returns>
    private Arrow CreateArrow() {
        Arrow arrow = Instantiate(arrowPrefab).GetComponent<Arrow>();
        arrow.gameObject.SetActive(false);
        return arrow;
    }

    /// <summary>
    /// Called when an Axe object is taken from the pool.
    /// </summary>
    /// <param name="axe">The Axe object taken from the pool.</param>
    private void OnGetAxe(Axe axe) {
        axe.gameObject.SetActive(true);
    }

    /// <summary>
    /// Called when an Arrow object is taken from the pool.
    /// </summary>
    /// <param name="arrow">The Arrow object taken from the pool.</param>
    private void OnGetArrow(Arrow arrow) {
        arrow.gameObject.SetActive(true);
    }

    /// <summary>
    /// Called when an Axe object is returned to the pool.
    /// </summary>
    /// <param name="axe">The Axe object returned to the pool.</param>
    private void OnReleaseAxe(Axe axe) {
        axe.gameObject.SetActive(false);
    }

    /// <summary>
    /// Called when an Arrow object is returned to the pool.
    /// </summary>
    /// <param name="arrow">The Arrow object returned to the pool.</param>
    private void OnReleaseArrow(Arrow arrow) {
        arrow.gameObject.SetActive(false);
    }

    /// <summary>
    /// Called when an Axe object is destroyed.
    /// </summary>
    /// <param name="axe">The Axe object to be destroyed.</param>
    private void OnDestroyAxe(Axe axe) {
        Destroy(axe.gameObject);
    }

    /// <summary>
    /// Called when an Arrow object is destroyed.
    /// </summary>
    /// <param name="arrow">The Arrow object to be destroyed.</param>
    private void OnDestroyArrow(Arrow arrow) {
        Destroy(arrow.gameObject);
    }
}
~~~


# defenition of the SingletoneClass :
you can use this class to make any class follows the singletone pattern and be accessable globally in the project

~~~csharp
using UnityEngine;

public class SingleToneClass<T> : MonoBehaviour where T : MonoBehaviour
{
    /// <summary>
    /// The singleton instance of the class.
    /// </summary>
    public static T Instance { get; private set; }

    /// <summary>
    /// Ensures that only one instance of the class exists.
    /// </summary>
    protected virtual void Awake()
    {
        if (Instance != null && Instance != this)
        {
            Debug.LogWarning($"Duplicate instance of {typeof(T).Name} found. Destroying the new one.");
            Destroy(this.gameObject);
            return;
        }

        Instance = this as T;
        DontDestroyOnLoad(this.gameObject); // Optional: Keep it across scenes
    }
}

~~~


--- 

Let me know if you'd like to add more details or real-world examples. 😊
