
# Integratin Nakama into your project 
To start using nakama into your project, You'll need to use the Nakama SDK in your project.

## Download and import Nakama SDK
  You can download the SDK from [Unity asset store](https://assetstore.unity.com/packages/tools/network/nakama-81338) or the [github page](https://github.com/heroiclabs/nakama-unity/releases/tag/v3.14.0).

  Once you downloaded the SDK, import it into your project.

## Using Nakama in the project

To start using Nakama and its features, first of all you're gonna need to create a new client at the game startup, and then authenticate the user. then and only then you can start using Nakama. To do so, create a file responsible for managing nakama (suggested to use [singletone pattern](../../Design-Patterns/Singletone-Pattern/)). Let's call it **NakamaManager.cs**

## Initialize the Client:

In the **NakamaManager.cs**, set up the client to connect to your Nakama server. For instance,:

  ```csharp
  var client = new Client("http", "localhost", 7350, "defaultkey");
  ```

## Authenticate Users:

Nakama supports various authentication methods:

- **Device Authentication:** Ideal for guest users or when you don't require a permanent account.

  - **Process:** Authenticate using a unique device identifier.

  - **Example:**

    ```csharp
    var deviceId = SystemInfo.deviceUniqueIdentifier;
    var session = await client.AuthenticateDeviceAsync(deviceId, true, "custom_username");
    // the true parameter means if there is no account corresponding with the unique id provided, The Nakama client will create one
    ```

- **Email and Password Authentication:** Suitable for users who prefer traditional login methods.

  - **Process:** Authenticate using an email address and password.

  - **Example:**

    ```csharp
    var email = "user@example.com";
    var password = "securepassword";
    var session = await client.AuthenticateEmailAsync(email, password, true, "custom_username");
    ```

- **Social Provider Authentication:** Allows users to log in using their social media accounts.

  - **Process:** Authenticate using tokens from services like Facebook, Google, or Apple.

  - **Example:**

    ```csharp
    var oauthToken = "your_social_oauth_token_like_facebook_token";
    var session = await client.AuthenticateFacebookAsync(oauthToken, true, "custom_username");
    ```

## Manage User Sessions:

- **Session Object:** Upon successful authentication, Nakama returns a session object containing user details and an authentication token.

- **Usage:** Use this session token for subsequent requests to authenticate the user.

By following these steps, you can effectively integrate Nakama into your project and manage user authentication. 

## Advanced Example:

Here is an advanced Example of how to create a client and authenticate users with nakama by the user's device ID. this script first checks if the session is still valid. if so, it will restore it, and if not, it will reauthenticate the user and save the auth token and the refresh token to the player prefs.
~~~csharp
using UnityEngine;
using Nakama;
public class NakamaManager : SingleToneClass<NakamaManager>{

    private IClient client;
    private ISession session;
    [SerializeField] private string localScheme = "http";
    [SerializeField] private string localServerKey = "defaultkey";
    [SerializeField] private string localHost = "127.0.0.1"; 
    [SerializeField] private int localPort = 7350;
    private const string SessionAuthToken = "nakama.authToken"; 
    private const string SessionRefreshToken = "nakama.refreshToken";
    private const string DeviceIdPrefsKey = "device_id";

    /// <summary>
    /// Initializes Nakama client and authenticates or resumes session on start.
    /// </summary>
    private async void Start() {
        InitializeNakamaClient();
        await AuthenticateOrResumeSession();
    }

    /// <summary>
    /// Initializes the Nakama client with the specified configuration.
    /// </summary>
    private void InitializeNakamaClient() {
        try {
            client = new Client(localScheme, localHost, localPort, localServerKey);
            Debug.Log("Nakama client initialized successfully.");
        } catch (Exception ex) {
            Debug.LogError($"Failed to initialize Nakama client: {ex.Message}");
        }
    }

    /// <summary>
    /// Authenticates the user or resumes an existing session.
    /// </summary>
    public async Task AuthenticateOrResumeSession() {
        if (TryResumeSession()) {
            // there is a valid existing session so we carry on with it
            Debug.Log("Resumed existing session.");
            return;
        }

        // if there is no valid session and the previous session is expiered, we authenticate the user by their device id
        string deviceId = SystemInfo.deviceUniqueIdentifier;
        PlayerPrefs.SetString(DeviceIdPrefsKey, deviceId);
        PlayerPrefs.Save();

        session = await AuthenticateUser(deviceId);
        SaveSessionTokens();
        Debug.Log($"Authenticated with new session. User ID: {session.UserId}, Username: {session.Username}, AuthToken: {session.AuthToken}");
    }

    /// <summary>
    /// Tries to resume an existing session using stored tokens.
    /// </summary>
    /// <returns>True if session is resumed successfully, otherwise false.</returns>
    private bool TryResumeSession() {
        string sessionAuthToken = PlayerPrefs.GetString(SessionAuthToken, null);
        string refreshToken = PlayerPrefs.GetString(SessionRefreshToken, null);
        Debug.Log($"SessionAuthToken: {sessionAuthToken}, RefreshToken: {refreshToken}");

        if (!string.IsNullOrEmpty(sessionAuthToken) && !string.IsNullOrEmpty(refreshToken)) {
            session = Session.Restore(sessionAuthToken, refreshToken);
            Debug.Log($"Session restored. IsExpired: {session.IsExpired}");
            if (!session.IsExpired) {
                return true;
            } else {
                Debug.Log("Session is expired.");
            }
        } else {
            Debug.Log("Session tokens are missing or invalid.");
        }
        return false;
    }

    /// <summary>
    /// Authenticates the user using the device ID.
    /// </summary>
    /// <param name="deviceId">The device ID.</param>
    /// <returns>The authenticated session.</returns>
    private async Task<ISession> AuthenticateUser(string deviceId) {
        Debug.Log("Authenticating with device ID...");
        var newSession = await client.AuthenticateDeviceAsync(deviceId);
        return newSession;
    }

    /// <summary>
    /// Saves the session tokens to PlayerPrefs.
    /// </summary>
    private void SaveSessionTokens() {
        PlayerPrefs.SetString(SessionRefreshToken, session.RefreshToken);
        PlayerPrefs.SetString(SessionAuthToken, session.AuthToken);
        PlayerPrefs.Save();
    }
~~~
