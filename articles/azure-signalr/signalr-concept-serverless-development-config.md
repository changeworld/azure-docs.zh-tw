---
title: 開發 & 設定 Azure Functions 應用程式-Azure SignalR
description: 有關如何使用 Azure Functions 和 Azure SignalR Service 開發和設定無伺服器即時應用程式的詳細資料
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: acb85a04b8a1ca491058702510079a36b93fc657
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151036"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>使用 Azure SignalR Service 來開發與設定 Azure Functions

Azure Functions 的應用程式可以利用 [Azure SignalR Service](../azure-functions/functions-bindings-signalr-service.md) 系結來新增即時功能。 用戶端應用程式會使用多個語言提供的用戶端 Sdk，以連接 Azure SignalR Service 並接收即時訊息。

本文說明開發和設定與 SignalR Service 整合的 Azure 函數應用程式的概念。

## <a name="signalr-service-configuration"></a>SignalR Service 設定

Azure SignalR Service 可以在不同的模式中設定。 搭配 Azure Functions 使用時，必須在 *無伺服器* 模式中設定服務。

在 Azure 入口網站中，找出 SignalR Service 資源的 [ *設定* ] 頁面。 將 [ *服務模式]* 設定為 [ *無伺服器*]。

![SignalR Service 模式](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 開發

以 Azure Functions 和 Azure SignalR Service 建立的無伺服器即時應用程式通常需要兩個 Azure Functions：

* "negotiate" 函式，用戶端會呼叫該函式來取得有效的 SignalR Service 存取權杖和服務端點 URL
* 一或多個處理來自 SignalR Service 和傳送訊息或管理群組成員資格之訊息的函數

### <a name="negotiate-function"></a>negotiate 函數

用戶端應用程式需要有效的存取權杖，才能連接到 Azure SignalR Service。 存取權杖可以匿名或驗證為指定的使用者識別碼。 無伺服器 SignalR Service 應用程式需要名為 "negotiate" 的 HTTP 端點，以取得權杖和其他連接資訊，例如 SignalR Service 端點 URL。

使用 HTTP 觸發的 Azure 函數和 *>signalrconnectioninfo* 輸入系結來產生連接資訊物件。 函式必須有結尾為的 HTTP 路由 `/negotiate` 。

使用 c # 中以 [類別為基礎的模型](#class-based-model) 時，您不需要 *>signalrconnectioninfo* 輸入系結，而且可以更輕鬆地新增自訂宣告。 查看 [以類別為基礎的模型中的協商體驗](#negotiate-experience-in-class-based-model)

如需有關如何建立 negotiate 函數的詳細資訊，請參閱[ *>signalrconnectioninfo*輸入](../azure-functions/functions-bindings-signalr-service-input.md)系結參考。

若要瞭解如何建立已驗證的權杖，請參閱 [使用 App Service 驗證](#using-app-service-authentication)。

### <a name="handle-messages-sent-from-signalr-service"></a>處理從 SignalR Service 傳送的訊息

使用 *SignalR 觸發* 程式系結來處理從 SignalR Service 傳送的訊息。 當用戶端傳送訊息或用戶端連線或中斷連線時，您可以觸發。

如需詳細資訊，請參閱[ *SignalR 觸發*](../azure-functions/functions-bindings-signalr-service-trigger.md)程式系結參考。

您也需要將您的函式端點設定為上游，讓服務會觸發函式，其中有來自用戶端的訊息。 如需有關如何設定上游的詳細資訊，請 [參閱此檔](concept-upstream.md)。

### <a name="sending-messages-and-managing-group-membership"></a>傳送訊息及管理群組成員資格

使用 *SignalR* 輸出系結，將訊息傳送至連線至 Azure SignalR Service 的用戶端。 您可以將訊息廣播到所有用戶端，也可以將訊息傳送給以特定使用者識別碼進行驗證或已新增至特定群組的用戶端子集。

您可以將使用者新增至一或多個群組。 您也可以使用 *SignalR* 輸出系結，在群組中新增或移除使用者。

如需詳細資訊，請參閱[ *SignalR*輸出](../azure-functions/functions-bindings-signalr-service-output.md)系結參考。

### <a name="signalr-hubs"></a>SignalR 中樞

SignalR 具有「中樞」的概念。 每個用戶端連線，以及從 Azure Functions 傳送的每則訊息都限定在特定的中樞。 您可以使用中樞做為將連接和訊息分成邏輯命名空間的方法。

## <a name="class-based-model"></a>以類別為基礎的模型

以類別為基礎的模型專用於 c #。 使用以類別為基礎的模型可以有一致的 SignalR 伺服器端程式設計體驗。 它具有下列功能。

* 較少的設定工作：使用類別名稱做為 `HubName` ，使用方法名稱做為， `Event` 而且 `Category` 會根據方法名稱自動決定。
* 自動參數系結：不 `ParameterNames` 需要或屬性 `[SignalRParameter]` 。 參數會自動系結至 Azure Function 方法的引數（依序）。
* 方便的輸出和協商體驗。

下列代碼示範這些功能：

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("negotiate")]
    public SignalRConnectionInfo Negotiate([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req)
    {
        return Negotiate(req.Headers["x-ms-signalr-user-id"], GetClaims(req.Headers["Authorization"]));
    }

    [FunctionName(nameof(OnConnected))]
    public async Task OnConnected([SignalRTrigger]InvocationContext invocationContext, ILogger logger)
    {
        await Clients.All.SendAsync(NewConnectionTarget, new NewConnection(invocationContext.ConnectionId));
        logger.LogInformation($"{invocationContext.ConnectionId} has connected");
    }

    [FunctionName(nameof(Broadcast))]
    public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        await Clients.All.SendAsync(NewMessageTarget, new NewMessage(invocationContext, message));
        logger.LogInformation($"{invocationContext.ConnectionId} broadcast {message}");
    }

    [FunctionName(nameof(OnDisconnected))]
    public void OnDisconnected([SignalRTrigger]InvocationContext invocationContext)
    {
    }
}
```

所有想要運用類別型模型的函式，都必須是繼承自 **ServerlessHub**之類別的方法。 範例中的類別名稱 `SignalRTestHub` 是中樞名稱。

### <a name="define-hub-method"></a>定義中樞方法

所有中樞方法都 **必須** 有以屬性裝飾的引數 `InvocationContext` `[SignalRTrigger]` ，並使用無參數的函式。 然後，會將 **方法名稱** 視為參數 **事件**。

根據預設， `category=messages` 除了方法名稱是下列其中一個名稱：

* **OnConnected**：視為 `category=connections, event=connected`
* **OnDisconnected**：視為 `category=connections, event=disconnected`

### <a name="parameter-binding-experience"></a>參數系結體驗

在以類別為基礎的模型中， `[SignalRParameter]` 因為所有引數都已標示為 `[SignalRParameter]` 預設值，但它是下列其中一種情況，所以是不必要的：

* 引數是以系結屬性裝飾。
* 引數的類型為 `ILogger` 或。 `CancellationToken`
* 引數是以屬性裝飾 `[SignalRIgnore]`

### <a name="negotiate-experience-in-class-based-model"></a>以類別為基礎的模型中的協商體驗

`[SignalR]`以類別為基礎的模型中的協商可以更有彈性，而不是使用 SignalR 輸入系結。 基類 `ServerlessHub` 有方法

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

這是使用者自訂或在函式 `userId` `claims` 執行期間的功能。

## <a name="use-signalrfilterattribute"></a>使用`SignalRFilterAttribute`

使用者可以繼承和執行抽象類別 `SignalRFilterAttribute` 。 如果擲回例外狀況 `FilterAsync` ， `403 Forbidden` 則會傳送回用戶端。

下列範例示範如何執行只允許叫用的客戶篩選 `admin` `broadcast` 。

```cs
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
internal class FunctionAuthorizeAttribute: SignalRFilterAttribute
{
    private const string AdminKey = "admin";

