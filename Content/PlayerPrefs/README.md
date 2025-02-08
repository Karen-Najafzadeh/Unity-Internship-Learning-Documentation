# **🎮 PlayerPrefs in Unity: A Complete Guide**  

`PlayerPrefs` is a built-in Unity system used to store **small amounts of persistent data**, such as player settings, scores, and game progress. It's a simple way to **save and retrieve data** between game sessions.

---

## **🔹 How PlayerPrefs Works**  
- `PlayerPrefs` stores **data locally** on the device.  
- It supports three data types:  
  - `int` → `SetInt()` / `GetInt()`  
  - `float` → `SetFloat()` / `GetFloat()`  
  - `string` → `SetString()` / `GetString()`  
- The data is saved in the **registry (Windows), plist (Mac/iOS), or PlayerPrefs file (Android/Linux).**  

---

## **🔹 Basic PlayerPrefs Usage**  

### **✅ Saving and Retrieving an Integer**
```csharp
// Save a high score
PlayerPrefs.SetInt("HighScore", 100);
PlayerPrefs.Save(); // Optional but ensures data is written immediately

// Retrieve the saved high score
int highScore = PlayerPrefs.GetInt("HighScore", 0); // Default: 0
Debug.Log("High Score: " + highScore);
```

### **✅ Saving and Retrieving a Float**
```csharp
// Save the player's best time
PlayerPrefs.SetFloat("BestTime", 52.3f);

// Retrieve the best time
float bestTime = PlayerPrefs.GetFloat("BestTime", 0.0f);
Debug.Log("Best Time: " + bestTime);
```

### **✅ Saving and Retrieving a String**
```csharp
// Save player name
PlayerPrefs.SetString("PlayerName", "ShadowKnight");

// Retrieve player name
string playerName = PlayerPrefs.GetString("PlayerName", "Guest");
Debug.Log("Player Name: " + playerName);
```

---

## **🔹 Removing Data from PlayerPrefs**  

### **✅ Delete a Single Key**
```csharp
PlayerPrefs.DeleteKey("HighScore"); 
```

### **✅ Delete All Saved Data**
```csharp
PlayerPrefs.DeleteAll();
```

⚠ **Warning:** `DeleteAll()` removes **all saved values**, so use it carefully.

---

## **🔹 Best Practices for Using PlayerPrefs**  

### ✅ **Use Constants Instead of Hardcoded Strings**
Instead of using `"HighScore"` multiple times, define a constant:
```csharp
public static class PlayerPrefKeys {
    public const string HighScore = "HighScore";
    public const string BestTime = "BestTime";
}
```
Then use:
```csharp
PlayerPrefs.SetInt(PlayerPrefKeys.HighScore, 200);
```

### ✅ **Always Call `PlayerPrefs.Save()` After Writing Data**
Although Unity automatically saves changes, calling `Save()` ensures immediate storage.
```csharp
PlayerPrefs.SetInt("Coins", 50);
PlayerPrefs.Save();
```

### ✅ **Use `HasKey()` to Check if a Value Exists**
```csharp
if (PlayerPrefs.HasKey("HighScore")) {
    Debug.Log("High score exists: " + PlayerPrefs.GetInt("HighScore"));
} else {
    Debug.Log("No high score found.");
}
```

### ✅ **Avoid Using PlayerPrefs for Large Data**
`PlayerPrefs` is best for **small settings or simple values**. For large game data, use:
- **JSON + Local File Storage**  
- **SQLite Database**  
- **Firebase Firestore or Remote Config**  

---

## **🔹 Advanced: Create a Wrapper Class (`GlobalValue`)**
To make PlayerPrefs easier to use, create a **static helper class**:

```csharp
public static class GlobalValue {
    public static int HighScore {
        get { return PlayerPrefs.GetInt("HighScore", 0); }
        set { PlayerPrefs.SetInt("HighScore", value); PlayerPrefs.Save(); }
    }

    public static string PlayerName {
        get { return PlayerPrefs.GetString("PlayerName", "Guest"); }
        set { PlayerPrefs.SetString("PlayerName", value); PlayerPrefs.Save(); }
    }
}
```

