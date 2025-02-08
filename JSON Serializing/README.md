

## 1. **JSON Basics**  
**JSON (JavaScript Object Notation)** is a lightweight data format, easy to read and write. Unity’s `JsonUtility` class provides built-in support for serializing and deserializing objects to/from JSON.

### Example JSON:
```json
{
  "playerName": "Brunella",
  "level": 5,
  "score": 1200
}
```

---

## 2. **Using `JsonUtility` in Unity**  
`JsonUtility` is fast and works well for most Unity objects, but it supports only **simple data types** (int, float, string, arrays, and serializable classes).

### Example: Serializing and Deserializing a Class

```csharp
using UnityEngine;

[System.Serializable]
public class PlayerData
{
    public string playerName;
    public int level;
    public int score;
}

public class JsonExample : MonoBehaviour
{
    void Start()
    {
        // Create a new player data object
        PlayerData playerData = new PlayerData
        {
            playerName = "Brunella",
            level = 5,
            score = 1200
        };

        // Serialize the player data to JSON
        string json = JsonUtility.ToJson(playerData);
        Debug.Log("Serialized JSON: " + json);

        // Deserialize the JSON back to an object
        PlayerData deserializedData = JsonUtility.FromJson<PlayerData>(json);
        Debug.Log($"Deserialized Data: {deserializedData.playerName}, Level: {deserializedData.level}, Score: {deserializedData.score}");
    }
}
```

### Output:
```
Serialized JSON: {"playerName":"Brunella","level":5,"score":1200}
Deserialized Data: Brunella, Level: 5, Score: 1200
```

---

## 3. **Working with Arrays and Lists**

`JsonUtility` supports arrays but doesn’t directly support `List<T>`. You can work around this by using a wrapper class.

### Example with a List:
```csharp
using System.Collections.Generic;
using UnityEngine;

[System.Serializable]
public class PlayerDataList
{
    public List<PlayerData> players;
}

public class JsonArrayExample : MonoBehaviour
{
    void Start()
    {
        PlayerDataList playerDataList = new PlayerDataList
        {
            players = new List<PlayerData>
            {
                new PlayerData { playerName = "Brunella", level = 5, score = 1200 },
                new PlayerData { playerName = "Argento", level = 3, score = 800 }
            }
        };

        string json = JsonUtility.ToJson(playerDataList);
        Debug.Log("Serialized JSON Array: " + json);

        PlayerDataList deserializedList = JsonUtility.FromJson<PlayerDataList>(json);
        Debug.Log("First Player: " + deserializedList.players[0].playerName);
    }
}
```

### Output:
```
Serialized JSON Array: {"players":[{"playerName":"Brunella","level":5,"score":1200},{"playerName":"Argento","level":3,"score":800}]}
First Player: Brunella
```

---

## 4. **Custom Serialization**

When `JsonUtility` isn’t enough (e.g., when dealing with dictionaries or nested complex data structures), you can use **`Newtonsoft.Json`** (from `Json.NET`).  

### Example with `Newtonsoft.Json` (Supports Dictionaries and Complex Types):
```csharp
using UnityEngine;
using Newtonsoft.Json;
using System.Collections.Generic;

public class NewtonsoftJsonExample : MonoBehaviour
{
    void Start()
    {
        Dictionary<string, int> playerScores = new Dictionary<string, int>
        {
            { "Brunella", 1200 },
            { "Argento", 800 }
        };

        string json = JsonConvert.SerializeObject(playerScores, Formatting.Indented);
        Debug.Log("Serialized Dictionary: " + json);

        Dictionary<string, int> deserializedScores = JsonConvert.DeserializeObject<Dictionary<string, int>>(json);
        Debug.Log("Brunella's Score: " + deserializedScores["Brunella"]);
    }
}
```

### Output:
```
Serialized Dictionary:
{
  "Brunella": 1200,
  "Argento": 800
}
Brunella's Score: 1200
```

---

## 5. **Custom Serialization with BinaryFormatter (Alternative)**  
For more secure or binary-based storage, `BinaryFormatter` might be useful, but keep in mind it’s not human-readable.

