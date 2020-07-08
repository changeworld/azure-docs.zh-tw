---
title: 開發 & 設定 Azure Functions 應用程式-Azure SignalR
description: 有關如何使用 Azure Functions 和 Azure SignalR Service 開發和設定無伺服器即時應用程式的詳細資料
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: dbacb6a5bbdead52750935c476f453423647fc0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84457128"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>使用 Azure SignalR Service 來開發與設定 Azure Functions

Azure Functions 應用程式可以利用[Azure SignalR Service](../azure-functions/functions-bindings-signalr-service.md)系結來新增即時功能。 用戶端應用程式會使用數種語言提供的用戶端 Sdk 來連接 Azure SignalR Service 並接收即時訊息。

本文說明開發和設定與 SignalR Service 整合的 Azure 函式應用程式的概念。

## <a name="signalr-service-configuration"></a>SignalR Service 設定

Azure SignalR Service 可以在不同的模式下設定。 與 Azure Functions 搭配使用時，服務必須以*無伺服器*模式設定。

在 [Azure 入口網站中，找出您 SignalR Service 資源的 [*設定*] 頁面。 將 [*服務模式]* 設定為 [*無伺服器*]。

![SignalR Service 模式](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 開發

以 Azure Functions 和 Azure SignalR Service 建立的無伺服器即時應用程式通常需要兩個 Azure Functions：

* "negotiate" 函式，用戶端會呼叫該函式來取得有效的 SignalR Service 存取權杖和服務端點 URL
* 一或多個函式，可處理來自 SignalR Service 和傳送訊息或管理群組成員資格的訊息

### <a name="negotiate-function"></a>negotiate 函式

用戶端應用程式需要有效的存取權杖，才能連接到 Azure SignalR Service。 存取權杖可以是匿名或通過指定的使用者識別碼驗證。 無伺服器 SignalR Service 應用程式需要名為「negotiate」的 HTTP 端點，才能取得權杖和其他連接資訊，例如 SignalR Service 端點 URL。

使用 HTTP 觸發的 Azure 函數和*SignalRConnectionInfo*輸入系結來產生連接資訊物件。 函式必須具有以結尾的 HTTP 路由 `/negotiate` 。

在 c # 中使用以[類別為基礎的模型](#class-based-model)時，您不需要*SignalRConnectionInfo*輸入系結，而且可以更輕鬆地新增自訂宣告。 請參閱[以類別為基礎的模型中的 Negotiate 體驗](#negotiate-experience-in-class-based-model)

如需有關如何建立 negotiate 函式的詳細資訊，請參閱[ *SignalRConnectionInfo*輸入](../azure-functions/functions-bindings-signalr-service-input.md)系結參考。

若要瞭解如何建立已驗證的權杖，請參閱[使用 App Service 驗證](#using-app-service-authentication)。

### <a name="handle-messages-sent-from-signalr-service"></a>處理從 SignalR Service 傳送的訊息

使用*SignalR 觸發*程式系結來處理從 SignalR Service 傳送的訊息。 您可以在用戶端傳送訊息或用戶端連接或中斷連線時觸發。

如需詳細資訊，請參閱[ *SignalR 觸發*程式系結參考](../azure-functions/functions-bindings-signalr-service-trigger.md)

### <a name="sending-messages-and-managing-group-membership"></a>傳送訊息和管理群組成員資格

使用*SignalR*輸出系結，將訊息傳送至連線至 Azure SignalR Service 的用戶端。 您可以將訊息廣播到所有用戶端，也可以將它們傳送到以特定使用者識別碼驗證或已新增至特定群組的用戶端子集。

使用者可以新增至一個或多個群組。 您也可以使用*SignalR*輸出系結，在群組中新增或移除使用者。

如需詳細資訊，請參閱[ *SignalR*輸出](../azure-functions/functions-bindings-signalr-service-output.md)系結參考。

### <a name="signalr-hubs"></a>SignalR 中樞

SignalR 具有「中樞」的概念。 每個用戶端連線以及從 Azure Functions 傳送的每個訊息都是以特定的中樞為範圍。 您可以使用中樞做為將連線和訊息分隔成邏輯命名空間的方式。

## <a name="class-based-model"></a>以類別為基礎的模型

以類別為基礎的模型專門用於 c #。 使用以類別為基礎的模型可以擁有一致的 SignalR 伺服器端程式設計經驗。 它具有下列功能。

* 較少的設定工作：類別名稱會當做使用 `HubName` ，方法名稱會當做使用， `Event` 而且 `Category` 會根據方法名稱自動決定。
* 自動參數系結：不 `ParameterNames` 需要或屬性 `[SignalRParameter]` 。 參數會依序自動系結至 Azure Function 方法的引數。
* 方便的輸出和談判體驗。

下列代碼將示範這些功能：

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

所有想要利用以類別為基礎的模型的函式，都必須是繼承自**ServerlessHub**之類別的方法。 範例中的類別名稱 `SignalRTestHub` 是中樞名稱。

### <a name="define-hub-method"></a>定義中樞方法

所有中樞方法都**必須**具有 `[SignalRTrigger]` 屬性，而且**必須**使用無參數的函式。 然後，**方法名稱**會被視為參數**事件**。

根據預設， `category=messages` 除了方法名稱是下列其中一個名稱：

* **OnConnected**：視為`category=connections, event=connected`
* **OnDisconnected**：視為`category=connections, event=disconnected`

### <a name="parameter-binding-experience"></a>參數系結體驗

在以類別為基礎的模型中， `[SignalRParameter]` 不需要，因為預設會將所有引數標記為， `[SignalRParameter]` 但它是下列其中一種情況：

* 引數是由系結屬性裝飾。
* 引數的類型為 `ILogger` 或`CancellationToken`
* 引數是以屬性裝飾`[SignalRIgnore]`

### <a name="negotiate-experience-in-class-based-model"></a>以類別為基礎的模型中的 Negotiate 體驗

不使用 SignalR 輸入系結 `[SignalR]` ，以類別為基礎的模型中的協商可能更有彈性。 基類 `ServerlessHub` 具有方法

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

這是使用者自訂或在函式 `userId` `claims` 執行期間所提供的功能。

## <a name="use-signalrfilterattribute"></a>使用`SignalRFilterAttribute`

使用者可以繼承和執行抽象類別 `SignalRFilterAttribute` 。 如果在中擲回例外狀況 `FilterAsync` ， `403 Forbidden` 則會傳回給用戶端。

下列範例示範如何執行只允許叫用的客戶篩選準則 `admin` `broadcast` 。

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

SignalR 用戶端應用程式可以利用其中一種語言的 SignalR 用戶端 SDK，輕鬆地連線到 Azure SignalR Service 並從中接收訊息。

### <a name="configuring-a-client-connection"></a>設定用戶端連接

若要連接到 SignalR Service，用戶端必須完成由下列步驟所組成的成功連線協調：

1. 對上述的*negotiate* HTTP 端點提出要求，以取得有效的連接資訊
1. 使用從*negotiate*端點取得的服務端點 URL 和存取權杖，連接到 SignalR Service

SignalR 用戶端 Sdk 已包含執行協調交握所需的邏輯。 將 negotiate 端點的 URL （減去 `negotiate` 區段）傳遞至 SDK 的 `HubConnectionBuilder` 。 以下是 JavaScript 的範例：

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

依照慣例，SDK 會自動附加 `/negotiate` 至 URL，並使用它來開始進行協商。

> [!NOTE]
> 如果您在瀏覽器中使用 JavaScript/TypeScript SDK，您需要在您的函數應用程式上[啟用跨原始來源資源分享（CORS）](#enabling-cors) 。

如需有關如何使用 SignalR 用戶端 SDK 的詳細資訊，請參閱您語言的檔：

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>從用戶端傳送訊息至服務

雖然 SignalR SDK 可讓用戶端應用程式叫用 SignalR 中樞的後端邏輯，但當您搭配 Azure Functions 使用 SignalR Service 時，尚不支援這項功能。 使用 HTTP 要求叫用 Azure Functions。

## <a name="azure-functions-configuration"></a>Azure Functions 設定

與 Azure SignalR Service 整合的 azure 函式應用程式可以像任何一般 Azure 函式應用程式一樣部署，方法是使用[持續部署](../azure-functions/functions-continuous-deployment.md)、 [zip 部署](../azure-functions/deployment-zip-push.md)和[從封裝執行](../azure-functions/run-functions-from-deployment-package.md)等技術。

不過，使用 SignalR Service 系結的應用程式有幾個特殊考慮。 如果用戶端在瀏覽器中執行，則必須啟用 CORS。 而且，如果應用程式需要驗證，您可以將 negotiate 端點與 App Service Authentication 整合。

### <a name="enabling-cors"></a>啟用 CORS

JavaScript/TypeScript 用戶端會向 negotiate 函式發出 HTTP 要求，以起始連接協商。 當用戶端應用程式裝載于與 Azure Function 應用程式不同的網域時，必須在函數應用程式上啟用跨原始來源資源分享（CORS），否則瀏覽器將會封鎖要求。

#### <a name="localhost"></a>Localhost

在您的本機電腦上執行函式應用程式時，您可以將 `Host` 區段新增至*local.settings.js* ，以啟用 CORS。 在 `Host` 區段中，新增兩個屬性：

* `CORS`-輸入做為用戶端應用程式來源的基底 URL
* `CORSCredentials`-將它設定為 `true` 以允許 "withCredentials" 要求

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

若要在 Azure 函式應用程式上啟用 CORS，請移至 Azure 入口網站中函數應用程式的 [*平臺功能*] 索引標籤下的 [cors 設定] 畫面。

> [!NOTE]
> Azure Functions Linux 使用量計畫中尚未提供 CORS 設定。 使用[AZURE API 管理](#cloud---azure-api-management)來啟用 CORS。

必須啟用 SignalR 用戶端的 CORS，才能呼叫 negotiate 函式。 選取核取方塊以啟用它。

在 [*允許的來源*] 區段中，使用 web 應用程式的原始基底 URL 來新增專案。

![設定 CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>雲端-Azure API 管理

Azure API 管理提供可將功能新增至現有後端服務的 API 閘道。 您可以使用它將 CORS 新增至您的函數應用程式。 它提供的取用量層具有按動作付費的定價和每月免費授與。

如需如何匯[入 Azure 函數應用程式](../api-management/import-function-app-as-api.md)的相關資訊，請參閱 API 管理檔。 匯入之後，您可以新增輸入原則，以啟用具有存取控制-允許憑證支援的 CORS。

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

### <a name="using-app-service-authentication"></a>使用 App Service 驗證

Azure Functions 具有內建的驗證，可支援 Facebook、Twitter、Microsoft 帳戶、Google 和 Azure Active Directory 等熱門提供者。 這項功能可以與*SignalRConnectionInfo*系結整合，以建立與已驗證使用者識別碼之 Azure SignalR Service 的連接。 您的應用程式可以使用以該使用者識別碼為目標的*SignalR*輸出系結來傳送訊息。

在 Azure 入口網站的函數應用程式的 [*平臺功能*] 索引標籤中，開啟 [*驗證/授權*設定] 視窗。 遵循[App Service 驗證](../app-service/overview-authentication-authorization.md)的檔，使用您選擇的身分識別提供者來設定驗證。

一旦設定之後，經過驗證的 HTTP 要求將會包含 `x-ms-client-principal-name` 和 `x-ms-client-principal-id` 標頭，分別包含已驗證身分識別的使用者名稱和使用者識別碼。

您可以在*SignalRConnectionInfo*系結設定中使用這些標頭來建立已驗證的連接。 以下是使用標頭的範例 c # negotiate 函式 `x-ms-client-principal-id` 。

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

接著，您可以藉由設定 SignalR 訊息的屬性，將訊息傳送給該使用者 `UserId` 。

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

如需其他語言的詳細資訊，請參閱 Azure Functions 參考的[Azure SignalR Service](../azure-functions/functions-bindings-signalr-service.md)系結。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用 Azure Functions 開發和設定無伺服器 SignalR Service 應用程式。 嘗試使用 [ [SignalR Service 總覽] 頁面](index.yml)上的其中一個 [快速入門] 或 [教學課程] 自行建立應用程式。