### **Usage:**
```csharp
GlobalValue.HighScore = 250;
Debug.Log("New High Score: " + GlobalValue.HighScore);
```

---

## **🚀 Summary**
| Feature           | Usage Example | Notes |
|------------------|--------------|--------------------------|
| Save Integer | `PlayerPrefs.SetInt("Score", 100);` | Stores whole numbers |
| Save Float | `PlayerPrefs.SetFloat("Volume", 0.5f);` | Stores decimals |
| Save String | `PlayerPrefs.SetString("Name", "Alex");` | Stores text |
| Retrieve Data | `PlayerPrefs.GetInt("Score", 0);` | Returns default if not found |
| Check Existence | `PlayerPrefs.HasKey("Score");` | Prevents errors |
| Delete Key | `PlayerPrefs.DeleteKey("Score");` | Removes only one key |
| Delete All | `PlayerPrefs.DeleteAll();` | Removes **all** saved data |

---

## **🔹 GlobalValue Class for Handling PlayerPrefs**
This class **encapsulates** `PlayerPrefs` operations, making it easier to use and reducing repetitive code.

### **📝 Example Implementation:**
```csharp
using UnityEngine;

public static class GlobalValue {
    
    // Player Score (Stored in PlayerPrefs)
    public static int PlayerScore {
        get { return PlayerPrefs.GetInt("PlayerScore", 0); }  // Default: 0
        set { PlayerPrefs.SetInt("PlayerScore", value); PlayerPrefs.Save(); }
    }

    // Player Name (Stored in PlayerPrefs)
    public static string PlayerName {
        get { return PlayerPrefs.GetString("PlayerName", "Guest"); }  // Default: "Guest"
        set { PlayerPrefs.SetString("PlayerName", value); PlayerPrefs.Save(); }
    }

    // Sound Settings (Stored in PlayerPrefs)
    public static bool IsSoundOn {
        get { return PlayerPrefs.GetInt("IsSoundOn", 1) == 1; }  // 1 = True, 0 = False
        set { PlayerPrefs.SetInt("IsSoundOn", value ? 1 : 0); PlayerPrefs.Save(); }
    }

    // Function to Reset All Data
    public static void ResetAll() {
        PlayerPrefs.DeleteAll();
        PlayerPrefs.Save();
    }
}
```

---

### **🔹 How This Works:**
1. **Encapsulates PlayerPrefs**:  
   - No need to manually call `PlayerPrefs.SetInt("PlayerScore", 100);` everywhere.  
   - Instead, you can just do `GlobalValue.PlayerScore = 100;` 🎯  

2. **Auto-Saves After Setting a Value**  
   - Every time a value is set, `PlayerPrefs.Save()` ensures it's written to storage.

3. **Default Values**  
   - If a value **isn't found**, a default is returned (`0` for int, `"Guest"` for strings, `true` for booleans).

4. **Boolean Handling**  
   - Unity `PlayerPrefs` only supports `int`, `float`, and `string`, so booleans are stored as `1` (true) or `0` (false).

---

## **🔹 How to Use It in Your Game**
### **✅ Set Values**
```csharp
GlobalValue.PlayerScore = 500;
GlobalValue.PlayerName = "Hero";
GlobalValue.IsSoundOn = false;
```

### **✅ Get Values**
```csharp
int score = GlobalValue.PlayerScore;
string name = GlobalValue.PlayerName;
bool sound = GlobalValue.IsSoundOn;

Debug.Log($"Player: {name}, Score: {score}, Sound: {sound}");
```

### **✅ Reset All PlayerPrefs Data**
```csharp
GlobalValue.ResetAll();
Debug.Log("All game data has been reset!");
```

---

