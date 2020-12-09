---
title: 快速入門 - 使用 Azure 通訊服務將 VOIP 通話新增至 Web 應用程式
description: 在本教學課程中，您將了解如何使用適用於 JavaScript 的 Azure 通訊服務通話用戶端程式庫
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d35e51d097c2d5e0b66c23efa27ae70c065d547c
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96584573"
---
在本快速入門中，您將了解如何使用適用於 JavaScript 的 Azure 通訊服務通話用戶端程式庫以開始通話。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) 作用中 LTS 和維修 LTS 版本 (建議使用 8.11.1 和 10.14.1)。
- 使用中的通訊服務資源。 [建立通訊服務資源](../../create-communication-resource.md)。
- 用來具現化通話用戶端的使用者存取權杖。 了解如何[建立和管理使用者存取權杖](../../access-tokens.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

開啟您的終端機或命令視窗，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir calling-quickstart && cd calling-quickstart
```

執行 `npm init -y` 以使用預設設定建立 **package.json** 檔案。

```console
npm init -y
```

### <a name="install-the-package"></a>安裝套件

使用 `npm install` 命令來安裝適用於 JavaScript 的 Azure 通訊服務通話用戶端程式庫。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

本快速入門中建議使用下列版本的 Webpack：

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

`--save` 選項會在您的 **package.json** 檔案中，將程式庫列為相依性。

### <a name="set-up-the-app-framework"></a>設定應用程式架構

本快速入門會使用 webpack 來組合應用程式資產。 執行下列命令來安裝 webpack、webpack-cli 和 webpack-dev-server 套件，並將這些套件列為 **package.json** 中的開發相依性：

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

在專案的根目錄中建立 **index.html** 檔案。 我們將使用此檔案來設定基本配置，讓使用者能夠與 Azure Communications Bot 通話。

程式碼如下：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

在專案的根目錄中建立名為 **client.js** 的檔案，以包含本快速入門的應用程式邏輯。 新增下列程式碼以匯入通話用戶端，並取得 DOM 元素的參考，以便可以附加我們的商務邏輯。 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務通話用戶端程式庫的一些主要功能：

| Name                             | 描述                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient 是通話用戶端程式庫的主要進入點。                                                                       |
| CallAgent                        | CallAgent 是用來啟動和管理通話。                                                                                            |
| AzureCommunicationUserCredential | AzureCommunicationUserCredential 類別會實作用來具現化 CallAgent 的 CommunicationUserCredential 介面。 |


## <a name="authenticate-the-client"></a>驗證用戶端

您必須將 `<USER_ACCESS_TOKEN>` 取代為資源的有效使用者存取權杖。 如果您還沒有可用的權杖，請參閱[使用者存取權杖](../../access-tokens.md)文件。 使用 `CallClient`，透過 `CommunicationUserCredential` 來初始化 `CallAgent` 執行個體，讓我們能夠撥打和接收通話。 將下列程式碼新增至 **client.js**：

```javascript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;
}
init();
```

## <a name="start-a-call"></a>開始通話

新增事件處理常式，以在按一下 `callButton` 時起始通話：

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>結束通話

新增事件接聽程式，以在按一下 `hangUpButton` 時結束目前的通話：

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

`forEveryone` 屬性會結束所有通話參與者的通話。

## <a name="run-the-code"></a>執行程式碼

使用 `webpack-dev-server` 來建置並執行您的應用程式。 執行下列命令，在本機 Web 伺服器上組合應用程式主機：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

開啟瀏覽器並巡覽至 http://localhost:8080/。 您應該會看見下列內容：

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="已完成的 JavaScript 應用程式螢幕擷取畫面。":::

您可以在文字欄位中提供使用者識別碼並且按一下 [開始通話] 按鈕，以進行外撥 VOIP 通話。 呼叫 `8:echo123` 會將您連線到 Echo Bot，這非常適合用於開始使用和驗證音訊裝置。
