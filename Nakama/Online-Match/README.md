Creating and managing multiplayer matches in Nakama involves setting up the server, creating and joining matches, and handling match states and opcodes. So I recommend you to see the [corresponding document](../Integration/) on how to integrate Nakama SDK into your project first.

After learning how to create a client and authenticate the user, then you're ready to jump into creating a multiplayer project. Here's a detailed guide to help you through the process:

# 1. Setting Up Nakama

- Make sure that you have Nakama SDK in your project and able to use the Nakama namespace.
- Create a script for managing Nakama connection and matchmaking. Let's call it, NakamaManager.cs.

# 2. Initialize Nakama
Initialize Nakama by connecting to the server and authenticating the user. Let's do that by device ID:
  
You also need to connect to a socket which is responsible for matching the players and sending and receiving match states that we're gonna need later.

~~~csharp
using Nakama;
using System.Threading.Tasks;

public class NakamaManager
{
    private IClient client;
    private ISession session;
    private ISocket socket;
    private IMatch currentMatch;

    public async Task InitializeAsync()
    {
        client = new Client("http", "127.0.0.1", 7350, "defaultkey");
        session = await client.AuthenticateDeviceAsync(SystemInfo.deviceUniqueIdentifier);
        socket = client.NewSocket();
        await socket.ConnectAsync(session);

        // Register the matchmaker matched event handler
        // the OnMatchmakerMatched method will be called whenever the match is made.
        socket.ReceivedMatchmakerMatched += OnMatchmakerMatched;
    }


    private async void OnMatchmakerMatched(IMatchmakerMatched matched){
        // for instance when the maatch is made and we've found a suitable opponent, we join the mathc.
    var match = await socket.JoinMatchAsync(matched);
    Debug.Log("Joined match with ID: " + match.Id);
}
}
~~~

## 2.1 Create the Match from Unity Client:
There are two main ways of matchmaking:

**First**: creating matches manually:

In this method, one player will create a match by using **socket.CreateMatchAsync();** method, and up to three other players can join the match by using **socket.JoinMatchAsync(matchId);** method.

The **socket.CreateMatchAsync()** method returns an IMatch object which represents the match that was just made.
It contains an ID, which we can use to join the match or send match states.
We can also get a reference to the local player who created the match.

~~~csharp
public async Task CreateMatch(){
    currentMatch = await socket.CreateMatchAsync();
    IUserPresence localPlayer = currentMatch.Self;
}
~~~

When the match is created you can simply join the match like this:

~~~csharp
public async Task JoinMatch(string matchId){
    currentMatch = await socket.JoinMatchAsync(matchId);
}
~~~

**Second**: creating matches with Random players using a match pool:

In this method, players are automatically matched with others based on certain criteria. This is useful for games where you want to quickly find opponents without manually creating and joining matches.

~~~csharp
public async Task AddToMatchmakerAsync()
{
    const string query = "*"; // Match any available opponents
    const int minPlayers = 2;
    const int maxPlayers = 2;

    var ticket = await socket.AddMatchmakerAsync(query, minPlayers, maxPlayers);
    Debug.Log("Matchmaker ticket added: " + ticket.Ticket);
}

~~~
---
### Remove the Player from the Matchmaker (Optional):

If the player decides to cancel the matchmaking request before a match is found, remove the ticket from the matchmaker.

~~~csharp
public async Task RemoveFromMatchmakerAsync(string ticketId)
{
    await socket.RemoveMatchmakerAsync(ticketId);
    Debug.Log("Matchmaker ticket removed: " + ticketId);
}
~~~

## 3. Handling Match States and Opcodes

Once players are connected to a match, you need to handle match states and opcodes to manage the game logic. This involves sending and receiving data between players.

~~~csharp
public async Task SendMatchState(byte opCode, byte[] state)
{
    await socket.SendMatchStateAsync(currentMatch.Id, opCode, state);
}

public void RegisterMatchStateHandler()
{
    socket.ReceivedMatchState += (IMatchState state) =>
    {
        // Handle received match state
        var opCode = state.OpCode;
        var receivedState = state.State;
        // Process the state based on opCode
    };
}
~~~

## 4. Leaving and Ending Matches

Players can leave a match or the match can be ended when the game is over.

~~~csharp
public async Task LeaveMatch()
{
    await socket.LeaveMatchAsync(currentMatch.Id);
    currentMatch = null;
}

public async Task EndMatch()
{
    // Custom logic to end the match
    await LeaveMatch();
}
~~~

By following these steps, you can create and manage multiplayer matches in Nakama using Unity. Make sure to handle all edge cases and errors to ensure a smooth multiplayer experience.

---

# Additional Resources:

[Nakama Matchmaking Documentation](https://heroiclabs.com/docs/nakama/concepts/multiplayer/matchmaker/): Provides detailed information on configuring and using Nakama's matchmaking features. 

[Pirate Panic Matchmaking Tutorial](https://heroiclabs.com/docs/nakama/tutorials/unity/pirate-panic/matchmaking/): A step-by-step guide on implementing matchmaking in a sample Unity project. 

[Joining a Match With Matchmaking](https://www.youtube.com/watch?v=-ZpmZo6ZvIo): this is a youtube video made by Heroiclabs teachuing how to join a match with matchmaking