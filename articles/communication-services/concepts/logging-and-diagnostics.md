---
title: 通訊服務記錄
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure 通訊服務中的記錄
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: aad4cdfe38ee9dd7530cb8ebe21cded18cb0a1ec
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128569"
---
# <a name="communication-services-logs"></a>通訊服務記錄

Azure 通訊服務會提供記錄功能，以供您監視通訊服務解決方案並對其進行偵錯。 這些功能可透過 Azure 入口網站來設定。

## <a name="enable-diagnostic-logs-in-your-resource"></a>在您的資源中啟用診斷記錄

根據預設，建立資源時會關閉記錄功能。 若要啟用記錄功能，請瀏覽至 [監視] 區段底下 [資源] 功能表中的 [診斷設定] 刀鋒視窗。 然後，按一下 [新增診斷設定]。

接下來，選取您想要的封存目標。 我們目前支援以儲存體帳戶和 Log Analytics 作為封存目標。 選取您想要擷取的記錄類型之後，請儲存診斷設定。
 
新的設定大約會在 10 分鐘內生效。 記錄會開始出現在「通訊服務」資源 [記錄] 窗格內所設定的封存目標中。

:::image type="content" source="./media/diagnostic-settings.png" alt-text="ACS 診斷設定選項。":::

如需如何設定診斷的詳細資訊，請參閱 [Azure 資源記錄](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)的概觀。

## <a name="resource-log-categories"></a>資源記錄類別

通訊服務提供三種類型的記錄供您啟用：

* **使用量記錄** - 提供與每個計費服務供應項目相關聯的使用量資料
* **聊天作業記錄** - 提供與聊天服務相關的基本資訊
* **簡訊作業記錄** - 提供與簡訊服務相關的基本資訊

### <a name="usage-logs-schema"></a>使用量記錄結構描述

| 屬性 | 描述 |
| -------- | ---------------|
| 時間戳記 | 產生記錄時的時間戳記 (UTC)。 |
| 作業名稱 | 與記錄相關聯的作業。 |
| 作業版本 | 與作業相關聯的 `api-version` (如果使用 API 執行 operationName 的話)。 如果沒有對應至此作業的 API，則版本代表該作業的版本，以防與作業建立關聯的屬性在未來變更。 |
| 類別 | 事件的記錄類別。 類別是您可在特定資源上啟用或停用記錄的資料粒度。 事件之屬性 Blob 內顯示的屬性，會與特定記錄類別和資源類型內的屬性相同。 |
| 相互關連識別碼 | 相互關聯事件的識別碼。 可用來識別多個資料表之間的相互關聯事件。 |
| 屬性 | 適用於各種通訊服務模式的其他資料。 |
| 記錄識別碼 | 給定使用量記錄的唯一識別碼。 |
| 使用量類型 | 使用量模式。 (例如，聊天、PSTN、NAT 等等) |
| 單位類型 | 在給定的使用量模式中，作為使用量依據的單位類型。 (例如，分鐘、MB、訊息數等)。 |
| 數量 | 此記錄所使用或取用的單位數。 |

### <a name="chat-operational-logs"></a>聊天作業記錄

| 屬性 | 描述 |
| -------- | ---------------|
| TimeGenerated | 產生記錄時的時間戳記 (UTC)。 |
| OperationName | 與記錄相關聯的作業。 |
| CorrelationID | 相互關聯事件的識別碼。 可用來識別多個資料表之間的相互關聯事件。 |
| OperationVersion | 與作業相關聯的 api-version (如果使用 API 執行 operationName 的話)。 如果沒有對應至此作業的 API，則版本代表該作業的版本，以防與作業建立關聯的屬性在未來變更。 |
| 類別 | 事件的記錄類別。 類別是您可在特定資源上啟用或停用記錄的資料粒度。 事件之屬性 Blob 內顯示的屬性，會與特定記錄類別和資源類型內的屬性相同。 |
| ResultType | 作業的狀態。 |
| ResultSignature | 作業的子狀態。 如果此作業對應至 REST API 呼叫，則此欄位是對應 REST 呼叫的 HTTP 狀態碼。 |
| ResultDescription | 此作業的靜態文字描述。 |
| DurationMs | 作業的持續時間 (以毫秒為單位)。 |
| CallerIpAddress | 呼叫端 IP 位址，如果作業對應至來自具有公開可用 IP 位址之實體的 API 呼叫。 |
| 層級 | 事件的嚴重性層級。 |
| URI | 要求的 URI。 |
| UserId | 要求傳送者的使用者識別碼。 |
| ChatThreadId | 與要求相關聯的聊天對話識別碼。 |
| ChatMessageId | 與要求相關聯的聊天訊息識別碼。 |
| SdkType | 在要求中使用的 Sdk 類型。 |
| PlatformType | 在要求中使用的平台類型。 |

### <a name="sms-operational-logs"></a>簡訊作業記錄

| 屬性 | 描述 |
| -------- | ---------------|
| TimeGenerated | 產生記錄時的時間戳記 (UTC)。 |
| OperationName | 與記錄相關聯的作業。 |
| CorrelationID | 相互關聯事件的識別碼。 可用來識別多個資料表之間的相互關聯事件。 |
| OperationVersion | 與作業相關聯的 api-version (如果使用 API 執行 operationName 的話)。 如果沒有對應至此作業的 API，則版本代表該作業的版本，以防與作業建立關聯的屬性在未來變更。 |
| 類別 | 事件的記錄類別。 類別是您可在特定資源上啟用或停用記錄的資料粒度。 事件之屬性 Blob 內顯示的屬性，會與特定記錄類別和資源類型內的屬性相同。 |
| ResultType | 作業的狀態。 |
| ResultSignature | 作業的子狀態。 如果此作業對應至 REST API 呼叫，則此欄位是對應 REST 呼叫的 HTTP 狀態碼。 |
| ResultDescription | 此作業的靜態文字描述。 |
| DurationMs | 作業的持續時間 (以毫秒為單位)。 |
| CallerIpAddress | 呼叫端 IP 位址，如果作業對應至來自具有公開可用 IP 位址之實體的 API 呼叫。 |
| 層級 | 事件的嚴重性層級。 |
| URI | 要求的 URI。 |
| OutgoingMessageLength | 外寄訊息中的字元數。 |
| IncomingMessageLength | 內送訊息中的字元數。 |
| DeliveryAttempts | 為了傳遞此訊息所嘗試的次數。 |
| PhoneNumber | 作為手機簡訊傳送目的地的電話號碼。 |
| SdkType | 在要求中使用的 SDK 類型。 |
| PlatformType | 在要求中使用的平台類型。 |
| 方法 | 在要求中使用的方法。 |
