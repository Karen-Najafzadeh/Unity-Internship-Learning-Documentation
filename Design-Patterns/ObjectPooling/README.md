
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


### **Combining Factory Method and Object Pooling Patterns in Unity**  

By combining **Factory Method** and **Object Pooling**, we can create enemies efficiently while managing memory usage by reusing instances instead of creating and destroying them repeatedly.

---

## **Step-by-Step Guide**
### **1. Define the Base Class (`Enemy`)**
We create an abstract base class for enemies, defining common behavior.

```csharp
using UnityEngine;

public abstract class Enemy : MonoBehaviour
{
    public abstract void Attack();

    public virtual void OnSpawn()
    {
        gameObject.SetActive(true);
    }

    public virtual void OnDespawn()
    {
        gameObject.SetActive(false);
    }
}
```
✔ **Why?**  
- Provides a common interface for all enemies.  
- `OnSpawn()` and `OnDespawn()` will be used for pooling logic.

---

### **2. Implement Specific Enemy Types**
Each enemy type inherits from `Enemy` and defines its own behavior.

```csharp
public class Zombie : Enemy
{
    public override void Attack()
    {
        Debug.Log("Zombie attacks!");
    }
}

public class Skeleton : Enemy
{
    public override void Attack()
    {
        Debug.Log("Skeleton attacks!");
    }
}
```
✔ **Why?**  
- Follows **polymorphism**: Each enemy has its own unique behavior.  
- They can be extended without modifying existing code (Open-Closed Principle).

---

### **3. Create the `EnemyFactory` to Generate Enemies**
Instead of creating enemies manually, we use a **factory method** to centralize enemy instantiation.

```csharp
using UnityEngine;

public class EnemyFactory : MonoBehaviour
{
    [SerializeField] private GameObject zombiePrefab;
    [SerializeField] private GameObject skeletonPrefab;

    public Enemy CreateEnemy(string enemyType)
    {
        switch (enemyType)
        {
            case "Zombie":
                return Instantiate(zombiePrefab).GetComponent<Zombie>();
            case "Skeleton":
                return Instantiate(skeletonPrefab).GetComponent<Skeleton>();
            default:
                throw new System.Exception("Unknown enemy type");
        }
    }
}
```
✔ **Why?**  
- Centralized logic for creating different enemy types.  
- Can be extended by adding more enemy types without changing client code.

---

### **4. Implement the `EnemyPoolManager` for Object Pooling**
Instead of constantly creating and destroying enemies, we reuse them with Unity’s `ObjectPool<T>`.

```csharp
using UnityEngine;
using UnityEngine.Pool;

public class EnemyPoolManager : MonoBehaviour
{
    [SerializeField] private EnemyFactory enemyFactory;

    private ObjectPool<Enemy> zombiePool;
    private ObjectPool<Enemy> skeletonPool;

    private void Start()
    {
        zombiePool = new ObjectPool<Enemy>(
            () => enemyFactory.CreateEnemy("Zombie"), 
            enemy => enemy.OnSpawn(),
            enemy => enemy.OnDespawn(),
            enemy => Destroy(enemy.gameObject),
            true, 5, 10);

        skeletonPool = new ObjectPool<Enemy>(
            () => enemyFactory.CreateEnemy("Skeleton"), 
            enemy => enemy.OnSpawn(),
            enemy => enemy.OnDespawn(),
            enemy => Destroy(enemy.gameObject),
            true, 5, 10);
    }

    public Enemy GetEnemy(string enemyType)
    {
        return enemyType switch
        {
            "Zombie" => zombiePool.Get(),
            "Skeleton" => skeletonPool.Get(),
            _ => throw new System.Exception("Unknown enemy type")
        };
    }

    public void ReleaseEnemy(Enemy enemy)
    {
        if (enemy is Zombie)
            zombiePool.Release(enemy);
        else if (enemy is Skeleton)
            skeletonPool.Release(enemy);
    }
}
```
✔ **Why?**  
- Uses **Unity’s ObjectPool<T>** to manage enemy instances.  
- `OnSpawn()` activates the enemy when retrieved from the pool.  
- `OnDespawn()` deactivates the enemy instead of destroying it.  
- Reduces garbage collection, improving performance.

---

### **5. Using the System in a Game Manager**
Now, let's integrate the pooling and factory system in a game manager.

```csharp
using UnityEngine;

public class GameManager : MonoBehaviour
{
    [SerializeField] private EnemyPoolManager enemyPoolManager;

    private void Start()
    {
        // Spawn enemies from the pool
        Enemy zombie = enemyPoolManager.GetEnemy("Zombie");
        zombie.Attack();

        Enemy skeleton = enemyPoolManager.GetEnemy("Skeleton");
        skeleton.Attack();

        // After some time, release them back to the pool
        Invoke(nameof(ReleaseEnemies), 3f);
    }

    private void ReleaseEnemies()
    {
        // Assume we store references to enemies when spawning them
        enemyPoolManager.ReleaseEnemy(FindObjectOfType<Zombie>());
        enemyPoolManager.ReleaseEnemy(FindObjectOfType<Skeleton>());
    }
}
```
✔ **Why?**  
- **Retrieves enemies from the pool**, instead of instantiating new ones.  
- **Releases enemies back to the pool**, improving performance.

