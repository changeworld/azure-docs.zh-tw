---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 77b1e9ab245f668ab81741451a5e032f37bc3625
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943888"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) 作用中 LTS 和維修 LTS 版本 (建議使用 8.11.1 和 10.14.1)。
- 作用中的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

開啟您的終端機或命令視窗，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

執行 `npm init -y` 以使用預設設定建立 **package.json** 檔案。

```console
npm init -y
```

### <a name="install-the-package"></a>安裝套件

使用 `npm install` 命令來安裝適用於 JavaScript 的 Azure 通訊服務系統管理用戶端程式庫。

```console

npm install @azure/communication-administration --save

```

`--save` 選項會在您的 **package.json** 檔案中，將程式庫列為相依性。

## <a name="set-up-the-app-framework"></a>設定應用程式架構

從專案目錄：

1. 在程式碼編輯器中開啟新的文字檔
1. 新增 `require` 呼叫以載入 `CommunicationIdentityClient`
1. 建立程式的結構，包括基本例外狀況處理

使用下列程式碼開始作業：

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. 在 user-tokens-quickstart 目錄中將新檔案儲存為 issue-token.js。

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>驗證用戶端

使用連接字串具現化 `CommunicationIdentityClient`。 以下程式碼會從名為 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的環境變數中，擷取資源的連接字串。 了解如何[管理資源的連接字串](../create-communication-resource.md#store-your-connection-string)。

將下列程式碼加入 `main` 方法：

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>建立使用者

Azure 通訊服務會維護輕量身分識別目錄。 使用 `createUser` 方法，在目錄中建立具有唯一 `Id` 的新項目。 您應該維護應用程式使用者與通訊服務所產生身分識別之間的對應 (例如，將其儲存在應用程式伺服器的資料庫中)。

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>發行使用者存取權杖

使用 `issueToken` 方法來發行通訊服務使用者的存取權杖。 如果您未提供選擇性的 `user` 參數，則會建立新的使用者，並連同權杖一起傳回。

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

使用者存取權杖是短期的認證，需要重新發行才能防止使用者發生服務中斷。 `expiresOn` 回應屬性會指出權杖的存留期。

## <a name="revoke-user-access-tokens"></a>撤銷使用者存取權杖

在某些情況下，您可能需要明確撤銷使用者存取權杖，例如，當使用者變更用來向服務進行驗證的密碼時。 這會使用 `revokeTokens` 方法，讓所有使用者的存取權杖失效。

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="delete-a-user"></a>刪除使用者

刪除使用者會撤銷所有作用中的權杖，並防止您發行身分識別的後續權杖。 也會移除所有與使用者相關聯的保存內容。

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>執行程式碼

從主控台提示中，瀏覽至包含 issue-token.js 檔案的目錄，然後執行下列 `node` 命令來執行應用程式。

```console
node ./issue-token.js
```