### Example:
```csharp
using System.IO;
using System.Runtime.Serialization.Formatters.Binary;
using UnityEngine;

[System.Serializable]
public class BinaryPlayerData
{
    public string playerName;
    public int level;
    public int score;
}

public class BinarySerializationExample : MonoBehaviour
{
    void Start()
    {
        BinaryPlayerData data = new BinaryPlayerData { playerName = "Brunella", level = 5, score = 1200 };

        // Serialize to binary file
        FileStream file = File.Create(Application.persistentDataPath + "/playerData.dat");
        BinaryFormatter bf = new BinaryFormatter();
        bf.Serialize(file, data);
        file.Close();
        Debug.Log("Data saved to binary file.");

        // Deserialize from binary file
        if (File.Exists(Application.persistentDataPath + "/playerData.dat"))
        {
            FileStream loadFile = File.Open(Application.persistentDataPath + "/playerData.dat", FileMode.Open);
            BinaryPlayerData loadedData = (BinaryPlayerData)bf.Deserialize(loadFile);
            loadFile.Close();
            Debug.Log($"Loaded Data: {loadedData.playerName}, Level: {loadedData.level}, Score: {loadedData.score}");
        }
    }
}
```

---

## When to Use Each Serialization Method:  

- **JsonUtility**: Fast, built-in, and lightweight. Great for Unity objects and simple data structures.  
- **Newtonsoft.Json**: Use when you need more advanced JSON features like dictionaries or nested objects.  
- **BinaryFormatter**: Ideal for binary files, but avoid if you want human-readable data. Useful for performance-critical applications.  

---

## More Examples


Below are several detailed examples that demonstrate how to serialize and deserialize JSON in Unity using both Unity’s built-in **JsonUtility** and the popular **Newtonsoft.Json** (Json.NET) library. These examples cover:

1. **A single serializable class**  
2. **A list of serializable classes (using a wrapper)**  
3. **A dictionary (using Newtonsoft.Json)**

---

## 1. Single Serializable Class

Unity’s built-in `JsonUtility` works well for simple classes. Make sure the class is marked with `[System.Serializable]`.

### Example: Character Data
```csharp
using UnityEngine;

[System.Serializable]
public class Character
{
    public string name;
    public int level;
    public float health;
}

public class SingleObjectJsonExample : MonoBehaviour
{
    void Start()
    {
        // Create an instance of Character
        Character hero = new Character
        {
            name = "Archer",
            level = 5,
            health = 100.0f
        };

        // Serialize the hero to JSON
        string heroJson = JsonUtility.ToJson(hero);
        Debug.Log("Serialized Character: " + heroJson);

        // Deserialize the JSON back to a Character object
        Character deserializedHero = JsonUtility.FromJson<Character>(heroJson);
        Debug.Log($"Deserialized Character: {deserializedHero.name}, Level: {deserializedHero.level}, Health: {deserializedHero.health}");
    }
}
```

**Output Example:**
```
Serialized Character: {"name":"Archer","level":5,"health":100}
Deserialized Character: Archer, Level: 5, Health: 100
```

---

## 2. List of Serializable Classes

Since **JsonUtility** does not directly support serializing plain lists, you can wrap the list in a container class.

### Example: Party of Characters
```csharp
using UnityEngine;
using System.Collections.Generic;

[System.Serializable]
public class Party
{
    public List<Character> characters;
}

public class ListJsonExample : MonoBehaviour
{
    void Start()
    {
        // Create a party with multiple characters
        Party party = new Party();
        party.characters = new List<Character>
        {
            new Character { name = "Warrior", level = 10, health = 150.0f },
            new Character { name = "Mage", level = 8, health = 80.0f }
        };

        // Serialize the party to JSON
        string partyJson = JsonUtility.ToJson(party, prettyPrint: true);
        Debug.Log("Serialized Party:\n" + partyJson);

        // Deserialize back into a Party object
        Party deserializedParty = JsonUtility.FromJson<Party>(partyJson);
        foreach (var character in deserializedParty.characters)
        {
            Debug.Log($"Character: {character.name}, Level: {character.level}, Health: {character.health}");
        }
    }
}
```

**Output Example:**
```
Serialized Party:
{
  "characters": [
    {"name":"Warrior","level":10,"health":150},
    {"name":"Mage","level":8,"health":80}
  ]
}
Character: Warrior, Level: 10, Health: 150
Character: Mage, Level: 8, Health: 80
```

