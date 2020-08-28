---
title: 備份和還原 Azure Service Fabric 執行者
description: 瞭解如何在您的 Azure Service Fabric 動作項目中實作備份和還原。
author: vturecek
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 9646a8c4b1c138d832b209e51898fb013ca810cb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006868"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>實作 Reliable Actors 備份與還原

> [!NOTE]
> Microsoft 建議使用[定期備份和還原](service-fabric-backuprestoreservice-quickstart-azurecluster.md)，設定可靠具狀態服務和 Reliable Actors 的資料備份。 
> 

在下列範例中，自訂動作項目服務會利用已經存在於 `ActorService` 中的遠端處理接聽程式，藉以公開用來備份動作項目資料的方法：

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```

在此範例中，`IMyActorService` 是遠端處理協定，它會實作 `IService` (C#) 與 `Service` (Java)，然後透過 `MyActorService` 來實作。 藉由新增這個遠端處理協定，透過使用 `ActorServiceProxy` 建立遠端 Proxy，`IMyActorService` 上的方法現在也可供用戶端使用：

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

如需有關 Reliable Actors 的詳細資訊，請參閱下列文章：
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
