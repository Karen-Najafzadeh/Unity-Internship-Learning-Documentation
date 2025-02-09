Below is a robust, step-by-step tutorial on implementing offline (local) notifications in Unity for Android using the `Unity.Notifications.Android` namespace. This guide covers everything from installation and setup to scheduling, canceling, and customizing notifications. Detailed examples and explanations are provided throughout.

---

# **Offline Notifications in Unity for Android**

Local (offline) notifications are a great way to re-engage users by delivering reminders or updates even when your app isn’t running. Unity’s Android Notifications package makes it simple to schedule and manage these notifications.

> **Note:** This tutorial assumes you’re developing for Android and have a basic understanding of Unity’s workflow.

---

## **1. Prerequisites**

Before you begin, ensure that:
- You have Unity 2018.3 or later.
- Your project is set up for Android build.
- You have installed the **Unity Android Notifications** package.

### **Installing the Android Notifications Package**

1. Open **Window > Package Manager**.
2. In the Package Manager, search for **"Unity Android Notifications"**.
3. Click **Install** to add the package to your project.

Once installed, you can include the namespace:

```csharp
using Unity.Notifications.Android;
```

---

## **2. Setting Up a Notification Channel**

On Android, notifications must be sent on a specific **notification channel**. You need to create and register a channel before scheduling any notifications.

### **Example: Creating a Notification Channel**

Create a new script (e.g., `NotificationChannelManager.cs`) and add the following code:

```csharp
using UnityEngine;
using Unity.Notifications.Android;

public class NotificationChannelManager : MonoBehaviour
{
    // Define a unique channel ID.
    public const string ChannelId = "offline_notifications";

    void Awake()
    {
        // Create a new notification channel.
        var channel = new AndroidNotificationChannel()
        {
            Id = ChannelId,
            Name = "Offline Notifications",
            Importance = Importance.Default, // Options: Low, Default, High, Max
            Description = "Notifications to re-engage users when offline.",
        };

        // Register the channel with the system.
        AndroidNotificationCenter.RegisterNotificationChannel(channel);
        Debug.Log("Notification channel registered: " + ChannelId);
    }
}
```

**Explanation:**
- **Channel Properties:**
  - `Id`: A unique string identifier for the channel.
  - `Name`: A user-visible name.
  - `Importance`: Determines how interruptive the notification is (e.g., sound, vibration).
  - `Description`: A brief description for the channel.
- **Registration:**  
  Call `AndroidNotificationCenter.RegisterNotificationChannel(channel)` to register the channel. This must be done before any notifications are scheduled.

---

## **3. Scheduling Offline (Local) Notifications**

Now that the channel is set up, you can schedule notifications to fire at a specific time—even when the app isn’t running.

### **Example: Scheduling a Notification**

Create a new script (e.g., `LocalNotificationManager.cs`) and add the following code:

```csharp
using UnityEngine;
using System;
using Unity.Notifications.Android;

public class LocalNotificationManager : MonoBehaviour
{
    void Start()
    {
        // Schedule a notification to fire 5 minutes from now.
        ScheduleNotification(5);
    }

    /// <summary>
    /// Schedules a local notification to fire after the specified delay in minutes.
    /// </summary>
    /// <param name="delayInMinutes">Delay before the notification fires.</param>
    public void ScheduleNotification(int delayInMinutes)
    {
        // Create a new AndroidNotification.
        var notification = new AndroidNotification
        {
            Title = "We miss you!",
            Text = "Come back and play to earn rewards!",
            SmallIcon = "default",   // Ensure you have an appropriate icon set up in your project
            LargeIcon = "default",   // Optional: Larger icon for devices that support it
            FireTime = DateTime.Now.AddMinutes(delayInMinutes), // Set fire time to current time + delay
            // Optionally, add additional settings:
            // RepeatInterval = new TimeSpan(1, 0, 0, 0) // For daily notifications
        };

        // Send the notification on the previously registered channel.
        int notificationId = AndroidNotificationCenter.SendNotification(notification, NotificationChannelManager.ChannelId);
        Debug.Log("Notification scheduled with ID: " + notificationId);
    }
}
```

