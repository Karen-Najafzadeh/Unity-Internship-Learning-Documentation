Leaderboards are a fundamental feature in many games, fostering competition and enhancing player engagement by ranking players based on their performance. Nakama provides a robust system for implementing leaderboards in Unity. This tutorial will guide you through the basics of setting up leaderboards in Nakama, including reading, writing, and editing records, accompanied by a real-world example.

**1. Setting Up Nakama in Unity**

Before diving into leaderboards, ensure that Nakama is properly integrated into your Unity project. See [Integrating nakama into your project](https://github.com/Karen-Najafzadeh/Unity-Internship-Learning-Documentation/tree/main/Nakama/Integration)

- **Initialize the Client and Socket**: Set up the Nakama client and establish a socket connection.

  ```csharp
  using Nakama;
  using UnityEngine;

  public class NakamaManager : MonoBehaviour
  {
      private IClient client;
      private ISession session;
      private ISocket socket;

      private async void Start()
      {
          client = new Client("http", "127.0.0.1", 7350, "defaultkey");
          session = await client.AuthenticateDeviceAsync(SystemInfo.deviceUniqueIdentifier);
          socket = client.NewSocket();
          await socket.ConnectAsync(session);
      }

      private async void OnApplicationQuit()
      {
          await socket.CloseAsync();
      }
  }
  ```

**2. Creating a Leaderboard**

Leaderboards in Nakama can be created server-side using the server runtime environment. This ensures that the leaderboard is properly configured and managed.

I've explained **[Here](https://github.com/Karen-Najafzadeh/Unity-Internship-Learning-Documentation/tree/main/Nakama/Initialization)** how to create a leaderboard from server-side.


**3. Writing a Leaderboard Record**

Players can submit their scores to the leaderboard using the following method:

```csharp
public async Task WriteLeaderboardRecordAsync(string leaderboardId, long score)
{
    var record = await client.WriteLeaderboardRecordAsync(session, leaderboardId, score);
    Debug.Log($"Leaderboard record written: {record.Score}");
}
```

**4. Reading Leaderboard Records**

To retrieve and display leaderboard records:

```csharp
public async Task ReadLeaderboardRecordsAsync(string leaderboardId, int limit)
{
    var records = await client.ListLeaderboardRecordsAsync(session, leaderboardId, limit: limit);
    foreach (var record in records.Records)
    {
        Debug.Log($"Rank: {record.Rank}, Score: {record.Score}, Player ID: {record.OwnerId}");
    }
}
```

**5. Editing Leaderboard Records**

Editing leaderboard records directly is not a common practice. Instead, players can submit new scores, and the leaderboard will update based on the configured operator (`BEST`, `SET`, `INCR`, `DECR`). For example, if the operator is set to `BEST`, submitting a new score will only update the record if the new score is higher than the existing one.

**Real-World Example: Implementing a Global High Score Leaderboard**

Imagine you're developing a racing game and want to implement a global leaderboard that ranks players based on their fastest race completion times.

1. **Create the Leaderboard**: Configure the leaderboard to sort scores in ascending order (since lower times are better) and use the `BEST` operator to keep the fastest time.

   ```typescript
   const id = "fastest_race_times";
   const authoritative = false;
   const sortOrder = nkruntime.SortOrder.ASCENDING;
   const operator = nkruntime.Operator.BEST;
   const resetSchedule = null;
   const metadata = {};

   nk.leaderboardCreate(id, authoritative, sortOrder, operator, resetSchedule, metadata);
   ```

2. **Submit a Race Time**: After a player completes a race, submit their completion time (in milliseconds) to the leaderboard.

   ```csharp
   public async Task SubmitRaceTimeAsync(long raceTime)
   {
       string leaderboardId = "fastest_race_times";
       await WriteLeaderboardRecordAsync(leaderboardId, raceTime);
   }
   ```

3. **Display the Leaderboard**: Retrieve and display the top 10 fastest race times.

   ```csharp
   public async Task DisplayTopRaceTimesAsync()
   {
       string leaderboardId = "fastest_race_times";
       int topN = 10;
       await ReadLeaderboardRecordsAsync(leaderboardId, topN);
   }
   ```

This setup allows players to compete for the fastest race times, with the leaderboard dynamically updating as new records are submitted.

**Additional Resources**

For more detailed information and advanced configurations, refer to the Nakama documentation:

- [Leaderboards in Nakama](https://heroiclabs.com/docs/nakama/concepts/leaderboards/)

- [Unity Leaderboard Tutorial](https://heroiclabs.com/docs/nakama/tutorials/unity/pirate-panic/leaderboards/)

By following this guide, you can effectively implement leaderboards in your Unity game using Nakama, enhancing player engagement through competitive rankings. 