---

## Custom object pooling 

Creating a custom object pooling system in Unity ensures efficient reuse of game objects, improving performance by reducing instantiation and garbage collection overhead. Here's a step-by-step guide to writing a custom, **efficient** object pooling system.

---

## **Step 1: Create a Generic Object Pool Class**  
We’ll create a reusable and efficient object pool that works with any object type.

### **ObjectPool.cs**
```csharp
using System.Collections.Generic;
using UnityEngine;

public class ObjectPool<T> where T : Component
{
    private readonly T prefab;                         // Prefab to instantiate
    private readonly Queue<T> pool = new Queue<T>();    // Queue to store pooled objects
    private readonly Transform parent;                  // Parent transform for organized hierarchy

    public ObjectPool(T prefab, int initialSize, Transform parent = null)
    {
        this.prefab = prefab;
        this.parent = parent;
        for (int i = 0; i < initialSize; i++)
        {
            T obj = CreateNewObject();
            pool.Enqueue(obj);
        }
    }

    private T CreateNewObject()
    {
        T newObj = Object.Instantiate(prefab, parent);
        newObj.gameObject.SetActive(false);
        return newObj;
    }

    public T Get()
    {
        if (pool.Count > 0)
        {
            T obj = pool.Dequeue();
            obj.gameObject.SetActive(true);
            return obj;
        }
        return CreateNewObject();
    }

    public void Release(T obj)
    {
        obj.gameObject.SetActive(false);
        pool.Enqueue(obj);
    }
}
```

---

## **Step 2: Create a Pool Manager**  
You can create a **PoolManager** to manage multiple object pools for different object types.

### **PoolManager.cs**
```csharp
using UnityEngine;

public class PoolManager : MonoBehaviour
{
    public static PoolManager Instance { get; private set; }

    [SerializeField] private GameObject bulletPrefab;
    [SerializeField] private GameObject enemyPrefab;

    public ObjectPool<Bullet> bulletPool;
    public ObjectPool<Enemy> enemyPool;

    private void Awake()
    {
        if (Instance == null) Instance = this;
        else Destroy(gameObject);

        bulletPool = new ObjectPool<Bullet>(bulletPrefab.GetComponent<Bullet>(), 20, transform);
        enemyPool = new ObjectPool<Enemy>(enemyPrefab.GetComponent<Enemy>(), 10, transform);
    }

    public Bullet GetBullet() => bulletPool.Get();
    public void ReleaseBullet(Bullet bullet) => bulletPool.Release(bullet);

    public Enemy GetEnemy() => enemyPool.Get();
    public void ReleaseEnemy(Enemy enemy) => enemyPool.Release(enemy);
}
```

---

## **Step 3: Use the Pool in Gameplay**  
Here’s how to use the pool in a player’s shooting mechanic.

### **PlayerShooting.cs**
```csharp
using UnityEngine;

public class PlayerShooting : MonoBehaviour
{
    public Transform shootPoint;

    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            Shoot();
        }
    }

    private void Shoot()
    {
        Bullet bullet = PoolManager.Instance.GetBullet();
        bullet.transform.position = shootPoint.position;
        bullet.transform.rotation = shootPoint.rotation;
        bullet.Init(ReleaseBulletAfterTime);
    }

    private void ReleaseBulletAfterTime(Bullet bullet)
    {
        PoolManager.Instance.ReleaseBullet(bullet);
    }
}
```

---

## **Step 4: Bullet Class Example**
The bullet will return itself to the pool after a certain time.

### **Bullet.cs**
```csharp
using UnityEngine;
using System;

public class Bullet : MonoBehaviour
{
    private Action<Bullet> releaseCallback;

    public void Init(Action<Bullet> releaseCallback)
    {
        this.releaseCallback = releaseCallback;
        Invoke(nameof(ReturnToPool), 2f);  // Return to pool after 2 seconds
    }

    private void ReturnToPool()
    {
        CancelInvoke();
        releaseCallback?.Invoke(this);
    }

    private void OnCollisionEnter(Collision collision)
    {
        ReturnToPool();  // Return to pool on collision
    }
}
```

---

## **Advantages of This Approach**
1. **Generic & Reusable**: Works with any object type (bullets, enemies, particles, etc.).
2. **Efficient**: Avoids frequent instantiation and destruction.
3. **Flexible**: Easy to expand for new object types.
4. **Organized**: Uses parent transforms to keep the hierarchy clean.

---

## **Optional Optimizations**
1. **Warm-Up the Pool**: Instantiate a set number of objects at the start to reduce runtime spikes.  
2. **Expand with Capacity Limits**: Prevent excessive growth by setting a maximum pool size.  
3. **Dynamic Pool Size**: Increase pool size if needed, but shrink it back when objects aren’t used for a while.  
4. **Object Reuse Detection**: Add checks to prevent double-release of an object.  

---

Let's add a maximum pool size example or an expandable pool implementation

