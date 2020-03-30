---
title: 開發&配置 Azure 函數應用 - Azure 信號R
description: 有關如何使用 Azure 函數和 Azure SignalR 服務開發和配置無伺服器即時應用程式的詳細資訊
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523165"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>使用 Azure SignalR Service 來開發與設定 Azure Functions

Azure 函數應用程式可以利用[Azure SignalR 服務綁定](../azure-functions/functions-bindings-signalr-service.md)添加即時功能。 用戶端應用程式使用多種語言的用戶端 SDK 連接到 Azure SignalR 服務並接收即時消息。

本文介紹了開發和配置與 SignalR 服務集成的 Azure 函數應用的概念。

## <a name="signalr-service-configuration"></a>信號R服務配置

Azure SignalR 服務可以以不同的模式進行配置。 當與 Azure 函數一起使用時，必須在*無伺服器*模式下佈建服務。

在 Azure 門戶中，找到 SignalR 服務資源的 *"設置"* 頁。 將*服務模式*設置為*無伺服器*。

![SignalR Service 模式](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 開發

以 Azure Functions 和 Azure SignalR Service 建立的無伺服器即時應用程式通常需要兩個 Azure Functions：

* "negotiate" 函式，用戶端會呼叫該函式來取得有效的 SignalR Service 存取權杖和服務端點 URL
* 傳送訊息或管理群組成員資格的一個或多個函式

### <a name="negotiate-function"></a>協商功能

用戶端應用程式需要有效的訪問權杖才能連接到 Azure SignalR 服務。 訪問權杖可以是匿名的，也可以對給定的使用者 ID 進行身份驗證。 無伺服器信號R 服務應用程式需要名為"協商"的 HTTP 終結點來獲取權杖和其他連接資訊，例如 SignalR 服務終結點 URL。

使用 HTTP 觸發的 Azure 函數和*SignalRConnectionInfo*輸入綁定生成連接資訊物件。 函數必須具有以 結尾的`/negotiate`HTTP 路由。

有關如何創建協商函數的詳細資訊，請參閱[*SignalRConnectionInfo*輸入綁定引用](../azure-functions/functions-bindings-signalr-service-input.md)。

要瞭解如何創建經過身份驗證的權杖，請參閱[使用應用服務身份驗證](#using-app-service-authentication)。

### <a name="sending-messages-and-managing-group-membership"></a>發送消息和管理組成員身份

使用*SignalR*輸出綁定將消息發送到連接到 Azure SignalR 服務的用戶端。 您可以將消息廣播給所有用戶端，也可以將它們發送到使用特定使用者 ID 進行身份驗證或已添加到特定組的用戶端子集。

使用者可以添加到一個或多個組。 您還可以使用*SignalR*輸出綁定將使用者添加或刪除組。

有關詳細資訊，請參閱[*SignalR*輸出綁定引用](../azure-functions/functions-bindings-signalr-service-output.md)。

### <a name="signalr-hubs"></a>信號器中心

SignalR 具有"集線器"的概念。 每個用戶端連接和從 Azure 函數發送的每條消息都限定在特定中心。 可以使用集線器作為將連接和消息分隔到邏輯命名空間的一種方式。

## <a name="client-development"></a>用戶端開發

SignalR 用戶端應用程式可以利用多種語言之一的 SignalR 用戶端 SDK 輕鬆連接到 Azure SignalR 服務的消息。

### <a name="configuring-a-client-connection"></a>配置用戶端連接

要連接到 SignalR 服務，用戶端必須完成包含以下步驟的成功連接協商：

1. 向上面討論的*協商*HTTP 終結點發出請求以獲取有效的連接資訊
1. 使用服務終結點 URL 和從*協商*終結點獲取的訪問權杖連接到 SignalR 服務

SignalR 用戶端 SDK 已包含執行協商握手所需的邏輯。 將協商終結點的 URL（減去`negotiate`段）傳遞給 SDK 的`HubConnectionBuilder`。 下面是 JavaScript 中的一個示例：

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

按照慣例，SDK 會自動追加`/negotiate`到 URL 並使用它開始協商。

> [!NOTE]
> 如果您在瀏覽器中使用 JavaScript/TypeScript SDK，則需要在函數應用上[啟用跨源資源分享 （CORS）。](#enabling-cors)

有關如何使用 SignalR 用戶端 SDK 的詳細資訊，請參閱語言文檔：

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JAVAscript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [JAVA](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>將消息從用戶端發送到服務

儘管 SignalR SDK 允許用戶端應用程式在 SignalR 中心中調用後端邏輯，但在將 SignalR 服務與 Azure 函數一起使用時，此功能尚不受支援。 使用 HTTP 要求調用 Azure 函數。

## <a name="azure-functions-configuration"></a>Azure 函數配置

與 Azure SignalR 服務集成的 Azure 函數應用可以像任何典型的 Azure 函數應用一樣部署，使用[連續部署](../azure-functions/functions-continuous-deployment.md)[、zip 部署](../azure-functions/deployment-zip-push.md)和[從包運行](../azure-functions/run-functions-from-deployment-package.md)等技術。

但是，對於使用 SignalR 服務綁定的應用，有幾個特殊注意事項。 如果用戶端在瀏覽器中運行，則必須啟用 CORS。 如果應用需要身份驗證，則可以將協商終結點與應用服務身份驗證集成。

### <a name="enabling-cors"></a>啟用 CORS

JavaScript/TypeScript 用戶端向協商函數發出 HTTP 要求以啟動連接協商。 當用戶端應用程式託管在與 Azure 函數應用不同的域上時，必須在功能應用上啟用跨源資源分享 （CORS），否則瀏覽器將阻止請求。

#### <a name="localhost"></a>Localhost

在本地電腦上運行"功能"應用時，可以將`Host`節添加到*本地.settings.json*以啟用 CORS。 在本節`Host`中，添加兩個屬性：

* `CORS`- 輸入是用戶端應用程式源的基本 URL
* `CORSCredentials`- 將其設置為`true`允許"帶憑據"請求

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

#### <a name="cloud---azure-functions-cors"></a>雲 - Azure 功能 CORS

要在 Azure 函數應用上啟用 CORS，請轉到 Azure 門戶中"功能"應用*的平臺功能*選項卡下的 CORS 配置螢幕。

> [!NOTE]
> CORS 配置在 Azure 函數 Linux 使用計畫中尚不可用。 使用[Azure API 管理](#cloud---azure-api-management)啟用 CORS。

必須啟用具有存取控制允許憑據的 CORS，以便 SignalR 用戶端調用協商函數。 選擇核取方塊以啟用它。

在 *"允許源"* 部分中，添加包含 Web 應用程式源基 URL 的條目。

![配置 CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>雲 - Azure API 管理

Azure API 管理提供 API 閘道，將功能添加到現有後端服務。 您可以使用它將 CORS 添加到函數應用。 它提供了一個消費層，即按行動付費定價和每月免費補助。

有關如何[導入 Azure 函數應用](../api-management/import-function-app-as-api.md)的資訊，請參閱 API 管理文檔。 導入後，可以添加入站策略，以啟用具有存取控制允許憑據支援的 CORS。

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

將 SignalR 用戶端配置為使用 API 管理 URL。

### <a name="using-app-service-authentication"></a>使用應用服務身份驗證

Azure 功能具有內置身份驗證，支援 Facebook、Twitter、微軟帳戶、Google 和 Azure 活動目錄等熱門供應商。 此功能可與*SignalRConnectionInfo*綁定集成，以創建與 Azure SignalR 服務的連接，該服務已通過使用者 ID 進行身份驗證。 您的應用程式可以使用面向該使用者 ID 的*SignalR*輸出綁定發送消息。

在 Azure 門戶中，在"功能"應用*的平臺功能*選項卡中，打開*身份驗證/授權*設置視窗。 按照[應用服務身份驗證](../app-service/overview-authentication-authorization.md)的文檔使用您選擇的標識提供程式配置身份驗證。

配置後，經過身份驗證的 HTTP`x-ms-client-principal-name`請求`x-ms-client-principal-id`將分別包含包含經過身份驗證標識的使用者名和使用者 ID 的標頭。

您可以在*SignalRConnectionInfo*綁定配置中使用這些標頭來創建經過身份驗證的連接。 下面是使用標頭的示例`x-ms-client-principal-id`C# 協商函數。

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

然後，您可以通過設置 SignalR 消息`UserId`的屬性向該使用者發送消息。

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

有關其他語言的資訊，請參閱 Azure 信號[R 服務綁定](../azure-functions/functions-bindings-signalr-service.md)，瞭解 Azure 函數引用。

## <a name="next-steps"></a>後續步驟

在本文中，您已經瞭解如何使用 Azure 函數開發和配置無伺服器 SignalR 服務應用程式。 嘗試使用[SignalR 服務概述頁上](index.yml)的快速啟動或教程之一自行創建應用程式。