---
title: Azure Service Fabric 動作專案中的重新進入
description: Service Fabric Reliable Actors 的重新進入簡介，以邏輯方式避免根據呼叫內容來封鎖封鎖。
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: c2356db0eee82a133afad6aa95299ced0595db32
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576055"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors 重新進入
Reliable Actors 執行階段預設允許邏輯呼叫以內容為基礎的重新進入。 這允許位於相同的呼叫內容鏈結的動作項目可重新進入。 例如，動作項目 A 傳送訊息給動作項目 B，而動作項目 B 又將訊息傳送給動作項目 C。當處理訊息時，如果動作項目 C 呼叫動作項目 A，則此訊息是可以重新進入的，因此將允許此訊息。 屬於不同呼叫內容的其他任何訊息都將在動作項目 A 上遭到封鎖，直到其處理完畢為止。

有兩個選項適用於 `ActorReentrancyMode` 列舉中定義的動作項目重新進入︰

* `LogicalCallContext` (預設行為)
* `Disallowed` - 停用重新進入

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
可在註冊期間在 `ActorService`的設定中設定重新進入。 此設定適用於動作項目服務中建立的所有動作項目執行個體。

下列範例會示範動作項目服務如何將重新進入模式設定為 `ActorReentrancyMode.Disallowed`。 在此情況下，如果動作項目傳送可重新進入的訊息給另一個動作項目類型，就會擲回 `FabricException` 類型的例外狀況。

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>後續步驟
* 參閱[動作項目 API 參考文件](/previous-versions/azure/dn971626(v=azure.100))來深入了解重新進入
