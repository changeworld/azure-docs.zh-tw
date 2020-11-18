---
title: Azure 通訊服務中的疑難排解
description: 了解如何收集所需資訊以針對通訊服務解決方案進行疑難排解。
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ff3e7fee87661fb89ba930b7368bd54e71ad57bf
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93357618"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Azure 通訊服務中的疑難排解

本文件將協助您對您在通訊服務解決方案中可能遇到的問題進行疑難排解。 如果您要對 SMS 進行疑難排解，您可以[使用事件方格啟用傳遞報告](../quickstarts/telephony-sms/handle-sms-events.md)以擷取 SMS 傳遞詳細資料。

## <a name="getting-help"></a>取得說明

我們鼓勵開發人員提交問題、建議功能，並回報問題，如同通訊服務 [GitHub 存放庫](https://github.com/Azure/communication)中的問題。 其他論壇包括：

* [Microsoft Q&A](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

根據您的 Azure 訂用帳戶[支援方案](https://azure.microsoft.com/support/plans/)，您可以直接透過 [Azure 入口網站](https://azure.microsoft.com/support/create-ticket/)提交支援票證。

為了協助您針對特定類型的問題進行疑難排解，系統可能會要求您提供下列任何資訊：

* **MS-CV 識別碼**：此識別碼用來針對呼叫和訊息進行疑難排解。 
* **呼叫識別碼**：這個識別碼是用來識別通訊服務呼叫。
* **SMS 訊息識別碼**：此識別碼是用來識別 SMS 訊息。
* **通話記錄**：這些記錄包含可用來對通話和網路問題進行疑難排解的詳細資訊。


## <a name="access-your-ms-cv-id"></a>存取您的 MS-CV 識別碼

初始化您的用戶端程式庫時，可以在 `clientOptions` 物件執行個體中設定診斷，以存取 MS-CV 識別碼。 您可以針對任何 Azure 用戶端程式庫 (包括交談、管理和 VoIP 通話) 設定診斷。

### <a name="client-options-example"></a>用戶端選項範例

下列程式碼片段示範診斷組態。 當用戶端程式庫在啟用診斷功能時使用，系統會將診斷詳細資料發出至已設定的事件接聽程式：

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>存取您的呼叫識別碼

透過的 Azure 入口網站提出與呼叫問題相關的支援要求時，系統可能會要求您提供所參考的呼叫識別碼。 這可以透過呼叫用戶端程式庫來存取：

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>存取您的 SMS 訊息識別碼

針對 SMS 問題，您可以從回應物件收集訊息識別碼。

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>啟用和存取通話記錄




# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列程式碼可用來設定 `AzureLogger`，以使用 JavaScript 用戶端程式庫將記錄輸出至主控台：

```javascript
import { AzureLogger } from '@azure/logger'; 

AzureLogger.verbose = (...args) => { console.info(...args); } 
AzureLogger.info = (...args) => { console.info(...args); } 
AzureLogger.warning = (...args) => { console.info(...args); } 
AzureLogger.error = (...args) => { console.info(...args); } 

callClient = new CallClient({logger: AzureLogger}); 
```

# <a name="ios"></a>[iOS](#tab/ios)

對 iOS 進行開發時，您的記錄會儲存在 `.blog` 檔案中。 請注意，記錄會加密，因此您無法直接檢視。

您可以開啟 Xcode 加以存取。 移至 [Windows] > [裝置和模擬器] > [裝置]。 選取您的裝置。 在 [已安裝的應用程式] 底下選取您的應用程式，然後按一下 [下載容器]。 

您將會取得 `xcappdata` 檔案。 以滑鼠右鍵按一下此檔案，然後選取 [顯示套件內容]。 接著，您會看到隨後可以將附加至 Azure 支援要求的 `.blog` 檔案。

# <a name="android"></a>[Android](#tab/android)

對 Android 進行開發時，您的記錄會儲存在 `.blog` 檔案中。 請注意，記錄會加密，因此您無法直接檢視。

在 Android Studio 上，從模擬器和裝置選取 [檢視] > [工具視窗] > [裝置檔案總管]，以瀏覽至 [裝置檔案總管]。 `.blog` 檔案會位於應用程式的目錄中，看起來會像是 `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog`。 您可以將此檔案附加至您的支援要求。 
   

---


## <a name="related-information"></a>相關資訊
- [記錄與診斷](logging-and-diagnostics.md)
- [計量](metrics.md)
