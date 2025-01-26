# Singletone Pattern

### defenition and usecase:
The Singleton Pattern ensures a class has only one instance and provides a global point of access to it. It's often used to manage shared resources like configuration settings, logging systems, database connections, or game managers in Unity, ensuring controlled access and avoiding the overhead of creating multiple instances.


# Example:
with following this pattern, you'll ensure that the class uses the singletone pattern

~~~csharp
using UnityEngine;

public class GameManager : MonoBehaviour
{
    // Static field for the instance
    public static GameManager Instance;

    // Awake is called when the script instance is being loaded
    private void Awake()
    {
        // Check if there's already an instance, if not, set this one
        if (Instance == null)
        {
            Instance = this;
            DontDestroyOnLoad(gameObject);  // Keep the instance across scenes
        }
        else
        {
            Destroy(gameObject);  // Destroy duplicates
        }
    }
}
~~~

# make it easy:
we can also create another class that uses the singletone pattern and inherit the classes we want from it. in this case, we can use the benefits of singletone pattern for each of those classes with only inheriting from the folloing class:

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
now we can use it to make each class, a singletone class:

~~~csharp
public class GameManager : SingleToneClass<GameManager> {
    // tadaaaaa, no actions needed, it's already using singletone pattern
}
~~~
