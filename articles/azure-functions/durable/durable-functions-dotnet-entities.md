---
title: .NET 中的持久性實體開發人員指南-Azure Functions
description: 如何在 .NET 中使用具有 Azure Functions 之 Durable Functions 延伸模組的持久性實體。
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 8fdf298357370415c1b3af95dd9ed22ad8539786
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85125475"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>.NET 中的持久性實體開發人員指南

在本文中，我們將詳細說明使用 .NET 來開發持久性實體的可用介面，包括範例和一般建議。 

實體函式提供無伺服器應用程式開發人員一種便利的方式，將應用程式狀態組織成更精細實體的集合。 如需有關基礎概念的詳細資訊，請參閱[持久實體：概念](durable-functions-entities.md)一文。

我們目前提供兩個 Api 來定義實體：

- 以**類別為基礎的語法**會將實體和作業表示為類別和方法。 此語法會產生容易閱讀的程式碼，並可讓您透過介面以類型檢查的方式叫用作業。 

- 以函**式為基礎的語法**是較低層級的介面，可將實體當做函數來表示。 它可精確控制如何分派實體作業，以及如何管理實體狀態。  

本文主要著重于以類別為基礎的語法，因為我們希望它更適合大部分的應用程式。 不過，以函[式為基礎的語法](#function-based-syntax)可能適用于想要針對實體狀態和作業定義或管理自己的抽象概念的應用程式。 此外，它可能適合用來執行需要以類別為基礎的語法目前不支援 genericity 的程式庫。 

> [!NOTE]
> 以類別為基礎的語法只是以函式為基礎的語法之上的一層，因此這兩種變異可以在同一個應用程式中交替使用。 
 
## <a name="defining-entity-classes"></a>定義實體類別

下列範例是 `Counter` 實體的實值，它會儲存 integer 類型的單一值，並提供四個作業 `Add` ： `Reset` 、 `Get` 和 `Delete` 。

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

函 `Run` 式包含使用以類別為基礎的語法時所需的樣板。 它必須是*靜態*的 Azure 函式。 它會針對實體所處理的每個作業訊息執行一次。 當 `DispatchAsync<T>` 呼叫且實體尚未存在於記憶體中時，它會建立類型的物件 `T` ，並從儲存體中找到的最後一個持續性 JSON 填入其欄位（如果有的話）。 然後，它會叫用具有相符名稱的方法。

> [!NOTE]
> 以類別為基礎之實體的狀態會在實體處理作業之前**隱含建立**，而且可以藉由呼叫在作業中**明確地刪除** `Entity.Current.DeleteState()` 。

### <a name="class-requirements"></a>類別需求
 
實體類別是 Poco （純舊的 CLR 物件），不需要特殊的超類、介面或屬性。 但是：

- 類別必須是可建構（請參閱[實體結構](#entity-construction)）。
- 類別必須是可序列化的 JSON （請參閱[實體序列化](#entity-serialization)）。

此外，任何要當做作業叫用的方法，都必須滿足額外的需求：

- 作業最多隻能有一個引數，而且不能有任何多載或泛型型別引數。
- 使用介面從協調流程呼叫的作業必須傳回 `Task` 或 `Task<T>` 。
- 引數和傳回值必須是可序列化的值或物件。

### <a name="what-can-operations-do"></a>作業可以做什麼？

所有實體作業都可以讀取和更新實體狀態，而對狀態的變更會自動儲存到儲存體。 此外，作業也可以在所有 Azure Functions 通用的一般限制內執行外部 i/o 或其他計算。

作業也可以存取內容所提供的功能 `Entity.Current` ：

* `EntityName`：目前正在執行之實體的名稱。
* `EntityKey`：目前正在執行之實體的索引鍵。
* `EntityId`：目前執行之實體的識別碼（包括名稱和索引鍵）。
* `SignalEntity`：將單向訊息傳送至實體。
* `CreateNewOrchestration`：啟動新的協調流程。
* `DeleteState`：刪除此實體的狀態。

例如，我們可以修改 counter 實體，讓它在計數器到達100時啟動協調流程，並將實體識別碼傳遞為輸入引數：

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount >= 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId);
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>直接存取實體

以類別為基礎的實體可以直接存取，使用實體及其作業的明確字串名稱。 我們提供下列範例：如需基礎概念（例如信號和呼叫）的更深入說明，請參閱[存取實體](durable-functions-entities.md#access-entities)中的討論。 

> [!NOTE]
> 可能的話，我們建議您[透過介面來存取實體](#accessing-entities-through-interfaces)，因為它提供更多的型別檢查。

### <a name="example-client-signals-entity"></a>範例：用戶端信號實體

下列 Azure Http 函數會使用 REST 慣例來執行刪除作業。 它會將刪除信號傳送到在 URL 路徑中傳遞其金鑰的計數器實體。

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>範例：用戶端讀取實體狀態

下列 Azure Http 函數會使用 REST 慣例來執行 GET 作業。 它會讀取計數器實體的目前狀態，其金鑰會在 URL 路徑中傳遞。

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> 所傳回的物件 `ReadEntityStateAsync` 只是本機複本，也就是來自某個較早時間點的實體狀態快照集。 特別是，它可能已過時，而修改此物件不會影響實際實體。 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>範例：協調流程第一個信號，然後呼叫實體

下列協調流程會指示計數器實體來遞增它，然後呼叫相同的實體來讀取其最新值。

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>透過介面存取實體

介面可用來透過產生的 proxy 物件來存取實體。 這種方法可確保作業的名稱和引數類型符合所實。 我們建議您盡可能使用介面來存取實體。

例如，我們可以修改計數器範例，如下所示：

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

實體類別和實體介面類別似于[奧爾良](https://www.microsoft.com/research/project/orleans-virtual-actors/)普及化的粒紋和細微性介面。 如需有關持久性實體與奧爾良之間的相似性和差異的詳細資訊，請參閱[與虛擬執行者比較](durable-functions-entities.md#comparison-with-virtual-actors)。

除了提供類型檢查之外，介面也有助於在應用程式內更清楚地區分問題。 例如，由於實體可能會執行多個介面，因此單一實體可以服務多個角色。 此外，由於介面可能會由多個實體執行，因此一般通訊模式可以實作為可重複使用的程式庫。

### <a name="example-client-signals-entity-through-interface"></a>範例：用戶端透過介面的信號實體

用戶端程式代碼可以使用 `SignalEntityAsync<TEntityInterface>` ，將信號傳送給實作為的實體 `TEntityInterface` 。 例如：

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

在此範例中， `proxy` 參數是動態產生的實例 `ICounter` ，它會在內部將呼叫轉譯為 `Delete` 信號。

> [!NOTE]
> Api 只能用於 `SignalEntityAsync` 單向作業。 即使作業傳回 `Task<T>` ，參數的值 `T` 一律會是 null 或，而不是 `default` 實際的結果。
例如，發出作業並不合理 `Get` ，因為不會傳回任何值。 相反地，用戶端可以使用 `ReadStateAsync` 來直接存取計數器狀態，或是啟動會呼叫作業的協調器函式 `Get` 。 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>範例：協調流程第一個信號，然後透過 proxy 呼叫實體

若要從協調流程內呼叫或發出實體的信號， `CreateEntityProxy` 可以搭配介面類別型使用，以產生實體的 proxy。 此 proxy 可接著用來呼叫或信號作業：

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

任何傳回的作業會隱含地 `void` 發出信號，以及傳回或的任何作業 `Task` 都會被 `Task<T>` 呼叫。 只要使用方法，就可以變更此預設行為，以及信號作業會傳回工作 `SignalEntity<IInterfaceType>` 。

### <a name="shorter-option-for-specifying-the-target"></a>指定目標的較短選項

使用介面呼叫或發出實體的信號時，第一個引數必須指定目標實體。 您可以藉由指定實體識別碼來指定目標，或在只有一個類別可執行實體的情況下（僅限實體索引鍵）來指定目標：

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

如果只指定實體索引鍵，而且在執行時間找不到唯一的執行， `InvalidOperationException` 則會擲回。 

### <a name="restrictions-on-entity-interfaces"></a>實體介面的限制

如同往常，所有參數和傳回型別都必須是 JSON 可序列化。 否則，會在執行時間擲回序列化例外狀況。

我們也會強制執行一些額外的規則：
* 實體介面必須僅定義方法。
* 實體介面不能包含泛型參數。
* 實體介面方法不能有一個以上的參數。
* 實體介面方法必須傳回 `void` 、 `Task` 或`Task<T>` 

如果違反其中任何一項規則， `InvalidOperationException` 當介面當做或的型別引數使用時，就會在執行時間擲回 `SignalEntity` `CreateProxy` 。 例外狀況訊息會說明哪一個規則已中斷。

> [!NOTE]
> 傳回的介面方法 `void` 只能有信號（單向），不能呼叫（雙向）。 傳回或的介面方法 `Task` `Task<T>` 可以是呼叫或信號。 如果呼叫，則會傳回作業的結果，或重新擲回作業所擲出的例外狀況。 不過，當發出信號時，它們不會從作業傳回實際的結果或例外狀況，而只會傳回預設值。

## <a name="entity-serialization"></a>實體序列化

由於實體的狀態是永久保存的，因此實體類別必須是可序列化的。 Durable Functions 執行時間會針對此目的使用[Json.NET](https://www.newtonsoft.com/json)程式庫，它支援多個原則和屬性來控制序列化和還原序列化程式。 最常用的 c # 資料類型（包括陣列和集合類型）已經可序列化，而且很容易用於定義持久性實體的狀態。

例如，Json.NET 可以輕鬆地序列化和還原序列化下列類別：

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>序列化屬性

在上述範例中，我們選擇包含數個屬性，讓基礎序列化更為可見：
- 我們使用來標注類別， `[JsonObject(MemberSerialization.OptIn)]` 以提醒我們類別必須是可序列化的，而且只會保存明確標示為 JSON 屬性的成員。
-  我們會將要保存的欄位加上批註， `[JsonProperty("name")]` 以提醒我們欄位是保存實體狀態的一部分，並指定要在 JSON 標記法中使用的屬性名稱。

不過，這些屬性並不是必要的;只要使用 Json.NET，就可以使用其他慣例或屬性。 例如，其中一個可以使用 `[DataContract]` 屬性，或完全沒有屬性：

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

根據預設，類別的名稱*不*會儲存為 JSON 標記法的一部分：也就是說，我們會使用 `TypeNameHandling.None` 做為預設設定。 您可以使用或屬性來覆寫這個預設行為 `JsonObject` `JsonProperty` 。

### <a name="making-changes-to-class-definitions"></a>對類別定義進行變更

在執行應用程式之後對類別定義進行變更時，必須特別小心，因為儲存的 JSON 物件可能不再符合新的類別定義。 儘管如此，只要一個人瞭解所使用的還原序列化程式，通常就可以正確地處理資料格式的變更 `JsonConvert.PopulateObject` 。

例如，以下是一些變更和其效果的範例：

1. 如果加入新的屬性（不存在於已儲存的 JSON 中），它會假設其預設值。
1. 如果已移除已儲存 JSON 中的屬性，則會遺失先前的內容。
1. 如果屬性已重新命名，則效果會如同移除舊的內容，並加入新的。
1. 如果屬性的類型已變更，因此無法再從已儲存的 JSON 還原序列化，則會擲回例外狀況。
1. 如果屬性的類型已變更，但仍可從已儲存的 JSON 還原序列化，則會這麼做。

有許多選項可用於自訂 Json.NET 的行為。 例如，若要在儲存的 JSON 包含不存在於類別中的欄位時強制例外，請指定屬性 `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)` 。 您也可以撰寫可讀取以任意格式儲存 JSON 之還原序列化的自訂程式碼。

## <a name="entity-construction"></a>實體結構

有時候我們會想要更充分掌控實體物件的結構化方式。 我們現在會描述數個選項，讓您在建立實體物件時變更預設行為。 

### <a name="custom-initialization-on-first-access"></a>第一次存取時的自訂初始化

有時候，我們必須先執行一些特殊的初始化作業，才能將作業分派至從未存取過的實體，或已被刪除。 若要指定此行為，可以在之前新增條件 `DispatchAsync` ：

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>實體類別中的系結

與一般函式不同的是，實體類別方法無法直接存取輸入和輸出系結。 相反地，其必須在進入點函式宣告中擷取繫結資料，然後再傳遞至 `DispatchAsync<T>` 方法。 任何傳遞至 `DispatchAsync<T>` 的物件都會自動以引數的形式傳遞至實體類別建構函式。

下列範例會說明如何讓以類別為基礎的實體可以使用來自 [Blob 輸入繫結](../functions-bindings-storage-blob-input.md)的 `CloudBlobContainer` 參考。

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

如需 Azure Functions 中繫結的詳細資訊，請參閱 [Azure Functions 觸發程序和繫結](../functions-triggers-bindings.md)文件。

### <a name="dependency-injection-in-entity-classes"></a>實體類別中的相依性插入

實體類別支援 [Azure Functions 相依性插入](../functions-dotnet-dependency-injection.md)。 下列範例會示範如何將 `IHttpClientFactory` 服務註冊到以類別為基礎的實體。

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

下列程式碼片段會示範如何將插入的服務併入到實體類別。

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> 若要避免序列化的問題，請務必排除用來儲存序列化中插入值的欄位。

> [!NOTE]
> 不同於在一般的 .NET Azure Functions 中使用建構函式插入時，以類別為基礎的實體「必須」** 將函式進入點方法宣告為 `static`。 宣告非靜態函式進入點可能會導致一般 Azure Functions 物件初始設定式與持久性實體物件初始設定式之間發生衝突。

## <a name="function-based-syntax"></a>以函式為基礎的語法

到目前為止，我們都把重點放在以類別為基礎的語法，因為我們希望它更適合大部分的應用程式。 不過，以函式為基礎的語法適用于想要針對實體狀態和作業定義或管理自己的抽象概念的應用程式。 此外，在執行需要 genericity 目前以類別為基礎的語法不支援的程式庫時，也可能適合使用此方法。 

使用以函式為基礎的語法時，Entity 函數會明確處理作業分派，並明確管理實體的狀態。 例如，下列程式碼顯示使用以函式為基礎的語法所實作用的*計數器*實體。  

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>實體內容物件

實體特有的功能可以透過類型的內容物件來存取 `IDurableEntityContext` 。 這個內容物件可做為實體函式的參數，以及透過非同步區域屬性來提供 `Entity.Current` 。

下列成員提供目前作業的相關資訊，並可讓我們指定傳回值。 

* `EntityName`：目前正在執行之實體的名稱。
* `EntityKey`：目前正在執行之實體的索引鍵。
* `EntityId`：目前執行之實體的識別碼（包括名稱和索引鍵）。
* `OperationName`：目前作業的名稱。
* `GetInput<TInput>()`：取得目前作業的輸入。
* `Return(arg)`：將值傳回至呼叫作業的協調流程。

下列成員會管理實體的狀態（[建立]、[讀取]、[更新]、[刪除]）。 

* `HasState`：實體是否存在，也就是具有某種狀態。 
* `GetState<TState>()`：取得實體的目前狀態。 如果尚未存在，則會建立它。
* `SetState(arg)`：建立或更新實體的狀態。
* `DeleteState()`：刪除實體的狀態（如果有的話）。 

如果傳回的狀態 `GetState` 是物件，則應用程式代碼可以直接修改它。 不需要 `SetState` 在結尾再次呼叫（但也不會有任何傷害）。 如果多次 `GetState<TState>` 呼叫，則必須使用相同的類型。

最後，使用下列成員來通知其他實體，或啟動新的協調流程：

* `SignalEntity(EntityId, operation, input)`：將單向訊息傳送至實體。
* `CreateNewOrchestration(orchestratorFunctionName, input)`：啟動新的協調流程。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解實體概念](durable-functions-entities.md)
