# Comprehensive Guide to Integrating GameAnalytics SDK in Unity

GameAnalytics is a free analytics tool for game developers, designed to help you track player behavior, in-game events, and overall game performance. With insights from GameAnalytics, you can optimize game design, retention, monetization, and more.

## Table of Contents
1. [Introduction](#introduction)
2. [Installation and Setup](#installation-and-setup)
3. [Configuration](#configuration)
4. [Tracking Events](#tracking-events)
   - [Business Events](#business-events)
   - [Resource Events](#resource-events)
   - [Progression Events](#progression-events)
   - [Design Events](#design-events)
   - [Error Events](#error-events)
5. [Advanced Configuration](#advanced-configuration)
6. [Debugging and Testing](#debugging-and-testing)
7. [Best Practices and Troubleshooting](#best-practices-and-troubleshooting)
8. [Using the GameAnalytics Dashboard](#using-the-gameanalytics-dashboard)
9. [Conclusion](#conclusion)

---

## Introduction

GameAnalytics allows you to gather key insights into your game's performance by tracking a variety of in-game events. Whether you want to monitor your in-game economy, progression, or even errors, GameAnalytics offers a robust set of tools to support your analysis and decision-making.

---

## Installation and Setup

### Using the Unity Package Manager

1. **Open Unity Package Manager:**
   - Go to `Window` → `Package Manager` in the Unity Editor.

2. **Add the Package:**
   - Click the `+` icon and choose **Add package from git URL**.
   - Enter the URL:  
     ```
     https://github.com/GameAnalytics/GA-SDK-UNITY.git
     ```
   - Click **Add** to install the SDK.

### Manual Installation

1. **Download the SDK:**
   - Get the latest Unity package from the [GameAnalytics GitHub releases page](https://github.com/GameAnalytics/GA-SDK-UNITY/releases).

2. **Import the Package:**
   - In Unity, go to `Assets` → `Import Package` → `Custom Package…` and select the downloaded package.
   - Click **Import** to include all the necessary files.

---

## Configuration

### Setting Up Your GameAnalytics Account

1. **Create an Account:**
   - Visit [GameAnalytics](https://gameanalytics.com/) and sign up.
   
2. **Create a New Game:**
   - In your GameAnalytics dashboard, add a new game or project.

3. **Obtain Keys:**
   - Copy the **Game Key** and **Secret Key** provided for your game.

### Configuring the SDK in Unity

1. **Locate the Configuration File:**
   - Find the file typically located at `Assets/GameAnalytics/Resources/GameAnalyticsSettings.asset`.

2. **Enter Your Keys:**
   - Paste your **Game Key** and **Secret Key** into the corresponding fields.

3. **Attach the GameAnalytics Component:**
   - Create a new GameObject (e.g., `AnalyticsManager`) in your scene.
   - Drag the GameAnalytics prefab onto the GameObject, or initialize it programmatically in your script:
     ```csharp
     void Start() {
         GameAnalytics.Initialize();
     }
     ```

---

## Tracking Events

GameAnalytics categorizes events into five main types. Below are examples and detailed explanations for each:

### Business Events (Monetization)

Track in-app purchases, ad revenue, or other financial transactions.

**Example:**
```csharp
GameAnalytics.NewBusinessEvent("USD", 5, "CoinPack", "StarterPack", "iap_google");
```
**Explanation:**
- **Currency:** E.g., "USD".
- **Amount:** The transaction amount.
- **Item Type/ID:** Identifies the purchased item.
- **Cart/Receipt:** Additional identifier such as store or campaign ID.

### Resource Events (In-Game Economy)

Monitor the flow of resources like coins, gems, or other in-game currencies.

**Example:**
```csharp
GameAnalytics.NewResourceEvent(GAResourceFlowType.Sink, "Gold", 50, "Shop", "WeaponUpgrade");
```
**Explanation:**
- **Flow Type:** Use `Source` for earning and `Sink` for spending.
- **Resource Name:** E.g., "Gold".
- **Amount:** How much was gained or spent.
- **Item Type/ID:** Context of the transaction (e.g., "Shop" or "Reward").

### Progression Events (Player Journey)

Track players' progress through levels, worlds, or challenges.

**Example:**
```csharp
GameAnalytics.NewProgressionEvent(GAProgressionStatus.Complete, "World1", "Level3", "HardMode");
```
**Explanation:**
- **Progression Status:** Options include `Start`, `Complete`, or `Fail`.
- **Stages:** Hierarchical identifiers such as world, level, and mode.

### Design Events (Custom Tracking)

Record custom in-game events like button clicks, menu selections, or any unique player interactions.

**Example:**
```csharp
GameAnalytics.NewDesignEvent("UI:MainMenu:PlayButtonClick");
```
**Explanation:**
- **Event ID:** A descriptive string that uniquely identifies the event.

### Error Events (Debugging)

Log errors and exceptions to help diagnose issues within your game.

**Example:**
```csharp
GameAnalytics.NewErrorEvent(GAErrorSeverity.Critical, "NullReferenceException in PlayerController.cs");
```
**Explanation:**
- **Severity:** Levels range from `Debug` to `Critical`.
- **Message:** A brief description of the error.

---

## Advanced Configuration

### Platform-Specific Settings

- **iOS:**
  - Configure `AdTracking` for AppTracking Transparency (ATT).
  - Update Xcode project settings as necessary.
- **Android:**
  - If using ProGuard or minification, add rules to keep GameAnalytics classes un-obfuscated.
- **WebGL:**
  - Ensure proper cross-origin settings and verify that analytics calls work in web builds.

### Custom Event Parameters

- **Extended Context:** Consider adding additional parameters to events to capture more context.  
- **Wrapper Functions:** Create helper functions to standardize event logging across your project.

---

## Debugging and Testing

### Enabling Verbose Logging

For development, enable verbose logging to see real-time data in the Unity console:
```csharp
GameAnalytics.SetEnabledVerboseLog(true);
```
Use this in your initialization code to monitor the flow of events.

### Editor Testing

- **Simulate Events:** Use the Unity Editor to simulate game sessions and verify that events are being sent.
- **Check Logs:** Monitor the Unity console for any errors or warnings from GameAnalytics.

---

## Best Practices and Troubleshooting

### Best Practices

- **Consistent Naming Conventions:** Use clear, descriptive names for events. For instance, prefix design events with the relevant UI section (`UI:MainMenu:ButtonClick`).
- **Rate Limiting:** Avoid sending an excessive number of events in a short period. Batch events if possible.
- **Privacy Compliance:** Ensure your analytics implementation respects player privacy and complies with GDPR or other relevant regulations.
- **Documentation:** Maintain an internal document mapping event names and their purposes to keep your analytics strategy organized.

### Troubleshooting Common Issues

- **No Data on Dashboard:**
  - Verify that the Game Key and Secret Key are correctly configured.
  - Ensure that the game is successfully communicating with GameAnalytics servers.
- **SDK Errors:**
  - Check for any error messages in the Unity console.
  - Consult the [official GameAnalytics documentation](https://gameanalytics.com/docs/) for detailed troubleshooting tips.
- **Platform-Specific Problems:**
  - Test on actual devices, as behavior in the Unity Editor may differ from mobile or web builds.

---

## Using the GameAnalytics Dashboard

### Interpreting Your Data

- **Overview:** The dashboard provides an at-a-glance view of key metrics like retention, session length, and revenue.
- **Event Reports:** Drill down into specific events (e.g., progression or business events) to see trends over time.
- **Custom Dashboards:** Create tailored reports to focus on the metrics most relevant to your game.

### Leveraging Analytics for Game Improvement

- **Player Behavior:** Identify bottlenecks or difficult levels where players tend to drop off.
- **Monetization:** Analyze the effectiveness of in-app purchases and promotions.
- **Iterative Design:** Use the insights from analytics to refine game mechanics and improve overall player engagement.

---

## Conclusion

Integrating the GameAnalytics SDK into your Unity project gives you powerful insights into how your game performs in real time. By following this comprehensive guide, you’ll be well-equipped to set up the SDK, track a wide range of events, and use the resulting data to continuously improve your game.

For further details or updates, refer to the [official GameAnalytics documentation](https://gameanalytics.com/docs/).

---