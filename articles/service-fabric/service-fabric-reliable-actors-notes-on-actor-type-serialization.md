---
title: 關於演員類型序列化的可靠演員注釋
description: 說明定義可用於定義 Service Fabric Reliable Actors 狀態與介面其可序列化類別的基本需求
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349313"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Service Fabric Reliable Actors 類型序列化的注意事項
所有方法的參數、執行元件介面中每個方法返回的任務的結果類型以及存儲在執行元件的狀態管理員中的物件必須是[資料協定可序列化](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)的。 這也適用於在 [動作項目事件介面](service-fabric-reliable-actors-events.md)中定義的方法引數。 (動作項目事件介面方法一律會傳回無效)。

## <a name="custom-data-types"></a>自訂資料類型
在此範例中，下列動作項目介面會定義一個方法，以傳回名為 `VoicemailBox` 的自訂資料類型：

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

此介面是由動作項目實作，其使用狀態管理員來儲存 `VoicemailBox` 物件︰

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

在此範例中， `VoicemailBox` 物件會在下列情況進行序列化︰

* 在動作項目執行個體與呼叫端之間傳輸物件。
* 物件已儲存在狀態管理員，以保存到磁碟並複寫到其他節點。

Reliable Actor 架構會使用 DataContract 序列化。 因此，必須分別使用**資料協定**和**資料成員**屬性對自訂資料物件及其成員進行點帶。

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>後續步驟
* [參與者生命週期和垃圾回收](service-fabric-reliable-actors-lifecycle.md)
* [動作項目計時器和提醒](service-fabric-reliable-actors-timers-reminders.md)
* [參與者事件](service-fabric-reliable-actors-events.md)
* [演員重入](service-fabric-reliable-actors-reentrancy.md)
* [動作項目多型和物件導向的設計模式](service-fabric-reliable-actors-polymorphism.md)
* [動作項目診斷與效能監視](service-fabric-reliable-actors-diagnostics.md)
