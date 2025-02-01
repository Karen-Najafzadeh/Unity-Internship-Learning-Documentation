

### **🔧 What is Editor Scripting?**

Editor scripting allows you to write scripts that affect how the **Unity Editor behaves**, rather than how your game behaves at runtime. This can include things like:
- **Custom editors for your components**
- **Custom inspector panels**
- **Custom editor windows**
- **Scene view tools**
- **Menu items**

By extending the editor, you can save a lot of time on repetitive tasks, make your workflow more efficient, and create specialized tools that fit your project’s needs.

---

### **🛠 Getting Started with Editor Scripting**

#### **1️⃣ Setting up Editor Scripts**
Editor scripts in Unity must be placed inside a folder named **Editor** (this is a special folder that Unity recognizes). Anything inside this folder will not be included in your game build.

**Steps:**
1. Create a new folder named `Editor` (inside the `Assets` folder).
2. Inside the `Editor` folder, create a new script. 
3. Ensure your script uses the `UnityEditor` namespace.

Here’s a basic script example:

```csharp
using UnityEngine;
using UnityEditor;

public class MyCustomEditorScript : EditorWindow
{
    [MenuItem("Window/My Custom Editor")]
    public static void ShowWindow()
    {
        EditorWindow.GetWindow(typeof(MyCustomEditorScript));
    }

    void OnGUI()
    {
        GUILayout.Label("My Custom Editor Window", EditorStyles.boldLabel);
        if (GUILayout.Button("Press Me"))
        {
            Debug.Log("Button Pressed!");
        }
    }
}
```

- This script creates a custom window in the Unity Editor under the **Window** menu.
- The `OnGUI()` method is where you define what the window looks like.

---

### **2️⃣ Custom Inspectors**

Sometimes you may want to customize how a component is displayed in the Inspector. Editor scripting allows you to create **Custom Inspectors**.

**Steps:**
1. Create a script for your component (e.g., `PlayerStats`).
2. Create a custom editor script to customize how it displays in the Inspector.

#### Example: Custom Inspector

```csharp
using UnityEngine;
using UnityEditor;

[CustomEditor(typeof(PlayerStats))]
public class PlayerStatsEditor : Editor
{
    public override void OnInspectorGUI()
    {
        PlayerStats stats = (PlayerStats)target;

        GUILayout.Label("Player Health");
        stats.health = EditorGUILayout.IntSlider("Health", stats.health, 0, 100);

        GUILayout.Label("Player Speed");
        stats.speed = EditorGUILayout.FloatField("Speed", stats.speed);

        if (GUILayout.Button("Reset Stats"))
        {
            stats.ResetStats();
        }

        // Always call this to ensure the Inspector is updated.
        EditorUtility.SetDirty(stats);
    }
}
```

- In this example, we customize the **PlayerStats** component's inspector to allow easy editing of health and speed using sliders and text fields.
- The `EditorGUILayout` class provides the UI elements for creating sliders, text fields, buttons, etc.

---

### **3️⃣ Creating Custom Editor Windows**

Custom **editor windows** are great for creating tools like level editors, asset managers, or debug panels.

#### Example: Custom Editor Window

```csharp
using UnityEngine;
using UnityEditor;

public class LevelEditorWindow : EditorWindow
{
    private int levelDifficulty = 1;

    [MenuItem("Window/Level Editor")]
    public static void OpenWindow()
    {
        LevelEditorWindow window = GetWindow<LevelEditorWindow>();
        window.titleContent = new GUIContent("Level Editor");
        window.Show();
    }

    void OnGUI()
    {
        GUILayout.Label("Level Settings", EditorStyles.boldLabel);

        levelDifficulty = EditorGUILayout.IntSlider("Difficulty", levelDifficulty, 1, 10);

        if (GUILayout.Button("Generate Level"))
        {
            GenerateLevel(levelDifficulty);
        }
    }

    void GenerateLevel(int difficulty)
    {
        // Logic for generating a level based on difficulty
        Debug.Log($"Generated level with difficulty: {difficulty}");
    }
}
```

- In this case, we create a **Level Editor** window where users can select the level's difficulty and then press a button to "generate" a new level.
- Custom windows allow you to control exactly how your editor looks and behaves.

---

### **4️⃣ Menu Items**

Editor scripting allows you to add custom **menu items** to Unity’s top bar (File, Edit, etc.). This is useful for triggering custom actions without needing to create an editor window.

#### Example: Adding a Menu Item

```csharp
using UnityEditor;
using UnityEngine;

public class MyMenuItems
{
    [MenuItem("Tools/Clear Console")]
    public static void ClearConsole()
    {
        var logEntries = System.Type.GetType("UnityEditor.LogEntries, UnityEditor.dll");
        var clearMethod = logEntries.GetMethod("Clear", System.Reflection.BindingFlags.Static | System.Reflection.BindingFlags.Public);
        clearMethod.Invoke(null, null);
        Debug.Log("Console cleared!");
    }
}
```

- In this example, a new menu item "Clear Console" is added under the **Tools** menu. When clicked, it clears the console logs.

---


### Real-world Example:

#### 1. **Building asset bundles**

For Instance, let's create a script to build our asset bundles.

Create a script inside the Editor Folder named `CreateAssetBundle`

```csharp
using UnityEditor;
using UnityEngine;
using System.IO;

public class CreateAssetBundle {
    [MenuItem("Assets/Build AssetBundles")]
    static void BuildAllAssetBundles(){
        string assetBundleDirectory = "Assets/StreamingAssets";
        if (!Directory.Exists(Application.streamingAssetsPath)){
            Directory.CreateDirectory(assetBundleDirectory);
        }
        BuildPipeline.BuildAssetBundles(assetBundleDirectory, BuildAssetBundleOptions.None, EditorUserBuildSettings.activeBuildTarget);
        Debug.Log("Asset Bundles built successfully!");
        
    }
}

```