## **🚀 Benefits of Using This GlobalValue Class**
✅ **Cleaner Code:** No need to write `PlayerPrefs.GetInt("Key")` everywhere.  
✅ **Auto-Save Mechanism:** Automatically saves when setting values.  
✅ **Default Values Support:** Prevents crashes due to missing data.  
✅ **Easier Refactoring:** If `PlayerPrefs` is replaced with another system (like a database), only this class needs modification.  

---

## **🔹 Possible Enhancements**
- **Encrypting Sensitive Data** (e.g., using `Convert.ToBase64String` for encoded storage).  
- **Event-Based Notifications** (Trigger an event when a value changes).  
- **Using a Singleton Instead of Static Class** (If you need non-static logic).  

## Question

isntead of hardcodiing "strings" in the code, what other things can i do ?

Hardcoding strings like `"PlayerScore"` directly in the code is **error-prone** and makes it harder to maintain. Instead, you can use **constants, enums, or static fields** to manage them more efficiently.

---

## **🔹 1. Using Constants (Best for Readability & Performance)**
A `const` field is **immutable** and has **zero runtime overhead** because it's replaced at compile time.

### **Example:**
```csharp
public static class PlayerPrefKeys {
    public const string PlayerScore = "PlayerScore";
    public const string PlayerName = "PlayerName";
    public const string IsSoundOn = "IsSoundOn";
}
```

### **Usage:**
```csharp
PlayerPrefs.SetInt(PlayerPrefKeys.PlayerScore, 100);
int score = PlayerPrefs.GetInt(PlayerPrefKeys.PlayerScore, 0);
```
✅ **Pros:**  
✔️ Fast, because `const` values are compiled directly into the code.  
✔️ Prevents typos and mistakes in string keys.  
✔️ Improves readability and maintainability.

