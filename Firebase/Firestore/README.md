

# **🔥 Firestore Database in Unity (Saving & Loading Player Data)**
Firestore is a **NoSQL cloud database** that stores data in **collections and documents**. It allows real-time syncing and offline support.

## **Why Use Firestore?**
✅ **Persistent Storage** – Player progress is saved even if they uninstall the game.  
✅ **Cloud Sync** – Play across multiple devices with the same account.  
✅ **Scalability** – Handles thousands of users efficiently.  
✅ **Security** – Only authenticated users can modify their data (using Firebase rules).  

---

# **1. Setting Up Firestore in Unity**
### **📌 Step 1: Enable Firestore in Firebase Console**
1. Open **Firebase Console** → Select your project.  
2. Click on **Firestore Database** in the left menu.  
3. Click **Create Database** → Choose "Start in test mode" (for now).  
4. Click **Enable** to initialize Firestore.  

---

# **2. Firestore Structure in Unity**
Firestore stores data in **Collections → Documents → Fields**.

### **Example Data Structure for a Game**
```
users (Collection)
   ├── userId123 (Document)
   │     ├── name: "PlayerOne"
   │     ├── level: 5
   │     ├── score: 1500
   │     ├── unlockedLevels: [1, 2, 3, 4, 5]
   │     ├── inventory: { "coins": 100, "gems": 5 }
```
- **Collection:** `"users"` → Stores all users.  
- **Document:** `"userId123"` → Stores one user’s data.  
- **Fields:** `"level"`, `"score"`, etc.

---

# **3. Installing Firestore SDK in Unity**
Make sure you have **Firebase SDK** installed and add this to your script:
```csharp
using Firebase.Firestore;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;
```
Then, initialize Firestore:
```csharp
FirebaseFirestore db;

void Start()
{
    db = FirebaseFirestore.DefaultInstance;
}
```

---

# **4. Saving Player Data to Firestore**
You’ll typically want to save:
- **Player Level**
- **High Score**
- **Unlocked Items**

```csharp
public async Task SavePlayerData(string userId, int level, int score, List<int> unlockedLevels)
{
    DocumentReference docRef = db.Collection("users").Document(userId);

    Dictionary<string, object> playerData = new Dictionary<string, object>
    {
        { "level", level },
        { "score", score },
        { "unlockedLevels", unlockedLevels }
    };

    await docRef.SetAsync(playerData);
    Debug.Log("Player data saved!");
}
```

### **🌍 Real-World Example**
A player **completes Level 5** with **1500 points**. Their progress is saved:
```
{
    "level": 5,
    "score": 1500,
    "unlockedLevels": [1, 2, 3, 4, 5]
}
```
Next time they log in, they can **continue from Level 5**.

---

# **5. Loading Player Data from Firestore**
To restore the player’s progress:

```csharp
public async Task LoadPlayerData(string userId)
{
    DocumentReference docRef = db.Collection("users").Document(userId);
    DocumentSnapshot snapshot = await docRef.GetSnapshotAsync();

    if (snapshot.Exists)
    {
        int level = snapshot.GetValue<int>("level");
        int score = snapshot.GetValue<int>("score");
        List<int> unlockedLevels = snapshot.GetValue<List<int>>("unlockedLevels");

        Debug.Log($"Loaded Data: Level={level}, Score={score}, Unlocked={string.Join(", ", unlockedLevels)}");
    }
    else
    {
        Debug.Log("No player data found.");
    }
}
```

### **🌍 Real-World Example**
The player reinstalls the game and logs in → Firebase loads their **last saved progress**.

---

# **6. Updating Player Data**
Instead of overwriting all data, we can update specific fields.

```csharp
public async Task UpdatePlayerScore(string userId, int newScore)
{
    DocumentReference docRef = db.Collection("users").Document(userId);
    await docRef.UpdateAsync("score", newScore);
    Debug.Log("Score updated!");
}
```

### **🌍 Real-World Example**
The player achieves a new high score → The `score` field updates **without affecting other data**.

---

# **7. Incrementing a Value (e.g., Coins, XP)**
To **increment** a value (instead of overwriting it):

```csharp
public async Task AddCoins(string userId, int coins)
{
    DocumentReference docRef = db.Collection("users").Document(userId);
    await docRef.UpdateAsync("inventory.coins", FieldValue.Increment(coins));
    Debug.Log($"{coins} coins added!");
}
```

### **🌍 Real-World Example**
The player collects **50 coins** in a level → The total coins **increase by 50**.

---

# **8. Deleting Data from Firestore**
## **A. Deleting a Single Field**
```csharp
await docRef.UpdateAsync("inventory.gems", FieldValue.Delete());
```
This removes `"gems"` from the inventory **without deleting other fields**.

## **B. Deleting an Entire Document**
```csharp
await docRef.DeleteAsync();
```
This **removes** the player’s document completely.

---

# **9. Securing Firestore (Firebase Rules)**
By default, anyone can modify the database. Secure it by **allowing only authenticated users** to access their own data.

Go to **Firebase Console → Firestore Rules** and set:

