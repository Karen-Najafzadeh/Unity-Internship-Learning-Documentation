Nakama Storage is a powerful feature that allows you to save, retrieve, update, and manage structured data for your game directly in the Nakama server. This can include player progress, leaderboards, game state, inventories, and more. Let me walk you through **step-by-step** on what it is, how to use it, and provide some examples.

---
# Recommendation

**I highly encourage you** to read **[this document](https://github.com/Karen-Najafzadeh/Unity-Internship-Learning-Documentation/tree/main/Nakama/Integration)** as it shows you how to initialize nakama client on your project, because the following code uses session authentication and initializing nakama client, and if you are unfamiliar with this concept, you might get into some trouble understanding the content.

---

## **Step-by-Step Guide to Nakama Storage**

### **1. What is Nakama Storage?**
Nakama Storage is a document-based system that organizes data into **collections**, **keys**, and **user IDs**. This structured approach allows you to categorize and manage game data for different players, groups, or globally shared objects.

- **Collection**: A category or grouping of data, like "player_data" or "game_settings".
- **Key**: A unique identifier within a collection, such as "level_progress" or "inventory".
- **User ID**: The player or system associated with the data. Data can be:
  - **User-Specific**: Bound to a particular player's user ID.
  - **Public**: Shared globally with no specific user.
  - **Group-Specific**: Associated with a group.

---

### **2. When to Use Nakama Storage**
Nakama Storage is useful in situations like:
- Storing and retrieving **player progress** (e.g., levels unlocked, scores, inventory).
- Saving **global game settings** or configurations (e.g., difficulty levels, announcements).
- Managing **shared group data**, like a team's achievements.
- Creating **custom leaderboard data** beyond Nakama's built-in leaderboards.

---

### **3. How to Use Nakama Storage**
Here's how you can work with Nakama Storage step by step:

---

#### **3.1. Writing Data to Nakama Storage**
To save data to Nakama storage, you use the `WriteStorageObjectsAsync()` function. Data is written to a specific **collection** and **key** for a user.

**Example Code: Writing Player Progress**
```csharp
using Nakama;
using System.Collections.Generic;
using UnityEngine;

public class NakamaStorageExample : MonoBehaviour
{
    private IClient client;
    private ISession session;

    private async void Start()
    {
        // Step 1: Connect to Nakama server
        client = new Client("http", "127.0.0.1", 7350, "defaultkey");
        session = await client.AuthenticateDeviceAsync(SystemInfo.deviceUniqueIdentifier);

        // Step 2: Prepare data to write
        var storageWrite = new WriteStorageObject
        {
            Collection = "player_data",
            Key = "level_progress",
            Value = "{\"level\": 3, \"score\": 1500}", // JSON data
            PermissionRead = 2, // Public Read
            PermissionWrite = 1 // Only owner can write
        };

        // Step 3: Write data to Nakama storage
        await client.WriteStorageObjectsAsync(session, new[] { storageWrite });
        Debug.Log("Data written successfully!");
    }
}
```

---

#### **3.2. Reading Data from Nakama Storage**
To read data, use the `ReadStorageObjectsAsync()` function. Specify the **collection**, **key**, and **user ID** (optional).

**Example Code: Reading Player Progress**
```csharp
using Nakama;
using System.Collections.Generic;
using UnityEngine;

public class NakamaStorageExample : MonoBehaviour
{
    private IClient client;
    private ISession session;

    private async void Start()
    {
        // Step 1: Connect to Nakama server
        client = new Client("http", "127.0.0.1", 7350, "defaultkey");
        session = await client.AuthenticateDeviceAsync(SystemInfo.deviceUniqueIdentifier);

        // Step 2: Prepare the query
        var storageRead = new StorageObjectId
        {
            Collection = "player_data",
            Key = "level_progress",
            UserId = session.UserId // Optional: Read your own data
        };

        // Step 3: Read data from Nakama storage
        var result = await client.ReadStorageObjectsAsync(session, new[] { storageRead });

        foreach (var obj in result.Objects)
        {
            Debug.Log($"Read Data: {obj.Value}");
        }
    }
}
```

---

#### **3.3. Updating Data**
To update data, simply write new data to the same **collection** and **key**.

---

#### **3.4. Deleting Data**
You can delete data using the `DeleteStorageObjectsAsync()` function.

**Example Code: Deleting Player Data**
```csharp
using Nakama;
using System.Collections.Generic;
using UnityEngine;

public class NakamaStorageExample : MonoBehaviour
{
    private IClient client;
    private ISession session;

    private async void Start()
    {
        // Step 1: Connect to Nakama server
        client = new Client("http", "127.0.0.1", 7350, "defaultkey");
        session = await client.AuthenticateDeviceAsync(SystemInfo.deviceUniqueIdentifier);

        // Step 2: Prepare the deletion request
        var storageDelete = new StorageObjectId
        {
            Collection = "player_data",
            Key = "level_progress",
            UserId = session.UserId // Specify the user
        };

        // Step 3: Delete data
        await client.DeleteStorageObjectsAsync(session, new[] { storageDelete });
        Debug.Log("Data deleted successfully!");
    }
}
```

---

### **4. Advanced Example: Player Inventory System**

Let’s create a **robust player inventory system** using Nakama Storage.

---

#### **Saving Player Inventory**
```csharp
var storageWrite = new WriteStorageObject
{
    Collection = "inventory",
    Key = "items",
    Value = "{\"items\": [\"sword\", \"shield\", \"potion\"]}", // JSON
    PermissionRead = 1, // Only owner can read
    PermissionWrite = 1 // Only owner can write
};
await client.WriteStorageObjectsAsync(session, new[] { storageWrite });
Debug.Log("Inventory saved!");
```

---

#### **Loading Player Inventory**
```csharp
var storageRead = new StorageObjectId
{
    Collection = "inventory",
    Key = "items",
    UserId = session.UserId
};

var result = await client.ReadStorageObjectsAsync(session, new[] { storageRead });
foreach (var obj in result.Objects)
{
    Debug.Log($"Inventory: {obj.Value}");
}
```

---

#### **Adding a New Item to Inventory**
To add a new item:
1. **Load the current inventory.**
2. **Modify the JSON.**
3. **Write the updated inventory back to storage.**

**Code:**
```csharp
var result = await client.ReadStorageObjectsAsync(session, new[] { storageRead });
foreach (var obj in result.Objects)
{
    var currentInventory = JsonUtility.FromJson<Inventory>(obj.Value);
    currentInventory.items.Add("new_item");
    var updatedJson = JsonUtility.ToJson(currentInventory);

    var updateStorage = new WriteStorageObject
    {
        Collection = "inventory",
        Key = "items",
        Value = updatedJson
    };
    await client.WriteStorageObjectsAsync(session, new[] { updateStorage });
    Debug.Log("Item added to inventory!");
}
```

---

#### **Deleting an Item**
You follow a similar process but remove an item instead.

---

### **5. Best Practices for Nakama Storage**
1. **Use Proper Permissions**:  
   - **Read/Write Permissions** control who can access or modify the data. 
     - 0 = No Access
     - 1 = Owner Only
     - 2 = Public Read
     - 3 = Public Read/Write

2. **Use Collections to Organize Data**:  
   - Example collections: `"player_stats"`, `"game_settings"`, `"inventory"`.

3. **Batch Operations**:  
   - You can write, read, or delete multiple objects in a single request for efficiency. a full and complete batch operation example is presented in **section 6**.

4. **Validation**:  
   - Use server-side Lua or TypeScript hooks to validate storage operations.

---

### **6. Batch Operations**

Here’s an example of how to perform **batch operations** in Nakama Storage, where you write, read, or delete multiple objects in a single request.

---

### **6.1. Writing Multiple Objects (Batch Write)**

You can write several objects to different collections and keys in one go using `WriteStorageObjectsAsync`.

**Example Code: Batch Write**
```csharp
using Nakama;
using System.Collections.Generic;
using UnityEngine;

public class NakamaBatchExample : MonoBehaviour
{
    private IClient client;
    private ISession session;

    private async void Start()
    {
        // Step 1: Connect to Nakama server
        client = new Client("http", "127.0.0.1", 7350, "defaultkey");
        session = await client.AuthenticateDeviceAsync(SystemInfo.deviceUniqueIdentifier);

        // Step 2: Prepare multiple objects to write
        var storageWriteBatch = new List<WriteStorageObject>
        {
            new WriteStorageObject
            {
                Collection = "player_data",
                Key = "level_progress",
                Value = "{\"level\": 5, \"score\": 3000}",
                PermissionRead = 2,
                PermissionWrite = 1
            },
            new WriteStorageObject
            {
                Collection = "inventory",
                Key = "items",
                Value = "{\"items\": [\"sword\", \"shield\"]}",
                PermissionRead = 1,
                PermissionWrite = 1
            },
            new WriteStorageObject
            {
                Collection = "settings",
                Key = "audio",
                Value = "{\"volume\": 80, \"mute\": false}",
                PermissionRead = 2,
                PermissionWrite = 1
            }
        };

        // Step 3: Write batch data to Nakama storage
        await client.WriteStorageObjectsAsync(session, storageWriteBatch);
        Debug.Log("Batch write completed successfully!");
    }
}
```

---

### **6.2. Reading Multiple Objects (Batch Read)**

You can retrieve multiple objects at once by specifying their collection and keys in a single request.

**Example Code: Batch Read**
```csharp
using Nakama;
using System.Collections.Generic;
using UnityEngine;

public class NakamaBatchExample : MonoBehaviour
{
    private IClient client;
    private ISession session;

    private async void Start()
    {
        // Step 1: Connect to Nakama server
        client = new Client("http", "127.0.0.1", 7350, "defaultkey");
        session = await client.AuthenticateDeviceAsync(SystemInfo.deviceUniqueIdentifier);

        // Step 2: Prepare multiple objects to read
        var storageReadBatch = new List<StorageObjectId>
        {
            new StorageObjectId { Collection = "player_data", Key = "level_progress", UserId = session.UserId },
            new StorageObjectId { Collection = "inventory", Key = "items", UserId = session.UserId },
            new StorageObjectId { Collection = "settings", Key = "audio", UserId = session.UserId }
        };

        // Step 3: Read batch data from Nakama storage
        var result = await client.ReadStorageObjectsAsync(session, storageReadBatch);
        foreach (var obj in result.Objects)
        {
            Debug.Log($"Read from Collection '{obj.Collection}' - Key '{obj.Key}': {obj.Value}");
        }
    }
}
```

---

### **6.3. Deleting Multiple Objects (Batch Delete)**

You can delete multiple objects at once by specifying their collection and keys.

**Example Code: Batch Delete**
```csharp
using Nakama;
using System.Collections.Generic;
using UnityEngine;

public class NakamaBatchExample : MonoBehaviour
{
    private IClient client;
    private ISession session;

    private async void Start()
    {
        // Step 1: Connect to Nakama server
        client = new Client("http", "127.0.0.1", 7350, "defaultkey");
        session = await client.AuthenticateDeviceAsync(SystemInfo.deviceUniqueIdentifier);

        // Step 2: Prepare multiple objects to delete
        var storageDeleteBatch = new List<StorageObjectId>
        {
            new StorageObjectId { Collection = "player_data", Key = "level_progress", UserId = session.UserId },
            new StorageObjectId { Collection = "inventory", Key = "items", UserId = session.UserId },
            new StorageObjectId { Collection = "settings", Key = "audio", UserId = session.UserId }
        };

        // Step 3: Delete batch data from Nakama storage
        await client.DeleteStorageObjectsAsync(session, storageDeleteBatch);
        Debug.Log("Batch delete completed successfully!");
    }
}
```

---

### **6.4. Key Points for Batch Operations**
- **Efficiency**: Batch operations minimize server requests, reducing network latency.
- **Flexibility**: You can mix different collections and keys in the same batch request.
- **Error Handling**: Handle potential errors using `try-catch` blocks for robust code.

---