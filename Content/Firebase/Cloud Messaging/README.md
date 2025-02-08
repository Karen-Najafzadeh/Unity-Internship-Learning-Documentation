

### **📱 What is Firebase Cloud Messaging (FCM)?**

**Firebase Cloud Messaging (FCM)** is a free service provided by Firebase that enables you to send messages (push notifications) to users across iOS, Android, and the web. 

You can send:
- **Notification messages** (simple messages that trigger a visual notification)
- **Data messages** (messages that trigger custom app behavior without displaying a notification)

### **🔧 Getting Started with Firebase Cloud Messaging in Unity**

#### **1️⃣ Setting Up Firebase Cloud Messaging in Unity**

Before integrating FCM, ensure you've set up Firebase in your Unity project (authentication, Firestore, etc.). If not, follow the initial Firebase setup steps.

**Steps:**
1. **Install Firebase SDK**:
   - Make sure you’ve installed the Firebase SDK for Unity.
   - Download the **Firebase Unity SDK** from the [Firebase Console](https://console.firebase.google.com/).

2. **Enable Cloud Messaging** in Firebase Console:
   - Go to your Firebase Console.
   - Select your project.
   - In the left panel, select **Cloud Messaging** under the **Grow** section.
   - Here, you’ll find your **Server key** and **Sender ID**, which are necessary for sending messages.

3. **Configure Unity for Push Notifications**:
   - For **Android**: Ensure your app has the correct permissions and settings in the `AndroidManifest.xml`.
   - For **iOS**: Set up the Apple Push Notification service (APNs) for push notifications in the Apple Developer Console.

4. **Download the Google-Services.json** (for Android) or **GoogleService-Info.plist** (for iOS) from Firebase and place them in the respective folder in your Unity project.

#### **2️⃣ Initializing Firebase Messaging**

Now that you have Firebase set up, let's write code to initialize Firebase and start listening for messages.

**Example: Firebase Messaging Initialization**

```csharp
using Firebase;
using Firebase.Messaging;
using UnityEngine;

public class FirebaseMessagingHandler : MonoBehaviour
{
    // This method is called when the app starts
    void Start()
    {
        // Initialize Firebase
        FirebaseApp.CheckAndFixDependenciesAsync().ContinueWith(task =>
        {
            FirebaseApp app = FirebaseApp.DefaultInstance;

            // Initialize Firebase Cloud Messaging
            FirebaseMessaging.TokenReceived += OnTokenReceived;
            FirebaseMessaging.MessageReceived += OnMessageReceived;

            // Get the FCM token (required for sending notifications)
            FirebaseMessaging.GetTokenAsync().ContinueWith(tokenTask =>
            {
                if (tokenTask.IsCompleted)
                {
                    string token = tokenTask.Result;
                    Debug.Log("FCM Token: " + token);
                    // Store this token to send notifications later
                }
                else
                {
                    Debug.LogError("Failed to get FCM token.");
                }
            });
        });
    }

    // Handle when a notification token is received (for device registration)
    private void OnTokenReceived(object sender, TokenReceivedEventArgs e)
    {
        Debug.Log("Received FCM token: " + e.Token);
    }

    // Handle when a message is received
    private void OnMessageReceived(object sender, MessageReceivedEventArgs e)
    {
        // e.Message contains the notification message
        if (e.Message.Notification != null)
        {
            Debug.Log("Received notification: " + e.Message.Notification.Title);
            Debug.Log("Message body: " + e.Message.Notification.Body);
        }

        // You can handle other message types like data messages here
    }

    // This method is called when the app is closed or in the background
    void OnApplicationPause(bool pauseStatus)
    {
        if (pauseStatus)
        {
            FirebaseMessaging.Dispose();
        }
    }
}
```

In the above code:
- The `FirebaseApp.CheckAndFixDependenciesAsync()` method ensures that all Firebase dependencies are resolved.
- The `FirebaseMessaging.GetTokenAsync()` method retrieves a unique device token required to send messages to that specific device.
- The `TokenReceived` and `MessageReceived` events are used to handle incoming messages.

---

### **3️⃣ Sending Push Notifications**

You can send notifications either directly from the Firebase Console or programmatically via the **Firebase Cloud Messaging API**.

#### **Sending Notifications from the Firebase Console**:

- Go to **Firebase Console** → **Cloud Messaging**.
- Click on **Send your first message**.
- You can create a notification by providing a title, message, and optional image URL.
- Choose the target: send to **all devices**, a **specific device**, or a **topic**.
- Click **Send**.

#### **Sending Notifications Programmatically**:

If you want to send notifications directly from your backend, you’ll need to use Firebase’s HTTP API to send push notifications.

**Example of Sending Notifications using FCM HTTP API**:

Here’s a simple example of sending a push notification to a specific device using **UnityWebRequest**:

```csharp
using UnityEngine;
using UnityEngine.Networking;
using System.Collections;
using System.Text;

public class FCMNotifier : MonoBehaviour
{
    private string serverKey = "YOUR_SERVER_KEY";  // Replace with your Firebase server key
    private string fcmUrl = "https://fcm.googleapis.com/fcm/send";

    public void SendNotification(string deviceToken, string title, string message)
    {
        StartCoroutine(SendNotificationCoroutine(deviceToken, title, message));
    }

    private IEnumerator SendNotificationCoroutine(string deviceToken, string title, string message)
    {
        var jsonMessage = new
        {
            to = deviceToken,
            notification = new
            {
                title = title,
                body = message
            }
        };

        string json = JsonUtility.ToJson(jsonMessage);

        using (UnityWebRequest request = new UnityWebRequest(fcmUrl, "POST"))
        {
            byte[] bodyRaw = Encoding.UTF8.GetBytes(json);
            request.uploadHandler = new UploadHandlerRaw(bodyRaw);
            request.downloadHandler = new DownloadHandlerBuffer();
            request.SetRequestHeader("Authorization", "key=" + serverKey);
            request.SetRequestHeader("Content-Type", "application/json");

            yield return request.SendWebRequest();

            if (request.result == UnityWebRequest.Result.Success)
            {
                Debug.Log("Push notification sent successfully");
            }
            else
            {
                Debug.LogError("Error sending push notification: " + request.error);
            }
        }
    }
}
```

- The `serverKey` is your Firebase server key from the Firebase Console.
- The `deviceToken` is the token you obtained for the device.
- The `notification` field contains the content of your push notification (title and body).

---

### **4️⃣ Handling Notification Clicks**

When users click on a notification, you can define custom behavior, such as navigating them to a specific scene or opening a specific UI. Unity provides a callback for handling the click of the notification.

**Example: Handling Notification Clicks in Unity**

```csharp
using Firebase.Messaging;
using UnityEngine;

public class NotificationHandler : MonoBehaviour
{
    // When the app is in the foreground
    private void OnMessageReceived(object sender, MessageReceivedEventArgs e)
    {
        if (e.Message.Notification != null)
        {
            Debug.Log("Received Notification: " + e.Message.Notification.Title);
            // Handle the notification click or show custom UI
        }
    }

    // When the app is in the background or closed
    private void OnApplicationPause(bool isPaused)
    {
        if (!isPaused)
        {
            FirebaseMessaging.MessageReceived += OnMessageReceived;
        }
        else
        {
            FirebaseMessaging.MessageReceived -= OnMessageReceived;
        }
    }
}
```

---

### **5️⃣ Managing Notifications**

Firebase allows you to target notifications in various ways:
- **Targeting specific devices** (using device tokens).
- **Targeting topics** (to send a notification to all users subscribed to a specific topic).
- **Targeting user segments** (by sending notifications based on user properties).

You can subscribe users to topics and send notifications to a topic like so:

```csharp
FirebaseMessaging.SubscribeAsync("game-updates");
```

On the backend, you can send notifications to the **game-updates** topic, and all devices subscribed to that topic will receive the notification.

---

### **⚡ Advanced Topics with Firebase Cloud Messaging**

- **Topic Messaging**: Allows sending messages to groups of devices based on topics (e.g., "events", "news", "promotions").
- **Custom Data Messages**: Instead of just showing notifications, you can send custom data and process it in your app.
- **Handling Background Messages**: FCM supports receiving notifications even when the app is in the background. You can handle background notifications differently by overriding the appropriate methods.

---

By integrating Firebase Cloud Messaging, you can keep your players engaged and informed by sending them timely notifications, whether they’re in the game or outside it. If you need further help with advanced use cases or more detailed steps, feel free to ask!