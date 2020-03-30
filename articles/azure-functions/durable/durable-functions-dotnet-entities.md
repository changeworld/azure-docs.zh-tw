---
title: .NET 中持久實體的開發人員指南 - Azure 函數
description: 如何使用 .NET 中的持久實體與 Azure 函數的持久函數擴展。
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278125"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>.NET 中持久實體的開發人員指南

在本文中，我們將詳細介紹用於使用 .NET 開發持久實體的可用介面，包括示例和一般建議。 

實體函數為無伺服器應用程式開發人員提供了一種將應用程式狀態組織為細細微性實體集合的便捷方法。 有關基本概念的更多詳細資訊，請參閱["持久實體：概念"](durable-functions-entities.md)一文。

我們目前提供兩個 API 用於定義實體：

- **基於類的語法**將實體和動作表示為類和方法。 此語法生成易於讀懂的代碼，並允許通過介面以類型檢查的方式叫用作業。 

- **基於函數的語法**是一個較低級別的介面，將實體表示為函數。 它提供對實體操作調度方式以及實體狀態管理方式的精確控制。  

本文主要側重于基於類的語法，因為我們預計它更適合大多數應用程式。 但是，[基於函數的語法](#function-based-syntax)可能適用于希望為實體狀態和操作定義或管理自己的抽象的應用程式。 此外，它可能適用于實現需要泛型的庫，這些庫當前不受基於類的語法的支援。 

> [!NOTE]
> 基於類的語法只是基於函數的語法之上的一個層，因此兩個變體可以在同一應用程式中互換使用。 
 
## <a name="defining-entity-classes"></a>定義實體類

下面的示例`Counter`是實體的實現，該實體存儲類型整數的單一值，並提供四個操作`Add` `Reset`、`Get`和`Delete`。

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

該`Run`函數包含使用基於類的語法所需的樣板。 它必須是*靜態*Azure 函數。 它對於實體處理的每個操作消息執行一次。 當`DispatchAsync<T>`調用並且實體尚未在記憶體中時，它將構造類型`T`的物件，並從在存儲中找到的最後一個持久化 JSON（如果有）填充其欄位。 然後，它調用具有匹配名稱的方法。

> [!NOTE]
> 基於類的實體的狀態在實體處理操作之前**隱式創建**，並且可以通過調用`Entity.Current.DeleteState()`在操作中**顯式刪除**。

### <a name="class-requirements"></a>類要求
 
實體類是 POCO（普通舊 CLR 物件），不需要特殊的超類、介面或屬性。 但是：

- 類必須是可構造的（請參閱[實體構造](#entity-construction)）。
- 類必須是 JSON 序列化的（請參閱[實體序列化](#entity-serialization)）。

此外，任何旨在作為操作調用的方法都必須滿足其他要求：

- 操作最多隻能有一個參數，並且不能具有任何重載或泛型型別參數。
- 打算使用介面從業務流程調用的操作必須返回`Task`或`Task<T>`。
- 參數和傳回值必須是可序列化的值或物件。

### <a name="what-can-operations-do"></a>操作可以做什麼？

所有實體操作都可以讀取和更新實體狀態，並且對狀態的更改將自動儲存到存儲。 此外，操作可以在所有 Azure 函數共有的一般限制內執行外部 I/O 或其他計算。

操作還可以訪問`Entity.Current`上下文提供的功能：

* `EntityName`：當前執行實體的名稱。
* `EntityKey`：當前執行實體的鍵。
* `EntityId`：當前執行實體的 ID（包括名稱和鍵）。
* `SignalEntity`：向實體發送單向消息。
* `CreateNewOrchestration`：開始新的業務流程。
* `DeleteState`：刪除此實體的狀態。

例如，我們可以修改計數器實體，以便在計數器達到 100 時啟動業務流程，並將實體 ID 作為輸入參數傳遞：

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>直接存取實體

可以直接存取基於類的實體，使用實體及其操作的顯式字串名稱。 下面我們提供了一些示例;有關基本概念（如信號與調用）的更深入解釋，請參閱[Access 實體](durable-functions-entities.md#access-entities)中的討論。 

> [!NOTE]
> 在可能的情況下，我們建議[通過介面訪問實體](#accessing-entities-through-interfaces)，因為它提供了更多的類型檢查。

### <a name="example-client-signals-entity"></a>示例：用戶端信號實體

以下 Azure Http 函數使用 REST 約定實現 DELETE 操作。 它將刪除信號發送到其金鑰在 URL 路徑中傳遞的計數器實體。

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

### <a name="example-client-reads-entity-state"></a>示例：用戶端讀取實體狀態

以下 Azure Http 函數使用 REST 約定實現 GET 操作。 它讀取其金鑰在 URL 路徑中傳遞的計數器實體的目前狀態。

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
> 返回`ReadEntityStateAsync`的物件只是本機複本，即來自某個較早時間點的實體狀態的快照。 特別是，它可能過時，並修改此物件對實際實體沒有影響。 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>示例：業務流程優先發出信號，然後調用實體

以下業務流程發出計數器實體增加該業務流程的信號，然後調用同一實體讀取其最新值。

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

## <a name="accessing-entities-through-interfaces"></a>通過介面訪問實體

介面可用於通過生成的代理物件訪問實體。 此方法可確保操作的名稱和參數類型與實現的內容匹配。 我們建議盡可能使用介面訪問實體。

例如，我們可以修改計數器示例，如下所示：

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

實體類和實體介面類別似于[奧爾良](https://www.microsoft.com/research/project/orleans-virtual-actors/)推廣的穀物和穀物介面。 有關持久實體和奧爾良之間的相似性和差異的詳細資訊，請參閱[與虛擬參與者的比較](durable-functions-entities.md#comparison-with-virtual-actors)。

除了提供類型檢查外，介面還可用於更好地分離應用程式中的顧慮。 例如，由於實體可以實現多個介面，因此單個實體可以充當多個角色。 此外，由於介面可能由多個實體實現，因此一般通訊模式可以作為可重用的庫實現。

### <a name="example-client-signals-entity-through-interface"></a>示例：通過介面發出實體的用戶端信號

用戶端代碼可用於`SignalEntityAsync<TEntityInterface>`向實現`TEntityInterface`的實體發送信號。 例如：

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

在此示例中，`proxy`參數是動態生成的 實例`ICounter`，在內部將調用`Delete`轉換為信號。

> [!NOTE]
> API`SignalEntityAsync`只能用於單向操作。 即使操作返回`Task<T>`，`T`參數的值將始終為空或`default`，而不是實際結果。
例如，由於不返回任何值，因此發出`Get`操作信號沒有意義。 相反，用戶端可以使用 來`ReadStateAsync`直接存取計數器狀態，也可以啟動叫用作業的`Get`協調器函數。 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>示例：業務流程優先發出信號，然後通過代理調用實體

要從業務流程中調用實體或發出信號，`CreateEntityProxy`可以使用介面類別型來生成實體的代理。 然後，此代理可用於調用或信號操作：

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

隱式，返回的任何操作`void`都發出信號，以及返回`Task`或`Task<T>`調用的任何操作。 可以顯式使用`SignalEntity<IInterfaceType>`方法更改此預設行為，即使返回 Task 也會發出信號操作。

### <a name="shorter-option-for-specifying-the-target"></a>用於指定目標的更短選項

使用介面調用實體或發出信號時，第一個參數必須指定目標實體。 目標可以通過指定實體 ID 來指定，或者，在只有一個實現實體的類的情況下，只需實體金鑰：

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

如果只指定實體金鑰，並且在運行時找不到唯一實現，`InvalidOperationException`則引發。 

### <a name="restrictions-on-entity-interfaces"></a>對實體介面的限制

與往常一樣，所有參數和返回類型都必須是 JSON 可序列化的。 否則，在運行時引發序列化異常。

我們還強制實施一些附加規則：
* 實體介面必須僅定義方法。
* 實體介面不得包含泛型參數。
* 實體介面方法不能有多個參數。
* 實體介面方法必須返回`void` `Task`、或`Task<T>` 

如果違反其中任何一個規則，則`InvalidOperationException`當介面用作`SignalEntity`或 的`CreateProxy`類型參數時，在運行時引發 。 異常消息解釋已斷開的規則。

> [!NOTE]
> 返回的介面`void`方法只能發出信號（單向），不能調用（雙向）。 介面方法返回`Task`或`Task<T>`可以調用或表示。 如果調用，它們返回操作的結果，或重新引發操作引發的異常。 但是，當發出信號時，它們不會從操作返回實際結果或異常，而只返回預設值。

## <a name="entity-serialization"></a>實體序列化

由於實體的狀態持久存在，因此實體類必須是可序列化的。 持久函數運行時為此使用[Json.NET](https://www.newtonsoft.com/json)庫，該庫支援許多策略和屬性來控制序列化和反序列化過程。 最常用的 C# 資料類型（包括陣列和集合類型）已可序列化，並可輕鬆用於定義持久實體的狀態。

例如，Json.NET可以輕鬆地序列化和反序列化以下類：

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

在上面的示例中，我們選擇包括幾個屬性，以使基礎序列化更加可見：
- 我們用標記類`[JsonObject(MemberSerialization.OptIn)]`來提醒我們類必須是可序列化的，並且僅保留顯式標記為 JSON 屬性的成員。
-  我們給要保留的欄位進行批號，`[JsonProperty("name")]`以提醒我們欄位是持久化實體狀態的一部分，並指定要在 JSON 表示形式中使用的屬性名稱。

但是，這些屬性不是必需的，但是，允許其他約定或屬性，只要它們使用Json.NET。 例如，一個人可能使用`[DataContract]`屬性，或者根本不使用屬性：

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

預設情況下，類的名稱*不會*存儲為 JSON 表示的一部分：也就是說，我們用作`TypeNameHandling.None`預設設置。 可以使用`JsonObject`或`JsonProperty`屬性重寫此預設行為。

### <a name="making-changes-to-class-definitions"></a>更改類定義

在運行應用程式後對類定義進行更改時，需要一些謹慎，因為存儲的 JSON 物件可能不再與新的類定義匹配。 但是，只要瞭解`JsonConvert.PopulateObject`所使用的反序列化過程，通常就可以正確處理不斷變化的資料格式。

例如，下面是一些更改及其效果的示例：

1. 如果添加新屬性（該屬性不存在於存儲的 JSON 中），則假定其預設值。
1. 如果刪除存儲的 JSON 中存在的屬性，則前面的內容將丟失。
1. 如果重命名了屬性，則效果就像刪除舊屬性並添加新屬性一樣。
1. 如果屬性的類型已更改，以便無法再從存儲的 JSON 進行反序列化，則引發異常。
1. 如果屬性的類型已更改，但仍可以從存儲的 JSON 中反序列化，則這樣做。

有許多選項可用於自訂Json.NET的行為。 例如，如果存儲的 JSON 包含類中不存在的欄位，則強制異常，請指定屬性`JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`。 還可以編寫自訂代碼進行反序列化，這些代碼可以讀取以任意格式存儲的 JSON。

## <a name="entity-construction"></a>實體構建

有時，我們希望對實體物件的構造方式施加更多控制。 現在，我們將介紹幾個選項，用於在構造實體物件時更改預設行為。 

### <a name="custom-initialization-on-first-access"></a>首次訪問時自訂初始化

有時，我們需要在將操作調度到從未訪問過或已刪除的實體之前執行一些特殊初始化。 要指定此行為，可以在 之前`DispatchAsync`添加條件：

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

### <a name="bindings-in-entity-classes"></a>實體類中的綁定

與常規函數不同，實體類方法不能直接存取輸入和輸出綁定。 相反地，其必須在進入點函式宣告中擷取繫結資料，然後再傳遞至 `DispatchAsync<T>` 方法。 任何傳遞至 `DispatchAsync<T>` 的物件都會自動以引數的形式傳遞至實體類別建構函式。

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

### <a name="dependency-injection-in-entity-classes"></a>實體類中的依賴項注入

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
> 為了避免序列化問題，請確保從序列化中排除用於存儲注入值的欄位。

> [!NOTE]
> 不同於在一般的 .NET Azure Functions 中使用建構函式插入時，以類別為基礎的實體「必須」** 將函式進入點方法宣告為 `static`。 宣告非靜態函式進入點可能會導致一般 Azure Functions 物件初始設定式與持久性實體物件初始設定式之間發生衝突。

## <a name="function-based-syntax"></a>基於函數的語法

到目前為止，我們專注于基於類的語法，因為我們預計它更適合大多數應用程式。 但是，基於函數的語法可以適用于希望為實體狀態和操作定義或管理自己的抽象的應用程式。 此外，在實現需要泛型的庫時，可能適合基於類的語法當前所支援的泛型。 

使用基於函數的語法，實體函數顯式處理操作調度，並顯式管理實體的狀態。 例如，以下代碼顯示使用基於函數的語法實現的*計數器*實體。  

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

### <a name="the-entity-context-object"></a>實體上下文物件

實體特定的功能可以通過類型的`IDurableEntityContext`上下文物件進行訪問。 此上下文物件作為實體函數的參數以及通過非同步本地屬性`Entity.Current`可用。

以下成員提供有關當前操作的資訊，並允許我們指定傳回值。 

* `EntityName`：當前執行實體的名稱。
* `EntityKey`：當前執行實體的鍵。
* `EntityId`：當前執行實體的 ID（包括名稱和鍵）。
* `OperationName`：當前操作的名稱。
* `GetInput<TInput>()`：獲取當前操作的輸入。
* `Return(arg)`：向調用該操作的業務流程返回一個值。

以下成員管理實體的狀態（創建、讀取、更新、刪除）。 

* `HasState`：實體是否存在，即具有某種狀態。 
* `GetState<TState>()`：獲取實體的目前狀態。 如果不存在，則創建它。
* `SetState(arg)`：創建或更新實體的狀態。
* `DeleteState()`：刪除實體的狀態（如果存在）。 

如果返回`GetState`的狀態是一個物件，則應用程式代碼可以直接修改它。 無需在末尾再次調用`SetState`（但也無傷害）。 如果`GetState<TState>`多次調用，則必須使用相同的類型。

最後，以下成員用於向其他實體發出信號，或啟動新的業務流程：

* `SignalEntity(EntityId, operation, input)`：向實體發送單向消息。
* `CreateNewOrchestration(orchestratorFunctionName, input)`：開始新的業務流程。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解實體概念](durable-functions-entities.md)
