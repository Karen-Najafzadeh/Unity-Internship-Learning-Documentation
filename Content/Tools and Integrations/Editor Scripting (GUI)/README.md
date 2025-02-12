

### **🔧 What is Editor Scripting?**

Editor scripting allows you to write scripts that affect how the **Unity Editor behaves**, rather than how your game behaves at runtime. This can include things like:
- **Custom editors for your components**
- **Custom inspector panels**
- **Custom editor windows**
- **Scene view tools**
- **Menu items**

By extending the editor, you can save a lot of time on repetitive tasks, make your workflow more efficient, and create specialized tools that fit your project’s needs.

---

# **GUILayout** and **EditorGUILayout**

When working with Unity’s Editor scripting in C#, you’ll often create custom user interfaces (UIs) for your tools and inspectors. Although you might see references to “GUILayout” when writing editor code, it’s important to clarify the distinction between two related (but different) classes:

- **GUILayout** (found in the UnityEngine namespace)  
- **EditorGUILayout** (found in the UnityEditor namespace)


- **GUILayout** for general-purpose, immediate mode GUI layout (e.g. grouping elements, defining spaces, and handling buttons or labels).  
- **EditorGUILayout** for editor-specific controls that automatically handle layout and serialization (for example, object fields, property fields, and other controls that integrate with Unity’s Inspector).

Below is an in‐depth look at what you need to know about these systems.

---

## 1. Immediate Mode GUI and GUILayout

Unity’s immediate mode GUI system works by redrawing its controls every frame in an `OnGUI()` method. The **GUILayout** class provides a set of static methods that let you build a UI without manually specifying pixel positions. Instead, you use layout groups that automatically arrange controls vertically, horizontally, or inside scroll views.

### Key Methods in GUILayout

Some of the most commonly used methods include:

- **Layout Groups:**  
  - `GUILayout.BeginHorizontal()` / `GUILayout.EndHorizontal()`  
  - `GUILayout.BeginVertical()` / `GUILayout.EndVertical()`
  - `GUILayout.BeginArea(Rect)` / `GUILayout.EndArea()`

- **Control Methods:**  
  - `GUILayout.Button(string)` – creates a clickable button.  
  - `GUILayout.Label(string)` – displays a text label.  
  - `GUILayout.TextField(string)` – shows an editable text field.  
  - `GUILayout.Toggle(bool, string)` – displays an on/off toggle.

- **Layout Options:**  
  These allow you to specify width, height, expansion properties, etc. For example:  
  - `GUILayout.Width(float)`  
  - `GUILayout.Height(float)`  
  - `GUILayout.ExpandWidth(bool)` and `GUILayout.ExpandHeight(bool)`

These methods are all static and are called inside the `OnGUI()` function to build the UI dynamically. This style is very flexible and can adapt to different window sizes and screen resolutions without hard-coding positions.

*(See Unity’s GUILayout documentation for more details citeturn0search0.)*

---

## 2. EditorGUILayout: The Editor-Specific Helper

When creating custom editors, Unity provides the **EditorGUILayout** class (in the UnityEditor namespace) as an extension of the basic layout system. While it uses many of the same concepts as GUILayout (automatic layout, grouping, etc.), it also offers controls tailored for editor use.

### Features of EditorGUILayout

- **Specialized Controls:**  
  Methods like `EditorGUILayout.ObjectField()`, `EditorGUILayout.PropertyField()`, and `EditorGUILayout.EnumPopup()` provide controls that work seamlessly with Unity’s serialization and undo systems. These controls simplify making custom inspectors for your scripts.

- **Automatic Integration:**  
  EditorGUILayout not only arranges the UI elements automatically but also integrates with the SerializedObject/SerializedProperty system. This means that you can easily create editors that support multi-object editing, Undo/Redo operations, and live updates in the Inspector.