**Explanation:**
- **Notification Properties:**
  - `Title` and `Text`: What the user will see.
  - `SmallIcon`/`LargeIcon`: The icons used in the notification (ensure these are configured in your Android resources).
  - `FireTime`: The exact time the notification will be delivered.
- **Scheduling:**  
  `AndroidNotificationCenter.SendNotification(notification, channelId)` schedules the notification. The returned `notificationId` can be used later to manage the notification (e.g., cancellation).
- **RepeatInterval (Optional):**  
  If you want the notification to repeat (e.g., daily), set the `RepeatInterval` property.

---

## **4. Canceling and Managing Notifications**

You may want to cancel notifications—for example, if a user opens the app before the notification fires. The Android Notifications package provides several methods to manage notifications.

### **Example: Canceling a Specific Notification**

```csharp
using UnityEngine;
using Unity.Notifications.Android;

public class NotificationCanceler : MonoBehaviour
{
    /// <summary>
    /// Cancels a notification with the given ID.
    /// </summary>
    /// <param name="notificationId">The ID of the notification to cancel.</param>
    public void CancelNotification(int notificationId)
    {
        AndroidNotificationCenter.CancelNotification(notificationId);
        Debug.Log("Notification canceled: " + notificationId);
    }
}
```

### **Example: Canceling All Notifications**

```csharp
using UnityEngine;
using Unity.Notifications.Android;

public class NotificationCancelerAll : MonoBehaviour
{
    public void CancelAllNotifications()
    {
        AndroidNotificationCenter.CancelAllScheduledNotifications();
        Debug.Log("All scheduled notifications have been canceled.");
    }
}
```

**Explanation:**
- **Canceling by ID:**  
  Use `AndroidNotificationCenter.CancelNotification(notificationId)` to cancel a specific notification.
- **Canceling All:**  
  Use `AndroidNotificationCenter.CancelAllScheduledNotifications()` to remove all pending notifications.

---

## **5. Handling Notification Clicks**

When a user taps a notification, you may want to handle that event. Unity provides methods to check if the app was launched by a notification.

### **Example: Handling Notification Launch Data**

Add the following code (typically in a startup script such as `NotificationHandler.cs`):

```csharp
using UnityEngine;
using Unity.Notifications.Android;

public class NotificationHandler : MonoBehaviour
{
    void Start()
    {
        // Check if the app was launched by a notification.
        var notificationIntentData = AndroidNotificationCenter.GetLastNotificationIntent();
        if (notificationIntentData != null)
        {
            Debug.Log("App launched from notification!");
            // You can retrieve details from the notification:
            Debug.Log("Notification Title: " + notificationIntentData.Notification.Title);
            Debug.Log("Notification Text: " + notificationIntentData.Notification.Text);

            // Perform actions based on the notification data (e.g., navigate to a specific screen)
        }
    }
}
```

**Explanation:**
- **GetLastNotificationIntent():**  
  Returns data about the notification that launched the app. If not null, you can inspect its properties to decide how to handle the launch.

---

## **6. Best Practices and Additional Tips**

- **Testing Notifications:**  
  When testing on a physical device, ensure notifications are enabled for your app. Emulators may behave differently.
- **Time Zones and FireTime:**  
  Use `DateTime.Now` carefully. Consider user time zones if scheduling notifications for global audiences.
- **Channel Management:**  
  Create separate channels for different types of notifications (e.g., promotions, reminders) so users can control them in Android settings.
- **Notification Customization:**  
  Experiment with properties like vibration patterns, sound, and custom icons to create an engaging notification experience.
- **App Lifecycle:**  
  Clear notifications on app launch if they’re no longer relevant, to avoid cluttering the notification tray.

---

## **7. Putting It All Together**

A typical implementation might include:
1. **Channel Creation:**  
   A manager script that registers channels during app startup.
2. **Scheduling Notifications:**  
   A script that schedules offline notifications based on game events (e.g., after a level ends or when the user exits).
