---
title: 信任的服務 JavaScript
description: 這是為通訊服務建立受信任服務的 JavaScript 版本。
author: dademath
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/28/2020
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: daf2d675bbbee324769b6e1e8d8d34587d37c72f
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886614"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 如需詳細資訊，請參閱[免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 其中一個[支援的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上有 [Visual Studio Code](https://code.visualstudio.com/)。
- [Node.js](https://nodejs.org/)、作用中 LTS 和維修 LTS 版本 (建議使用 10.14.1)。 請使用 `node --version` 命令來檢查您的版本。 
- 適用於 Visual Studio Code 的 [Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。 
- 作用中的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../../quickstarts/create-communication-resource.md)。

## <a name="overview"></a>概觀

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="信任的服務架構圖":::

在本教學課程中，我們將建立 Azure 函式，作為信任的權杖佈建服務。 您可以使用本教學課程來啟動您自己的權杖佈建服務。

此服務負責向 Azure 通訊服務驗證使用者。 您的通訊服務應用程式使用者將需要 `Access Token`，才能參與聊天對話和 VoIP 通話。 Azure 函式會在使用者與通訊服務之間作為信任的中介管道。 這可讓您佈建存取權杖，而不需要向使用者公開您的資源連接字串。

如需詳細資訊，請參閱[的用戶端-伺服器架構](../../concepts/client-and-server-architecture.md)和[驗證和授權](../../concepts/authentication.md)概念性文件。

## <a name="setting-up"></a>設定

### <a name="azure-functions-set-up"></a>設定 Azure Functions

首先，我們要設定 Azure 函式的基本結構。 您可以在這裡找到設定的逐步指示：[使用 Visual Studio Code 建立函式](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)

我們的 Azure 函式需要下列設定：

- 語言：JavaScript
- 範本：HTTP 觸發程序
- 授權層級：匿名 (如果您偏好使用不同的授權模型，可於稍後切換)
- 函式名稱：使用者定義

依照上述設定的 [Azure Functions 指示](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)操作後，您在 Visual Studio Code 中應該會有 Azure 函式的專案，且有包含函式本身的 `index.js` 檔案。 此檔案內的程式碼應如下所示：

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

現在，我們將繼續安裝 Azure 通訊服務程式庫。

### <a name="install-communication-services-libraries"></a>安裝通訊服務程式庫

我們將使用 `Administration` 程式庫來產生 `User Access Tokens`。

使用 `npm install` 命令來安裝適用於 JavaScript 的 Azure 通訊服務系統管理用戶端程式庫。

```console

npm install @azure/communication-administration --save

```

`--save` 選項會在您的 **package.json** 檔案中，將程式庫列為相依性。

在 `index.js` 檔案頂端，匯入 `CommunicationIdentityClient` 的介面

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');
```

## <a name="access-token-generation"></a>產生存取權杖

為了讓我們的 Azure 函式產生 `User Access Tokens`，我們必須先對通訊服務資源使用連接字串。

如需關於擷取連接字串的詳細資訊，請造訪[資源佈建快速入門](../../quickstarts/create-communication-resource.md)。

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

接著，我們將修改原始函式以產生 `User Access Tokens`。 

`User Access Tokens` 是藉由從 `createUser` 方法建立使用者而產生的。 建立使用者之後，我們可以使用 `issueToken` 方法為該使用者產生 Azure Function 傳回的權杖。

在此範例中，我們會將權杖範圍設定為 `voip`。 您的應用程式可能需要其他範圍。 深入了解[範圍](../../quickstarts/access-tokens.md)

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    const response = {
        "User" : userToken.user,
        "Token": userToken.token,
        "ExpiresOn": userToken.expiresOn
    }

    context.res = {
        body: response
    };
}
```

針對現有的通訊服務 `CommunicationUser`，您可以略過建立步驟而直接產生存取權杖。 如需詳細資訊，請參閱[建立使用者存取權杖快速入門](../../quickstarts/access-tokens.md)。

## <a name="test-the-azure-function"></a>測試 Azure 函式

使用 `F5` 在本機執行 Azure 函式。 這會在本機初始化 Azure 函式，並使其可透過下列方式存取：`http://localhost:7071/api/FUNCTION_NAME`。 查看關於[在本機執行](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-locally)的其他文件

在您的瀏覽器上開啟 URL，您應該會看到回應本文，以及通訊使用者識別碼、權杖，和權杖的到期日。

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="此螢幕擷取畫面顯示已建立 Azure 函式的回應範例。":::

## <a name="deploy-the-function-to-azure"></a>將函式部署至 Azure

若要部署您的 Azure 函式，您可以依照[逐步指示](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#sign-in-to-azure)操作

一般而言，您將需要：
1. 從 Visual Studio 登入 Azure
2. 將您的專案發佈至 Azure 帳戶。 在此，您將需要選擇現有的訂用帳戶。
3. 使用 Visual Studio 精靈建立新的 Azure 函式資源，或使用現有的資源。 對於新的資源，您必須將其設定為您所需的區域、執行階段和唯一識別碼。
4. 等待部署完成
5. 執行函式 🎉

## <a name="run-azure-function"></a>執行 Azure 函式

使用 URL `http://<function-appn-ame>.azurewebsites.net/api/<function-name>` 執行 Azure 函式

以滑鼠右鍵按一下 Visual Studio Code 上的函式，並複製函式 URL，以找出 URL。

如需[執行 Azure 函式](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-in-azure)的詳細資訊