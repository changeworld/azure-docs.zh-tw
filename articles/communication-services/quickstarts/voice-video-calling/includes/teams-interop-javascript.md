---
title: 快速入門 - 加入 Teams 會議
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: dbba87be839d7f172d42827698a8e485c2edddd8
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256240"
---
## <a name="prerequisites"></a>先決條件

- 運作中的[通訊服務通話應用程式](../getting-started-with-calling.md)。
- [Teams 部署](/deployoffice/teams-install)。


## <a name="add-the-teams-ui-controls"></a>新增 Teams UI 控制項

使用下列程式碼片段取代 index.html 中的程式碼。
文字方塊將用來輸入 Teams 會議內容，而按鈕則用來加入指定的會議：

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>啟用 Teams UI 控制項

使用下列程式碼片段取代 client.js 檔案的內容。

```javascript
import { CallClient } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
    })
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>取得 Teams 會議連結

您可以使用圖形 API 來擷取 Teams 會議連結。 詳細說明請見 [Graph 文件](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta)。
通訊服務通話 SDK 會接受完整的 Teams 會議連結。 此連結也可以作為 `onlineMeeting` 資源的一部分傳回，並可以在 [`joinWebUrl` 屬性](/graph/api/resources/onlinemeeting?view=graph-rest-beta)底下存取。您也可以從 Teams 會議邀請本身的 [加入會議] URL 取得所需的會議資訊。

## <a name="run-the-code"></a>執行程式碼

Webpack 使用者可以使用 `webpack-dev-server` 來建置及執行您的應用程式。 執行下列命令，在本機 Web 伺服器上組合應用程式主機：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

開啟瀏覽器並巡覽至 http://localhost:8080/。 您應該會看見下列內容：

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="已完成的 JavaScript 應用程式螢幕擷取畫面。":::

將 Teams 內容插入到文字方塊中，然後按 [加入 Teams 會議] 從通訊服務應用程式內加入 Teams 會議。
