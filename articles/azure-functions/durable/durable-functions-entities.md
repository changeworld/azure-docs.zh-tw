---
title: 持久性實體 - Azure Functions
description: 了解什麼是持久性實體，以及如何在 Azure Functions 的 Durable Functions 擴充功能中使用這些實體。
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4f45ac40e7df865bdb4722d086325096c377cd59
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877537"
---
# <a name="entity-functions"></a>實體函式

實體函式會定義用於讀取和更新一小段狀態 (稱為「持久性實體」  ) 的作業。 和協調器函式一樣，實體函式也是具有特殊觸發程序類型 (「實體觸發程序」  ) 的函式。 不同於協調器函式，實體函式會明確管理實體的狀態，而不是透過控制流程來隱含表示狀態。
實體提供一個方法來擴充應用程式，方法是將工作分散到許多實體，而每個實體都有適度大小的狀態。

> [!NOTE]
> 只有 Durable Functions 2.0 和更新版本有提供實體函式和相關功能。

## <a name="general-concepts"></a>一般概念

實體的行為有點像透過訊息進行通訊的小型服務。 每個實體都有唯一的身分識別和內部狀態 (如果有的話)。 就像服務或物件一樣，實體會在出現提示時執行作業。 當它執行時，作業可能會更新實體的內部狀態。 它也可以呼叫外部服務並等候回應。 實體會使用透過可靠佇列隱含傳送的訊息，與其他實體、協調流程和用戶端進行通訊。 

為了避免衝突，單一實體上的所有作業保證會以序列方式執行，也就是一個接一個執行。 

### <a name="entity-id"></a>實體識別碼
實體是透過唯一識別碼 (即「實體識別碼」  ) 來存取。 實體識別碼就只是一對可唯一識別實體執行個體的字串。 其中包括：

* **實體名稱**，這是用來識別實體類型的名稱。 例如 "Counter"。 此名稱必須符合實作為實體的實體函式名稱。 它不會區分大小寫。
* **實體索引鍵**，這是可在所有其他同名實體之間唯一識別實體的字串。 例如 GUID。

例如，`Counter` 實體函式可用來保存線上遊戲中的分數。 遊戲的每個執行個體都會有唯一的實體識別碼，例如 `@Counter@Game1` 和 `@Counter@Game2`。 以特定實體為目標的所有作業都必須將實體識別碼指定為參數。

### <a name="entity-operations"></a>實體作業 ###

若要在實體上叫用作業，請指定：

* 目標實體的**實體識別碼**。
* **作業名稱**，這是指定要執行之作業的字串。 例如，`Counter` 實體可支援 `add`、`get` 或 `reset` 作業。
* **作業輸入**，這是作業的選擇性輸入參數。 例如，新增作業可接受整數數量作為輸入。
* **排定的時間**，這是指定作業傳遞時間的選擇性參數。 例如，可以將作業可靠地排定在未來幾天執行。

作業可以傳回結果值或錯誤結果，例如 JavaScript 錯誤或 .NET 例外狀況。 呼叫作業的協調流程可以觀察到此結果或錯誤。

實體作業也可以建立、讀取、更新和刪除實體的狀態。 實體的狀態一律會永久保存在儲存體中。

## <a name="define-entities"></a>定義實體

目前有兩個不同的 API 可用來定義實體：

**以函式為基礎的語法**，其中實體會表示為函式，而作業會由應用程式明確分派。 此語法很適合於具有簡單狀態、少數作業或一組動態作業 (例如在應用程式架構中) 的實體。 此語法的維護工作可能會很繁瑣，因為它不會在編譯時期攔截類型錯誤。

**以類別為基礎的語法 (僅限 .NET)** ，其中實體和作業是由類別和方法表示。 此語法會產生更易讀取的程式碼，並允許以型別安全的方式叫用作業。 以類別為基礎的語法是以函式為基礎的語法之上的精簡層，因此這兩個變體可以在相同的應用程式中交替使用。

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>範例：以函式為基礎的語法 - C#

下列程式碼會舉例說明實作為耐久函式的簡單 `Counter` 實體。 此函式會定義三個作業 (`add`、`reset` 和 `get`)，每個作業都會以整數狀態運作。

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

