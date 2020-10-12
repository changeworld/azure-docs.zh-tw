---
title: Durable Functions 中的 HTTP Api-Azure Functions
description: 了解如何在 Azure Functions 的「長期函式」延伸模組中實作 HTTP API。
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84697838"
---
# <a name="http-api-reference"></a>HTTP API 參考

Durable Functions 擴充功能會公開一組內建的 HTTP Api，可用來執行協調 [流程](durable-functions-types-features-overview.md#orchestrator-functions)、 [實體](durable-functions-types-features-overview.md#entity-functions)和工作 [中樞](durable-functions-task-hubs.md)的管理工作。 這些 HTTP Api 是 Azure Functions 主控制項授權的擴充性 webhook，但 Durable Functions 擴充功能直接處理。

擴充功能所執行的所有 HTTP Api 都需要下列參數。 所有參數的資料類型是 `string`。

| 參數        | 參數類型  | 說明 |
|------------------|-----------------|-------------|
| **`taskHub`**    | 查詢字串    | [工作中樞](durable-functions-task-hubs.md)的名稱。 如果未指定，則會假設為目前函式應用程式的工作中樞名稱。 |
| **`connection`** | 查詢字串    | 儲存體帳戶之連接字串的**名稱**。 如果未指定，則會假設為函式應用程式的預設連接字串。 |
| **`systemKey`**  | 查詢字串    | 叫用 API 所需的授權金鑰。 |

`systemKey` 是 Azure Functions 主機自動產生的授權金鑰。 它特別授與「長期工作」延伸模組 API 的存取權，並且可以用與[其他授權金鑰](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)相同的方式來管理。 您可以 `taskHub` `connection` `systemKey` 使用 [協調流程用戶端](durable-functions-bindings.md#orchestration-client) 系結 api （例如 `CreateCheckStatusResponse` `CreateHttpManagementPayload` .net 中的和 api），或 `createCheckStatusResponse` `createHttpManagementPayload` JavaScript 中的和 api，產生包含正確、和查詢字串值的 url。

接下來的幾個章節會涵蓋延伸模組支援的特定 HTTP API，並且提供如何使用的範例。

## <a name="start-orchestration"></a>啟動協調流程

開始執行指定之協調器函式的新實例。

### <a name="request"></a>要求

在1.x 版的函式執行時間中，要求的格式如下 (會顯示多行以提供清楚的) ：

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

在2.x 版的函式執行時間中，URL 格式具有所有相同的參數，但有稍微不同的前置詞：

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位              | 參數類型  | 描述 |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | 要啟動的協調器函式名稱。 |
| **`instanceId`**   | URL             | 選擇性參數。 協調流程執行個體的識別碼。 如果未指定，協調器函式會以隨機實例識別碼作為開頭。 |
| **`{content}`**    | 要求內容 | 選擇性。 JSON 格式的協調器函數輸入。 |

### <a name="response"></a>回應

可以傳回幾個可能的狀態字碼值。

* **HTTP 202 (已接受) **：指定的協調器函式已排程為開始執行。 `Location`回應標頭包含用來輪詢協調流程狀態的 URL。
* **HTTP 400 (錯誤的要求) **：指定的協調器函數不存在、指定的實例識別碼無效，或要求內容不是有效的 JSON。

以下是範例要求，它會啟動協調器函式 `RestartVMs` 並包含 JSON 物件裝載：

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

**HTTP 202**案例的回應承載是具有下欄欄位的 JSON 物件：

| 欄位                       | 說明                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |協調流程執行個體的識別碼。 |
| **`statusQueryGetUri`**     |協調流程執行個體的狀態 URL。 |
| **`sendEventPostUri`**      |協調流程執行個體的「引發事件」URL。 |
| **`terminatePostUri`**      |協調流程執行個體的「終止」URL。 |
| **`purgeHistoryDeleteUri`** |協調流程實例的「清除歷程記錄」 URL。 |
| **`rewindPostUri`**         | (預覽) 協調流程實例的「倒轉」 URL。 |

所有欄位的資料類型是 `string`。

以下是協調流程實例的回應承載範例， `abc123` 其識別碼 (格式，以提供可讀性) ：

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

HTTP 回應的目標是要與 *輪詢取用者模式*相容。 它也包含下列值得注意的回應標頭：

* **位置**：狀態端點的 URL。 此 URL 包含與欄位相同的值 `statusQueryGetUri` 。
* **再試一次**：輪詢作業之間等候的秒數。 預設值為 `10`。

如需非同步 HTTP 輪詢模式的詳細資訊，請參閱 [HTTP 非同步作業追蹤](durable-functions-http-features.md#async-operation-tracking) 檔。

## <a name="get-instance-status"></a>取得執行個體狀態

取得指定協調流程執行個體的狀態。

### <a name="request"></a>要求

在1.x 版的函式執行時間中，要求的格式如下 (會顯示多行以提供清楚的) ：

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

在2.x 版的函式執行時間中，URL 格式具有所有相同的參數，但有稍微不同的前置詞：

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位                   | 參數類型  | 描述 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | 協調流程執行個體的識別碼。 |
| **`showInput`**         | 查詢字串    | 選擇性參數。 如果設定為 `false` ，則函數輸入將不會包含在回應承載中。|
| **`showHistory`**       | 查詢字串    | 選擇性參數。 如果設定為 `true`，則協調流程執行歷程記錄將會包含在回應承載中。|
| **`showHistoryOutput`** | 查詢字串    | 選擇性參數。 如果設定為 `true` ，則會將函數輸出包含在協調流程執行歷程記錄中。|
| **`createdTimeFrom`**   | 查詢字串    | 選擇性參數。 當指定時，會篩選在指定的 ISO8601 時間戳記或之後所建立之傳回實例的清單。|
| **`createdTimeTo`**     | 查詢字串    | 選擇性參數。 當指定時，會篩選在指定的 ISO8601 時間戳記或之前建立的傳回實例清單。|
| **`runtimeStatus`**     | 查詢字串    | 選擇性參數。 指定時，會根據所傳回執行個體的執行階段狀態來篩選所傳回執行個體的清單。 若要查看可能的運行時狀態值清單，請參閱 [查詢實例](durable-functions-instance-management.md) 文章。 |

### <a name="response"></a>回應

可以傳回幾個可能的狀態字碼值。

* **HTTP 200 (確定)**：指定的執行個體處於已完成狀態。
* **HTTP 202 (已接受)**：指定的執行個體正在進行中。
* **HTTP 400 (不正確的要求)**：指定的執行個體失敗或終止。
* **HTTP 404 (找不到)**：指定的執行個體不存在或尚未開始執行。
* **HTTP 500 (內部伺服器錯誤)**：指定的執行個體失敗且發生未處理的例外狀況。

**HTTP 200** 和 **HTTP 202** 案例的回應承載是 JSON 物件，具有下列欄位：

| 欄位                 | 資料類型 | 描述 |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | 字串    | 執行個體的執行階段狀態。 值包括 [執行中]**、[擱置]**、[失敗]**、[已取消]**、[終止]**、[已完成]**。 |
| **`input`**           | JSON      | JSON 資料，用來初始化執行個體。 這個欄位為 `null`，如果 `showInput` 查詢字串參數設定為 `false`。|
| **`customStatus`**    | JSON      | 用於自訂協調流程狀態的 JSON 資料。 如果未設定，欄位會是 `null`。 |
| **`output`**          | JSON      | 執行個體的 JSON 輸出。 如果執行個體不是已完成狀態，則這個欄位是 `null`。 |
| **`createdTime`**     | 字串    | 執行個體建立的時間。 使用 ISO 8601 延伸標記法。 |
| **`lastUpdatedTime`** | 字串    | 執行個體保存的時間。 使用 ISO 8601 延伸標記法。 |
| **`historyEvents`**   | JSON      | 包含協調流程執行歷程記錄的 JSON 陣列。 這個欄位為 `null`，除非 `showHistory` 查詢字串參數設定為 `true`。 |

以下是範例回應承載，其中包含協調流程執行歷程記錄和活動輸出 (針對可讀性格式化)：

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202** 回應也包含**位置**回應標頭，它參考與先前所述 `statusQueryGetUri` 欄位相同的 URL。

## <a name="get-all-instances-status"></a>取得所有執行個體狀態

您也可以 `instanceId` 從「取得實例狀態」要求中移除，以查詢所有實例的狀態。 在此情況下，基本參數與「取得實例狀態」相同。 也支援篩選的查詢字串參數。

請記住一點，`connection` 和 `code` 是選擇性的。 如果您對函數具有匿名驗證，則 `code` 不需要。
如果您不想要使用 AzureWebJobsStorage 應用程式設定中定義的不同儲存體連接字串，則可以放心地忽略連接查詢字串參數。

### <a name="request"></a>要求

在1.x 版的函式執行時間中，要求的格式如下 (會顯示多行以提供清楚的) ：

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

在2.x 版的函式執行時間中，URL 格式具有所有相同的參數，但有稍微不同的前置詞：

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位                   | 參數類型  | 描述 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | 協調流程執行個體的識別碼。 |
| **`showInput`**         | 查詢字串    | 選擇性參數。 如果設定為 `false` ，則函數輸入將不會包含在回應承載中。|
| **`showHistory`**       | 查詢字串    | 選擇性參數。 如果設定為 `true`，則協調流程執行歷程記錄將會包含在回應承載中。|
| **`showHistoryOutput`** | 查詢字串    | 選擇性參數。 如果設定為 `true` ，則會將函數輸出包含在協調流程執行歷程記錄中。|
| **`createdTimeFrom`**   | 查詢字串    | 選擇性參數。 當指定時，會篩選在指定的 ISO8601 時間戳記或之後所建立之傳回實例的清單。|
| **`createdTimeTo`**     | 查詢字串    | 選擇性參數。 當指定時，會篩選在指定的 ISO8601 時間戳記或之前建立的傳回實例清單。|
| **`runtimeStatus`**     | 查詢字串    | 選擇性參數。 指定時，會根據所傳回執行個體的執行階段狀態來篩選所傳回執行個體的清單。 若要查看可能的運行時狀態值清單，請參閱 [查詢實例](durable-functions-instance-management.md) 文章。 |
| **`top`**               | 查詢字串    | 選擇性參數。 指定時，會限制查詢所傳回的實例數目。 |

### <a name="response"></a>回應

以下是回應承載範例，包括協調流程狀態 (針對可讀性格式化)：

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> 如果執行個體資料表中有大量資料列，則這項作業可能會耗用非常大量 Azure 儲存體 I/O。 在 [Durable Functions (Azure Functions) 中的效能和級別](durable-functions-perf-and-scale.md#instances-table)文件中可以找到執行個體資料表的更多詳細資料。
>

如果有更多結果，則會在回應標頭中傳回接續 token。  標頭的名稱為 `x-ms-continuation-token`。

如果您在下一個要求標頭中設定接續 token 值，您可以取得下一個結果頁面。 要求標頭的名稱也是 `x-ms-continuation-token` 。

## <a name="purge-single-instance-history"></a>清除單一實例記錄

刪除指定之協調流程實例的記錄和相關成品。

### <a name="request"></a>要求

在1.x 版的函式執行時間中，要求的格式如下 (會顯示多行以提供清楚的) ：

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

在2.x 版的函式執行時間中，URL 格式具有所有相同的參數，但有稍微不同的前置詞：

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位             | 參數類型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 協調流程執行個體的識別碼。 |

### <a name="response"></a>回應

您可以傳回下列 HTTP 狀態碼值。

* **HTTP 200 (確定) **：已成功清除實例記錄。
* **) 找不到 HTTP 404 (**：指定的實例不存在。

**HTTP 200**案例的回應承載是具有下欄欄位的 JSON 物件：

| 欄位                  | 資料類型 | 描述 |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | 整數   | 已刪除的實例數目。 針對單一實例案例，此值應該一律為 `1` 。 |

以下是範例回應裝載 (針對可讀性格式化)：

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>清除多個實例記錄

您也可以 `{instanceId}` 從「清除單一實例歷程記錄」要求中移除，以刪除工作中樞內多個實例的記錄和相關成品。 若要選擇性地清除實例歷程記錄，請使用「取得所有實例狀態」要求中所述的相同篩選。

### <a name="request"></a>要求

在1.x 版的函式執行時間中，要求的格式如下 (會顯示多行以提供清楚的) ：

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

在2.x 版的函式執行時間中，URL 格式具有所有相同的參數，但有稍微不同的前置詞：

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位                 | 參數類型  | 說明 |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | 查詢字串    | 篩選在指定的 ISO8601 時間戳記或之後所建立之已清除實例的清單。|
| **`createdTimeTo`**   | 查詢字串    | 選擇性參數。 當指定時，會篩選在指定的 ISO8601 時間戳記或之前建立的已清除實例清單。|
| **`runtimeStatus`**   | 查詢字串    | 選擇性參數。 若有指定，會根據其執行時間狀態篩選已清除的實例清單。 若要查看可能的運行時狀態值清單，請參閱 [查詢實例](durable-functions-instance-management.md) 文章。 |

> [!NOTE]
> 如果實例和/或歷程記錄資料表中有大量資料列，則這項作業可能非常耗費 Azure 儲存體 i/o。 如需這些資料表的詳細資訊，請參閱 [Durable Functions (Azure Functions) 檔中的效能和規模 ](durable-functions-perf-and-scale.md#instances-table) 。

### <a name="response"></a>回應

您可以傳回下列 HTTP 狀態碼值。

* **HTTP 200 (確定) **：已成功清除實例記錄。
* **找不到 HTTP 404 () **：找不到符合篩選運算式的實例。

**HTTP 200**案例的回應承載是具有下欄欄位的 JSON 物件：

| 欄位                   | 資料類型 | 描述 |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | 整數   | 已刪除的實例數目。 |

以下是範例回應裝載 (針對可讀性格式化)：

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>引發事件

將事件通知訊息傳送至執行中協調流程執行個體。

### <a name="request"></a>要求

在1.x 版的函式執行時間中，要求的格式如下 (會顯示多行以提供清楚的) ：

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

在2.x 版的函式執行時間中，URL 格式具有所有相同的參數，但有稍微不同的前置詞：

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位             | 參數類型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 協調流程執行個體的識別碼。 |
| **`eventName`**   | URL             | 目標協調流程執行個體等候之事件的名稱。 |
| **`{content}`**   | 要求內容 | JSON 格式的事件裝載。 |

### <a name="response"></a>回應

可以傳回幾個可能的狀態字碼值。

* **HTTP 202 (已接受)**：已接受引發的事件以進行處理。
* **HTTP400 (不正確的要求)**：要求內容不是類型 `application/json` 或不是有效的 JSON。
* **HTTP 404 (找不到)**：找不到指定的執行個體。
* **HTTP 410 (不存在)**：指定的執行個體已完成或失敗，且無法再處理任何引發的事件。

以下是範例要求，它會將 JSON 字串 `"incr"` 傳送給等候名為**作業** 之事件的執行個體：

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

此 API 的回應不包含任何內容。

## <a name="terminate-instance"></a>終止執行個體

終止執行中協調流程執行個體。

### <a name="request"></a>要求

在1.x 版的函式執行時間中，要求的格式如下 (會顯示多行以提供清楚的) ：

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

在2.x 版的函式執行時間中，URL 格式具有所有相同的參數，但有稍微不同的前置詞：

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數。

| 欄位             | 參數類型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 協調流程執行個體的識別碼。 |
| **`reason`**      | 查詢字串    | 選擇性。 終止協調流程執行個體的原因。 |

### <a name="response"></a>回應

可以傳回幾個可能的狀態字碼值。

* **HTTP 202 (已接受)**：已接受終止要求以進行處理。
* **HTTP 404 (找不到)**：找不到指定的執行個體。
* **HTTP 410 (不存在)**：指定的執行個體已完成或失敗。

以下是範例要求，它會終止執行中執行個體並且指定**錯誤**的原因：

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

此 API 的回應不包含任何內容。

## <a name="rewind-instance-preview"></a>倒轉執行個體 (預覽)

藉由重新執行最近失敗的作業，將失敗的協調流程執行個體還原為執行中狀態。

### <a name="request"></a>要求

在1.x 版的函式執行時間中，要求的格式如下 (會顯示多行以提供清楚的) ：

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

在2.x 版的函式執行時間中，URL 格式具有所有相同的參數，但有稍微不同的前置詞：

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數。

| 欄位             | 參數類型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 協調流程執行個體的識別碼。 |
| **`reason`**      | 查詢字串    | 選擇性。 倒轉協調流程執行個體的原因。 |

### <a name="response"></a>回應

可以傳回幾個可能的狀態字碼值。

* **HTTP 202 (已接受)**：已接受倒轉要求以進行處理。
* **HTTP 404 (找不到)**：找不到指定的執行個體。
* **HTTP 410 (不存在)**：指定的執行個體已完成或終止。

以下是範例要求，它會倒轉失敗的執行個體並且指定**修正**的原因：

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

此 API 的回應不包含任何內容。

## <a name="signal-entity"></a>信號實體

將單向作業訊息傳送至 [持久實體](durable-functions-types-features-overview.md#entity-functions)。 如果實體不存在，則會自動建立。

> [!NOTE]
> 從 Durable Functions 2.0 開始提供長期實體。

### <a name="request"></a>要求

HTTP 要求的格式如下 (會顯示多行以提供清楚的) ：

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位             | 參數類型  | 描述 |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | 實體 (類型) 名稱。 |
| **`entityKey`**   | URL             | 實體 (唯一識別碼) 的索引鍵。 |
| **`op`**          | 查詢字串    | 選擇性。 要叫用之使用者定義作業的名稱。 |
| **`{content}`**   | 要求內容 | JSON 格式的事件裝載。 |

以下是將使用者定義的「新增」訊息傳送至 `Counter` 名為之實體的範例要求 `steps` 。 訊息的內容為值 `5` 。 如果實體不存在，此要求將會建立此實體：

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> 根據預設， [在 .net 中](durable-functions-dotnet-entities.md#defining-entity-classes)使用以類別為基礎的實體，指定 `op` 的值 `delete` 將會刪除實體的狀態。 但是，如果實體定義名為 `delete` 的作業，則會改為叫用該使用者定義的作業。

### <a name="response"></a>回應

這項作業有幾個可能的回應：

* **HTTP 202 (已接受) **：已接受非同步處理的信號操作。
* **HTTP 400 (錯誤的要求) **：要求內容不是類型 `application/json` 、不是有效的 JSON，或具有不正確 `entityKey` 值。
* **找不到 HTTP 404 () **：找不到指定的 `entityName` 。

成功的 HTTP 要求不包含回應中的任何內容。 失敗的 HTTP 要求可能會在回應內容中包含 JSON 格式的錯誤資訊。

## <a name="get-entity"></a>取得實體

取得指定之實體的狀態。

### <a name="request"></a>要求

HTTP 要求的格式如下 (會顯示多行以提供清楚的) ：

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>回應

此操作有兩個可能的回應：

* **HTTP 200 (確定) **：指定的實體存在。
* **找不到 HTTP 404 () **：找不到指定的實體。

成功的回應會包含實體的 JSON 序列化狀態做為其內容。

### <a name="example"></a>範例
下列範例 HTTP 要求會取得 `Counter` 名為的現有實體的狀態 `steps` ：

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

如果 `Counter` 實體只包含儲存在欄位中的幾個步驟 `currentValue` ，則回應內容看起來可能會像下列 (格式，以提供可讀性) ：

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>列出實體

您可以依機構名稱或上次作業日期來查詢多個實體。

### <a name="request"></a>要求

HTTP 要求的格式如下 (會顯示多行以提供清楚的) ：

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位                       | 參數類型  | 描述 |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | 選擇性。 指定時，會依機構名稱篩選傳回的實體清單 (不區分大小寫的) 。 |
| **`fetchState`**            | 查詢字串    | 選擇性參數。 如果設定為 `true` ，則實體狀態將會包含在回應承載中。 |
| **`lastOperationTimeFrom`** | 查詢字串    | 選擇性參數。 當指定時，會篩選已在指定的 ISO8601 時間戳記之後處理作業的傳回實體清單。 |
| **`lastOperationTimeTo`**   | 查詢字串    | 選擇性參數。 當指定時，會篩選已在指定的 ISO8601 時間戳記之前處理作業的傳回實體清單。 |
| **`top`**                   | 查詢字串    | 選擇性參數。 指定時，會限制查詢傳回的實體數目。 |


### <a name="response"></a>回應

成功的 HTTP 200 回應包含 JSON 序列化的實體陣列，並選擇性地包含每個實體的狀態。

根據預設，作業會傳回符合查詢準則的前100個實體。 呼叫端可以指定的查詢字串參數值， `top` 以傳回不同的結果數目上限。 如果有更多結果超出傳回的結果，則會在回應標頭中傳回接續 token。 標頭的名稱為 `x-ms-continuation-token`。

如果您在下一個要求標頭中設定接續 token 值，您可以取得下一個結果頁面。 要求標頭的名稱也是 `x-ms-continuation-token` 。

### <a name="example---list-all-entities"></a>範例-列出所有實體

下列範例 HTTP 要求會列出工作中樞內的所有實體：

```http
GET /runtime/webhooks/durabletask/entities
```

回應 JSON 看起來可能像下列 (格式，以提供可讀性) ：

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>範例-篩選實體清單

下列範例 HTTP 要求只會列出類型的前兩個實體 `counter` ，也會提取其狀態：

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

回應 JSON 看起來可能像下列 (格式，以提供可讀性) ：

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [瞭解如何使用 Application Insights 來監視您的持久函式](durable-functions-diagnostics.md)