❌ **Cons:**  
- Constants **cannot be modified at runtime** (but for PlayerPrefs keys, this isn't an issue).

---

## **🔹 2. Using Static Readonly Fields (When Modification Might Be Needed)**
If you want something similar to `const`, but with the **option to modify it at runtime**, use `static readonly`.

### **Example:**
```csharp
public static class PlayerPrefKeys {
    public static readonly string PlayerScore = "PlayerScore";
    public static readonly string PlayerName = "PlayerName";
}
```
✅ **Pros:**  
✔️ Can be assigned at runtime if needed.  
✔️ Avoids hardcoded string mistakes like `const`.

❌ **Cons:**  
- Slightly slower than `const` (but negligible in this case).

---

## **🔹 3. Using an Enum (Less Flexible for Strings)**
Enums are great for predefined values, but they **cannot directly store string values**. However, you can use an **extension method** to convert them to strings.

### **Example:**
```csharp
public enum PlayerPrefKey {
    PlayerScore,
    PlayerName,
    IsSoundOn
}

public static class PlayerPrefKeyExtensions {
    public static string ToKeyString(this PlayerPrefKey key) {
        return key.ToString();  // Converts enum to string automatically
    }
}
```

### **Usage:**
```csharp
PlayerPrefs.SetInt(PlayerPrefKey.PlayerScore.ToKeyString(), 100);
int score = PlayerPrefs.GetInt(PlayerPrefKey.PlayerScore.ToKeyString(), 0);
```
✅ **Pros:**  
✔️ Makes the code more structured and prevents typos.  
✔️ Enums provide **auto-complete** in IDEs.  

❌ **Cons:**  
- Slightly more verbose (`ToKeyString()` call).  
- Enum values are stored as **string**, meaning you **can't change them dynamically** like in a `Dictionary`.

---

## **🔹 4. Using a Dictionary (For Dynamic & Expandable Keys)**
If you want **a flexible way to manage keys**, a `Dictionary` is a good option.

### **Example:**
```csharp
using System.Collections.Generic;

public static class PlayerPrefKeys {
    private static readonly Dictionary<string, string> keys = new Dictionary<string, string> {
        { "PlayerScore", "PlayerScore" },
        { "PlayerName", "PlayerName" },
        { "IsSoundOn", "IsSoundOn" }
    };

    public static string GetKey(string key) {
        return keys.ContainsKey(key) ? keys[key] : null;
    }
}
```

### **Usage:**
```csharp
PlayerPrefs.SetInt(PlayerPrefKeys.GetKey("PlayerScore"), 100);
int score = PlayerPrefs.GetInt(PlayerPrefKeys.GetKey("PlayerScore"), 0);
```
✅ **Pros:**  
✔️ Can dynamically modify the dictionary at runtime.  
✔️ Avoids using **hardcoded strings** everywhere.  

❌ **Cons:**  
- Slightly **slower** due to dictionary lookups.  
- Requires **extra validation** to avoid missing keys.

---

## **🚀 Best Choice?**
| **Method**      | **Performance** | **Readability** | **Flexibility** | **Best Use Case** |
|----------------|---------------|---------------|---------------|----------------|
| `const string`  | ✅ Fastest | ✅ Best | ❌ Cannot modify | Use for fixed keys |
| `static readonly` | ✅ Fast | ✅ Good | ✅ Can modify | If you might change key values |
| `enum` + `ToKeyString()` | ✅ Fast | ✅ Good | ❌ Limited to enum values | If you prefer enum-based keys |
| `Dictionary<string, string>` | ❌ Slower | ✅ Best | ✅ Very flexible | If keys might change dynamically |

### **💡 My Recommendation**
Use `const string` or `static readonly` for **simple PlayerPrefs keys**.  
Use `Dictionary<string, string>` **only if you need dynamic key mapping**.  

---

Since JSON files are stored in the device’s **persistent storage**, players can easily find and modify them using a text editor, which can lead to **cheating** (e.g., setting high scores to max, unlocking all levels, giving themselves unlimited coins, etc.).

---

## **🔹 How Players Can Cheat Using JSON**
If you store data like this:
```json
{
    "playerName": "ShadowKnight",
    "highScore": 99999999,
    "inventory": ["Sword", "Shield", "Potion"],
    "coins": 999999
}
```
A player can open the file, change the values, and reload the game with **unlimited resources**.

---

## **🔹 How to Prevent JSON Modification (Anti-Cheat Methods)**  

### **1️⃣ Encrypt the JSON Data**
Before saving, encrypt the data so it becomes unreadable.  

🔹 **Example: Encrypted JSON Before Saving**  
```json
U2FsdGVkX1+lsKeYKhpGc7p9sJj6QpqjAzpTzU5o...
```
Players **won't be able to modify it** without knowing the decryption key.

**C# Example for Encrypting & Decrypting JSON using AES:**
```csharp
using System;
using System.Security.Cryptography;
using System.Text;

public static class EncryptionHelper {
    private static string key = "1234567890123456"; // 16-char secret key

    public static string Encrypt(string data) {
        byte[] keyBytes = Encoding.UTF8.GetBytes(key);
        using (Aes aes = Aes.Create()) {
            aes.Key = keyBytes;
            aes.IV = keyBytes;
            using (var encryptor = aes.CreateEncryptor()) {
                byte[] dataBytes = Encoding.UTF8.GetBytes(data);
                byte[] encrypted = encryptor.TransformFinalBlock(dataBytes, 0, dataBytes.Length);
                return Convert.ToBase64String(encrypted);
            }
        }
    }

    public static string Decrypt(string encryptedData) {
        byte[] keyBytes = Encoding.UTF8.GetBytes(key);
        using (Aes aes = Aes.Create()) {
            aes.Key = keyBytes;
            aes.IV = keyBytes;
            using (var decryptor = aes.CreateDecryptor()) {
                byte[] encryptedBytes = Convert.FromBase64String(encryptedData);
                byte[] decrypted = decryptor.TransformFinalBlock(encryptedBytes, 0, encryptedBytes.Length);
                return Encoding.UTF8.GetString(decrypted);
            }
        }
    }
}
```
🔹 **Modify Save & Load Methods**  
Before saving:
```csharp
string encryptedJson = EncryptionHelper.Encrypt(json);
File.WriteAllText(filePath, encryptedJson);
```
Before loading:
```csharp
string encryptedJson = File.ReadAllText(filePath);
string decryptedJson = EncryptionHelper.Decrypt(encryptedJson);
PlayerData data = JsonUtility.FromJson<PlayerData>(decryptedJson);
```

---

### **2️⃣ Store Important Data in a Secure Server**
Instead of saving critical game data locally, store it on a **secure backend like Firebase Firestore, PlayFab, or a custom game server**.  
🔹 **Benefits:**  
✅ Players can't modify data easily  
✅ You can detect & ban cheaters  
✅ Progress is saved across devices  

Example with **Firebase Firestore**:
```csharp
using Firebase.Firestore;

Firestore db = FirebaseFirestore.DefaultInstance;

// Save Data
Dictionary<string, object> data = new Dictionary<string, object> {
    { "playerName", "ShadowKnight" },
    { "highScore", 3000 },
    { "coins", 500 }
};

db.Collection("users").Document("player123").SetAsync(data);

// Load Data
db.Collection("users").Document("player123").GetSnapshotAsync().ContinueWith(task => {
    if (task.Result.Exists) {
        Dictionary<string, object> playerData = task.Result.ToDictionary();
        string name = playerData["playerName"].ToString();
        int score = Convert.ToInt32(playerData["highScore"]);
    }
});
```

---

### **3️⃣ Use Checksums or Hashing**
Before saving, generate a **checksum** (a unique signature of the data).  
When loading, **compare** the checksum to detect tampering.  

🔹 **Example: Generating a SHA256 Hash**
```csharp
using System.Security.Cryptography;

public static string GenerateHash(string data) {
    using (SHA256 sha256 = SHA256.Create()) {
        byte[] bytes = Encoding.UTF8.GetBytes(data);
        byte[] hash = sha256.ComputeHash(bytes);
        return Convert.ToBase64String(hash);
    }
}
```
Before saving:
```csharp
string json = JsonUtility.ToJson(playerData);
string hash = GenerateHash(json);
File.WriteAllText(filePath, json + "\n" + hash);
```
Before loading:
```csharp
string[] lines = File.ReadAllLines(filePath);
string json = lines[0];
string savedHash = lines[1];

if (GenerateHash(json) != savedHash) {
    Debug.Log("Data has been modified! Possible cheat detected.");
}
```
🔹 **If the hash doesn’t match, don’t load the data.**

---

### **4️⃣ Use Obfuscation (Making Code Harder to Read)**
Obfuscation hides important values by encoding them in a way that makes it harder for hackers to modify.  

🔹 Example: **Base64 Encoding**
```csharp
using System;

string encoded = Convert.ToBase64String(Encoding.UTF8.GetBytes("9999"));
string decoded = Encoding.UTF8.GetString(Convert.FromBase64String(encoded));
```
🔹 **Example: XOR Encoding**
```csharp
public static string XOREncrypt(string text, string key) {
    char[] output = new char[text.Length];
    for (int i = 0; i < text.Length; i++) {
        output[i] = (char)(text[i] ^ key[i % key.Length]);
    }
    return new string(output);
}
```

---

## **🔹 Best Approach?**
✅ **For offline games** → Use **encrypted JSON** + **checksums**  
✅ **For online games** → Store critical data in **Firebase / PlayFab / custom backend**  
✅ **For premium games** → Add **anti-cheat detection** (Unity Cloud, GameAnalytics)  

---

## **🚀 Final Thoughts**
If a player **really** wants to cheat, they **will find a way**.  
But by implementing **encryption, hashing, and online storage**, you can **make it much harder** for them.  