---

## 3. Dictionaries with Newtonsoft.Json

Unity’s `JsonUtility` does not support dictionaries. For more complex data structures—such as dictionaries—you can use **Newtonsoft.Json**. To use Newtonsoft.Json in Unity, you may add it via the Unity Package Manager (search for “Newtonsoft Json for Unity”) or include the DLL in your project.

### Example: Dictionary of Scores
```csharp
using UnityEngine;
using Newtonsoft.Json;
using System.Collections.Generic;

public class DictionaryJsonExample : MonoBehaviour
{
    void Start()
    {
        // Create a dictionary with player scores
        Dictionary<string, int> scores = new Dictionary<string, int>
        {
            { "Alice", 100 },
            { "Bob", 80 }
        };

        // Serialize the dictionary to JSON using Newtonsoft.Json
        string scoresJson = JsonConvert.SerializeObject(scores, Formatting.Indented);
        Debug.Log("Serialized Dictionary:\n" + scoresJson);

        // Deserialize the JSON back into a Dictionary
        Dictionary<string, int> deserializedScores = JsonConvert.DeserializeObject<Dictionary<string, int>>(scoresJson);
        foreach (var kvp in deserializedScores)
        {
            Debug.Log($"Player: {kvp.Key}, Score: {kvp.Value}");
        }
    }
}
```

**Output Example:**
```
Serialized Dictionary:
{
  "Alice": 100,
  "Bob": 80
}
Player: Alice, Score: 100
Player: Bob, Score: 80
```

---

## 4. Combining Complex Structures

You might sometimes need to combine different types of data (e.g., a list of characters along with a dictionary of additional stats). Here’s a more advanced example that shows nested structures.

### Example: Complex Data Structure
```csharp
using UnityEngine;
using Newtonsoft.Json;
using System.Collections.Generic;

[System.Serializable]
public class CharacterStats
{
    public string characterName;
    public int level;
    public float health;
}

[System.Serializable]
public class GameData
{
    // Using a list of characters via JsonUtility
    public List<CharacterStats> party;

    // This dictionary will be serialized using Newtonsoft.Json later
    // (Not directly serialized by JsonUtility)
    [System.NonSerialized]
    public Dictionary<string, int> highScores;
}

public class ComplexJsonExample : MonoBehaviour
{
    void Start()
    {
        GameData data = new GameData();
        data.party = new List<CharacterStats>
        {
            new CharacterStats { characterName = "Knight", level = 12, health = 180.0f },
            new CharacterStats { characterName = "Rogue", level = 9, health = 110.0f }
        };
        data.highScores = new Dictionary<string, int>
        {
            { "Level1", 5000 },
            { "Level2", 7500 }
        };

        // Serialize the party list using JsonUtility
        string partyJson = JsonUtility.ToJson(data, prettyPrint: true);
        Debug.Log("Serialized Party Part with JsonUtility:\n" + partyJson);

        // Serialize the dictionary using Newtonsoft.Json
        string highScoresJson = JsonConvert.SerializeObject(data.highScores, Formatting.Indented);
        Debug.Log("Serialized High Scores with Newtonsoft.Json:\n" + highScoresJson);

        // For a full combined serialization, you might create a combined DTO (Data Transfer Object)
        // that uses only Newtonsoft.Json if you need one unified JSON string.
    }
}
```

**Output Example:**
```
Serialized Party Part with JsonUtility:
{
  "party": [
    {"characterName":"Knight","level":12,"health":180},
    {"characterName":"Rogue","level":9,"health":110}
  ]
}
Serialized High Scores with Newtonsoft.Json:
{
  "Level1": 5000,
  "Level2": 7500
}
```

---

## Key Takeaways

- **JsonUtility** is excellent for simple types and Unity-specific objects, but requires wrappers for lists and doesn’t support dictionaries.
- **Newtonsoft.Json** is more flexible and supports complex data structures like dictionaries out of the box.
- When designing your data structures, ensure classes meant for JSON serialization are marked with `[System.Serializable]`.
- You can mix and match these approaches depending on your needs. For a unified JSON representation, consider using one method for all your data if possible.