3. **Handling Clicks:**  
   A script that checks if the app was launched from a notification and navigates accordingly.
4. **Cancellation:**  
   Canceling notifications when the user returns to the app to avoid redundant alerts.

By combining these scripts, you create a robust offline notification system that can re-engage your users even when your app isn’t running.

---


## **Providing More Data for Notifications:**  
   You can supply additional information when creating a notification. Although the Unity Android Notifications API has a set of predefined fields (such as `Title`, `Text`, `SmallIcon`, `LargeIcon`, `TickerText`, and `FireTime`), you can also leverage certain fields or techniques to associate extra data with a notification. For example:  
   - **Using Extended Fields:**  
     Some versions of the API (or custom implementations) let you add extra information such as a badge number (`Number`) or even use properties like `TickerText` to give users a brief preview.  
   - **Custom Data via Notification IDs or an External Mapping:**  
     While the notification itself may not have a dedicated “payload” property like iOS’s `userInfo`, you can design your notification-management system to store additional data (such as a JSON string, or simply a type identifier) in a centralized manager. When the notification is clicked and your app is launched via `GetLastNotificationIntent()`, you can use the notification ID (or a custom field) to look up the extra data you stored in your own dictionary or configuration.  

## **Managing a Growing Number of Notifications:**  
   As your app scales and you have many different types of notifications, keeping track of which notification ID corresponds to what type (or what in-app action) is essential. There are several strategies you can use:  

   - **Use Enums for Notification Types:**  
     Defining an `enum` for your different notification types makes your code more readable and helps ensure that each type has a unique, well-known value. For example:
     
     ```csharp
     public enum NotificationType
     {
         DailyReminder = 1000,
         SpecialOffer = 2000,
         FriendRequest = 3000,
         AchievementUnlocked = 4000
     }
     ```

     Here, each type is given a unique starting ID (you can choose the numbers to allow room for multiple notifications per type if needed). For instance, if you intend to have only one active notification of each type, you might directly cast the enum to an integer when scheduling.  
     
   - **Maintain a Mapping or Manager Class:**  
     In addition to using enums, you can create a manager class that keeps track of notification IDs and any associated extra data. This manager can store a dictionary that maps a notification ID to a custom data object or even a notification type. This way, if you need to cancel or update a particular notification, you know exactly which ID to use.
     
   - **Overwriting Existing Notifications:**  
     If you want to ensure that only one notification per type exists at a time, use the same ID when scheduling a notification. Scheduling a new notification with an existing ID typically overwrites the previous one (or you can explicitly cancel it first).

---

## **Example: Using Enums and a Notification Manager**

Below is a code example that demonstrates how to:  
- Define an enum for notification types.  
- Schedule notifications with a unique ID based on that enum.  
- Maintain a dictionary for extra data (if needed) so that when a notification is received or clicked, you can look up the associated information.

### **Step 1. Define an Enum for Notification Types**

```csharp
public enum NotificationType
{
    DailyReminder = 1000,
    SpecialOffer = 2000,
    FriendRequest = 3000,
    AchievementUnlocked = 4000
}
```

### **Step 2. Create a Notification Manager**

This manager will both schedule notifications (with extra information) and track them by using the enum-based IDs.

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using Unity.Notifications.Android;

public class NotificationManager : MonoBehaviour
{
    // Use a dictionary to map notification IDs to additional custom data.
    // For example, you might store a simple string identifier or JSON.
    private Dictionary<int, string> notificationDataMap = new Dictionary<int, string>();

    // Define a channel ID (assume you have already registered this channel elsewhere).
    private const string ChannelId = "offline_notifications";

