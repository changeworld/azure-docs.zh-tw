---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 2f884a09e6b51b1c72034a62eaea601a4e69ecd2
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024233"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署通訊服務資源。 [建立通訊服務資源](../../create-communication-resource.md)。
- 在通訊服務資源中取得的電話號碼。 [如何取得電話號碼](../../telephony-sms/get-phone-number.md)。
- 用來啟用通話用戶端的 `User Access Token`。 如需[如何取得 `User Access Token`](../../access-tokens.md) 的詳細資訊


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

此程式碼如下：

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
      id="callee-phone-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 230px;"
    />
    <div>
      <button id="call-phone-button" type="button">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-phone-button" type="button" disabled="true">
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

const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential('your-token-here');
    callAgent = await callClient.createCallAgent(tokenCredential);
  //  callButton.disabled = false;
}

init();

```

## <a name="start-a-call-to-phone"></a>啟動通話

指定您在通訊服務資源中取得的電話號碼，將用來啟動通話：
> [!WARNING]
> 請注意，電話號碼應以 E. 164 國際標準格式提供。 (例如：+12223334444)

新增事件處理常式，以在按一下 `callPhoneButton` 時，對您提供的電話號碼起始通話：


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.call(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: 'YOUR AZURE REGISTERED PHONE NUMBER HERE: +12223334444'}
  });
  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>結束通話

新增事件接聽程式，以在按一下 `hangUpPhoneButton` 時結束目前的通話：

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

`forEveryone` 屬性會結束所有通話參與者的通話。

## <a name="run-the-code"></a>執行程式碼

使用 `webpack-dev-server` 來建置並執行您的應用程式。 執行下列命令，在本機 Web 伺服器上組合應用程式主機：


```console
npx webpack-dev-server --entry ./client.js --output bundle.js
```

開啟瀏覽器並巡覽至 `http://localhost:8080/`。 您應該會看見下列內容：

:::image type="content" source="../media/javascript/pstn-calling-javascript-app.png" alt-text="已完成的 JavaScript 應用程式螢幕擷取畫面。":::

您可以在新增的文字欄位中提供電話號碼，並按一下 [啟動通話] 按鈕，以對實際的電話號碼進行通話。

> [!WARNING]
> 請注意，電話號碼應以 E. 164 國際標準格式提供。 (例如：+12223334444)
