## KadoSaku v6 Unity SDK

### Dependencies and Settings

This SDK must be installed using **Touchten Framework Wizard**.
It depends on *KadoSaku-WebClient*, its webapp counterpart.

Don't forget to setup your KadoSaku Settings in Unity Menu
Unity Menu Bar &gt; KadoSaku &gt; Edit Core Settings
- *Game App Id*: get from KadoSaku Portal
- *Game App Secret*: get from KadoSaku Portal
- *Enable Test Mode*: Enable this to use KadoSaku's staging URL for testing purposes
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
**Using lambda expression**

```csharp
KS.Reward.CacheReward ( result => {
    if (result.IsSuccess) {
        // TODO: Add your code here ...
    }
});
```

**Using anonymous callback method, checking result using Error and Cancelled property from IResult**

```csharp
KS.Reward.CacheReward ( result => {
    if (result.Error != null) {
        // Error handling
        Debug.LogError(result.Error.Message);
    }
    else if (result.Cancelled != null && result.Cancelled == true) {
        // "Cancelled" handling (only applicable to KS.Reward.ShowReward() and KS.Reward.ShowIncentivizedReward() )
    }
    else {
        // "Success" handling
    }
});
```

**Analytics: Updating User Id and Session Id**

Since KadoSaku v6 is not coupled to any analytics module, it provides a way to update user id and session id for KadoSaku analytics purposes.

Steps:
1. Create a class that implements `KS.IAnalyticsData`
```csharp
public class InheritedAnalyticsData : MonoBehaviour, KS.IAnalyticsData
{

}
```
2. Call `KS.User.UpdateUserData(this);`. `KS.User.UpdateUserData()` can be called outside the implementer class, as long as the implementer class is passed as parameter.
```csharp
void Start ()
{
    KS.User.UpdateUserData(this);
}
```
3. Implement the two methods needed to update user Id and session Id
```csharp
#region IAnalyticsData implementation
public void getUserSession(Action<string> callback)
{
    callback("userSessionId");
}

public void getUserId(Action<string> callback)
{
    callback("userId");
}
#endregion
```
