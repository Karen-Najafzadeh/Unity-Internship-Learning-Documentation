Below is an in-depth tutorial for integrating and using the Facebook SDK in Unity. This guide covers everything from installation to advanced features and best practices, similar to the GameAnalytics tutorial.

---

# Comprehensive Guide to Integrating Facebook SDK in Unity

The Facebook SDK for Unity lets you integrate Facebook features into your game, including login, social sharing, App Events, and deep linking. This integration can help you enhance user engagement, gather analytics, and foster community growth.

## Table of Contents
1. [Introduction](#introduction)
2. [Installation and Setup](#installation-and-setup)
3. [Configuration](#configuration)
4. [Core Features](#core-features)
   - [Facebook Login](#facebook-login)
   - [Sharing Content](#sharing-content)
   - [App Events and Analytics](#app-events-and-analytics)
   - [Deep Linking](#deep-linking)
5. [Platform-Specific Configuration](#platform-specific-configuration)
6. [Debugging and Testing](#debugging-and-testing)
7. [Best Practices and Troubleshooting](#best-practices-and-troubleshooting)
8. [Conclusion](#conclusion)

---

## Introduction

The Facebook SDK for Unity allows you to connect your game with Facebook’s platform. By integrating Facebook features, you can:
- Allow users to log in using their Facebook account.
- Enable social sharing to increase your game’s reach.
- Track user actions and gather analytics through App Events.
- Implement deep linking to direct users to specific in-game content.

---

## Installation and Setup

### 1. Download the SDK
- **Official Source:** Visit the [Facebook for Developers website](https://developers.facebook.com/docs/unity) to download the latest Unity package for the Facebook SDK.
- **Unity Asset Store:** Alternatively, you might find the SDK listed in the Unity Asset Store.

### 2. Import the SDK into Unity
1. In Unity, go to **Assets** → **Import Package** → **Custom Package...**
2. Select the downloaded Facebook SDK package.
3. Click **Import** to add all necessary assets to your project.

### 3. Create a Facebook App
1. Log in to the [Facebook Developers](https://developers.facebook.com/) portal.
2. Click **My Apps** and select **Create App**.
3. Choose the appropriate app type (e.g., “Consumer”) and provide the required details.
4. Once created, note the **App ID**—this will be used in your Unity project.

---

## Configuration

### 1. Configuring Facebook Settings in Unity
- Locate the **FacebookSettings** asset (usually found in `Assets/FacebookSDK/Resources/`).
- Enter your **Facebook App ID** and other required details, such as the app name.
- Configure permissions based on the features you intend to use (for example, requesting `public_profile` and `email` for login).

### 2. Initialize the SDK
Add initialization code to your startup scene. For example, in a script attached to your main GameObject:

```csharp
using UnityEngine;
using Facebook.Unity;
using System.Collections.Generic;

public class FacebookManager : MonoBehaviour
{
    void Awake()
    {
        if (!FB.IsInitialized)
        {
            FB.Init(InitCallback, OnHideUnity);
        }
        else
        {
            FB.ActivateApp();
        }
    }

    private void InitCallback()
    {
        if (FB.IsInitialized)
        {
            FB.ActivateApp();
            Debug.Log("Facebook SDK Initialized Successfully.");
        }
        else
        {
            Debug.LogError("Failed to Initialize the Facebook SDK");
        }
    }

    private void OnHideUnity(bool isGameShown)
    {
        Time.timeScale = isGameShown ? 1 : 0;
    }
}
```

---

## Core Features

### Facebook Login

Enable users to sign in with their Facebook account:

```csharp
public void FacebookLogin()
{
    var permissions = new List<string>() { "public_profile", "email" };
    FB.LogInWithReadPermissions(permissions, AuthCallback);
}

private void AuthCallback(ILoginResult result)
{
    if (FB.IsLoggedIn)
    {
        var aToken = Facebook.Unity.AccessToken.CurrentAccessToken;
        Debug.Log("Facebook Login Success! User ID: " + aToken.UserId);
    }
    else
    {
        Debug.Log("User cancelled Facebook login");
    }
}
```

### Sharing Content

Allow users to share links or images on their timeline:

```csharp
public void ShareLink()
{
    FB.ShareLink(
        new System.Uri("https://yourgamewebsite.com"),
        "Check out this awesome game!",
        "I’m playing this great game, and you should try it too.",
        new System.Uri("https://yourgamewebsite.com/image.png"),
        ShareCallback
    );
}

private void ShareCallback(IShareResult result)
{
    if (result.Cancelled || !string.IsNullOrEmpty(result.Error))
    {
        Debug.Log("ShareLink Error: " + result.Error);
    }
    else if (!string.IsNullOrEmpty(result.PostId))
    {
        Debug.Log("Share succeeded with post id: " + result.PostId);
    }
    else
    {
        Debug.Log("Share succeeded");
    }
}
```

### App Events and Analytics

Track user behavior by logging App Events:

```csharp
public void LogLevelComplete(int levelNumber)
{
    var parameters = new Dictionary<string, object>();
    parameters["level"] = levelNumber;
    
    FB.LogAppEvent(
        "level_complete", 
        1.0f, 
        parameters
    );
    Debug.Log("Logged level completion event for level: " + levelNumber);
}
```

### Deep Linking

Implement deep linking to bring users directly to specific content in your game:
- Configure deep linking settings in the Facebook Developer portal.
- Use the `FB.Mobile.FetchDeferredAppLinkData` method to handle deferred deep links:

```csharp
public void HandleDeepLink()
{
    FB.Mobile.FetchDeferredAppLinkData(DeepLinkCallback);
}

private void DeepLinkCallback(IAppLinkResult result)
{
    if (!string.IsNullOrEmpty(result.Url))
    {
        Debug.Log("Deep link URL: " + result.Url);
        // Parse URL parameters and navigate to specific content
    }
    else
    {
        Debug.Log("No deep link data found.");
    }
}
```

---

## Platform-Specific Configuration

### Android
- Update the **AndroidManifest.xml** to include the necessary permissions and intent filters.
- Add your **Facebook App ID** in the manifest meta-data.

### iOS
- Edit the **Info.plist** to include Facebook-specific keys, such as `FacebookAppID` and URL schemes.
- Make sure to set up the appropriate **iOS capabilities** (e.g., Keychain Sharing).

### WebGL
- Adjust settings for WebGL builds if you plan to support Facebook features on browser-based games.

---

## Debugging and Testing

### Enable Debugging
- Use `Debug.Log` in your scripts to output information about Facebook API calls.
- The Facebook SDK also provides detailed logs in the Unity console when running in development mode.

### Test with Facebook Test Users
- Create test users from the Facebook Developer portal to safely test login and sharing functionalities without affecting your live app.

### Simulator and Device Testing
- Test your integration on both Android and iOS devices, as behavior may differ between the Unity Editor and actual devices.

---

## Best Practices and Troubleshooting

### Best Practices
- **Permission Management:** Request only the permissions you need to reduce friction during login.
- **User Experience:** Provide clear feedback to users during login and sharing processes.
- **Analytics:** Use App Events to track key in-game actions for better insights.
- **Privacy Compliance:** Ensure you follow Facebook’s data usage policies and respect user privacy.

### Troubleshooting Common Issues
- **Initialization Failures:** Check your Internet connection, Facebook App ID, and configuration settings in the Facebook Developer portal.
- **Login Issues:** Verify requested permissions and ensure your Facebook app is set to Live mode when testing with non-test users.
- **Platform-Specific Bugs:** Consult platform-specific documentation (Android/iOS) to resolve issues related to manifest or plist configurations.

---

## Conclusion

Integrating the Facebook SDK into your Unity project opens up a wealth of social features and analytical tools to enhance your game’s user experience and reach. By following this comprehensive guide, you’ll be able to implement Facebook Login, Sharing, App Events, and Deep Linking, while also setting up robust debugging and troubleshooting practices.

For further details and updates, refer to the [official Facebook SDK documentation for Unity](https://developers.facebook.com/docs/unity).
