## KadoSaku v6 Unity SDK

KadoSaku v6 Version 6.0.7.1

Incorporated in Touchten Framework Version 5.5.2

### Dependencies and Settings

This SDK must be installed using **Touchten Framework Wizard**.

It depends on *KadoSaku-WebClient*, its webapp counterpart.

Don't forget to setup your KadoSaku Settings in Unity Editor Menu

`Touchten > KadoSaku > Edit Core Settings`

- `Game App Id`: get from KadoSaku Portal
- `Game App Secret`: get from KadoSaku Portal
- `Enable Test Mode`: Enable this to use KadoSaku's staging URL for testing purposes
- `Enable KadoSaku Debug Message`: Enable this to report KadoSaku system to console

### Basic Usage

**Adding namespace**
```csharp
using KadoSaku6.Api;
```

**Caching Reward**
```csharp
if(!KS.IsRewardReady) {
    KS.Reward.CacheReward((result) => {
        Debug.Log("<color=cyan>Cache incentivized reward: " + result.ToString() + "</color>");
    });   
} 
```

**Showing Reward**

There are two types of rewards, standard reward and incentivized reward.

Each incentivized reward has `inAppId`, which can be used to give in-game reward to player.
```csharp
string inAppId = 'inAppId';
if(KS.IsRewardReady) {
    KS.Reward.ShowIncentivizedReward(inAppId, (result) => {
        Debug.Log(result.InAppReward.Id);
        Debug.Log(result.InAppReward.CurrencyId);
        Debug.Log(result.InAppReward.Message);
        Debug.Log(result.InAppReward.Quantity);
    }); 
}
```

**Implementing callback handler**

There are some ways to handle the callback:

**1. Create a callback handler**
```csharp
void CacheReward () {
    KS.Reward.CacheReward (onCacheResult);
}

void onCacheResult (IActionResult result) {
    if(result.IsSuccess) {
        // TODO: Add your code here ...
    }
}

```

**2. Using Anonymous Callback Method**
```csharp
KS.Reward.CacheReward (delegate(IActionResult result) {
    if (result.IsSuccess) {
        // TODO: Add your code here ...
    }
});
```

**3. Using lambda expression**

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

### Analytics: Updating User Id and Session Id

**Will be applicable for version 6.0.7.2 onward**

Since KadoSaku v6 is not coupled to any analytics module, it provides a way to update user id and session id for KadoSaku analytics purposes.

This is optional. If you don't follow these steps, user id and session id will be an empty string, and will not be sent to KadoSaku webapp.

Example:

```csharp
//Step 1: Create a class that implements KS.IAnalyticsData
public class ImplAnalyticsData : MonoBehaviour, KS.IAnalyticsData
{
    //Step 2: Call KS.User.UpdateUserData(this);
    //KS.User.UpdateUserData() can be called outside the implementer class,
    //as long as the implementer class instance is passed as parameter.
    //KS.User.UpdateUserData() must be called after KadoSaku init has been completed.

    void OnEnable ()
    {
        KS.onInitCompleteE += OnInitCompleted;
    }

    void OnInitCompleted (InitResult result)
    {
        KS.User.UpdateUserData(this);
        KS.onInitCompleteE -= OnInitCompleted;
    }
    
    //Step 3: Implement the two methods needed to update user id and session id
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
}
```
### Analytics: Implementing Custom Events for KadoSakuv6

For analytics purposes, there are two additional custom events for KadoSakuv6.

As an example, assume the implementation of sending analytics custom event has been wrapped in this method:

```csharp
SendEvent ("customEventName", customData);
```

**initKadoSaku**

*when kadosaku v6 finish initializing in game*

This custom event is called right after KadoSaku GameObject reference has been initialized. Typically, it is called in the same code block as `KS.User.UpdateUserData()` (see the previous section about Updating User Id and Session Id).
```csharp
void OnEnable ()
{
    KS.onInitCompleteE += OnInitCompleted;
}

void OnInitCompleted (InitResult result)
{
    SendEvent ("initKadosaku", customData);
    KS.onInitCompleteE -= OnInitCompleted;
}
```

**kadosaku**

*upon triggering kadosaku v6*

This custom event consists of three trigger events:

1.  Before `KS.Reward.ShowIncentivizedReward()` is called
2.  When receiving success callback
3.  When receiving error/cancelled callback
4.  When KadoSaku webview fails to show

Below is an example snippet to demonstrate all those trigger events.

```csharp
SendEvent ("kadosaku", KS_REQUEST, ON_REQUEST);
if(KS.IsRewardReady) {
    KS.Reward.ShowIncentivizedReward(achievementId, onCompleted, onClosed, (result) => {
        if(result.IsSuccess) {
            SendEvent ("kadosaku", KS_FEEDBACK, SUCCESS_GIVEREWARD);
        }
        else {
            SendEvent ("kadosaku", KS_FEEDBACK, SUCCESS_NOTGIVEREWARD);
        }
    });
}
else{
    SendEvent ("kadosaku", KS_FEEDBACK, FAILED);
}
```

Feel free to implement the callback handling, either by checking `result.IsSuccess` or by using the `onCompleted` and `onClosed` handlers (`onCompleted` and `onClosed` handlers will be available for version 6.0.7.2 onward).