如需以函式為基礎的語法及其使用方式的詳細資訊，請參閱[以函式為基礎的語法](durable-functions-dotnet-entities.md#function-based-syntax)。

### <a name="example-class-based-syntax---c"></a>範例：以類別為基礎的語法 - C#

下列範例等同於使用類別和方法來實作 `Counter` 實體。

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

此實體的狀態是 `Counter` 類型的物件，其中包含可儲存計數器目前值的欄位。 為了將此物件保存在儲存體中，該物件會由 [Json.NET](https://www.newtonsoft.com/json) 程式庫進行序列化和還原序列化。 

如需以類別為基礎的語法及其使用方式的詳細資訊，請參閱[定義實體類別](durable-functions-dotnet-entities.md#defining-entity-classes)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>範例：JavaScript 實體

從 `durable-functions` npm 套件 **1.3.0** 版開始，JavaScript 提供了耐久性實體。 下列程式碼是實作為以 JavaScript 撰寫的耐久函式的 `Counter` 實體。

**Counter/function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Counter/index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

## <a name="access-entities"></a>存取實體

您可使用單向或雙向通訊來存取實體。 下列術語可區分這兩種形式的通訊： 

* **呼叫**實體時會使用雙向 (來回) 通訊。 您會將作業訊息傳送至實體，然後等候回應訊息，再繼續操作。 回應訊息可提供結果值或錯誤結果，例如 JavaScript 錯誤或 .NET 例外狀況。 接著呼叫端會觀察到此結果或錯誤。
* 傳送**訊號**給實體時會使用單向 (啟動後便不予理會) 通訊。 您會傳送作業訊息，但不等候回應。 雖然訊息保證最終會傳遞，但傳送者不知道何時，且無法觀察到任何結果值或錯誤。

您可以從用戶端函式、協調器函式或實體函式內存取實體。 並非所有形式的通訊都受到所有內容支援：

* 從用戶端，您可以傳送訊號給實體，而且可以讀取實體狀態。
* 從協調流程，您可以傳送訊號給實體，而且可以呼叫實體。
* 從實體，您可以傳送訊號給實體。

下列範例說明這些用來存取實體的各種方式。

### <a name="example-client-signals-an-entity"></a>範例：用戶端傳送訊號給實體

若要從一般 Azure 函式 (也稱為用戶端函式) 存取實體，請使用[實體用戶端繫結](durable-functions-bindings.md#entity-client)。 下列範例會說明由佇列觸發的函式，該函式會使用此繫結對實體傳送訊號。

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> 為了簡單起見，下列範例會顯示用來存取實體的鬆散型別語法。 一般來說，我們建議您[透過介面存取實體](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)，因為此方式可提供更多類型檢查。

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

---

「訊號」  一詞表示實體 API 的叫用是單向且非同步的。 用戶端函式無法知道實體處理作業的時間。 此外，用戶端函式無法觀察到任何結果值或例外狀況。 

### <a name="example-client-reads-an-entity-state"></a>範例：用戶端讀取實體狀態

用戶端函式也可以查詢實體的狀態，如下列範例所示：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

---

實體狀態查詢會傳送至持久性追蹤存放區，並傳回實體最近的持久性狀態。 此狀態一律是「已認可」狀態，也就是在執行作業的過程中，永遠不會有暫時的中繼狀態。 不過，相較於實體的記憶體內部狀態，此狀態可能會過時。 只有協調流程可以讀取實體的記憶體內部狀態，下一節會有相關說明。

### <a name="example-orchestration-signals-and-calls-an-entity"></a>範例：協調流程傳送訊號並呼叫實體

協調器函式可以使用[協調流程觸發程序繫結](durable-functions-bindings.md#orchestration-trigger)上的 API 來存取實體。 下列程式碼範例說明呼叫 `Counter` 實體並對其傳送訊號的協調器函式。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript 目前不支援從協調器對實體發出訊號。 請改用 `callEntity`。

---

只有協調流程能夠呼叫實體並取得回應，而此回應可能是傳回值或例外狀況。 使用[用戶端繫結](durable-functions-bindings.md#entity-client)的用戶端函式只能對實體傳送訊號。

> [!NOTE]
> 從協調器函式呼叫實體類似於從協調器函式呼叫[活動函式](durable-functions-types-features-overview.md#activity-functions)。 主要差異在於實體函式是具有位址 (實體識別碼) 的持久性物件。 實體函式支援指定作業名稱。 另一方面，活動函式則是無狀態的，且沒有操作概念。

### <a name="example-entity-signals-an-entity"></a>範例：實體傳送訊號給實體

實體函式可以在執行作業時傳送訊號給其他實體，甚或傳送給本身。
例如，我們可以修改上述的 `Counter` 實體範例，以便在計數器達到值 100 時，傳送「達到里程碑」訊號給某個監視器實體。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>實體協調 (目前僅限 .NET)

有時候您可能需要協調多個實體的作業。 例如，在銀行應用程式中，您可能有代表個別銀行帳戶的實體。 將資金從某個帳戶轉移到另一個帳戶時，您必須確定來源帳戶有足夠的資金。 您也必須確定會以交易一致的方式完成來源和目的地帳戶的更新。

### <a name="example-transfer-funds-c"></a>範例：轉移資金 (C#)

下列程式碼範例會使用協調器函式，在兩個帳戶實體之間轉移資金。 若要協調實體更新，就必須使用 `LockAsync` 方法在協調流程中建立「重要區段」  。

> [!NOTE]
> 為了簡單起見，此範例會重複使用先前定義的 `Counter` 實體。 在實際的應用程式中，建議您定義更詳細的 `BankAccount` 實體。

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

在 .NET 中，`LockAsync` 會傳回 `IDisposable`，而在完成處置時結束重要區段。 這個 `IDisposable` 結果可以與 `using` 區塊一起使用，以獲得用語法表示的重要區段。

在上述範例中，協調器函式會將資金從來源實體轉移到目的地實體。 `LockAsync` 方法已鎖定來源和目的地帳戶實體。 此鎖定確保了在協調流程邏輯於 `using` 陳述式結束而離開「重要區段」之前，其他所有用戶端皆無法查詢或修改任一帳戶的狀態。 此行為可以防止來源帳戶出現透支的可能性。

> [!NOTE] 
> 當協調流程終止 (按慣例或因為錯誤) 時，任何進行中的重要區段都會隱含地結束，而且會釋放所有的鎖定。

### <a name="critical-section-behavior"></a>重要區段行為

`LockAsync` 方法會在協調流程中建立重要區段。 這些重要區段會防止其他協調流程對一組指定的實體進行重疊變更。 從內部來看，`LockAsync` API 會將「鎖定」作業傳送至實體，並在收到來自上述每個相同實體的「已取得鎖定」回應訊息時傳回。 鎖定和解除鎖定是所有實體皆支援的內建作業。

當實體處於鎖定狀態時，就不會允許來自其他用戶端的任何作業。 此行為可確保一次只有一個協調流程執行個體可以鎖定實體。 如果呼叫端嘗試在協調流程鎖定的實體上叫用作業，該作業將會放入擱置作業佇列中。 在握有鎖定實體的協調流程釋放該鎖定之前，系統不會處理任何擱置作業。

> [!NOTE] 
> 此行為與大多數程式設計語言 (例如，C# 中的 `lock` 陳述式) 中使用的同步處理基元有些微不同。 例如，在 C# 中，所有執行緒都必須使用 `lock` 陳述式，以確保多個執行緒會正確同步。 不過，實體不會要求所有呼叫端明確鎖定實體。 如果有任何呼叫端鎖定實體，該實體上的所有其他作業將會遭到封鎖，並排入到佇列中的該鎖定之後。

實體的鎖定具有持久性，因此即使執行中的程序已回收，鎖定仍會保留下來。 在內部，鎖定會保留下來而成為實體持久性狀態的一部分。

與交易不同的是，重要區段不會在發生錯誤時自動復原變更。 任何錯誤處理 (如復原或重試) 都必須明確地編碼，例如，藉由攔截錯誤或例外狀況。 這是刻意設計的選擇。 自動復原協調流程的所有效果，通常很難或根本不可能，因為協調流程可以執行活動並呼叫無法復原的外部服務。 此外，復原嘗試可能會失敗，而且需要進一步的錯誤處理。

### <a name="critical-section-rules"></a>關鍵區段規則

不同於大部分程式設計語言中的低階鎖定基元，關鍵區段*保證不會鎖死*。 為了避免鎖死，我們會強制執行下列限制： 

* 重要區段無法形成巢狀結構。
* 重要區段無法建立子協調流程。
* 重要區段只能呼叫其已鎖定的實體。
* 重要區段無法使用多個平行呼叫來呼叫相同實體。
* 重要區段只能對其尚未鎖定的實體傳送訊號。

違反這些規則會造成執行階段錯誤 (例如 .NET 中的 `LockingRulesViolationException`)，其中包含說明違反哪項規則的訊息。

## <a name="comparison-with-virtual-actors"></a>與虛擬執行者的比較

許多持久性實體的功能都受到[執行者模型](https://en.wikipedia.org/wiki/Actor_model)的啟發。 如果您已熟悉執行者，或許就能理解本文所述的許多概念。 持久性實體特別類似於 [Orleans 專案](http://dotnet.github.io/orleans/)所推廣的[虛擬執行者](https://research.microsoft.com/projects/orleans/)或「粒紋」。 例如：

* 持久性實體可透過實體識別碼來定址。
* 持久性實體的作業會以序列方式一次執行一個，以免產生競爭條件。
* 系統會在呼叫或發出訊號時隱含地建立持久性實體。
* 沒有執行中的作業時，會以無訊息方式從記憶體中卸載持久性實體。

有一些重要的差異值得注意：

* 持久性實體更注重持久性而非延遲，因此可能不適用於具有嚴格延遲需求的應用程式。
* 持久性實體沒有內建的訊息逾時。 在 Orleans 中，所有訊息都會在可設定的時間經過後逾時。 預設值為 30 秒。
* 實體之間傳送的訊息會以可靠的方式依序傳遞。 在 Orleans 中，透過資料流傳送的內容可支援可靠或排序的傳遞，但不保證會傳遞粒紋之間的所有訊息。
* 實體中的要求/回應模式僅限於協調流程。 如同原始動作項目模型 (而不像 Orleans 中的粒紋)，從實體，只允許單向傳訊 (也稱為傳送訊號)。 
* 持久性實體不會鎖死。 在 Orleans 中，可能會發生鎖死，且必須到訊息逾期後才會解決。
* 持久性實體可與持久性協調流程搭配使用，並支援分散式鎖定機制。 


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [閱讀 .NET 中持久性實體的開發人員指南](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [了解工作中樞](durable-functions-task-hubs.md)
