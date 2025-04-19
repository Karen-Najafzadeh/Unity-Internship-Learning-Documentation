Below is a complete reference for integrating Steam Leaderboards into your Unity game using Steamworks.NET, covering setup, dashboard configuration, code examples, callbacks, best practices, and troubleshooting.

**Summary:** After authenticating the user, you can create or find Steam leaderboards via the Steamworks dashboard and Steamworks.NET APIs, upload player scores, download leaderboard entries (globally, around the user, or among friends), and display them in your UI. Proper use of CallResult handlers, pagination options, and error checks ensures a smooth, reliable experience.

---

## 1. Introduction to Steam Leaderboards

Steam Leaderboards let you rank and compare player scores globally or among friends. They provide a competitive layer and social engagement within your game citeturn1search4.

---

## 2. Prerequisites

- **Steamworks Partner Account & AppID**: Register on Steamworks and obtain your Application ID citeturn2view0.  
- **Steamworks SDK & Steamworks.NET**: Download the native Steamworks SDK via your Steamworks dashboard and import [Steamworks.NET](https://github.com/rlabrecque/Steamworks.NET) into Unity citeturn1search6.  
- **steam_appid.txt**: Place a file named `steam_appid.txt` at the project root containing your AppID citeturn2view0.  
- **SteamManager**: Attach the provided `SteamManager` MonoBehaviour to a GameObject in your first scene to initialize the Steam API citeturn2view0.

---

## 3. Defining Leaderboards in the Steamworks Dashboard

1. **Navigate** to **Steamworks Partner Site → Features → Steam Leaderboards → Step by Step: Leaderboards** citeturn2view0.  
2. **Create a New Leaderboard** on the **Leaderboard Configuration** page by setting:  
   - **Name** (internal ID)  
   - **Community Name** (public display)  
   - **Sort Method**: Ascending for low‑score, Descending for high‑score citeturn2view0.  
   - **Display Type**: Numeric, Seconds, or Milliseconds citeturn2view0.  
   - **Reads/Writes**: Configure trusted writes or friend‑only reads if needed citeturn2view0.

---

## 4. Finding or Creating a Leaderboard in Code

Before uploading or downloading scores, obtain a leaderboard handle asynchronously:

```csharp
using Steamworks;

private static SteamLeaderboard_t s_CurrentLeaderboard;
private static CallResult<LeaderboardFindResult_t> s_FindResult;

public static void InitLeaderboard(string name)
{
    // Async call to find (or create) the leaderboard
    SteamAPICall_t handle = SteamUserStats.FindOrCreateLeaderboard(
        name,
        ELeaderboardSortMethod.k_ELeaderboardSortMethodDescending,
        ELeaderboardDisplayType.k_ELeaderboardDisplayTypeNumeric
    );
    s_FindResult = new CallResult<LeaderboardFindResult_t>();
    s_FindResult.Set(handle, OnFindLeaderboard);
}

// Callback handler when FindOrCreateLeaderboard completes
private static void OnFindLeaderboard(LeaderboardFindResult_t pCallback, bool bIOFailure)
{
    if (bIOFailure || pCallback.m_bLeaderboardFound == 0)
    {
        Debug.LogError("Could not find or create leaderboard");  // bIOFailure indicates I/O issue
        return;
    }
    s_CurrentLeaderboard = pCallback.m_hSteamLeaderboard;
    Debug.Log("Leaderboard initialized: " + s_CurrentLeaderboard.m_SteamLeaderboard);
}
```

- **Use `CallResult<>`** (not `Callback<>`) to prevent garbage collection of handlers citeturn3view0.  
- **`FindOrCreateLeaderboard`** instantly creates the leaderboard if it doesn’t exist citeturn3view0.

---

## 5. Uploading Player Scores

Once you have `s_CurrentLeaderboard`, upload a player’s score:

```csharp
private static CallResult<LeaderboardScoreUploaded_t> s_UploadResult;

public static void UploadScore(int score)
{
    if (s_CurrentLeaderboard == SteamLeaderboard_t.Invalid)
    {
        Debug.LogError("Leaderboard not initialized");
        return;
    }
    SteamAPICall_t handle = SteamUserStats.UploadLeaderboardScore(
        s_CurrentLeaderboard,
        ELeaderboardUploadScoreMethod.k_ELeaderboardUploadScoreMethodKeepBest,
        score,
        null, 0
    );
    s_UploadResult = new CallResult<LeaderboardScoreUploaded_t>();
    s_UploadResult.Set(handle, OnScoreUploaded);
}

private static void OnScoreUploaded(LeaderboardScoreUploaded_t pCallback, bool bIOFailure)
{
    if (bIOFailure || pCallback.m_bSuccess == 0)
    {
        Debug.LogError("Failed to upload score"); 
    }
    else
    {
        Debug.Log($"New Rank: {pCallback.m_nGlobalRankNew}, Score: {pCallback.m_nScore}");
    }
}
```

- **`KeepBest`** ensures only the player’s highest score is stored.  
- **Store additional user data** (e.g., replay IDs) via the `details` parameters if desired.

*References: ﹘ API usage and callbacks from native sample citeturn2view0, C# example from NCoso citeturn3view0.*

---

## 6.1 Downloading and Displaying Leaderboard Entries

Retrieve entries around the user or globally:

```csharp
private static CallResult<LeaderboardScoresDownloaded_t> s_DownloadResult;

public static void DownloadScoresGlobal(int start, int end)
{
    if (s_CurrentLeaderboard == SteamLeaderboard_t.Invalid) return;
    SteamAPICall_t handle = SteamUserStats.DownloadLeaderboardEntries(
        s_CurrentLeaderboard,
        ELeaderboardDataRequest.k_ELeaderboardDataRequestGlobal,
        start, end
    );
    s_DownloadResult = new CallResult<LeaderboardScoresDownloaded_t>();
    s_DownloadResult.Set(handle, OnScoresDownloaded);
}

private static void OnScoresDownloaded(LeaderboardScoresDownloaded_t pCallback, bool bIOFailure)
{
    if (bIOFailure) { Debug.LogError("Failed to download scores"); return; }
    for (int i = 0; i < pCallback.m_cEntryCount; i++)
    {
        LeaderboardEntry_t entry;
        SteamUserStats.GetDownloadedLeaderboardEntry(
            pCallback.m_hSteamLeaderboardEntries, i, out entry, null, 0
        );
        Debug.Log($"Rank {entry.m_nGlobalRank}: {entry.m_steamIDUser} Score {entry.m_nScore}");
    }
}
```

- **Request Types**:  
  - `Global` for all entries citeturn2view0.  
  - `GlobalAroundUser` for nearby ranks citeturn2view0.  
  - `Friends` for friend-only leaderboard citeturn2view0.  
- **`GetDownloadedLeaderboardEntry`** extracts each entry’s data including SteamID and score citeturn2view0.

---





















## 6.2. Getting User's rank

There are two primary ways to retrieve a player’s rank on a Steam leaderboard in Unity using Steamworks.NET.

In brief, you can obtain the user’s rank either immediately after uploading their score—via the `LeaderboardScoreUploaded_t` callback, which supplies the new global rank—or by explicitly downloading the entries around the user with `DownloadLeaderboardEntries` using the `k_ELeaderboardDataRequestGlobalAroundUser` flag and then reading the `m_nGlobalRank` field from the returned `LeaderboardEntry_t`.

---

## 6.2.1. Retrieving Rank After Score Upload

When you upload a score, Steam immediately returns a `LeaderboardScoreUploaded_t` callback. This struct includes the new global rank for the user.

```csharp
private CallResult<LeaderboardScoreUploaded_t> _uploadResult;

public void UploadScore(int score)
{
    SteamAPICall_t handle = SteamUserStats.UploadLeaderboardScore(
        _currentLeaderboard,
        ELeaderboardUploadScoreMethod.k_ELeaderboardUploadScoreMethodKeepBest,
        score,
        null, 0
    );
    _uploadResult = new CallResult<LeaderboardScoreUploaded_t>();
    _uploadResult.Set(handle, OnScoreUploaded);
}

private void OnScoreUploaded(LeaderboardScoreUploaded_t callback, bool ioFailure)
{
    if (ioFailure || callback.m_bSuccess == 0)
    {
        Debug.LogError("Score upload failed");
        return;
    }
    int newRank = callback.m_nGlobalRankNew;
    Debug.Log($"Player's new global rank: {newRank}");
}
```

- `m_nGlobalRankNew` is the player’s updated rank after the upload citeturn2search5.  
- Check `m_bSuccess` to ensure the upload succeeded before reading the rank citeturn2search5.

---

## 6.2.2. Downloading Rank “Around” the User

Alternatively, you can fetch the user’s current position by downloading leaderboard entries centered on that user. Use `ELeaderboardDataRequest.k_ELeaderboardDataRequestGlobalAroundUser` and request a small range around index 0.

```csharp
private CallResult<LeaderboardScoresDownloaded_t> _downloadResult;

public void FetchUserRank()
{
    SteamAPICall_t handle = SteamUserStats.DownloadLeaderboardEntries(
        _currentLeaderboard,
        ELeaderboardDataRequest.k_ELeaderboardDataRequestGlobalAroundUser,
        -1, 1
    );
    _downloadResult = new CallResult<LeaderboardScoresDownloaded_t>();
    _downloadResult.Set(handle, OnScoresDownloaded);
}

private void OnScoresDownloaded(LeaderboardScoresDownloaded_t callback, bool ioFailure)
{
    if (ioFailure)
    {
        Debug.LogError("Failed to download leaderboard entries");
        return;
    }
    LeaderboardEntry_t entry;
    SteamUserStats.GetDownloadedLeaderboardEntry(
        callback.m_hSteamLeaderboardEntries, 
        0, // index 0 is always the user's own entry
        out entry, 
        null, 0
    );
    int userRank = entry.m_nGlobalRank;
    Debug.Log($"Player’s current global rank: {userRank}");
}
```

1. **DownloadLeaderboardEntries** with `k_ELeaderboardDataRequestGlobalAroundUser` tells Steam to return entries with the user’s own position centered in the list citeturn1search3.  
2. In the `LeaderboardScoresDownloaded_t` callback, use `GetDownloadedLeaderboardEntry` at index 0 to access the user’s entry and read `m_nGlobalRank` 

---

## 6.2.3. Notes & Best Practices

- Always confirm `SteamManager.Initialized` before calling any Steam API.  
- Use `CallResult<>` rather than `Callback<>` in Steamworks.NET to prevent garbage‑collection of your handlers.  
- Ensure your leaderboard handle (`_currentLeaderboard`) was obtained earlier via `FindOrCreateLeaderboard`.  
- For ranges larger than ±1 around the user, adjust the `start`/`end` parameters accordingly (negative values fetch before the user, positive values after) 

With these approaches, you can reliably display or act on a player’s rank in your Unity game’s Steam leaderboards.


## 7. Handling Multiple Leaderboards

To manage several leaderboards, store each CallResult in a collection to prevent garbage collection and map names to handles:

```csharp
private Dictionary<string, SteamLeaderboard_t> _leaderboards = new();
private List<CallResult<LeaderboardFindResult_t>> _findResults = new();

// For each leaderboard name:
SteamAPICall_t hCall = SteamUserStats.FindLeaderboard(name);
var result = new CallResult<LeaderboardFindResult_t>();
result.Set(hCall, (callback, fail) => {
    if (!fail && callback.m_bLeaderboardFound != 0)
        _leaderboards[name] = callback.m_hSteamLeaderboard;
});
_findResults.Add(result);
```

This pattern avoids lost callbacks when multiple leaderboards are initialized concurrently citeturn1search3.

---

## 8. Best Practices & Error Handling

- **Early Stats Request**: Call `SteamUserStats.RequestCurrentStats()` on startup to sync remote data first citeturn2view0.  
- **Guard API Calls**: Always verify `SteamManager.Initialized` before calls.  
- **Batch `StoreStats()`**: Minimize server writes by grouping updates.  
- **Offline Mode**: Cache score uploads and retry when online.  
- **Debug Logging**: Log both success and failure callbacks for clear diagnostics.

---

## 9. Testing & Troubleshooting

- **Editor vs. Build**: Achievements/leaderboards work only when launched via Steam with a valid AppID citeturn2view0.  
- **Underscore Issue**: Avoid underscores in leaderboard names; some users reported failures when using them citeturn3view0.  
- **steam_api.dll Errors**: Ensure `steam_api.dll` and `steam_appid.txt` are alongside the executable citeturn2view0.  
- **Callbacks Not Firing**: Use `CallResult<>` instead of `Callback<>` to avoid GC issues citeturn3view0.

---

## 10. Further Resources

- Official Leaderboards Guide: Step by Step: Leaderboards citeturn2view0  
- ISteamUserStats Interface Reference citeturn1search17  
- Steamworks.NET GitHub Examples citeturn1search6  
- Community Q&A (“How to do leaderboards in Steamworks.NET?”) citeturn3view0  

With this, you have end‑to‑end coverage of Steam Leaderboards integration in Unity. Happy ranking!