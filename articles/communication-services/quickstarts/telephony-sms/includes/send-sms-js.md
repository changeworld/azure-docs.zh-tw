---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: cdd4988f9a23904c0771852c4539aa9bce2ee683
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943954"
---
藉由使用通訊服務 JavaScript SMS 用戶端程式庫來傳送 SMS 訊息，以開始使用 Azure 通訊服務。

完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) 作用中 LTS 和維修 LTS 版本 (建議使用 8.11.1 和 10.14.1)。
- 作用中的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../../create-communication-resource.md)。
- 已啟用 SMS 的電話號碼。 [取得電話號碼](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先決條件檢查

- 在終端機或命令視窗中執行 `node --version`，確認已安裝 Node.js。
- 若要檢視與您通訊服務資源相關聯的電話號碼，請登入 [Azure 入口網站](https://portal.azure.com/)、尋找您的通訊服務資源，然後從左側瀏覽窗格開啟 [電話號碼] 索引標籤。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

首先，開啟您的終端機或命令視窗，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir sms-quickstart && cd sms-quickstart
```

執行 `npm init -y` 以使用預設設定建立 **package.json** 檔案。

```console
npm init -y
```

使用文字編輯器，在專案根目錄中建立名為 **send-sms.js** 的檔案。 您會在下列各節中，將本快速入門的所有原始程式碼新增至此檔案。

### <a name="install-the-package"></a>安裝套件

使用 `npm install` 命令來安裝適用於 JavaScript 的 Azure 通訊服務 SMS 用戶端程式庫。

```console
npm install @azure/communication-sms --save
```

`--save` 選項會在您的 **package.json** 檔案中，將程式庫列為相依性。

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務 SMS 用戶端程式庫的一些主要 Node.js 功能。

| Name                                  | 描述                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | 這是所有 SMS 功能所需的類別。 您可以使用您的訂用帳戶資訊將其具現化，並用來傳送 SMS 訊息。 |
| SendSmsOptions | 此介面提供設定傳遞報告的選項。 如果 `enable_delivery_report` 設定為 `true`，則事件會在傳遞成功時發出。 |
| SendMessageRequest | 此介面是用來建立 SMS 要求的模型 (例如： 設定往返電話號碼以及 SMS 內容)。 |

## <a name="authenticate-the-client"></a>驗證用戶端

從用戶端程式庫匯入 **SmsClient**，並使用您的連接字串將其具現化。 以下程式碼會從名為 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的環境變數中，擷取資源的連接字串。 了解如何[管理資源的連接字串](../../create-communication-resource.md#store-your-connection-string)。

在 **send-sms.js** 中加入以下程式碼：

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>傳送 SMS 訊息

呼叫 `send` 方法，以傳送 SMS 訊息。 將此程式碼加入到 **send-sms.js** 的結尾處：

```javascript
await smsClient.send({
  from: "<leased-phone-number>",
  to: ["<to-phone-number>"],
  message: "Hello World 👋🏻 via Sms"
}, {
  enableDeliveryReport: true //Optional parameter
});
```

您應該將 `<leased-phone-number>` 取代為與您通訊服務資源相關聯且已啟用 SMS 的電話號碼，並使用您想要對其傳送訊息的電話號碼取代 `<to-phone-number>`。 所有的電話號碼參數都應該遵守 [E-164 標準](../../../concepts/telephony-sms/plan-solution.md#optional-reading-international-public-telecommunication-numbering-plan-e164)。

`enableDeliveryReport` 參數是選擇性參數，可讓您用來設定傳遞報告。 這適用於想要在傳遞 SMS 訊息時發出事件的案例。 請參閱[處理 SMS 事件](../handle-sms-events.md)快速入門，以設定 SMS 訊息的傳遞報告。

## <a name="run-the-code"></a>執行程式碼

使用 `node` 命令來執行您新增至 **send-sms.js** 檔案的程式碼。

```console

node ./send-sms.js

```