---
title: 列舉 Azure Service Fabric 上的執行者
description: 瞭解如何使用範例，在 Azure Service Fabric 應用程式中列舉 Reliable Actors 和其中繼資料。
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: dd3a61db32fb8e442beb42bd45c88da8559a29dd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016643"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>列舉 Service Fabric Reliable Actors
Reliable Actors 服務允許用戶端列舉服務所裝載之動作項目的相關中繼資料。 因為動作項目服務是已資料分割的具狀態服務，所以會針對每個分割區執行列舉。 由於每個分割區可能包含許多動作項目，因此，列舉會以一組分頁式結果形式傳回。 頁面會以迴圈方式讀取，直到讀取所有頁面為止。 下列範例示範如何在動作項目服務的其中一個分割區中，建立所有作用中動作項目的清單︰

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>後續步驟
* [動作項目狀態管理](service-fabric-reliable-actors-state-management.md)
* [動作專案生命週期與垃圾收集](service-fabric-reliable-actors-lifecycle.md)
* [動作項目 API 參考文件](/previous-versions/azure/dn971626(v=azure.100))
* [.NET 範例程式碼](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 範例程式碼 (英文)](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