    /// <summary>
    /// Schedules a notification with extra custom data.
    /// </summary>
    /// <param name="type">The type of notification (from the NotificationType enum).</param>
    /// <param name="delayInMinutes">Delay in minutes until the notification fires.</param>
    /// <param name="extraData">Extra custom data to associate with the notification.</param>
    public void ScheduleNotification(NotificationType type, int delayInMinutes, string extraData = "")
    {
        // Create a new notification.
        var notification = new AndroidNotification
        {
            Title = "Notification: " + type.ToString(),
            Text = "Tap here to see more details about " + type.ToString() + ".",
            SmallIcon = "default",  // Ensure you have proper icons set up.
            LargeIcon = "default",
            FireTime = DateTime.Now.AddMinutes(delayInMinutes),
            // Optionally, you can set RepeatInterval for recurring notifications.
        };

        // Use the enum value (cast to int) as the notification ID.
        int notifId = (int)type;

        // Schedule the notification.
        AndroidNotificationCenter.SendNotification(notification, ChannelId);

        // Store the extra data associated with this notification.
        if (!string.IsNullOrEmpty(extraData))
        {
            if (notificationDataMap.ContainsKey(notifId))
                notificationDataMap[notifId] = extraData;
            else
                notificationDataMap.Add(notifId, extraData);
        }

        Debug.Log($"Scheduled notification (ID: {notifId}, Type: {type}) to fire in {delayInMinutes} minutes.");
    }

    /// <summary>
    /// Retrieve extra data associated with a given notification ID.
    /// </summary>
    public string GetExtraData(int notifId)
    {
        if (notificationDataMap.TryGetValue(notifId, out string data))
            return data;
        return string.Empty;
    }

    /// <summary>
    /// Cancel a notification by its enum type.
    /// </summary>
    public void CancelNotification(NotificationType type)
    {
        int notifId = (int)type;
        AndroidNotificationCenter.CancelNotification(notifId);
        if (notificationDataMap.ContainsKey(notifId))
            notificationDataMap.Remove(notifId);
        Debug.Log("Canceled notification with ID: " + notifId);
    }
}
```

### **Step 3. Using the Notification Manager**

Attach the `NotificationManager` script to a GameObject in your scene. Then, from another script (or via UI interactions), you can schedule, cancel, or retrieve extra data for notifications.

```csharp
using UnityEngine;

public class NotificationDemo : MonoBehaviour
{
    public NotificationManager notificationManager;

    void Start()
    {
        if (notificationManager == null)
        {
            Debug.LogError("NotificationManager reference is missing!");
            return;
        }

        // Example: Schedule a daily reminder to fire in 2 minutes with extra data.
        notificationManager.ScheduleNotification(NotificationType.DailyReminder, 2, "DailyBonus=100");

        // Example: Schedule a special offer notification to fire in 5 minutes.
        notificationManager.ScheduleNotification(NotificationType.SpecialOffer, 5, "{ \"discount\": 25, \"item\": \"Premium Skin\" }");

        // Later on, you could retrieve the extra data by using the notification ID:
        // string extra = notificationManager.GetExtraData((int)NotificationType.DailyReminder);
        // Debug.Log("Extra Data for DailyReminder: " + extra);
    }
}
```

---

## **Summary**

- **Providing More Data:**  
  You can add extra information to a notification by either using the available fields (like `TickerText`) or—more robustly—by maintaining a mapping (e.g., via a dictionary) between a notification’s unique ID and its associated extra data. This is especially useful when the notification itself has limited fields for custom payloads.

- **Managing Large Numbers of Notifications:**  
  When notifications grow in number, using an `enum` to define unique notification types (and their associated base IDs) is a great way to keep track of them. Combine that with a manager class that maps IDs to extra data, and you have a scalable system for scheduling, canceling, and processing notifications.

Using these techniques, you can robustly manage offline notifications in your Unity project, ensuring that each notification carries the necessary context and is easily tracked even as the system grows. Happy coding!

## **8. Final Thoughts**

Using the `Unity.Notifications.Android` namespace, you can efficiently manage local notifications to re-engage your audience. This tutorial has walked you through creating a channel, scheduling notifications, handling user interaction, and managing notifications—all with detailed code examples and explanations.

Experiment with these examples and tailor them to your app’s needs. With offline notifications properly set up, you can significantly enhance user engagement and retention in your Unity projects!

Happy coding!