---

## Expandable Pool

Let’s modify our `ObjectPool` class to implement **maximum pool size** and **dynamic expansion**. The goal is to expand the pool when necessary but stop adding new objects once it reaches a defined maximum size. We’ll also shrink the pool back when objects aren’t used for a while.

---

### **Updated ObjectPool with Maximum Size and Auto-Shrinking**

```csharp
using System.Collections.Generic;
using UnityEngine;

public class ObjectPool<T> where T : Component
{
    private readonly T prefab;                     // Prefab to instantiate
    private readonly Queue<T> pool = new Queue<T>(); // Queue to store pooled objects
    private readonly Transform parent;
    
    private int currentSize = 0;                     // Current size of the pool
    private readonly int maxSize;                    // Maximum size the pool can grow to
    private float lastShrinkTime = 0f;               // Last time the pool shrank
    private readonly float shrinkInterval = 10f;     // Time interval to check for shrinking (in seconds)

    public ObjectPool(T prefab, int initialSize, int maxSize, Transform parent = null)
    {
        this.prefab = prefab;
        this.maxSize = maxSize;
        this.parent = parent;

        for (int i = 0; i < initialSize; i++)
        {
            T obj = CreateNewObject();
            pool.Enqueue(obj);
        }
    }

    private T CreateNewObject()
    {
        if (currentSize >= maxSize)
        {
            Debug.LogWarning("Pool has reached its maximum size!");
            return null;
        }

        T newObj = Object.Instantiate(prefab, parent);
        newObj.gameObject.SetActive(false);
        currentSize++;
        return newObj;
    }

    public T Get()
    {
        CheckAndShrinkPool();

        if (pool.Count > 0)
        {
            T obj = pool.Dequeue();
            obj.gameObject.SetActive(true);
            return obj;
        }

        // If no available objects, create a new one if under max size
        return CreateNewObject();
    }

    public void Release(T obj)
    {
        obj.gameObject.SetActive(false);
        pool.Enqueue(obj);
    }

    private void CheckAndShrinkPool()
    {
        if (Time.time - lastShrinkTime < shrinkInterval)
            return;

        if (pool.Count > maxSize / 2)
        {
            int shrinkAmount = pool.Count / 4;  // Reduce by 25% of current pool size
            for (int i = 0; i < shrinkAmount; i++)
            {
                T obj = pool.Dequeue();
                Object.Destroy(obj.gameObject);
                currentSize--;
            }

            Debug.Log($"Pool shrank by {shrinkAmount} objects.");
        }

        lastShrinkTime = Time.time;
    }
}
```

---

### **What’s New?**
1. **Maximum Size Limit**  
   - Ensures the pool doesn’t grow beyond a set limit.  
   - Logs a warning if the pool tries to expand beyond its maximum size.  

2. **Auto-Shrinking**  
   - Every `shrinkInterval` seconds, it checks if the pool size exceeds half of its maximum size.  
   - If so, it removes and destroys a portion (25%) of the excess objects.  

3. **Dynamic Growth**  
   - Expands dynamically if more objects are needed, but only up to the maximum size.  

---

### **Usage Example**

#### **PoolManager.cs**
```csharp
private void Awake()
{
    bulletPool = new ObjectPool<Bullet>(bulletPrefab.GetComponent<Bullet>(), 20, 100, transform);
    enemyPool = new ObjectPool<Enemy>(enemyPrefab.GetComponent<Enemy>(), 10, 50, transform);
}
```

Here, the bullet pool starts with **20 bullets** and can expand up to a **maximum of 100 bullets**. If the pool isn’t used much, it will **shrink back** periodically.

---

### **Testing the System**
1. **Simulate High Usage**: Continuously request new bullets beyond the initial pool size.  
   - Notice how the pool expands but stops once it hits the maximum size.  

2. **Simulate Low Usage**: Let the pool rest for a while without requesting objects.  
   - After `shrinkInterval` seconds, it shrinks the pool back.  

---

### **Optional Enhancements**
1. **Object Validation**: Add a method to check if objects are in a valid state before reusing them.  
2. **Debugging Tools**: Track active vs pooled objects for monitoring pool usage.  



## **Final Summary**
1. **Factory Method Pattern**:  
   - `EnemyFactory` is responsible for creating enemy objects dynamically.  
   - Allows easy addition of new enemy types without modifying existing logic.  

2. **Object Pooling Pattern**:  
   - `EnemyPoolManager` stores and reuses enemies instead of constantly creating/destroying them.  
   - `OnSpawn()` and `OnDespawn()` manage activation/deactivation instead of destruction.  

### **Advantages of This Combination**
✅ **Performance Optimization** – Avoids frequent instantiation and destruction of enemies.  
✅ **Scalability** – Easily extendable to add new enemy types.  
✅ **Memory Efficiency** – Reduces garbage collection and improves FPS stability.  
✅ **Encapsulation & Maintainability** – Centralizes creation and pooling logic, keeping code modular.

This pattern combination is widely used in **games that spawn multiple objects**, such as **wave-based survival games, bullet hell shooters, and RTS games**. 🚀
