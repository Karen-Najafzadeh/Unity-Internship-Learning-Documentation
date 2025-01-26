# how to create an object pool

~~~csharp

// using namespaces
using UnityEngine;
using UnityEngine.Pool;


//define a singletone class responsible for managing the pools
public class PoolManager : SingleToneClass<PoolManager>  {

    // assinging the prefab of the object
    [SerializeField] private GameObject arrowPrefab;

    // defining the pool of type Arrow
    public ObjectPool<Arrow> arrowPool;

    // creating the pool
    private void Start(){
        arrowPool = new ObjectPool<Arrow>(CreateArrow, OnGetArrow, OnReleaseArrow, OnDestroyArrow, true, 5, 10);
    }

    // CreateArrow: A function that creates a new Arrow object. This function is called when the pool needs to instantiate a new Arrow because the pool is empty.
    private Arrow CreateArrow() {
        Arrow arrow = Instantiate(arrowPrefab).GetComponent<Arrow>();
        arrow.gameObject.SetActive(false);
        return arrow;
    }

    // OnGetArrow: A function that is called when an Arrow object is retrieved from the pool. This can be used to reset or initialize the object before it is used.
    private void OnGetArrow(Arrow arrow) {
        arrow.gameObject.SetActive(true);
    }


    // OnReleaseArrow: A function that is called when an Arrow object is returned to the pool. This can be used to clean up or deactivate the object.
    private void OnReleaseArrow(Arrow arrow) {
        arrow.gameObject.SetActive(false);
    }


    // OnDestroyArrow: A function that is called when an Arrow object is destroyed. This can be used to perform any necessary cleanup.
    private void OnDestroyArrow(Arrow arrow) {
        Destroy(arrow.gameObject);
    }
}
~~~


# defenition of the SingletoneClass :

~~~csharp
using UnityEngine;

public class SingleToneClass<T> : MonoBehaviour where T : MonoBehaviour
{
    public static T Instance { get; private set; }

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
