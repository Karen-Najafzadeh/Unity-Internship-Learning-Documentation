## **Firebase Authentication in Unity (Detailed Explanation with Real-World Examples)**  

Firebase Authentication provides a secure way to manage user sign-ins using various methods like **Email/Password**, **Anonymous Login**, **Google Sign-In**, and more. Let's break it down with examples and real-world scenarios.  

---

# **Why Use Firebase Authentication?**
1. **User Accounts & Progress Sync** – Allows users to sign in on different devices and retain their game progress.
2. **Security** – Firebase securely manages authentication and prevents unauthorized access.
3. **Personalized Experience** – Enables features like leaderboards, cloud saves, and social interactions.

---

# **How Authentication Works**
Firebase Authentication manages users through the **FirebaseAuth** class. It provides:  
- **Sign up** (creating new users)  
- **Sign in** (logging in existing users)  
- **Sign out** (logging users out)  
- **User session management**  

Each user has a **unique FirebaseUser ID** (`user.UserId`), which is used to associate data (like saved progress) in Firestore.

---

# **1. Setting Up Firebase Authentication in Unity**
Before writing code, make sure you:  
✅ **Enable Authentication in Firebase Console:**  
1. Go to **Firebase Console** → **Authentication** → **Sign-in method**  
2. Enable **Email/Password**, **Anonymous Sign-in**, or other methods you want to use.

✅ **Import Firebase SDK** (`FirebaseAuth.unitypackage`).

---

# **2. Implementing Authentication in Unity**
## **📌 A. User Registration (Sign Up)**
Let's create a system for new players to register using email and password.

```csharp
using Firebase.Auth;
using System.Threading.Tasks;
using UnityEngine;

public class AuthManager : MonoBehaviour
{
    private FirebaseAuth auth;
    private FirebaseUser user;

    void Start()
    {
        auth = FirebaseAuth.DefaultInstance;
    }

    public async Task SignUp(string email, string password)
    {
        try
        {
            var result = await auth.CreateUserWithEmailAndPasswordAsync(email, password);
            user = result.User;
            Debug.Log("User registered successfully: " + user.Email);
        }
        catch (FirebaseException e)
        {
            Debug.LogError($"Registration Failed: {e.Message}");
        }
    }
}
```

### **🌍 Real-World Example**
A player downloads your game **"Space Explorer"** and wants to save their progress online. They sign up with:  
📧 `player123@example.com`  
🔑 `securepassword123`  
Now, Firebase stores their account, and you can associate their data with `user.UserId`.

---

## **📌 B. User Sign-In**
Once registered, players can sign in with their email and password.

```csharp
public async Task SignIn(string email, string password)
{
    try
    {
        var result = await auth.SignInWithEmailAndPasswordAsync(email, password);
        user = result.User;
        Debug.Log("User signed in: " + user.Email);
    }
    catch (FirebaseException e)
    {
        Debug.LogError($"Sign-in Failed: {e.Message}");
    }
}
```

### **🌍 Real-World Example**
After registering, the same player installs the game on another device. They enter:  
📧 `player123@example.com`  
🔑 `securepassword123`  
Now, they retrieve their saved game progress from Firestore.

---

## **📌 C. Sign Out**
Players should be able to log out when they want.

```csharp
public void SignOut()
{
    auth.SignOut();
    Debug.Log("User signed out.");
}
```

### **🌍 Real-World Example**
The player shares their phone with a sibling. They **log out** so their sibling can use their own account.

---

## **📌 D. Check If User is Already Signed In**
To keep users logged in, check their authentication state at game launch.

```csharp
void Start()
{
    auth = FirebaseAuth.DefaultInstance;
    if (auth.CurrentUser != null)
    {
        Debug.Log($"User already signed in: {auth.CurrentUser.Email}");
    }
}
```

### **🌍 Real-World Example**
The player launches the game, and instead of forcing them to log in every time, Firebase automatically restores their session.

---

## **3. Anonymous Authentication**
Anonymous sign-in lets players play **without creating an account**, but their data is still linked.

```csharp
public async Task SignInAnonymously()
{
    var result = await auth.SignInAnonymouslyAsync();
    user = result.User;
    Debug.Log("Signed in anonymously: " + user.UserId);
}
```

### **🌍 Real-World Example**
A new player installs the game and starts playing **without registering**. Later, they decide to create an account.  
You can **link** their anonymous account to an email/password login:

```csharp
public async Task LinkAccount(string email, string password)
{
    Credential credential = EmailAuthProvider.GetCredential(email, password);
    await auth.CurrentUser.LinkWithCredentialAsync(credential);
    Debug.Log("Anonymous account linked to: " + email);
}
```

---

## **4. Password Reset**
If a player forgets their password, Firebase lets them reset it.

```csharp
public async Task ResetPassword(string email)
{
    await auth.SendPasswordResetEmailAsync(email);
    Debug.Log($"Password reset email sent to {email}");
}
```

### **🌍 Real-World Example**
A returning player forgets their password. They enter their email, receive a **reset link**, and create a new password.

---

## **5. Social Sign-In (Google, Facebook, Apple)**
You can enable Google or Facebook sign-in by:
1. Enabling it in Firebase Console.
2. Integrating the respective SDKs.
3. Using FirebaseAuth to authenticate.

Example for Google Sign-In:
```csharp
// Requires Google Sign-In SDK
public async Task SignInWithGoogle()
{
    Credential credential = GoogleAuthProvider.GetCredential("google-id-token", null);
    var result = await auth.SignInWithCredentialAsync(credential);
    Debug.Log("Signed in with Google: " + result.User.Email);
}
```

### **🌍 Real-World Example**
Instead of creating a password, a player taps **"Sign in with Google"**, and Firebase authenticates them using their Google account.

---

# **6. Storing and Using User Data**
Once a player is authenticated, you can store game progress in Firestore using their `user.UserId`:

```csharp
private FirebaseFirestore db = FirebaseFirestore.DefaultInstance;

public async Task SavePlayerData(string userId, int score)
{
    DocumentReference docRef = db.Collection("users").Document(userId);
    await docRef.SetAsync(new { score = score });
    Debug.Log("Player data saved!");
}
```

### **🌍 Real-World Example**
A player reaches **level 5** and earns **1000 points**. This data is stored in Firestore under their unique user ID.

---

# **7. Handling Authentication Errors**
Always handle authentication errors properly.

```csharp
catch (FirebaseAuthException e)
{
    switch (e.ErrorCode)
    {
        case AuthError.MissingEmail:
            Debug.LogError("Email is missing.");
            break;
        case AuthError.WeakPassword:
            Debug.LogError("Weak password. Try a stronger one.");
            break;
        case AuthError.WrongPassword:
            Debug.LogError("Incorrect password.");
            break;
        default:
            Debug.LogError($"Authentication Error: {e.Message}");
            break;
    }
}
```

---

# **Conclusion**
Firebase Authentication provides a flexible and secure way to manage user logins in your Unity game. Here’s a quick recap of what you can do:
✅ **Sign up users**  
✅ **Log in users (Email, Google, Anonymous, etc.)**  
✅ **Keep users signed in**  
✅ **Allow users to reset passwords**  
✅ **Store player progress linked to user accounts**  

By implementing these authentication methods, you can provide a **seamless login experience** and ensure players' data is always secure and accessible across devices. 🚀  