- **Ease of Use in Custom Editors:**  
  Because editor windows and custom inspectors use these methods, you can rapidly prototype tools. For example, a custom inspector might include a vertical group for standard fields and then a horizontal group for custom buttons:

  ```csharp
  using UnityEditor;
  using UnityEngine;

  [CustomEditor(typeof(MyComponent))]
  public class MyComponentEditor : Editor {
      public override void OnInspectorGUI() {
          // Draw default properties using serialized properties.
          SerializedProperty myProp = serializedObject.FindProperty("myValue");
          EditorGUILayout.PropertyField(myProp);

          // Add a button using GUILayout.
          if (GUILayout.Button("Do Something")) {
              Debug.Log("Button pressed!");
          }

          // Use an object field with EditorGUILayout.
          SerializedProperty objProp = serializedObject.FindProperty("someObject");
          objProp.objectReferenceValue = EditorGUILayout.ObjectField("Some Object",
              objProp.objectReferenceValue, typeof(GameObject), true);

          // Apply changes to the serialized object.
          serializedObject.ApplyModifiedProperties();
      }
  }
  ```

  In the above snippet, you see both **EditorGUILayout** (for fields that need editor integration) and **GUILayout** (for a simple button) working together.  
  *(Refer to Unity’s EditorGUILayout documentation citeturn0search1 for a full list of available methods.)*

---

## 3. How and When to Use These Systems

### Use Cases

- **Runtime UI:**  
  While GUILayout is part of the immediate mode GUI system and can be used at runtime (though Unity’s newer UI system is generally preferred), its primary strength lies in rapid prototyping or debugging overlays.

- **Editor Extensions:**  
  When building custom editor windows, property drawers, or inspectors, you’ll almost always use **EditorGUILayout**. It’s specifically designed to interface with Unity’s editor backend, handling serialized properties and providing built-in support for Undo/Redo operations.

### Best Practices

- **Grouping Controls:**  
  Use Begin/EndHorizontal or Begin/EndVertical to logically group controls. This not only organizes your code but also makes your UI more understandable.

- **Avoid Heavy Processing in OnGUI():**  
  Since OnGUI is called multiple times per frame, try to minimize heavy computations within it.

- **Separation of Concerns:**  
  For editor scripting, consider separating layout code (using GUILayout/EditorGUILayout) from your business logic. This makes it easier to maintain and update your custom tools.

- **Combine with Serialized Properties:**  
  When editing fields of a MonoBehaviour or ScriptableObject, use the SerializedObject/SerializedProperty pattern along with EditorGUILayout. This ensures that changes are recorded properly and can be undone.  
  *(A detailed explanation of EditorGUILayout’s benefits in custom editors can be found in many online guides, such as the article on Sharp Coder Blog citeturn0search2 and Unity Editor Scripting Series on Medium citeturn0search9.)*

---

## 4. Summary

- **GUILayout (UnityEngine):**  
  Provides a set of static methods for building immediate mode GUIs with automatic layout. It’s versatile and can be used both at runtime and in the editor, though its design is best suited for quick-and-dirty UIs or editor tools.

- **EditorGUILayout (UnityEditor):**  
  Extends the basic layout system with controls that are editor-specific. It simplifies creating custom inspectors and editor windows by integrating with Unity’s serialization and undo systems.

- **Common Patterns:**  
  Custom editors often mix GUILayout for simple layout tasks (like placing buttons or labels) with EditorGUILayout for more complex, data-bound fields. This combination allows you to build powerful, flexible UIs inside the Unity Editor.

By understanding and using these classes effectively, you can create custom editor tools that are both robust and easy to use, ultimately improving your workflow within Unity.

---

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

---

For more details, you may consult the official Unity documentation and various tutorials available online:  
- Unity’s GUILayout Scripting API citeturn0search0  
- Unity’s EditorGUILayout Scripting API citeturn0search1

This overview should give you a comprehensive picture of how GUILayout and its editor-specific counterpart work in Unity C# editor scripting.