now if you head to `Assets` window, you'll be able to see an option named `Build AssetBundles`. if you click on it, if will check if `Assets/StreamingAssets` folder exists and biulds the asset bundles there. If not, it will create the directory and build the asset bundles.


#### 2. **JSON Exporter**

Let's say we have several platforms and enemies in our game environment and we want to export the position and scale of each platform and position of every enemy to JSON format, so that we can use it later in level design or something else.


First of all, create a script containing the properties you want to export. Let's call it `LevelData`:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[System.Serializable]
public class LevelData{
    public List<PlatformDataConfig> platforms;
    
    
    // or other properties like:
    public List<EnemyDataConfig> enemies;
    public Vector3DataConfig playerInitialPosition;
    .
    .
    .
}

// now define which properties you want to export
[System.Serializable]
public class PlatformDataConfig{
    public Vector3DataConfig position;
    public Vector3DataConfig scale;
}

[System.Serializable]
public class EnemyDataConfig
{
    public Vector3DataConfig position;
}

[System.Serializable]
public class Vector3DataConfig
{
    public float x, y, z;

    // Constructor to assign values
    public Vector3DataConfig(float x, float y, float z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    // Constructor to initialize from a Unity Vector3
    public Vector3DataConfig(Vector3 vector)
    {
        this.x = vector.x;
        this.y = vector.y;
        this.z = vector.z;
    }

    // Convert back to Unity Vector3
    public Vector3 ToVector3() => new Vector3(x, y, z);
}

```



Next, create a script, handling the logic for exporting the position and scale. Let's call it `JSONExporter`:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEditor;



public class JSONExporter : MonoBehaviour {

    [SerializeField] private GameObject platformdParent; // the parent which all the platforms are inside of it
    [SerializeField] private GameObject enemyParent; // same with enemies

    private List<GameObject> platformObjects = new List<GameObject>();
    private List<GameObject> enemyObjects = new List<GameObject>();

    public void CollectObjects(){

        //Ensure that the platformObjects are cleared before collecting new objects:
        ClearObjects();

        // collecting platforms
        foreach (Transform child in platformdParent.transform) {
            Debug.Log($"Ground: {child.name}");
            platformObjects.Add(child.gameObject);
        }

        //collecting enemies
        foreach (Transform child in enemyParent.transform) {
            enemyObjects.Add(child.gameObject);
        }

        ExportToJSON();
    }

    private void ClearObjects(){
        platformObjects.Clear();
        enemyObjects.Clear();
    }

    private void ExportToJSON(){
        LevelData levelData = new LevelData();
        levelData.platforms = new List<GroundDataConfig>();
        levelData.enemies = new List<EnemyDataConfig>();

        // adding platforms
        foreach(GameObject platform in platformObjects){
        GroundDataConfig platform = new PlatformDataConfig();
        platform.position = new Vector3DataConfig(ground.transform.position);
        platform.scale = new Vector3DataConfig(ground.transform.localScale);
        levelData.platforms.Add(platform);
        }

        //adding enemies
        foreach(GameObject enemy in enemyObjects){
        EnemyDataConfig enemyData = new EnemyDataConfig();
        enemyData.position = new Vector3DataConfig(enemy.transform.position);
        levelData.enemies.Add(enemyData);
        }

        //exporting to JSON
        string jsonString = JsonUtility.ToJson(levelData, true);
        Debug.Log("JSON saved!");

        // here you can get the JSON file in the console. Or Alternatively you can write it on a file.
        Debug.Log(jsonString);
    }
}
```

Finally, we want to be able to export the JSON file with one click, even when the game is not running. To do so, create a script in the `Editor` directory. Let's call that `JSONExporterEditor`:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEditor;

[CustomEditor(typeof(JSONExporter))]
public class JSONExporterEditor : Editor{
    public override void OnInspectorGUI() {
        DrawDefaultInspector();
        JSONExporter jsonExporter = (JSONExporter)target;
        if (GUILayout.Button("Translate the level as JSON")){
            jsonExporter.CollectObjects();
        }
    }
}
```

assing the script to a game object and from the inspector, you can see a button called `Translate the level as JSON` which will turn the data you want into JSON representation.

### **⚡ Best Practices in Editor Scripting**

1. **Optimize Performance**: Avoid running expensive operations during `OnGUI` or `Update` unless absolutely necessary. Keep the editor responsive by minimizing heavy operations in the editor loop.
2. **UI Layout**: Use `EditorGUILayout` and `GUILayout` for automatically managing layout, but customize it with `GUI` if you need more control.
3. **Prevent Code from Being Built**: Always ensure that your editor scripts are inside an **Editor folder** to avoid them being included in the final build.
4. **Use SerializedObject/SerializedProperty**: When dealing with custom inspectors, use `SerializedObject` and `SerializedProperty` to ensure proper undo functionality, real-time updating, and serialization.

---

### **🛠 Advanced Topics in Editor Scripting**

- **Asset Post-processing**: Automatically process or modify assets as they are imported (e.g., compress textures, generate prefabs).
- **Custom Property Drawers**: Create custom UI elements for your component properties (e.g., color pickers, sliders).
- **Drag-and-Drop in Editor**: Implement drag-and-drop functionality in custom windows or inspectors.
- **Editor Coroutines**: Run coroutines in the editor (useful for long-running tasks like building or processing data).

---

Editor scripting can significantly boost your productivity by automating tedious tasks, improving your workflows, and providing a better development experience. Whether it's creating custom tools or fine-tuning the way components are displayed in the Inspector, Unity's editor scripting capabilities are invaluable for any serious game developer.
