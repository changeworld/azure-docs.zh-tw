---
title: 刪除 Azure Service Fabric 執行者
description: 瞭解如何在 Azure Service Fabric 應用程式中手動並完全刪除 Reliable Actors 及其狀態。
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75645611"
---
# <a name="delete-reliable-actors-and-their-state"></a>刪除 Reliable Actors 及其狀態
已停用動作項目的記憶體回收只會清除動作項目物件；但不會移除動作項目的狀態管理員中儲存的資料。 重新啟用動作項目後，會再次透過狀態管理員提供其資料。 在動作項目將資料儲存於狀態管理員後停用，而永遠不會重新啟用的情況下，可能需要清除其資料。

[動作項目服務](service-fabric-reliable-actors-platform.md) 提供了從遠端呼叫端刪除動作項目的函式︰

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

根據動作項目目前是否作用中而定，刪除動作項目具有下列效果︰

* **作用中動作項目**
  * 動作項目會從作用中動作項目清單中移除並且停用。
  * 其狀態會永久刪除。
* **非作用中動作項目**
  * 其狀態會永久刪除。

動作項目無法從其中一個動作項目方法呼叫刪除本身，因為在動作項目呼叫內容中執行動作項目時無法刪除該動作項目，而執行階段已取得動作項目呼叫的鎖定以強制執行單一執行緒存取。

如需有關 Reliable Actors 的詳細資訊，請參閱下列內容：
* [動作項目計時器和提醒](service-fabric-reliable-actors-timers-reminders.md)
* [動作專案事件](service-fabric-reliable-actors-events.md)
* [動作專案重新進入](service-fabric-reliable-actors-reentrancy.md)
* [動作項目診斷與效能監視](service-fabric-reliable-actors-diagnostics.md)
* [動作項目 API 參考文件](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C # 範例程式碼](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [JAVA 範例程式碼](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
