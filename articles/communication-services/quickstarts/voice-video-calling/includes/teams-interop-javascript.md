---
title: 快速入門 - 加入 Teams 會議
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: dd75e5e97e5dca898ba10e91528782861fb949ec
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114559"
---
## <a name="prerequisites"></a>先決條件

- 運作中的[通訊服務通話應用程式](../getting-started-with-calling.md)。
- [Teams 部署](https://docs.microsoft.com/deployoffice/teams-install)。

## <a name="enable-teams-interoperability"></a>啟用 Teams 互通性

Teams 互通性功能目前為個人預覽版。 若要為您的通訊服務資源啟用這項功能，請傳送電子郵件到 [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com)，並附上：

1. 通訊服務資源所在 Azure 訂用帳戶的訂用帳戶識別碼。
2. Teams 租用戶識別碼。 若要取得此識別碼，最簡單的方式是[取得並分享小組的連結](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team)。

您必須是這兩個實體的擁有組織成員，才能使用這項功能。

## <a name="add-the-teams-ui-controls"></a>新增 Teams UI 控制項

在 HTML 中新增新的文字方塊和按鈕。 文字方塊將用來輸入 Teams 會議內容，而按鈕則用來加入指定的會議：

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
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>啟用 Teams UI 控制項

我們現在可以將 [加入 Teams 會議] 按鈕繫結至會加入所提供 Teams 會議的程式碼：

```javascript
meetingButton.addEventListener("click", () => {
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>取得會議內容

Teams 內容可使用圖形 API 來擷取。 詳細說明請見 [Graph 文件](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?view=graph-rest-beta&tabs=http)。

您也可以從會議邀請本身的 **加入會議** URL 取得所需的會議資訊。

## <a name="run-the-code"></a>執行程式碼

Webpack 使用者可以使用 `webpack-dev-server` 來建置及執行您的應用程式。 執行下列命令，在本機 Web 伺服器上組合應用程式主機：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

開啟瀏覽器並巡覽至 http://localhost:8080/。 您應該會看見下列內容：

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="已完成的 JavaScript 應用程式螢幕擷取畫面。":::

將 Teams 內容插入到文字方塊中，然後按 [加入 Teams 會議] 從通訊服務應用程式內加入 Teams 會議。