    public override Task FilterAsync(InvocationContext invocationContext, CancellationToken cancellationToken)
    {
        if (invocationContext.Claims.TryGetValue(AdminKey, out var value) &&
            bool.TryParse(value, out var isAdmin) &&
            isAdmin)
        {
            return Task.CompletedTask;
        }

        throw new Exception($"{invocationContext.ConnectionId} doesn't have admin role");
    }
}
```

利用屬性來授權函數。

```cs
[FunctionAuthorize]
[FunctionName(nameof(Broadcast))]
public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
{
}
```

## <a name="client-development"></a>用戶端開發

SignalR 用戶端應用程式可以透過數種語言的其中一種來利用 SignalR 用戶端 SDK，輕鬆地連接並接收 Azure SignalR Service 的訊息。

### <a name="configuring-a-client-connection"></a>設定用戶端連接

若要連接到 SignalR Service，用戶端必須完成由下列步驟所組成的成功連線協商：

1. 對上述的 *協商* HTTP 端點提出要求，以取得有效的連接資訊
1. 使用從 *negotiate* 端點取得的服務端點 URL 和存取權杖連接到 SignalR Service

SignalR 用戶端 Sdk 已包含執行協調信號結算所需的邏輯。 將 negotiate 端點的 URL （減去 `negotiate` 區段）傳遞給 SDK `HubConnectionBuilder` 。 以下是 JavaScript 中的範例：

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

依照慣例，SDK 會自動附加 `/negotiate` 至 URL，並使用它來開始進行協商。

> [!NOTE]
> 如果您在瀏覽器中使用 JavaScript/TypeScript SDK，您需要在函式應用程式上 [啟用跨原始來源資源分享 (CORS) ](#enabling-cors) 。

如需有關如何使用 SignalR 用戶端 SDK 的詳細資訊，請參閱您的語言檔：

* [.NET Standard](/aspnet/core/signalr/dotnet-client)
* [JavaScript](/aspnet/core/signalr/javascript-client)
* [Java](/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>從用戶端傳送訊息至服務

如果您已針對 SignalR 資源設定 [上游](concept-upstream.md) ，則可以使用任何 SignalR 用戶端，將訊息從用戶端傳送到您的 Azure Functions。 以下是 JavaScript 中的範例：

```javascript
connection.send('method1', 'arg1', 'arg2');
```

## <a name="azure-functions-configuration"></a>Azure Functions 設定

與 Azure SignalR Service 整合的 Azure 函數應用程式，可以像任何一般的 Azure 函數應用程式一樣部署，方法是使用 [持續部署](../azure-functions/functions-continuous-deployment.md)、 [zip 部署](../azure-functions/deployment-zip-push.md)和 [從套件執行](../azure-functions/run-functions-from-deployment-package.md)的技術。

不過，使用 SignalR Service 系結的應用程式有一些特殊考慮。 如果用戶端在瀏覽器中執行，則必須啟用 CORS。 如果應用程式需要驗證，您可以整合 negotiate 端點與 App Service Authentication。

### <a name="enabling-cors"></a>啟用 CORS

JavaScript/TypeScript 用戶端會向 negotiate 函式發出 HTTP 要求，以起始連接協商。 當用戶端應用程式裝載在與 Azure 函數應用程式不同的網域時，必須在函數應用程式上啟用跨原始來源資源分享 (CORS) ，否則瀏覽器將會封鎖要求。

#### <a name="localhost"></a>Localhost

在您的本機電腦上執行函數應用程式時，您可以將 `Host` 區段新增至 *local.settings.js* ，以啟用 CORS。 在 `Host` 區段中，新增兩個屬性：

* `CORS` -輸入做為用戶端應用程式來源的基底 URL
* `CORSCredentials` -將它設定為 `true` 以允許 "withCredentials" 要求

範例：

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>雲端 Azure Functions CORS

若要在 Azure 函數應用程式上啟用 CORS，請移至 Azure 入口網站中函數應用程式的 [ *平臺功能* ] 索引標籤下的 [cors 設定] 畫面。

> [!NOTE]
> Azure Functions Linux 使用量方案尚未提供 CORS 設定。 使用 [AZURE API 管理](#cloud---azure-api-management) 來啟用 CORS。

必須針對 SignalR 用戶端啟用具有存取控制-允許認證的 CORS，才能呼叫 negotiate 函數。 選取核取方塊以啟用它。

在 [ *允許的來源* ] 區段中，新增含有 web 應用程式之 [來源基底 URL] 的專案。

![設定 CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>雲端-Azure API 管理

Azure API 管理提供 API 閘道，可將功能新增至現有的後端服務。 您可以使用它來將 CORS 新增至函數應用程式。 它提供的耗用量層有依動作付費定價和每月免費授權。

如需如何匯 [入 Azure 函數應用程式](../api-management/import-function-app-as-api.md)的相關資訊，請參閱 API 管理檔。 匯入之後，您可以新增輸入原則，以啟用具有存取控制-允許認證支援的 CORS。

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

將您的 SignalR 用戶端設定為使用 API 管理 URL。

### <a name="using-app-service-authentication"></a>使用 App Service Authentication

Azure Functions 有內建的驗證功能，支援 Facebook、Twitter、Microsoft 帳戶、Google 和 Azure Active Directory 等熱門提供者。 這項功能可以與 *>signalrconnectioninfo* 系結整合，以建立與已驗證使用者識別碼之 Azure SignalR Service 的連接。 您的應用程式可以使用以該使用者識別碼為目標的 *SignalR* 輸出系結來傳送訊息。

在 [Azure 入口網站] 的函式應用程式的 [ *平臺功能* ] 索引標籤中，開啟 [ *驗證/授權* 設定] 視窗。 遵循 [App Service authentication](../app-service/overview-authentication-authorization.md) 的檔，使用您選擇的身分識別提供者來設定驗證。

一旦設定之後，經過驗證的 HTTP 要求會 `x-ms-client-principal-name` 分別包含和 `x-ms-client-principal-id` 標頭，其中包含已驗證身分識別的使用者名稱和使用者識別碼。

您可以在 *>signalrconnectioninfo* 系結設定中使用這些標頭來建立驗證的連接。 以下是使用標頭的範例 c # negotiate 函數 `x-ms-client-principal-id` 。

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

然後，您可以藉由設定 SignalR 訊息的屬性，將訊息傳送給該使用者 `UserId` 。

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

如需其他語言的詳細資訊，請參閱 Azure Functions 參考的 [Azure SignalR Service](../azure-functions/functions-bindings-signalr-service.md) 系結。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用 Azure Functions 開發和設定無伺服器 SignalR Service 應用程式。 請嘗試使用 [SignalR Service 總覽頁面](index.yml)上的其中一個快速入門或教學課程來自行建立應用程式。