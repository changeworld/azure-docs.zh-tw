---
title: 向 Azure 通訊服務驗證
titleSuffix: An Azure Communication Services concept document
description: 深入瞭解應用程式或服務可向通訊服務驗證的各種方式。
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 4d6e02852dcd2d30a764417a4b5e0e012a1d2ab5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571091"
---
# <a name="authenticate-to-azure-communication-services"></a>向 Azure 通訊服務驗證

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

本文提供使用 *存取金鑰* 和 *使用者存取權杖* 來向 Azure 通訊服務驗證用戶端的相關資訊。 每個用戶端與 Azure 通訊服務的互動都必須經過驗證。

下表說明 Azure 通訊服務用戶端程式庫支援的驗證選項：

| 用戶端程式庫 | 存取金鑰    | 使用者存取權杖 |
| -------------- | ------------- | ------------------ |
| 系統管理 | 支援     | 不支援      |
| SMS            | 支援     | 不支援      |
| 聊天           | 不支援 | 支援          |
| 呼叫        | 不支援 | 支援          |

以下是每個授權選項的簡短說明：

- 適用于 SMS 和管理作業的 **存取金鑰** 驗證。 存取金鑰驗證適用于在信任的服務環境中執行的應用程式。 若要使用存取金鑰進行驗證，用戶端會 [ (HMAC) 產生雜湊式訊息驗證碼 ](https://en.wikipedia.org/wiki/HMAC) ，並將它包含在 `Authorization` 每個 HTTP 要求的標頭中。 如需詳細資訊，請參閱 [使用存取金鑰進行驗證](#authenticate-with-an-access-key)。
- 用於聊天和通話的 **使用者存取權杖** 驗證。 使用者存取權杖可讓您的用戶端應用程式直接向 Azure 通訊服務進行驗證。 這些權杖會在您建立的伺服器端權杖布建服務上產生。 然後，這些用戶端裝置會提供使用權杖初始化聊天和呼叫用戶端程式庫的用戶端裝置。 如需詳細資訊，請參閱 [使用使用者存取權杖進行驗證](#authenticate-with-a-user-access-token)。

## <a name="authenticate-with-an-access-key"></a>使用存取金鑰進行驗證

存取金鑰驗證會使用共用秘密金鑰，針對使用 SHA256 演算法計算的每個 HTTP 要求產生 HMAC，並使用配置將它傳送到 `Authorization` 標頭 `HMAC-SHA256` 。

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

使用存取金鑰驗證的 Azure 通訊服務用戶端程式庫應該使用資源的連接字串來初始化。 如果您不是使用用戶端程式庫，您可以使用資源的存取金鑰，以程式設計方式產生 Hmac。 若要深入瞭解連接字串，請造訪 [資源布建快速入門](../quickstarts/create-communication-resource.md)。

### <a name="sign-an-http-request"></a>簽署 HTTP 要求

如果您不是使用用戶端程式庫向 Azure 通訊服務 REST Api 發出 HTTP 要求，您必須以程式設計方式為每個 HTTP 要求建立 Hmac。 下列步驟說明如何建立授權標頭：

1. `x-ms-date`請在標頭或標準 HTTP 標頭中指定要求的國際標準時間 (UTC) 時間戳記 `Date` 。 服務會驗證這一點，以防止特定的安全性攻擊，包括重新執行攻擊。
1. 使用 SHA256 演算法來雜湊 HTTP 要求本文，然後透過 `x-ms-content-sha256` 標頭傳遞要求。
1. 藉由將 HTTP 動詞命令串連 (例如 `GET` 或 `PUT`) 、HTTP 要求路徑和的值，以及 `Date` `Host` `x-ms-content-sha256` 下列格式的 HTTP 標頭，來建立要簽署的字串：
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. 針對您在上一個步驟中建立的 UTF-8 編碼字串產生 HMAC-256 簽章。 接下來，將您的結果編碼為 Base64。 請注意，您也需要以 Base64 解碼存取金鑰。 使用下列格式 (顯示為虛擬程式碼) ：
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_access_key>)))
    ```
1. 指定授權標頭，如下所示：
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    其中 `<hmac-sha256-signature>` 是在上一個步驟中計算的 HMAC。

## <a name="authenticate-with-a-user-access-token"></a>使用使用者存取權杖進行驗證

使用者存取權杖可讓您的用戶端應用程式直接向 Azure 通訊服務進行驗證。 為了達成此目的，您應該設定信任的服務，以驗證您的應用程式使用者，並使用管理用戶端程式庫來發出使用者存取權杖。 請造訪 [用戶端和伺服器架構](./client-and-server-architecture.md) 概念檔，以深入瞭解我們的架構考慮。

`CommunicationUserCredential`類別包含提供使用者存取權杖認證給用戶端程式庫以及管理其生命週期的邏輯。

### <a name="initialize-the-client-libraries"></a>初始化用戶端程式庫

若要初始化需要使用者存取權杖驗證的 Azure 通訊服務用戶端程式庫，您必須先建立 `CommunicationUserCredential` 類別的實例，然後使用它來初始化 API 用戶端。

下列程式碼片段示範如何使用使用者存取權杖來初始化聊天用戶端程式庫：

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>重新整理使用者存取權杖

使用者存取權杖是短期的認證，需要重新發出以防止您的使用者遇到服務中斷。 此函式會接受重新整理回呼函式 `CommunicationUserCredential` ，可讓您在使用者存取權杖到期之前加以更新。 您應該使用此回呼，從信任的服務中提取新的使用者存取權杖。

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

此 `refreshProactively` 選項可讓您決定要如何管理權杖生命週期。 根據預設，當令牌過時時，回呼會封鎖 API 要求，並嘗試重新整理它。 當 `refreshProactively` 設為時，會 `true` 排程回呼，並在權杖到期前以非同步方式執行。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立使用者存取權杖](../quickstarts/access-tokens.md)

如需詳細資訊，請參閱下列文章：
- [了解用戶端與根伺服器架構](../concepts/client-and-server-architecture.md)