```firestore
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

### **🌍 Real-World Example**
Without these rules, **anyone** could change another player’s data. With these rules, users can **only modify their own** data.

---

# **10. Firestore Best Practices**
### ✅ **Use `user.UserId` for Player Data**
- Each player gets a unique ID, preventing conflicts.

### ✅ **Use `UpdateAsync()` for Partial Updates**
- Only update **what’s necessary** (instead of overwriting the entire document).

### ✅ **Store Small Data in Firestore, Large Data in Storage**
- Use Firestore for **scores, progress, settings**.
- Use **Firebase Storage** for **images, large assets**.

---


## **🔥 Batch Operations in Firestore (Handling Multiple Writes Efficiently)**  

Firestore supports **batch operations**, which allow multiple writes to be performed as a **single atomic transaction**. This ensures that either **all operations succeed** or **none are applied**.  

---

## **1. Why Use Batch Operations?**  
✅ **Performance Boost** – Reduces network calls by sending multiple writes in a single request.  
✅ **Atomic Transactions** – Either **all changes are applied** or **none** (prevents partial updates).  
✅ **Consistency** – Ensures that player data doesn’t end up in an inconsistent state.  

---

## **2. Implementing Batch Writes in Unity**  
### **📌 Example: Save Player Progress and Inventory in One Operation**  
If a player **completes a level**, you might want to update:  
- **Level progress** (`level`, `score`, `unlockedLevels`).  
- **Inventory** (`coins`, `gems`).  

Instead of making **multiple separate writes**, we use **a batch write**:

```csharp
public async Task SavePlayerDataWithBatch(string userId, int level, int score, List<int> unlockedLevels, int coins, int gems)
{
    WriteBatch batch = db.StartBatch();
    
    DocumentReference userDoc = db.Collection("users").Document(userId);

    batch.Set(userDoc, new Dictionary<string, object>
    {
        { "level", level },
        { "score", score },
        { "unlockedLevels", unlockedLevels },
        { "inventory", new Dictionary<string, object>
            {
                { "coins", coins },
                { "gems", gems }
            }
        }
    });

    await batch.CommitAsync();
    Debug.Log("Batch operation completed! Player data updated.");
}
```
### **🌍 Real-World Example**  
A player **completes Level 5** and earns **100 coins & 5 gems**. The batch updates:  
- `"level": 5`  
- `"score": 1500`  
- `"coins": 100`  
- `"gems": 5`  
Everything is saved **at the same time** to ensure consistency.

---

## **3. Updating Multiple Documents in a Batch**  
Suppose you need to **update multiple players' scores** at once (e.g., at the end of a tournament).  

```csharp
public async Task UpdateMultiplePlayerScores(Dictionary<string, int> playerScores)
{
    WriteBatch batch = db.StartBatch();

    foreach (var player in playerScores)
    {
        DocumentReference docRef = db.Collection("users").Document(player.Key);
        batch.Update(docRef, "score", player.Value);
    }

    await batch.CommitAsync();
    Debug.Log("Batch update for multiple player scores completed!");
}
```
### **🌍 Real-World Example**  
If **3 players** finish a tournament, their scores update simultaneously:  
```json
{
    "userId123": { "score": 2000 },
    "userId456": { "score": 1750 },
    "userId789": { "score": 2200 }
}
```
Without batch operations, you'd have to **send multiple separate requests**, which would be slower.

---

## **4. Deleting Multiple Documents in a Batch**  
If a **player resets their progress**, you may need to delete multiple fields **or an entire document**.

```csharp
public async Task ResetPlayerData(string userId)
{
    WriteBatch batch = db.StartBatch();
    
    DocumentReference userDoc = db.Collection("users").Document(userId);

    // Delete specific fields
    batch.Update(userDoc, new Dictionary<string, object>
    {
        { "score", FieldValue.Delete() },
        { "unlockedLevels", FieldValue.Delete() },
        { "inventory", FieldValue.Delete() }
    });

    await batch.CommitAsync();
    Debug.Log("Player progress reset successfully!");
}
```
### **🌍 Real-World Example**  
A player selects **"Reset Progress"**, so their `score`, `unlockedLevels`, and `inventory` are erased, while their user profile remains.

---

## **5. Firestore Transactions vs. Batch Writes**  
| Feature | Batch Write | Transaction |
|---------|------------|------------|
| **Purpose** | Multiple updates at once | Ensure data consistency based on current values |
| **Number of Reads** | 0 | At least 1 (to check current values) |
| **Atomicity** | All changes succeed or fail | All changes succeed or fail |
| **Use Case** | Writing multiple updates efficiently | Preventing data overwrites |

For example, if **two players collect the same loot**, a transaction prevents them from **accidentally overwriting** each other’s data.

---

## **🔥 Final Takeaways**
✅ **Use batch writes** when you need to update **multiple documents efficiently**.  
✅ **Batch writes reduce network requests**, making your game faster.  
✅ **Use transactions** if updates depend on existing data (e.g., preventing duplicate rewards).  
✅ **Ensure consistency** by committing all changes at once.  

---

Now your game can **efficiently manage player data**, **prevent inconsistent updates**, and **improve performance**! 🚀🎮

---

# **🌟 Final Summary**
🔹 **Firestore allows you to store and retrieve player data** (levels, scores, items).  
🔹 **You can update progress without overwriting all data**.  
🔹 **Firestore rules protect data, ensuring only the right users can modify it**.  
🔹 **Use incremental updates for coins, XP, and achievements**.  

---

# **🚀 Next Steps**
Now that you know how to **save & load data**, you can:
✅ **Sync player progress across devices**  
✅ **Implement leaderboards (Firestore or Nakama)**  
✅ **Allow in-game purchases (store gems, coins, etc.)**  

