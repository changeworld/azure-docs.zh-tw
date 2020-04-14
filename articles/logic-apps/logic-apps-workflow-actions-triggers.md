---
title: 觸發器與操作型態的架構參考
description: Azure 邏輯應用中工作流定義語言觸發器和操作類型的架構參考指南
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 7e14cc00d1bd716b3e4880e585b05447d2e55e2b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257431"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Azure 邏輯應用中觸發器和操作類型的架構參考指南

此引用描述用於識別邏輯應用的基礎工作流定義中的觸發器和操作的一般類型,工作流[定義語言](../logic-apps/logic-apps-workflow-definition-language.md)描述和驗證了該定義。 要查找可在邏輯應用中使用的特定連接器觸發器和操作,請參閱[連接器概述](https://docs.microsoft.com/connectors/)下的清單。

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>觸發程序概觀

每個工作流都包含一個觸發器,該觸發器定義實例化並啟動工作流的調用。 以下是一般觸發程序類別：

* 輪詢** 觸發程序，用來定期檢查服務的端點

* 推送** 觸發程序，用來建立端點的訂閱，並提供*回呼 URL*，讓端點在指定的事件發生或資料可供使用時，可以通知觸發程序。 然後，觸發程序會等到端點回應後才引發。

觸發程序具有下列最上層元素 (儘管有些是選用元素)：  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*觸發器名稱*> | String | 觸發程序的名稱 | 
| <*觸發器類型*> | String | 觸發程序類型，例如 "Http" 或 "ApiConnection" | 
| <*觸發器輸入*> | JSON 物件 | 定義觸發程序行為的輸入 | 
| <*時間單位*> | String | 說明觸發程序多久引發一次的時間單位：[秒]、[分鐘]、[小時]、[天]、[週]、[月] | 
| <*時間單位數*> | 整數 | 一個值，指定觸發程序根據頻率多久會引發一次，這是觸發程序再次引發之前等待的時間單位數 <p>以下是最小和最大間隔： <p>- 月：1-16 個月 </br>- 天：1-500 天 </br>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果間隔為 6，而頻率為 [月]，則週期為每 6 個月一次。 | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*符合條件的陣列*> | Array | 包含一個或多個[條件](#trigger-conditions)以確定是否運行工作流的陣列。 僅適用於觸發程序。 | 
| <*執行時設定選項*> | JSON 物件 | 您可以藉由設定 `runtimeConfiguration` 屬性來變更觸發程序執行階段行為。 如需詳細資訊，請參閱[執行階段組態設定](#runtime-config-options)。 | 
| <*分割上運算式*> | String | 針對會傳回陣列的觸發程序，您可以指定運算式，將陣列項目[分割或解除批次**](#split-on-debatch)為多個工作流程執行個體，以進行處理。 | 
| <*操作選項*> | String | 您可以藉由設定 `operationOptions` 屬性來變更預設行為。 如需詳細資訊，請參閱[作業選項](#operation-options)。 | 
|||| 

## <a name="trigger-types-list"></a>觸發程序類型清單

每一種觸發程序類型都有不同的介面，以及可定義觸發程序行為的輸入。 

### <a name="built-in-triggers"></a>內建觸發程序

| 觸發程序類型 | 描述 | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | 檢查或輪詢** 任何端點。 此端點必須藉由使用 "202" 非同步模式或藉由傳回陣列，來遵守特定觸發合約。 | 
| [**HTTPWebhook**](#http-webhook-trigger) | 為您的邏輯應用程式建立可呼叫的端點，但會呼叫指定的 URL 以進行註冊或取消註冊。 |
| [**週期性**](#recurrence-trigger) | 根據已定義的排程來引發。 您可以設定一個未來的日期和時間來引發此觸發程序。 您也可以根據頻率來指定工作流程的執行時間和日期。 | 
| [**要求**](#request-trigger)  | 為您的邏輯應用程式建立可呼叫的端點，也稱為「手動」觸發程序。 例如，請參閱[透過 HTTP 端點呼叫、觸發或巢狀處理工作流程](../logic-apps/logic-apps-http-endpoint.md)。 | 
||| 

### <a name="managed-api-triggers"></a>受控 API 觸發程序

| 觸發程序類型 | 描述 | 
|--------------|-------------| 
| [**Api 連線**](#apiconnection-trigger) | 使用 [Microsoft 管理的 API](../connectors/apis-list.md) 來檢查或輪詢** 端點。 | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | 藉由呼叫 [Microsoft 管理的 API](../connectors/apis-list.md) 進行訂閱及取消訂閱，為您的邏輯應用程式建立可呼叫的端點。 | 
||| 

## <a name="triggers---detailed-reference"></a>觸發程序 - 詳細參考

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection 觸發程序  

此觸發程序會使用 [Microsoft 管理的 API](../connectors/apis-list.md)，讓此觸發程序的參數可根據端點而有所差異，以檢查或輪詢** 端點。 此觸發程序定義中的許多區段都是選用的。 此觸發程序的行為取決於是否包含區段。

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*必要*

| 值 | 類型 | 描述 |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | String | 觸發程序的名稱 |
| <*連線名稱*> | String | 工作流程所使用的受控 API 連線名稱 |
| <*方法類型*> | String | 與受控 API 通訊時所使用的 HTTP 方法："GET"、"PUT"、"POST"、"PATCH"、"DELETE" |
| <*api 操作*> | String | 要呼叫的 API 作業 |
| <*時間單位*> | String | 說明觸發程序多久引發一次的時間單位：[秒]、[分鐘]、[小時]、[天]、[週]、[月] |
| <*時間單位數*> | 整數 | 一個值，指定觸發程序根據頻率多久會引發一次，這是觸發程序再次引發之前等待的時間單位數 <p>以下是最小和最大間隔： <p>- 月：1-16 個月 </br>- 天：1-500 天 </br>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果間隔為 6，而頻率為 [月]，則週期為每 6 個月一次。 |
||||

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*重試行為*> | JSON 物件 | 自訂間歇性失敗 (狀態碼為 408、429 和 5XX) 和任何連線例外狀況的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md#retry-policies)。 | 
| <*查詢參數*> | JSON 物件 | 要包含在 API 呼叫中的任何查詢參數。 例如，`"queries": { "api-version": "2018-01-01" }` 物件會將 `?api-version=2018-01-01` 新增至呼叫。 | 
| <*最大執行*> | 整數 | 預設情況下,工作流實例同時(併發或並行)運行,最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要藉由設定新的 <*count*> 值來變更此限制，請參閱[變更觸發程序並行](#change-trigger-concurrency)。 | 
| <*最大執行佇列*> | 整數 | 當工作串流已執行最大實體數(您可以根據`runtimeConfiguration.concurrency.runs`屬性進行變更)時,任何新執行都放入此佇列,最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要變更預設限制，請參閱[變更等候執行限制](#change-waiting-runs)。 | 
| <*分割上運算式*> | String | 針對會傳回陣列的觸發程序，此運算式會參考要使用的陣列，讓您可以為每個陣列項目建立和執行工作流程執行個體，而非使用 "for each" 迴圈。 <p>例如，此運算式表示在觸發程序的主體內容中傳回的陣列項目：`@triggerbody()?['value']` |
| <*操作選項*> | String | 您可以藉由設定 `operationOptions` 屬性來變更預設行為。 如需詳細資訊，請參閱[作業選項](#operation-options)。 |
||||

*輸出*
 
| 元素 | 類型 | 描述 |
|---------|------|-------------|
| headers | JSON 物件 | 回應中的標頭 |
| body | JSON 物件 | 回應中的本文 |
| 狀態碼 | 整數 | 回應的狀態碼 |
|||| 

*範例*

此觸發程序定義會每天在 Office 365 Outlook 帳戶的收件匣內檢查電子郵件：

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook 觸發程序

此觸發程序會使用 [Microsoft 管理的 API](../connectors/apis-list.md) 將訂閱要求傳送至端點、提供可讓端點傳送回應的*回呼 URL*，並等候端點回應。 如需詳細資訊，請參閱[端點訂閱](#subscribe-unsubscribe)。

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*連線名稱*> | String | 工作流程所使用的受控 API 連線名稱 | 
| <*身體內容*> | JSON 物件 | 任何要以承載的形式傳送至受控 API 的訊息內容 | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*重試行為*> | JSON 物件 | 自訂間歇性失敗 (狀態碼為 408、429 和 5XX) 和任何連線例外狀況的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md#retry-policies)。 | 
| <*查詢參數*> | JSON 物件 | 要包含在 API 呼叫中的任何查詢參數 <p>例如，`"queries": { "api-version": "2018-01-01" }` 物件會將 `?api-version=2018-01-01` 新增至呼叫。 | 
| <*最大執行*> | 整數 | 預設情況下,工作流實例同時(併發或並行)運行,最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要藉由設定新的 <*count*> 值來變更此限制，請參閱[變更觸發程序並行](#change-trigger-concurrency)。 | 
| <*最大執行佇列*> | 整數 | 當工作串流已執行最大實體數(您可以根據`runtimeConfiguration.concurrency.runs`屬性進行變更)時,任何新執行都放入此佇列,最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要變更預設限制，請參閱[變更等候執行限制](#change-waiting-runs)。 | 
| <*分割上運算式*> | String | 針對會傳回陣列的觸發程序，此運算式會參考要使用的陣列，讓您可以為每個陣列項目建立和執行工作流程執行個體，而非使用 "for each" 迴圈。 <p>例如，此運算式表示在觸發程序的主體內容中傳回的陣列項目：`@triggerbody()?['value']` |
| <*操作選項*> | String | 您可以藉由設定 `operationOptions` 屬性來變更預設行為。 如需詳細資訊，請參閱[作業選項](#operation-options)。 | 
|||| 

*範例*

此觸發程序定義會訂閱 Office 365 Outlook API、提供 API 端點的回呼 URL，並在新的電子郵件送達時等候端點回應。

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP 觸發程序

此觸發器根據指定的定期計畫向指定的 HTTP 或 HTTPS 終結點發送請求。 然後,觸發器檢查回應以確定工作流是否運行。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*必要*

| 屬性 | 值 | 類型 | 描述 |
|----------|-------|------|-------------|
| `method` | <*方法類型*> | String | 發送傳出請求的方法:"GET"、"PUT"、"開波"、"PATCH"或"刪除" |
| `uri` | <*HTTP 或 HTTPS-終結點-URL*> | String | 要送出傳出請求的 HTTP 或 HTTPS 終結點網址。 字串大小上限：2 KB <p>對於 Azure 服務或資源,此 URI 語法包括資源 ID 和要訪問的資源的路徑。 |
| `frequency` | <*時間單位*> | String | 說明觸發程序多久引發一次的時間單位：[秒]、[分鐘]、[小時]、[天]、[週]、[月] |
| `interval` | <*時間單位數*> | 整數 | 一個值，指定觸發程序根據頻率多久會引發一次，這是觸發程序再次引發之前等待的時間單位數 <p>以下是最小和最大間隔： <p>- 月：1-16 個月 </br>- 天：1-500 天 </br>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果間隔為 6，而頻率為 [月]，則週期為每 6 個月一次。 |
|||||

*選用*

| 屬性 | 值 | 類型 | 描述 |
|----------|-------|------|-------------|
| `headers` | <*標題內容*> | JSON 物件 | 需要隨要求包含的任何標頭 <p>例如，若要設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*查詢參數*> | JSON 物件 | 需要在要求中使用的任何查詢參數 <p>例如，`"queries": { "api-version": "2018-01-01" }` 物件會將 `?api-version=2018-01-01` 新增至要求。 |
| `body` | <*身體內容*> | JSON 物件 | 要以承載的形式透過要求傳送的訊息內容 |
| `authentication` | <*認證類型與屬性值*> | JSON 物件 | 請求用於驗證出站請求的身份驗證模型。 有關詳細資訊,請參閱[將身份驗證加入到出站呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。 除了排程器之外，還可支援 `authority` 屬性。 如果未指定,預設值為`https://management.azure.com/`,但您可以使用其他值。 |
| `retryPolicy` > `type` | <*重試行為*> | JSON 物件 | 自訂間歇性失敗 (狀態碼為 408、429 和 5XX) 和任何連線例外狀況的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md#retry-policies)。 |
| `runs` | <*最大執行*> | 整數 | 預設情況下,工作流實例同時(併發或並行)運行,最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要藉由設定新的 <*count*> 值來變更此限制，請參閱[變更觸發程序並行](#change-trigger-concurrency)。 |
| `maximumWaitingRuns` | <*最大執行佇列*> | 整數 | 當工作串流已執行最大實體數(您可以根據`runtimeConfiguration.concurrency.runs`屬性進行變更)時,任何新執行都放入此佇列,最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要變更預設限制，請參閱[變更等候執行限制](#change-waiting-runs)。 |
| `operationOptions` | <*操作選項*> | String | 您可以藉由設定 `operationOptions` 屬性來變更預設行為。 如需詳細資訊，請參閱[作業選項](#operation-options)。 |
|||||

*輸出*

| 元素 | 類型 | 描述 |
|---------|------|-------------| 
| headers | JSON 物件 | 回應中的標頭 | 
| body | JSON 物件 | 回應中的本文 | 
| 狀態碼 | 整數 | 回應的狀態碼 | 
|||| 

*連入要求的需求*

若要與邏輯應用程式妥善搭配運作，端點必須符合特定的觸發模式或合約，並辨識下列屬性：  
  
| 回應 | 必要 | 描述 | 
|----------|----------|-------------| 
| 狀態碼 | 是 | 「200 正常」狀態碼會啟動執行。 其他任何的狀態碼則不會啟動執行。 | 
| Retry-after 標頭 | 否 | 邏輯應用再次輪詢終結點的秒數 | 
| 位置標頭 | 否 | 在下一個輪詢間隔時所要呼叫的 URL。 如果未指定，則會使用原本的 URL。 | 
|||| 

不同要求的範例行為**

| 狀態碼 | 多久之後重試 | 行為 | 
|-------------|-------------|----------|
| 200 | {無} | 執行工作流程，然後在所定義的週期之後再次檢查是否有其他資料。 | 
| 200 | 10 秒 | 執行工作流程，然後在 10 秒之後再次檢查是否有其他資料。 |  
| 202 | 60 秒 | 不觸發工作流程。 下一次嘗試會依據所定義的週期，在一分鐘內開始。 如果所定義的週期不超過一分鐘，則 retry-after 標頭的優先順序較高。 否則會採用所定義的週期。 | 
| 400 | {無} | 不正確的要求，不執行工作流程。 如果未定義任何 `retryPolicy`，則會使用預設原則。 達到重試次數之後，觸發程序會在所定義的週期之後再次檢查是否還有資料。 | 
| 500 | {無}| 伺服器錯誤，不執行工作流程。 如果未定義任何 `retryPolicy`，則會使用預設原則。 達到重試次數之後，觸發程序會在所定義的週期之後再次檢查是否還有資料。 | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook 觸發程序  

此觸發程序會建立可藉由呼叫指定的端點 URL 來註冊訂閱的端點，讓您的邏輯應用程式可供呼叫。 當您在工作流程中建立此觸發程序時，連出要求將會使呼叫註冊訂閱。 如此，觸發程序即可開始接聽事件。 若有作業使這個觸發程序失效，連出要求即會自動使呼叫取消訂閱。 如需詳細資訊，請參閱[端點訂閱](#subscribe-unsubscribe)。

您也可以指定 **HTTPWebhook** 觸發程序的[非同步限制](#asynchronous-limits)。 此觸發程序的行為取決於您所使用或省略的區段。

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

某些值 (例如 <*method-type*>) 同時適用於 `"subscribe"` 和 `"unsubscribe"` 物件。

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*方法類型*> | String | 用於訂閱要求的 HTTP 方法："GET"、"PUT"、"POST"、"PATCH" 或 "DELETE" | 
| <*端點訂閱網址*> | String | 要傳送訂閱要求的目標端點 URL | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*方法類型*> | String | 用於取消要求的 HTTP 方法："GET"、"PUT"、"POST"、"PATCH" 或 "DELETE" | 
| <*端點取消訂閱網址*> | String | 要傳送取消要求的目標端點 URL | 
| <*身體內容*> | String | 要在訂閱或取消要求中傳送的任何訊息內容 | 
| <*驗證類型*> | JSON 物件 | 請求用於驗證出站請求的身份驗證模型。 有關詳細資訊,請參閱[將身份驗證加入到出站呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。 |
| <*重試行為*> | JSON 物件 | 自訂間歇性失敗 (狀態碼為 408、429 和 5XX) 和任何連線例外狀況的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md#retry-policies)。 | 
| <*最大執行*> | 整數 | 預設情況下,工作流實例都同時執行(併發或並行),最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要藉由設定新的 <*count*> 值來變更此限制，請參閱[變更觸發程序並行](#change-trigger-concurrency)。 | 
| <*最大執行佇列*> | 整數 | 當工作串流已執行最大實體數(您可以根據`runtimeConfiguration.concurrency.runs`屬性進行變更)時,任何新執行都放入此佇列,最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要變更預設限制，請參閱[變更等候執行限制](#change-waiting-runs)。 | 
| <*操作選項*> | String | 您可以藉由設定 `operationOptions` 屬性來變更預設行為。 如需詳細資訊，請參閱[作業選項](#operation-options)。 | 
|||| 

*輸出* 

| 元素 | 類型 | 描述 |
|---------|------|-------------| 
| headers | JSON 物件 | 回應中的標頭 | 
| body | JSON 物件 | 回應中的本文 | 
| 狀態碼 | 整數 | 回應的狀態碼 | 
|||| 

*範例*

此觸發程序會建立對指定端點的訂閱、提供唯一回呼 URL，並等候新發佈的技術文章。

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>循環觸發程序  

此觸發程序會根據指定的週期排程來執行，並提供建立定期執行工作流程的簡單方式。

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*時間單位*> | String | 說明觸發程序多久引發一次的時間單位：[秒]、[分鐘]、[小時]、[天]、[週]、[月] | 
| <*時間單位數*> | 整數 | 一個值，指定觸發程序根據頻率多久會引發一次，這是觸發程序再次引發之前等待的時間單位數 <p>以下是最小和最大間隔： <p>- 月：1-16 個月 </br>- 天：1-500 天 </br>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果間隔為 6，而頻率為 [月]，則週期為每 6 個月一次。 | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*開始日期-時間-格式-YYYY-MM-DDThh:mm:ss*> | String | 使用以下格式的開始日期和時間： <p>YYYY-MM-DDThh:mm:ss (如果您指定時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未指定時區) <p>因此，舉例來說，如果您想要的是 2017 年 9 月 18 日下午 2:00，則請指定 "2017-09-18T14:00:00"，然後指定一個時區，例如 "Pacific Standard Time"，或指定不含時區的 "2017-09-18T14:00:00Z"。 <p>**註:** 此開始時間在未來最多為 49 年,必須遵循[UTC 日期格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)的[ISO 8601 日期時間規範](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations),但沒有[UTC 偏移](https://en.wikipedia.org/wiki/UTC_offset)量。 如果您不指定時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>就簡單排程來說，開始時間係指第一次發生的時間，而就複雜排程來說，觸發程序會在開始時間一到就立即引發。 如需有關開始日期和時間的詳細資訊，請參閱[建立及排定定期執行的工作](../connectors/connectors-native-recurrence.md)。 | 
| <*時區*> | String | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 指定您要套用的時區。 | 
| <*一小時或多小時標記*> | 整數或整數陣列 | 當您想要執行工作流程時，如果針對 `frequency` 指定 "Day" 或 "Week"，便可指定從 0 到 23 的一或多個整數 (以逗號分隔) 來表示一天中的哪幾個整點。 <p>例如，如果您指定 "10"、"12" 及 "14"，就會得出上午 10 點、下午 12 點及下午 2 點作為整點標記。 | 
| <*一分鐘或多分鐘標記*> | 整數或整數陣列 | 當您想要執行工作流程時，如果針對 `frequency` 指定 "Day" 或 "Week"，便可指定從 0 到 59 的一或多個整數 (以逗號分隔) 來表示小時中的哪幾個分鐘。 <p>例如，您可以指定 "30" 作為分鐘標記，然後使用上個範例代表一天中的整點，這樣就會得出上午 10:30、下午 12:30 及下午 2:30。 | 
| weekDays | 字串或字串陣列 | 當您想要執行工作流程時，如果針對 `frequency` 指定 "Week"，便可指定一或多天 (以逗號分隔)："Monday"、"Tuesday"、"Wednesday"、"Thursday"、"Friday"、"Saturday" 及 "Sunday" | 
| <*最大執行*> | 整數 | 預設情況下,工作流實例都同時執行(併發或並行),最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要藉由設定新的 <*count*> 值來變更此限制，請參閱[變更觸發程序並行](#change-trigger-concurrency)。 | 
| <*最大執行佇列*> | 整數 | 當工作串流已執行最大實體數(您可以根據`runtimeConfiguration.concurrency.runs`屬性進行變更)時,任何新執行都放入此佇列,最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要變更預設限制，請參閱[變更等候執行限制](#change-waiting-runs)。 | 
| <*操作選項*> | String | 您可以藉由設定 `operationOptions` 屬性來變更預設行為。 如需詳細資訊，請參閱[作業選項](#operation-options)。 | 
|||| 

*範例 1*

這個基本週期觸發程序會每天執行：

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*範例 2*

您可以指定開始日期和時間來引發觸發程序。 此週期觸發程序會於指定的日期開始，然後每天引發：

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*範例 3*

此週期觸發程序會於 2017 年 9 月 9 日下午 2:00 開始，在每週星期一早上 10:30、下午 12:30 及下午 2:30 (太平洋標準時間) 引發：

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

如需此觸發程序外加範例的詳細資訊，請參閱[建立及排定定期執行的工作](../connectors/connectors-native-recurrence.md)。

<a name="request-trigger"></a>

### <a name="request-trigger"></a>要求觸發程序

此觸發程序會藉由建立可接受連入要求的端點，讓您的邏輯應用程式可供呼叫。 針對此觸發程序，請提供 JSON 結構描述，用以說明及驗證承載，或觸發程序從連入要求接收的輸入。 此結構描述也可讓工作流程中的後續動作更容易參考觸發程序屬性。

若要呼叫此觸發程序，您必須使用[工作流程服務 REST API](https://docs.microsoft.com/rest/api/logic/workflows) 中說明的 `listCallbackUrl` API。 若要了解如何使用這個觸發程序作為 HTTP 端點，請參閱[透過 HTTP 端點呼叫、觸發或巢狀處理工作流程](../logic-apps/logic-apps-http-endpoint.md)。

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*屬性名稱*> | String | JSON 結構描述中的屬性名稱，用以說明承載 | 
| <*屬性類型*> | String | 屬性的類型 | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*方法類型*> | String | 連入要求呼叫您的邏輯應用程式時所必須使用的方法："GET"、"PUT"、"POST"、"PATCH"、"DELETE" |
| <*相對路徑為接受參數*> | String | 端點 URL 可接受的參數相對路徑 | 
| <*必要屬性*> | Array | 需要值的一或多個屬性 | 
| <*最大執行*> | 整數 | 預設情況下,工作流實例都同時執行(併發或並行),最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要藉由設定新的 <*count*> 值來變更此限制，請參閱[變更觸發程序並行](#change-trigger-concurrency)。 | 
| <*最大執行佇列*> | 整數 | 當工作串流已執行最大實體數(您可以根據`runtimeConfiguration.concurrency.runs`屬性進行變更)時,任何新執行都放入此佇列,最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要變更預設限制，請參閱[變更等候執行限制](#change-waiting-runs)。 | 
| <*操作選項*> | String | 您可以藉由設定 `operationOptions` 屬性來變更預設行為。 如需詳細資訊，請參閱[作業選項](#operation-options)。 | 
|||| 

*範例*

此觸發程序會指定連入要求必須使用 HTTP POST 方法來呼叫觸發程序，並且包含結構描述可驗證來自連入要求的輸入：

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>觸發條件

對於任何觸發程序且僅限觸發程序，您可以加入含有一或多個條件運算式以用來決定是否應執行工作流程的陣列。 要將`conditions`該屬性添加到工作流中的觸發器,請在代碼檢視編輯器中打開邏輯應用。

例如，您可以指定要藉由參考觸發程序在 `conditions` 屬性中的狀態碼，而僅在網站傳回內部伺服器錯誤時引發觸發程序：

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

根據預設，觸發程序只會在收到「200 確定」回應時引發。 當運算式參考觸發程序的狀態碼時，就會取代觸發程序的預設行為。 因此，如果您要針對多個狀態碼 (例如狀態碼 "200" 和 "201") 來引發觸發程序，您必須將此運算式納入您的條件中：

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>觸發多個執行

如果觸發程序傳回要讓邏輯應用程式處理的陣列，有時候 "for each" 迴圈會花太多時間來處理每個陣列項目。 您可以改用觸發程序中的 **SplitOn** 屬性來對該陣列進行「解除批次」**。 討論將拆分陣列項,並啟動為每個陣列項運行的新工作流實例。 例如，當您想要輪詢的端點可能在輪詢間隔之間傳回多個新項目時，就適用此方法。 如需 **SplitOn** 在單一邏輯應用程式中可處理的陣列項目數上限，請參閱[限制和設定](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 

> [!NOTE]
> 您無法將 **SplitOn** 用於同步回應模式。 任何使用 **SplitOn** 並包含回應動作的工作流程會以非同步的方式執行，且會立即傳送 `202 ACCEPTED` 回應。
>
> 啟用觸發器併發時,[拆分限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)將顯著減小。 如果項目數超過此限制,則禁用 SplitOn 功能。
 
如果觸發程序的 Swagger 說明承載屬於陣列，則 **SplitOn** 屬性會自動新增至您的觸發程序。 否則，就要在其中有陣列需要解除批次的回應承載內，新增此屬性。

*範例*

假設您有一個會傳回以下回應的 API： 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```

您的邏輯應用程式只需要陣列中的 `Rows` 所包含的內容，因此您可以建立如下列範例所示的觸發程序：

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> 如果您使用 `SplitOn` 命令，就無法取得陣列外的屬性。 因此針對此範例，您就無法在 API 傳回的回應中取得 `status`屬性。
> 
> 為避免在 `?` 屬性不存在時發生失敗，此範例使用了 `Rows` 運算子。

您的工作流程定義現在可以使用 `@triggerBody().name` 來取得 `name` 值；此值在第一個執行中為 `"customer-name-one"`，在第二個執行中為 `"customer-name-two"`。 因此，觸發程序的輸出看起來就像下面這些範例︰

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>動作概觀

Azure Logic Apps 提供各種不同的動作類型 - 各有不同的輸入會定義動作的獨特行為。 動作具有下列高階元素 (儘管有些是選用元素)：

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------|
| <*動作名稱*> | String | 動作的名稱 | 
| <*動作類型*> | String | 動作類型，例如："Http" 或 "ApiConnection"| 
| <*輸入名稱*> | String | 定義動作行為的輸入名稱 | 
| <*輸入值*> | 各種類型 | 輸入值，可以是字串、整數、JSON 物件等等 | 
| <*上一個觸發器或操作狀態*> | JSON 物件 | 必須先行執行，這個現行動作才可隨即執行的觸發程序或動作所具備的名稱和產生的狀態 | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------|
| <*重試行為*> | JSON 物件 | 自訂間歇性失敗 (狀態碼為 408、429 和 5XX) 和任何連線例外狀況的重試行為。 如需詳細資訊，請參閱重試原則。 | 
| <*執行時設定選項*> | JSON 物件 | 對於某些動作，您可以在執行階段藉由設定 `runtimeConfiguration` 屬性來變更動作的行為。 如需詳細資訊，請參閱[執行階段組態設定](#runtime-config-options)。 | 
| <*操作選項*> | String | 對於某些動作，您可以藉由設定 `operationOptions` 屬性來變更預設行為。 如需詳細資訊，請參閱[作業選項](#operation-options)。 | 
|||| 

## <a name="action-types-list"></a>動作類型清單

以下是一些常用的動作類型： 

* [內建動作類型](#built-in-actions)，例如下列範例： 

  * [**HTTP**](#http-action)，用以透過 HTTP 或 HTTPS 呼叫端點

  * [**回應**](#response-action)，用以回應要求

  * [**執行以**](#run-javascript-code)執行 JavaScript 程式碼段的 JavaScript 程式碼

  * [**函式**](#function-action)，用以呼叫 Azure Functions

  * 資料作業動作，例如[**聯結**](#join-action)、[**撰寫**](#compose-action)、[**資料表**](#table-action)、[**選取**](#select-action)，以及其他會建立資料或從各種不同的輸入轉換資料的動作

  * [**工作流程**](#workflow-action)，用以呼叫另一個邏輯應用程式工作流程

* [受控 API 動作類型](#managed-api-actions)，例如會呼叫多種由 Microsoft 管理的連接器和 API (例如 Azure 服務匯流排、Office 365 Outlook、Power BI、Azure Blob 儲存體、OneDrive、GitHub 等) 的 [**ApiConnection**](#apiconnection-action) 和 [**ApiConnectionWebHook**](#apiconnectionwebhook-action)

* [控制工作流程動作類型](#control-workflow-actions)，例如 [**If**](#if-action)、[**Foreach**](#foreach-action)、[**Switch**](#switch-action)、[**Scope**](#scope-action) 和 [**Until**](#until-action)，其中包含其他動作，並且可協助您組織工作流程執行

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>內建動作

| 動作類型 | 描述 | 
|-------------|-------------| 
| [**撰寫**](#compose-action) | 從可具有多種類型的輸入建立單一輸出。 | 
| [**執行 JavaScript 程式碼**](#run-javascript-code) | 運行符合特定條件的 JavaScript 代碼段。 關於程式碼要求與詳細資訊,請參閱[使用內聯代碼新增和執行程式碼段](../logic-apps/logic-apps-add-run-inline-code.md)。 |
| [**函式**](#function-action) | 呼叫 Azure 函式。 | 
| [**HTTP**](#http-action) | 呼叫 HTTP 端點。 | 
| [**加入**](#join-action) | 從陣列中的所有項目建立字串，並使用指定的分隔字元來分隔這些項目。 | 
| [**剖析 JSON**](#parse-json-action) | 從 JSON 內容中的屬性建立易記的權杖。 其後，您可以在邏輯應用程式中納入權杖，以參考這些屬性。 | 
| [**查詢**](#query-action) | 根據條件或篩選條件，從另一個陣列中的項目建立陣列。 | 
| [**回應**](#response-action) | 建立對連入呼叫或要求的回應。 | 
| [**選擇**](#select-action) | 根據指定的對應從另一個陣列中轉換項目，以使用 JSON 物件建立陣列。 | 
| [**表**](#table-action) | 從陣列建立 CSV 或 HTML 資料表。 | 
| [**終止**](#terminate-action) | 停止正在執行的工作流程。 | 
| [**等**](#wait-action) | 將工作流程暫停指定的持續期間，或直到指定的日期和時間為止。 | 
| [**工作流程**](#workflow-action) | 將工作流程內嵌於另一個工作流程內。 | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>受控 API 動作

| 動作類型 | 描述 | 
|-------------|-------------|  
| [**Api 連線**](#apiconnection-action) | 使用 [Microsoft 管理的 API](../connectors/apis-list.md) 來呼叫 HTTP 端點。 | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | 運作方式與 HTTPWebhook 類似，但使用 [Microsoft 管理的 API](../connectors/apis-list.md)。 | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>控制工作流程動作

這些動作可協助您控制工作流程執行，且包含其他動作。 在控制工作流程動作以外，您可以直接參考該控制工作流程動作內的動作。 例如，如果您在範圍內有 `Http` 動作，則可以從工作流程中的任一處參考 `@body('Http')` 運算式。 不過，存在於控制工作流程動作內的動作，就只能在相同控制工作流程結構中的其他動作「之後」執行。

| 動作類型 | 描述 | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | 以迴圈對陣列中的每個項目執行相同的動作。 | 
| [**如果**](#if-action) | 根據是否符合指定的條件來執行動作。 | 
| [**影響範圍**](#scope-action) | 根據一組動作的群組狀態來執行動作。 | 
| [**開關**](#switch-action) | 在運算式、物件或權杖中的值符合各個案例所指定的值時，執行組織為案例的動作。 | 
| [**直到**](#until-action) | 以迴圈執行動作，直到符合指定的條件為止。 | 
|||  

## <a name="actions---detailed-reference"></a>動作 - 詳細參考

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection 動作

此動作會將 HTTP 要求傳送至 [Microsoft 管理的 API](../connectors/apis-list.md)，因此需要 API 和參數的相關資訊，以及有效連線的參考。 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*動作名稱*> | String | 連接器所提供的動作名稱 | 
| <*api 名稱*> | String | 由 Microsoft 管理、用於連線的 API 名稱 | 
| <*方法類型*> | String | 呼叫 API 的 HTTP 方法："GET"、"PUT"、"POST"、"PATCH" 或 "DELETE" | 
| <*api 操作*> | String | 要呼叫的 API 作業 | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*其他特定於操作的輸入屬性*> | JSON 物件 | 任何其他套用至這個特定動作的輸入屬性 | 
| <*重試行為*> | JSON 物件 | 自訂間歇性失敗 (狀態碼為 408、429 和 5XX) 和任何連線例外狀況的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md#retry-policies)。 | 
| <*查詢參數*> | JSON 物件 | 要包含在 API 呼叫中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2018-01-01" }` 物件會將 `?api-version=2018-01-01` 新增至呼叫。 | 
| <*其他特定於操作的屬性*> | JSON 物件 | 任何其他套用至這個特定動作的屬性 | 
|||| 

*範例*

此定義會說明 Office 365 Outlook 連接器 (此為 Microsoft 管理的 API) 的**傳送電子郵件**動作： 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>APIConnectionWebhook 動作

此動作會使用 [Microsoft 管理的 API](../connectors/apis-list.md) 透過 HTTP 將訂閱要求傳送至端點、提供可讓端點傳送回應的*回呼 URL*，並等候端點回應。 如需詳細資訊，請參閱[端點訂閱](#subscribe-unsubscribe)。

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

某些值 (例如 <*method-type*>) 同時適用於 `"subscribe"` 和 `"unsubscribe"` 物件。

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*動作名稱*> | String | 連接器所提供的動作名稱 | 
| <*方法類型*> | String | 用來訂閱或取消訂閱端點的 HTTP 方法："GET"、"PUT"、"POST"、"PATCH" 或 "DELETE" | 
| <*api 訂閱網址*> | String | 用來訂閱 API 的 URI | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*api 取消訂閱網址*> | String | 用來取消訂閱 API 的 URI | 
| <*標題內容*> | JSON 物件 | 要在要求中傳送的任何標頭 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*身體內容*> | JSON 物件 | 要在要求中傳送的任何訊息內容 | 
| <*驗證類型*> | JSON 物件 | 請求用於驗證出站請求的身份驗證模型。 有關詳細資訊,請參閱[將身份驗證加入到出站呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。 |
| <*重試行為*> | JSON 物件 | 自訂間歇性失敗 (狀態碼為 408、429 和 5XX) 和任何連線例外狀況的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md#retry-policies)。 | 
| <*查詢參數*> | JSON 物件 | 要包含在 API 呼叫中的任何查詢參數 <p>例如，`"queries": { "api-version": "2018-01-01" }` 物件會將 `?api-version=2018-01-01` 新增至呼叫。 | 
| <*其他特定於操作的輸入屬性*> | JSON 物件 | 任何其他套用至這個特定動作的輸入屬性 | 
| <*其他特定於操作的屬性*> | JSON 物件 | 任何其他套用至這個特定動作的屬性 | 
|||| 

您也可以採用與 [HTTP 非同步限制](#asynchronous-limits)相同的方式，指定對 **ApiConnectionWebhook** 動作的限制。

<a name="compose-action"></a>

### <a name="compose-action"></a>撰寫動作

此動作會從多個輸入 (包括運算式) 建立單一輸出。 輸出和輸入都具有 Azure Logic Apps 原生支援的任何類型，例如陣列、JSON 物件、XML 和二進位檔。 其後，您可以在其他動作中使用該動作的輸出。 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*必要* 

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*輸入到群組*> | 任意 | 用來建立單一輸出的輸入 | 
|||| 

*範例 1*

<!-- markdownlint-disable MD038 -->
此動作定義會合併 `abcdefg ` 與尾端空格和值 `1234`：
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

以下是此動作建立的輸出：

`abcdefg 1234`

*範例 2*

此動作定義會合併包含 `abcdefg` 的字串變數與包含 `1234` 的整數變數：

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

以下是此動作建立的輸出：

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>執行 JavaScript 程式碼操作

此操作運行 JavaScript 代碼段,並通過以後操作`Result`可以引用的權杖返回結果。

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*必要*

| 值 | 類型 | 描述 |
|-------|------|-------------|
| <*JavaScript-程式碼段*> | 不定 | 要運行的 JavaScript 代碼。 關於程式碼要求與詳細資訊,請參閱[使用內聯代碼新增和執行程式碼段](../logic-apps/logic-apps-add-run-inline-code.md)。 <p>在屬性`code`中,代碼段可以使用`workflowContext`唯讀物件作為輸入。 此物件具有子屬性,使代碼可以訪問工作流中觸發器和以前操作的結果。 有關`workflowContext`物件的詳細資訊,請參閱[程式碼中的參考觸發器和操作結果](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext)。 |
||||

*在某些情況下需要*

該`explicitDependencies`屬性指定要顯式包括觸發器、以前操作或兩者的結果,作為代碼段的依賴項。 有關新增這些相依項的詳細資訊,請參閱[新增內聯代碼的參數](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters)。 

對於屬性`includeTrigger`,可以`true`指定`false`或值。

| 值 | 類型 | 描述 |
|-------|------|-------------|
| <*以前的操作*> | 字串陣列 | 具有指定操作名稱的陣列。 使用工作流定義中顯示的操作名稱,其中操作名稱使用下劃線 (*),而不是空格 ("")。 |
||||

*範例 1*

此操作運行獲取邏輯應用名稱的代碼,並返回文本"從\<邏輯應用名稱> Hello world"作為結果。 在此範例中,代碼通過唯`workflowContext.workflow.name``workflowContext`讀對象訪問屬性來引用工作流的名稱。 有關使用物件的詳細資訊,`workflowContext`請參考[觸發器與操作結果](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext)。

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*範例 2*

此操作在邏輯應用中運行代碼,當新電子郵件到達 Office 365 Outlook 帳戶時觸發。 邏輯應用還使用發送審批電子郵件操作,該操作轉發來自已接收電子郵件的內容以及批准請求。

代碼從觸發器`Body`的屬性中提取電子郵件位址,並從審批操作中返回位址`SelectedOption`以及 屬性值。 該操作明確包括作為`explicitDependencies` > `actions`屬性中的依賴項發送審批電子郵件操作。

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>函式動作

此動作會呼叫先前建立的 [Azure 函式](../azure-functions/functions-create-first-azure-function.md)。

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------|  
| <*Azure 函數 ID*> | String | 您想要呼叫的 Azure 函式所具備的資源識別碼。 此值得格式如下：<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*方法類型*> | String | 用來呼叫函式的 HTTP 方法："GET"、"PUT"、"POST"、"PATCH" 或 "DELETE" <p>若未指定，預設方法將是 "POST"。 | 
||||

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------|  
| <*標題內容*> | JSON 物件 | 要透過呼叫傳送的任何標頭 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*身體內容*> | JSON 物件 | 要在要求中傳送的任何訊息內容 | 
| <*查詢參數*> | JSON 物件 | 要包含在 API 呼叫中的任何查詢參數 <p>例如，`"queries": { "api-version": "2018-01-01" }` 物件會將 `?api-version=2018-01-01` 新增至呼叫。 | 
| <*其他特定於操作的輸入屬性*> | JSON 物件 | 任何其他套用至這個特定動作的輸入屬性 | 
| <*其他特定於操作的屬性*> | JSON 物件 | 任何其他套用至這個特定動作的屬性 | 
||||

當您儲存邏輯應用程式時，Logic Apps 引擎會對所參考的函式執行下列檢查︰

* 您的工作流程必須能夠存取該函式。

* 您的工作流程只能使用標準 HTTP 觸發程序或一般 JSON Webhook 觸發程序。 

  Logic Apps 引擎會取得及快取在執行階段上使用的觸發程序 URL。 不過，若有任何作業讓快取的 URL 失效，**函式**動作就會在執行階段失敗。 若要解決此問題，請再次儲存邏輯應用程式，讓邏輯應用程式再次取得及快取觸發程序 URL。

* 函式不可以有任何已定義的路由。

* 只允許使用「函式」和「匿名」授權層級。 

*範例*

此動作定義會呼叫先前建立的 "GetProductID" 函式：

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>HTTP 動作

此操作向指定的 HTTP 或 HTTPS 終結點發送請求,並檢查回應以確定工作流是否運行。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*必要*

| 屬性 | 值 | 類型 | 描述 |
|----------|-------|------|-------------|
| `method` | <*方法類型*> | String | 發送傳出請求的方法:"GET"、"PUT"、"開波"、"PATCH"或"刪除" |
| `uri` | <*HTTP 或 HTTPS-終結點-URL*> | String | 要送出傳出請求的 HTTP 或 HTTPS 終結點網址。 字串大小上限：2 KB <p>對於 Azure 服務或資源,此 URI 語法包括資源 ID 和要訪問的資源的路徑。 |
|||||

*選用*

| 屬性 | 值 | 類型 | 描述 |
|----------|-------|------|-------------|
| `headers` | <*標題內容*> | JSON 物件 | 需要隨要求包含的任何標頭 <p>例如，若要設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*查詢參數*> | JSON 物件 | 需要在要求中使用的任何查詢參數 <p>例如，`"queries": { "api-version": "2018-01-01" }` 物件會將 `?api-version=2018-01-01` 新增至呼叫。 |
| `body` | <*身體內容*> | JSON 物件 | 要以承載的形式透過要求傳送的訊息內容 |
| `authentication` | <*認證類型與屬性值*> | JSON 物件 | 請求用於驗證出站請求的身份驗證模型。 有關詳細資訊,請參閱[將身份驗證加入到出站呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。 除了排程器之外，還可支援 `authority` 屬性。 如果未指定,預設值為`https://management.azure.com/`,但您可以使用其他值。 |
| `retryPolicy` > `type` | <*重試行為*> | JSON 物件 | 自訂間歇性失敗 (狀態碼為 408、429 和 5XX) 和任何連線例外狀況的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md#retry-policies)。 |
| <*其他特定於操作的輸入屬性*> | <*輸入屬性*> | JSON 物件 | 任何其他套用至這個特定動作的輸入屬性 |
| <*其他特定於操作的屬性*> | <*屬性值*> | JSON 物件 | 任何其他套用至這個特定動作的屬性 |
|||||

*範例*

此動作定義會將要求傳送至指定的端點，以取得最新消息：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>聯結動作

此動作會從陣列中的所有項目建立字串，並使用指定的分隔字元來分隔這些項目。 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*陣列*> | Array | 提供來源項目的陣列或運算式。 如果您指定運算式，請以雙引號括住該運算式。 | 
| <*分隔符*> | 單一字元字串 | 分隔字串中各個項目的字元 | 
|||| 

*範例*

假設您有先前建立的 "myIntegerArray" 變數，且其中包含下列整數陣列： 

`[1,2,3,4]` 

此動作定義會在運算式中使用 `variables()` 函式以取得變數中的值，並使用這些值建立此字串 (以逗號分隔)：`"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>剖析 JSON 動作

此動作會從 JSON 內容中的屬性建立易記的欄位或*權杖*。 其後，您可以在邏輯應用程式中改用權杖，以存取這些屬性。 例如，如果您想要使用 Azure 服務匯流排和 Azure Cosmos DB 等服務的 JSON 輸出，您可以在邏輯應用程式中納入此動作，這樣您就可以更輕鬆地參考該輸出中的資料。

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*JSON 源*> | JSON 物件 | 您想要剖析的 JSON 內容 | 
| <*JSON 架構*> | JSON 物件 | 說明基礎 JSON 內容的 JSON 結構描述，動作會用它來剖析來源 JSON 內容。 <p>**提示**：在 Logic Apps 設計工具中，您可以提供此結構描述，或提供範例承載讓動作得以產生結構描述。 | 
|||| 

*範例*

此操作定義建立可在工作流中使用,但只能在按照**Parse JSON**操作執行的操作中使用這些權杖:

`FirstName`、`LastName` 和 `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

在此範例中，"content" 屬性會指定動作要剖析的 JSON 內容。 您也可以提供此 JSON 內容作為用來產生結構描述的範例承載。

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

"schema" 屬性會指定用來說明 JSON 內容的 JSON 結構描述：

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>查詢動作

此動作會根據指定的條件或篩選條件，從另一個陣列中的項目建立陣列。

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*陣列*> | Array | 提供來源項目的陣列或運算式。 如果您指定運算式，請以雙引號括住該運算式。 |
| <*條件或篩選器*> | String | 用來對來源陣列中的項目進行篩選的條件 <p>**注意**：如果沒有符合條件的值，則動作會建立空陣列。 |
|||| 

*範例*

此動作定義會建立值大於指定值 (2) 的陣列：

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>回應動作  

此動作會建立 HTTP 要求的回應承載。 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*回應狀態代碼*> | 整數 | 要傳送至連入要求的 HTTP 狀態碼。 預設代碼為「200 確定」，但代碼可以是任何以 2xx、4xx 或 5xx (不含 3xxx) 開頭的有效狀態碼。 | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*回應標頭*> | JSON 物件 | 要隨附於回應的一個或多個標頭 | 
| <*回應體*> | 各種類型 | 回應本文，可以是字串、JSON 物件，甚至是上一節中的二進位內容 | 
|||| 

*範例*

此動作定義會使用指定的狀態碼、訊息本文和訊息標頭建立 HTTP 要求的回應：

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*限制*

不同於其他動作，**回應**動作有其特殊限制： 

* 只有在工作流程以 HTTP 要求觸發程序啟動時，您的工作流程才可以使用**回應**動作，這表示您的工作流程必須由 HTTP 要求觸發。

* 您的工作流程可以在 **Foreach** 迴圈、**Until** 迴圈 (包括循序迴圈) 和平行分支*以外*的任何位置使用**回應**動作。 

* 只有在**回應**動作所需的所有動作都已在 [HTTP 要求逾時限制](../logic-apps/logic-apps-limits-and-config.md#request-limits)內完成時，原始的 HTTP 要求才會取得工作流程的回應。

  不過，如果您的工作流程呼叫另一個邏輯應用程式作為巢狀工作流程，父工作流程將會等待巢狀工作流程完成，無論在巢狀工作流程完成之前經過了多少時間。

* 當您的工作流程使用**回應**動作和同步回應模式時，工作流程不可同時在觸發程序定義中使用 **splitOn** 命令，因為該命令會建立多個執行。 在使用 PUT 方法時，請檢查是否有此情況，如果有，請傳回「不正確的要求」回應。

  否則，如果您的工作流程使用 **splitOn** 命令和**回應**動作，工作流程將會以非同步方式執行，並立即傳回「202 已接受」回應。

* 如果您的工作流程執行觸及**回應**動作，但連入要求已接收回到應，**回應**動作會因為衝突而標示為「失敗」。 因此，您的邏輯應用程式執行也會標示為「失敗」狀態。

<a name="select-action"></a>

### <a name="select-action"></a>選取動作

此動作會根據指定的對應從另一個陣列中轉換項目，以使用 JSON 物件建立陣列。 來源陣列與輸出陣列一律有相同數目的項目。 雖然您無法變更輸出陣列中的物件數目，但您可以為這些物件新增或移除屬性及其值。 `select` 屬性會指定至少一個索引鍵/值組，用以定義轉換來源陣列中的項目時的對應。 索引鍵/值組代表輸出陣列中所有物件的屬性及其值。

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*必要* 

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*陣列*> | Array | 提供來源項目的陣列或運算式。 請確實以雙引號括住運算式。 <p>**注意**：如果來源陣列是空的，動作將會建立空陣列。 | 
| <*鍵名*> | String | 配置給<*表示式*結果的屬性名稱> <p>若要為輸出陣列中的所有物件新增屬性，請為該屬性提供 <*key-name*>，並提供 <*expression*> 作為屬性值。 <p>若要移除陣列中所有物件的屬性，請省略該屬性的 <*key-name*>。 | 
| <*表達*> | String | 轉換來源陣列的項目並將結果分配給<*鍵名稱的*運算式> | 
|||| 

**選取**動作會建立陣列作為輸出，因此需要使用此輸出的任何動作都必須接受陣列，或者您必須將陣列轉換為取用者動作可接受的類型。 例如，若要將輸出陣列轉換為字串，您可以將該陣列傳至**撰寫**動作，然後在您其他動作中參考**撰寫**動作的輸出。

*範例*

此動作定義會從整數陣列建立 JSON 物件陣列。 此動作會逐一查看來源陣列、使用 `@item()` 運算式取得每個整數值，並將每個值指派給各個 JSON 物件中的 "`number`" 屬性：

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

以下是此動作建立的陣列：

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

若要在其他動作中使用此陣列輸出，請將此輸出傳至**撰寫**動作：

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

然後，您即可在其他的動作中使用**撰寫**動作的輸出，例如，**Office 365 Outlook - 傳送電子郵件**動作：

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>資料表動作

此動作會從陣列建立 CSV 或 HTML 資料表。 對於含有 JSON 物件的陣列，此動作會從物件的屬性名稱自動建立資料行標頭。 對於含有其他資料類型的陣列，您必須指定資料行標頭和值。 例如，下列陣列包含此動作可用於資料行標頭名稱的 "ID" 和 "Product_Name" 屬性：

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*必要* 

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| \<CSV*或*HTML>| String | 您想要建立的資料表格式 | 
| <*陣列*> | Array | 提供資料表來源項目的陣列或運算式 <p>**注意**：如果來源陣列是空的，動作將會建立空資料表。 | 
|||| 

*選用*

若要指定或自訂資料行標頭和值，請使用 `columns` 陣列。 當 `header-value` 配對具有相同的標頭名稱時，其值將會出現在具有該標頭名稱的相同資料行中。 否則，每個唯一的標頭分別會定義唯一的資料行。

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*欄名稱*> | String | 資料行的標頭名稱 | 
| <*欄值*> | 任意 | 該資料行中的值 | 
|||| 

*範例 1*

假設您有先前建立的 "myItemArray" 變數，且目前包含下列陣列：

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

此動作定義會從 "myItemArray" 變數建立 CSV 資料表。 `from` 屬性所使用的運算式會透過 `variables()` 函式從 "myItemArray" 取得陣列：

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

以下是此動作建立的 CSV 資料表： 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*範例 2*

此動作定義會從 "myItemArray" 變數建立 HTML 資料表。 `from` 屬性所使用的運算式會透過 `variables()` 函式從 "myItemArray" 取得陣列：

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

以下是此動作建立的 HTML 資料表： 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>蘋果</td></tr><tr><td>1</td><td>橘子</td></tr></tbody></table>

*範例 3*

此動作定義會從 "myItemArray" 變數建立 HTML 資料表。 不過，此範例會以 "Stock_ID" 和 [描述] 覆寫預設資料行標頭名稱，並將「有機」一詞新增至 [描述] 資料行中的值。

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

以下是此動作建立的 HTML 資料表： 

<table><thead><tr><th>Stock_ID</th><th>描述</th></tr></thead><tbody><tr><td>0</td><td>有機蘋果</td></tr><tr><td>1</td><td>有機橘子</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>終止動作

此操作停止工作流實例的運行,取消正在進行的任何操作,跳過任何剩餘操作,並返回指定的狀態。 例如，您可以在邏輯應用程式因錯誤狀態而必須完全結束時，使用**終止**動作。 此動作不會影響已完成的動作，且不可出現在 **Foreach** 和 **Until** 迴圈 (包括循序迴圈) 內。

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*地位*> | String | 要傳回的執行狀態：「失敗」、「已取消」或「成功」 |
|||| 

*選用*

只有在 "runStatus" 屬性設定為 [失敗] 狀態時，才適用 "runStatus" 物件的屬性。

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*錯誤代碼或名稱*> | String | 錯誤的代碼或名稱 |
| <*錯誤訊息*> | String | 說明錯誤和應用程式使用者可行動作的訊息或文字 | 
|||| 

*範例*

此動作定義會停止執行工作流程、將執行狀態設定為 [失敗]，並傳回的狀態、錯誤碼和錯誤訊息：

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>等候動作

此動作會在一段指定間隔內暫停工作流程的執行，或暫停到指定的時間為止 (並非同時滿足二個條件)。

*指定的間隔*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*指定的時間*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*單位數*> | 整數 | 針對**延遲**動作要等候的單位數 | 
| <*區間*> | String | 針對**延遲**動作要等候的間隔：[秒]、[分]、[小時]、[天]、[週]、[月] | 
| <*日期時間戳*> | String | 針對**延遲到**動作，要繼續執行的日期和時間。 此值必須使用 [UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)。 | 
|||| 

*範例 1*

此動作定義會暫停工作流程 15 分鐘：

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*範例 2*

此動作定義會暫停工作流程，直到指定的時間到達：

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>工作流程動作

此動作會呼叫先前建立的另一個邏輯應用程式，這表示您可以包含並重複使用其他的邏輯應用程式工作流程。 您也可以在巢狀邏輯應用程式之後的動作中使用子系或*巢狀*邏輯應用程式的輸出，只要子系邏輯應用程式傳回回應即可。

Logic Apps 引擎會檢查是否可存取您想要呼叫的觸發程序，因此請確定您可以存取該觸發程序。 此外，巢狀邏輯應用程式必須符合下列準則：

* 觸發程序會使巢狀邏輯應用程式可供呼叫，例如[要求](#request-trigger)或 [HTTP](#http-trigger) 觸發程序

* 與父邏輯應用程式相同的 Azure 訂用帳戶

* 若要在父邏輯應用程式中使用巢狀邏輯應用程式的輸出，巢狀邏輯應用程式必須具有[回應](#response-action)動作

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*巢狀邏輯套用名稱*> | String | 您要呼叫的邏輯應用程式所具備的名稱 | 
| <*觸發器名稱*> | String | 您要呼叫的巢狀邏輯應用程式中包含的觸發程序名稱 | 
| <*Azure 訂閱 ID*> | String | 巢狀邏輯應用程式的 Azure 訂用帳戶識別碼 |
| <*Azure 資源群組*> | String | 巢狀邏輯應用程式的 Azure 資源群組名稱 |
| <*巢狀邏輯套用名稱*> | String | 您要呼叫的邏輯應用程式所具備的名稱 |
||||

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------|  
| <*標題內容*> | JSON 物件 | 要透過呼叫傳送的任何標頭 | 
| <*身體內容*> | JSON 物件 | 要透過呼叫傳送的任何訊息內容 | 
||||

*輸出*

此動作的輸出會隨著巢狀邏輯應用程式的回應動作而有所不同。 如果巢狀邏輯應用程式未包含回應動作，輸出會是空的。

*範例*

在 "Start_search" 動作順利完成後，此工作流程動作定義會呼叫名為 "Get_product_information" 的另一個邏輯應用程式，而傳入指定的輸入：

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>控制工作流程動作詳細資料

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach 動作

此迴圈動作會逐一查看陣列，並對每個陣列項目執行動作。 根據預設，"for each" 迴圈會依據迴圈數目上限以平行方式執行。 對此最大值,請參考[限制與設定](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。[瞭解如何建立每個' 迴圈](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)。

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*必要* 

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*動作-1...n*> | String | 對每個陣列項目執行的動作名稱 | 
| <*動作定義-1...n*> | JSON 物件 | 所執行動作的定義 | 
| <*每個運算式*> | String | 參考指定陣列中各個項目的運算式 | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*計數*> | 整數 | 預設情況下,「對於每個」迴圈反覆運算同時執行(併發或並行),最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 若要藉由設定新的 <*count*> 值來變更此限制，請參閱[變更 "for each" 迴圈並行](#change-for-each-concurrency)。 | 
| <*操作選項*> | String | 若要以循序方式 (而非以平行方式) 執行 "for each" 迴圈，請將 <*operation-option*> 設定為 `Sequential` 或將 <*count*> 設定為 `1`，但勿同時設定兩者。 如需詳細資訊，請參閱[循序執行 "for each" 迴圈](#sequential-for-each)。 | 
|||| 

*範例*

此 "for each" 迴圈會為陣列中的每個項目傳送電子郵件，其中包含內送電子郵件的附件。 此迴圈會將內含附件的電子郵件傳送給檢閱附件的人員。

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

若要指定僅傳入一個陣列作為觸發程序的輸出，此運算式會從觸發程序主體取得 <*array-name*> 陣列。 為避免在此陣列不存在時導致作業失敗，運算式使用了 `?` 運算子：

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>If 動作

此動作是*條件陳述式*，可用來評估代表條件的運算式，並根據是否符合條件來執行不同的分支。 如果符合條件，此條件就會標示為「成功」狀態。 了解[如何建立條件陳述式](../logic-apps/logic-apps-control-flow-conditional-statement.md)。

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*條件*> | JSON 物件 | 要評估的條件，可以是運算式 | 
| <*操作-1*> | JSON 物件 | <*condition*> 評估為 true 時所要執行的動作 | 
| <*動作定義*> | JSON 物件 | 動作的定義 | 
| <*動作-2*> | JSON 物件 | <*condition*> 評估為 false 時所要執行的動作 | 
|||| 

`actions` 或 `else` 物件中的動作會獲得下列狀態：

* 「Succeeded (成功)」：當動作執行且成功時
* 「Failed (失敗)」：當動作執行但失敗時
* 「Skipped (略過)」：當個別的分支並未實行時

*範例*

此條件會指定當整數變數的值大於零時，工作流程將會檢查網站。 如果變數小於或等於零，則工作流程會檢查不同的網站。

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>條件使用運算式的方式

以下是說明如何在條件中使用運算式的範例︰
  
| JSON | 結果 | 
|------|--------| 
| "expression": "@parameters('<*hasSpecialAction*>')" | (僅限布林運算式) 條件會略過任何評估為 true 的值。 <p>若要將其他類型轉換成布林值，請使用函式：`empty()` 或 `equals()`。 | 
| "expression": "@greater(actions('<*action*>').output.value, parameters('<*threshold*>'))" | 使用比較函式時，只有在 <*action*> 的輸出大於 <*threshold*> 值時，才會執行動作。 | 
| "expression": "@or(greater(actions('<*action*>').output.value, parameters('<*threshold*>')), less(actions('<*same-action*>').output.value, 100))" | 使用邏輯函式和建立巢狀布林運算式時，若 <*action*> 的輸出大於 <*threshold*> 值或低於 100，則會執行動作。 | 
| "expression": "@equals(length(actions('<*action*>').outputs.errors), 0))" | 您可以使用陣列函式來檢查陣列是否有任何項目。 當 `errors` 陣列空白時，就會執行動作。 | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>範圍動作

此動作會按邏輯將動作分成數個「範圍」**，而在該範圍中的動作執行完成後取得自己的狀態。 隨後，您可以使用範圍的狀態來判斷是否有其他動作在執行。 了解[如何建立範圍](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)。

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------|  
| <*內行動-1...n*> | JSON 物件 | 在範圍內執行的一或多個動作 |
| <*操作輸入*> | JSON 物件 | 每個動作的輸入 |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Switch 動作

此動作也稱為 *Switch 陳述式*，會將其他動作組織為*案例*，並將值指派給每個案例，但預設的案例除外 (如果有的話)。 當您的工作流程執行時，**Switch** 動作會將運算式、物件或權杖的值與每個案例的指定值做比較。 如果 **Switch** 動作找到相符的案例，您的工作流程就只會執行該案例的動作。 每當 **Switch** 動作執行時，都只會有一個相符的案例存在，或沒有相符案例存在。 如果沒有相符項目存在，**Switch** 動作會執行預設動作。 了解[如何建立 Switch 陳述式](../logic-apps/logic-apps-control-flow-switch-statement.md)。

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*必要*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*運算式-物件或權杖*> | 不定 | 要評估的運算式、JSON 物件或權杖 | 
| <*動作名稱*> | String | 要為相符案例執行的動作所具備的名稱 | 
| <*動作定義*> | JSON 物件 | 要為相符案例執行的動作所具備的定義 | 
| <*符合值*> | 不定 | 要與評估的結果比較的值 | 
|||| 

*選用*

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*預設操作名稱*> | String | 沒有相符案例存在時要執行的預設動作所具備的名稱 | 
| <*預設操作定義*> | JSON 物件 | 沒有相符案例存在時要執行的動作所具備的定義 | 
|||| 

*範例*

此動作定義會評估核准要求電子郵件的回應者是選取了 [核准] 選項還是 [拒絕] 選項。 根據此選擇，**Switch** 動作會執行相符案例的動作，也就是將另一則電子郵件傳送給回應者，但在每個案例中使用不同的用詞。 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>直到動作

此迴圈動作包含會持續執行直到符合指定條件為止的動作。 迴圈會在所有其他動作都執行完成後，才在最後一個步驟檢查條件。 您可以在 `"actions"` 物件中包含多個動作，而動作至少須定義一項限制。 了解[如何建立 "until" 迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)。 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| 值 | 類型 | 描述 | 
|-------|------|-------------| 
| <*動作名稱*> | String | 您要在迴圈內執行的動作名稱 | 
| <*動作類型*> | String | 您要執行的動作類型 | 
| <*操作輸入*> | 各種類型 | 要執行的動作所使用的輸入 | 
| <*條件*> | String | 在迴圈中的所有動作都執行完成後所要評估的條件或運算式 | 
| <*循環計數*> | 整數 | 動作可以執行的迴圈數目上限。 有關預設限制和最大限制的詳細資訊,請參閱 Azure[邏輯應用的限制和設定](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 | 
| <*循環逾時*> | String | 迴圈可執行的時間長度上限。 預設 `timeout` 值為 `PT1H`，這是必要的 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)。 |
|||| 

*範例*

此迴圈動作定義會持續將 HTTP 要求傳送至指定的 URL，直到符合下列條件之一為止：

* 要求取得狀態碼為「200 確定」的回應。
* 迴圈已執行 60 次。
* 迴圈執行已達一小時。

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhook 和訂閱

以 Webhook 為基礎的觸發程序和動作不會定期檢查端點，而是會等待這些端點的特定事件或資料。 這些觸發程序和動作會藉由提供可讓端點傳送回應的*回呼 URL*，來*訂閱*端點。

當工作流程以任何方式發生變更時 (例如，當認證更新，或觸發程序或動作的輸入參數變更時)，就會進行 `subscribe` 呼叫。 此呼叫使用與標準 HTTP 動作相同的參數。 

當作業使觸發程序或動作失效時，就會自動進行 `unsubscribe` 呼叫，例如：

* 刪除或停用觸發程序。 
* 刪除或停用工作流程。 
* 刪除或停用訂用帳戶。 

為了支援這些呼叫，`@listCallbackUrl()` 運算式會為觸發程序或動作傳回唯一的「回呼 URL」。 此 URL 代表使用服務 REST API 之端點的唯一識別碼。 此函式的參數與 Webhook 觸發程序或動作的參數相同。

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>變更非同步持續時間

對於觸發程序和動作，您都可以藉由新增 `limit.timeout` 屬性，將非同步模式的持續時間限定於特定的時間間隔。 如此，若動作在間隔結束後仍未完成，動作的狀態就會標示為 `Cancelled`，且附有 `ActionTimedOut` 代碼。 `timeout` 屬性會使用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)。

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>執行階段組態設定

通過將這些`runtimeConfiguration`屬性添加到觸發器或操作定義,可以更改觸發器和操作的預設運行時行為。

| 屬性 | 類型 | 描述 | 觸發程序或動作 | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | 整數 | 變更可同時執行(並發或並行)的工作串流實體的[*預設限制*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 調整此值有助於限制後端系統接收的請求數。 <p>將 `runs` 屬性設定為 `1` 的效用，與將 `operationOptions` 屬性設定為 `SingleInstance` 相同。 您可以設定任一屬性，但勿同時設定。 <p>若要變更預設限制，請參閱[變更觸發程序並行](#change-trigger-concurrency)或[循序觸發執行個體](#sequential-trigger)。 | 所有觸發程序 | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | 整數 | 變更邏輯應用已執行最大並發實體時必須等待執行的工作串流實體的[*預設限制*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 <p>若要變更預設限制，請參閱[變更等候執行限制](#change-waiting-runs)。 | 所有觸發程序 | 
| `runtimeConfiguration.concurrency.repetitions` | 整數 | 變更可同時執行的「每個」 循環的發運算次數(並發或並行)的[*預設限制*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 <p>將 `repetitions` 屬性設定為 `1` 的效用，與將 `operationOptions` 屬性設定為 `SingleInstance` 相同。 您可以設定任一屬性，但勿同時設定。 <p>若要變更預設限制，請參閱[變更 "for each" 並行](#change-for-each-concurrency)或[循序執行 "for each" 迴圈](#sequential-for-each)。 | 動作： <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | 整數 | 對於支援並啟用分形的特定操作,此值指定要檢索的*最小*結果數。 <p>要開啟分頁,請參閱[使用分頁抓取批次資料、專案或結果](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | 動作:變化 |
| `runtimeConfiguration.secureData.properties` | Array | 在許多觸發器和操作上,這些設置會隱藏邏輯應用的運行歷史記錄中的輸入、輸出或兩者。 <p>要瞭解有關保護此資料的更多資訊,請參閱[隱藏執行歷程記錄的輸入與輸出](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view)。 | 大多數觸發器和操作 |
| `runtimeConfiguration.staticResult` | JSON 物件 | 對於支援並啟用[靜態結果](../logic-apps/test-logic-apps-mock-data-static-results.md)設定的`staticResult`操作, 物件具有以下屬性: <p>- `name`引用當前操作的靜態結果定義名稱,該名稱顯示在邏輯應用工作流`staticResults``definition`的屬性中的屬性內。 有關詳細資訊,請參閱[靜態結果 - 工作流定義語言的架構參考](../logic-apps/logic-apps-workflow-definition-language.md#static-results)。 <p> - `staticResultOptions`,它指定靜態結果是否用於`Enabled`當前操作。 <p>要開啟靜態結果,[請參考您設定靜態結果使用模擬資料測試邏輯應用](../logic-apps/test-logic-apps-mock-data-static-results.md) | 動作:變化 |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>作業選項

您可以使用觸發程序或動作定義中的 `operationOptions` 屬性，變更觸發程序和動作的預設行為。

| 作業選項 | 類型 | 描述 | 觸發程序或動作 | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | String | 同步執行 HTTP 型動作，而不是非同步執行。 <p><p>若要設定此選項，請參閱[以同步方式執行動作](#asynchronous-patterns)。 | 動作： <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[回應](#response-action) | 
| `OptimizedForHighThroughput` | String | 將每 5 分鐘的動作執行數目[預設限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)變更為[數目上限](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 <p><p>若要設定此選項，請參閱[在高輸送量模式中執行](#run-high-throughput-mode)。 | 所有動作 | 
| `Sequential` | String | 逐一執行 "for each" 迴圈反覆項目，而非一併以平行方式同時執行。 <p>此選項的效用與將 `runtimeConfiguration.concurrency.repetitions` 屬性設定為 `1` 相同。 您可以設定任一屬性，但勿同時設定。 <p><p>若要設定此選項，請參閱[循序執行 "for each" 迴圈](#sequential-for-each)。| 動作： <p>[Foreach](#foreach-action) | 
| `SingleInstance` | String | 循序為每個邏輯應用程式執行個體執行觸發程序，並等候先前作用中的執行完成後，再觸發下一個邏輯應用程式執行個體。 <p><p>此選項的效用與將 `runtimeConfiguration.concurrency.runs` 屬性設定為 `1` 相同。 您可以設定任一屬性，但勿同時設定。 <p>若要設定此選項，請參閱[循序觸發執行個體](#sequential-trigger)。 | 所有觸發程序 | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>變更觸發程序並行

默認情況下,邏輯應用工作流實例都同時運行(併發或並行)。 此行為意味著每個觸發器實例在以前處於活動狀態的工作流實例完成運行之前觸發。 但是,併發執行實體的數量具有[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 當併發運行的工作流實例數達到此限制時,任何其他新實例都必須等待運行。 此限制有助於控制後端系統接收的要求數目。

當您開啟觸發器的併發控制時,觸發器實體並行執行,最多達到[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 要更改此預設併發限制,可以使用代碼檢視編輯器或邏輯應用設計器,因為透過設計器更改併發設置會添加或更新基礎觸發器定義`runtimeConfiguration.concurrency.runs`中的屬性,反之亦然。 此屬性控制可並行運行的新工作流實例的最大數量。

如果要在觸發器上啟用併發,需要考慮以下事項:

* 啟用並發時,[辯論陣列](#split-on-debatch)的[SplitOn 限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)將顯著減小。 如果項目數超過此限制,則禁用 SplitOn 功能。

* 啟用併發控制后,無法禁用併發。

* 啟用並發時,[辯論陣列](#split-on-debatch)的[SplitOn 限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)將顯著減小。 如果項目數超過此限制,則禁用 SplitOn 功能。

* 啟用併發后,長時間運行的邏輯應用實例可能會導致新的邏輯應用實例進入等待狀態。 此狀態阻止 Azure 邏輯應用創建新實例,即使併發運行次數小於指定的最大併發運行數也是如此。

  * 要中斷此狀態,請取消*仍在運行*的最早實例。

    1. 在邏輯應用的功能表上,選擇 **「概述**」。。

    1. 在 **「執行歷史紀錄」** 部分中,選擇仍在運行的最早實例,例如:

       ![選擇最早的執行的實體](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > 要僅查看仍在運行的實例,請打開 **「全部」** 清單,然後選擇「**正在執行**」。

    1. 在**邏輯應用運行**下,選擇 **"取消運行**"。

       ![尋找最早的執行的實體](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * 要解決這種可能性,向任何可能阻止這些運行的操作添加超時。 如果您在程式碼編輯器中工作,請參閱[變更非同步持續時間](#asynchronous-limits)。 否則,如果您使用的是設計器,請按照以下步驟操作:

    1. 在邏輯應用中,在要添加超時的操作上,在右上角選擇橢圓 **(...)** 按鈕,然後選擇 **"設置**"。

       ![開啟動作設定](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. 在**超時**下,以[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)指定超時持續時間。

       ![指定逾時持續時間](./media/logic-apps-workflow-actions-triggers/timeout.png)

* 要按順序運行邏輯應用,請使用代碼檢視編輯器或設計器將觸發器`1`的併發設置為。 請確保不要在代碼檢視編輯器`operationOptions``SingleInstance`中將觸發器的屬性設置為。 否則，將會發生驗證錯誤。 如需詳細資訊，請參閱[循序觸發執行個體](#sequential-trigger)。

#### <a name="edit-in-code-view"></a>在程式碼檢視中編輯 

在基礎觸發器定義中,新增`runtimeConfiguration.concurrency.runs`屬性,該屬性的值可以從`1`範圍到`50`。

以下是將並行執行的執行個體限制為 10 個的範例：

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

如需詳細資訊，請參閱[執行階段組態設定](#runtime-config-options)。

#### <a name="edit-in-logic-apps-designer"></a>在 Logic Apps 設計工具中編輯

1. 在觸發器的右上角,選擇橢圓 **(...)** 按鈕,然後選擇 **「設置**」。

1. 在 [並行控制]**** 底下，將 [限制]**** 設定成 [開啟]****。 

1. 將 [平行處理原則的程度]**** 滑桿拖曳至您要的值。 若要循序執行邏輯應用程式，將滑桿值拖曳至 [1]****。

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>變更 "for each" 並行

默認情況下,「對於每個」迴圈反覆運算都同時運行(併發或並行)。 此行為意味著每次反覆運算在上一次反覆運算完成運行之前開始運行。 但是,併發運行反覆運算的數量具有[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 當併發運行反覆運算的數量達到此限制時,任何其他反覆運算都必須等待運行。

若要變更預設限制，您可以使用程式碼檢視編輯器或 Logic Apps 設計工具，因為透過此設計工具變更並行設定，會新增或更新基礎 "for each" 動作定義中的 `runtimeConfiguration.concurrency.repetitions` 屬性，反之亦然。 此屬性可控制能夠以平行方式執行的反覆項目數上限。

> [!NOTE] 
> 如果您使用設計工具或程式碼檢視編輯器將 "for each" 動作設定為循序執行，請勿在程式碼檢視編輯器中將此動作的 `operationOptions` 屬性設定為 `Sequential`。 否則，將會發生驗證錯誤。 如需詳細資訊，請參閱[循序執行 "for each" 迴圈](#sequential-for-each)。

#### <a name="edit-in-code-view"></a>在程式碼檢視中編輯 

在基礎「對於每個」定義中,添加或更新`runtimeConfiguration.concurrency.repetitions`屬性,該屬性可以具有介`1`於`50`和的值。

以下是將並行執行的反覆項目限制為 10 個的範例：

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

如需詳細資訊，請參閱[執行階段組態設定](#runtime-config-options)。

#### <a name="edit-in-logic-apps-designer"></a>在 Logic Apps 設計工具中編輯

1. 在「**對於每個**操作」中,從右上角選擇橢圓 **(...)** 按鈕,然後選擇 **「設置**」。

1. 在 [並行控制]**** 底下，將 [並行控制]**** 設定成 [開啟]****。

1. 將 [平行處理原則的程度]**** 滑桿拖曳至您要的值。 若要循序執行邏輯應用程式，將滑桿值拖曳至 [1]****。

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>變更等候執行數限制

默認情況下,邏輯應用工作流實例都同時運行(併發或並行)。 此行為意味著每個觸發器實例在以前處於活動狀態的工作流實例完成運行之前觸發。 但是,併發執行實體的數量具有[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 當併發運行的工作流實例數達到此限制時,任何其他新實例都必須等待運行。

等待執行的次數也有[預設限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 當等待運行數達到此限制時,邏輯應用引擎不再接受新運行。 要求和 Webhook 觸發程序會傳回 429 錯誤，且週期性觸發程序會開始略過輪詢嘗試。

您不僅可以[更改觸發器併發的預設限制](#change-trigger-concurrency),還可以更改等待運行的預設限制。 在基礎觸發器定義中,新增`runtimeConfiguration.concurrency.maximumWaitingRuns`屬性,該屬性的值可以從`1`範圍到`100`。

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

如需詳細資訊，請參閱[執行階段組態設定](#runtime-config-options)。

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>循序觸發執行個體

要僅在上一個實例完成運行後運行每個邏輯應用工作流實例,將觸發器設置為按順序運行。 您可以使用程式碼檢視編輯器或 Logic Apps 設計工具，因為透過此設計工具變更並行設定，也會新增或更新基礎觸發程序定義中的 `runtimeConfiguration.concurrency.runs` 屬性，反之亦然。

> [!NOTE] 
> 如果您使用設計工具或程式碼檢視編輯器將觸發程序設定為循序執行，請勿在程式碼檢視編輯器中將觸發程序的 `operationOptions` 屬性設定為 `Sequential`。 否則，將會發生驗證錯誤。 

#### <a name="edit-in-code-view"></a>在程式碼檢視中編輯

在觸發程序定義中設定其中一個屬性，但勿同時設定。 

將 `runtimeConfiguration.concurrency.runs` 屬性設定為 `1`：

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*-或-*

將 `operationOptions` 屬性設定為 `SingleInstance`：

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

關於詳細資訊,請參閱[執行時設定設定](#runtime-config-options)與[操作選項](#operation-options)。

#### <a name="edit-in-logic-apps-designer"></a>在 Logic Apps 設計工具中編輯

1. 在觸發器的右上角,選擇橢圓 **(...)** 按鈕,然後選擇 **「設置**」。

1. 在 [並行控制]**** 底下，將 [限制]**** 設定成 [開啟]****。 

1. 將 [平行處理原則的程度]**** 滑桿拖曳至數字 `1`。 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>循序執行 "for each" 迴圈

若要等到上一個反覆項目執行完成後才執行 "for each" 迴圈反覆項目，請將 "for each" 動作設定為循序執行。 您可以使用程式碼檢視編輯器或 Logic Apps 設計工具，因為透過此設計工具變更動作的並行，也會新增或更新基礎動作定義中的 `runtimeConfiguration.concurrency.repetitions` 屬性，反之亦然。

> [!NOTE] 
> 如果您使用設計工具或程式碼檢視編輯器將 "for each" 動作設定為循序執行，請勿在程式碼檢視編輯器中將此動作的 `operationOptions` 屬性設定為 `Sequential`。 否則，將會發生驗證錯誤。 

#### <a name="edit-in-code-view"></a>在程式碼檢視中編輯

在動作定義中設定其中一個屬性，但勿同時設定。 

將 `runtimeConfiguration.concurrency.repetitions` 屬性設定為 `1`：

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*-或-*

將 `operationOptions` 屬性設定為 `Sequential`：

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

關於詳細資訊,請參閱[執行時設定設定](#runtime-config-options)與[操作選項](#operation-options)。

#### <a name="edit-in-logic-apps-designer"></a>在 Logic Apps 設計工具中編輯

1. 在「**對於每個**操作的右上角」中,選擇橢圓 **(...)** 按鈕,然後選擇 **「設置**」。

1. 在 [並行控制]**** 底下，將 [並行控制]**** 設定成 [開啟]****。

1. 將 [平行處理原則的程度]**** 滑桿拖曳至數字 `1`。

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>同步執行動作

根據預設，所有 HTTP 型動作皆會依循標準的非同步作業模式。 此模式會指定當 HTTP 型動作傳送要求到指定的端點時，遠端伺服器會傳回「202 已接受」回應。 此回覆表示伺服器已接受要求並進行處理。 Logic Apps 引擎會持續檢查回應的位置標頭所指定的 URL，直到處理停止 (可從任何非 202 回應看出)。

不過，要求有其逾時限制，因此對於長時間執行的動作，您可以藉由在動作的輸入下方新增 `operationOptions` 屬性並將其設定為 `DisableAsyncPattern`，來停用非同步行為。

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

如需詳細資訊，請參閱[作業選項](#operation-options)。

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>在高輸送量模式中執行

對單一個邏輯應用定義,每 5 分鐘執行一次的操作數具有[預設限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 若要[盡可能提高](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)此限制，請將 `operationOptions` 屬性設定為 `OptimizedForHighThroughput`。 此設定會讓邏輯應用程式處於「高輸送量」模式。

> [!NOTE]
> 高輸送量模式為預覽狀態。 您也可以視需要將工作負載分散到多個邏輯應用程式。

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>驗證觸發器和操作

HTTP 和 HTTPS 終結點支援不同類型的身份驗證。 根據用於進行出站調用或訪問這些終結點的請求的觸發器或操作,您可以從不同的身份驗證類型範圍中進行選擇。 有關詳細資訊,請參閱[將身份驗證加入到出站呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

## <a name="next-steps"></a>後續步驟

* 深入了解[工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md)
