## KadoSaku v6

### Dependencies and Settings

This SDK must be installed using **Touchten Framework Wizard**.
It is dependent to *KadoSaku-WebClient*, its webapp counterpart.

Don't forget to setup your KadoSaku Settings in Unity Menu
Unity Menu Bar &gt; KadoSaku &gt; Edit Core Settings
- *Game App Id*: get from portal.touchten.com
- *Game App Secret*: get from portal.touchten.com
- *Enable KadoSaku Debug Message*: Enable this to report KadoSaku system to console

### Method Usage Example

**Add namespace**
```csharp
using KadoSaku6.Api;
```

**Using Callback Method**
```csharp
void CacheReward () {
    KS.Reward.CacheReward (onCacheResult);
}

// Create a method that satisfies the delegate
void onCacheResult (IActionResult result) {
    if(result.IsSuccess) {
        // TODO: Add your code here ...
    }
}

```

**Using Anonymous Callback Method**
```csharp
KS.Reward.CacheReward (delegate(IActionResult result) {
    if (result.IsSuccess) {
        // TODO: Add your code here ...
    }
});
```
> **Notes** usually the signature is provided by IDE's *intellisense*

**Using anonymous callback method, checking result using Error and Cancelled property from IResult**

```csharp

```

**Handle Error and Cancelled property from IResult**
```csharp
string leaderboardId = "000000000000001";
KS.Leaderboard.GetLeaderboardInfo (leaderboardId, delegate(ILeadeboardInfoResult result) {
if (result.Error != null) {
// TODO: Handle error here
Debug.LogError(result.Error.Message);
return; // Don't forget to return to stop program execution
}

if (result.Cancelled == false) {

// TODO: Handle cancelled here
return;
}

// If there is no Error and not cancelled
// TODO: Add your code here ...
Debug.Log(result.Name);
});
```
