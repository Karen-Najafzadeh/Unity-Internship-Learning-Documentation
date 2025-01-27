# Singleton Pattern

### Definition and Use Case:
The Singleton Pattern ensures a class has only one instance and provides a global point of access to it. It's often used to manage shared resources like configuration settings, logging systems, database connections, or game managers in Unity, ensuring controlled access and avoiding the overhead of creating multiple instances.

### Example:
By following this pattern, you'll ensure that the class uses the singleton pattern.

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

Now from anywhere in any script, we can have access to the GameManager like this:

```csharp
GameManager.Instance.AnyPublicMethodDefinedInGameManager();
```

### Making it Easier:
We can also create another class that uses the singleton pattern and inherit the classes we want from it. In this case, we can use the benefits of the singleton pattern for each of those classes by only inheriting from the following class:

~~~csharp
using UnityEngine;

public class SingletonClass<T> : MonoBehaviour where T : MonoBehaviour
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

Now we can use it to make each class a singleton class:

~~~csharp
public class GameManager : SingletonClass<GameManager> {
    // No actions needed, it's already using the singleton pattern
}
~~~

### Real-World Example:
Consider a logging system where you want to ensure that all parts of your application use the same logger instance.

~~~csharp
public class Logger : SingletonClass<Logger>
{
    public void Log(string message)
    {
        Debug.Log(message);
    }
}
~~~

Now you can use the Logger class from anywhere in your application:

```csharp
Logger.Instance.Log("This is a log message.");
```

### Additional Examples:

#### Configuration Manager:
A configuration manager that loads settings from a file and provides access to them.

~~~csharp
public class ConfigurationManager : SingletonClass<ConfigurationManager>
{
    public string ConfigValue { get; private set; }

    private void Start()
    {
        // Load configuration settings
        ConfigValue = "SomeConfigValue";
    }

    public string GetConfigValue()
    {
        return ConfigValue;
    }
}
~~~

Usage:

```csharp
string configValue = ConfigurationManager.Instance.GetConfigValue();
```

#### Audio Manager:
An audio manager to control background music and sound effects.

~~~csharp
public class AudioManager : SingletonClass<AudioManager>
{
    public AudioSource BackgroundMusic;

    public void PlayBackgroundMusic()
    {
        if (!BackgroundMusic.isPlaying)
        {
            BackgroundMusic.Play();
        }
    }

    public void StopBackgroundMusic()
    {
        if (BackgroundMusic.isPlaying)
        {
            BackgroundMusic.Stop();
        }
    }
}
~~~

Usage:

```csharp
AudioManager.Instance.PlayBackgroundMusic();
```
