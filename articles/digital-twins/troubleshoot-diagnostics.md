---
title: 啟用和查詢診斷記錄
titleSuffix: Azure Digital Twins
description: 瞭解如何使用診斷設定來啟用記錄，以及查詢記錄以進行立即查看。
author: baanders
ms.author: baanders
ms.date: 11/9/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d988617fcaf7479c7bb3356e6ef6f87824ed23a7
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616649"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>針對 Azure 數位 Twins 進行疑難排解：診斷記錄

Azure 數位 Twins 可以為您的服務實例收集記錄，以監視其效能、存取和其他資料。 您可以使用這些記錄來瞭解 Azure 數位 Twins 實例中發生的情況，並對問題執行根本原因分析，而不需要 Azure 支援聯絡。

本文說明如何在 [Azure 入口網站](https://portal.azure.com)中 [**設定診斷設定**](#turn-on-diagnostic-settings)，以開始從您的 Azure 數位 Twins 實例收集記錄。 您也可以指定記錄檔的儲存位置 (例如 Log Analytics 或您選擇的儲存體帳戶) 。

本文也包含 Azure 數位 Twins 所收集的所有 [記錄類別](#log-categories) 和 [記錄架構](#log-schemas) 清單。

設定記錄檔之後，您也可以 [**查詢記錄**](#view-and-query-logs) 以快速收集自訂見解。

## <a name="turn-on-diagnostic-settings"></a>開啟診斷設定 

開啟診斷設定，以開始在您的 Azure 數位 Twins 實例上收集記錄。 您也可以選擇要儲存匯出記錄的目的地。 以下說明如何啟用 Azure 數位 Twins 實例的診斷設定。

1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後流覽至您的 Azure 數位 Twins 實例。 您可以在入口網站的搜尋列中輸入其名稱來尋找它。 

2. 從功能表選取 [ **診斷設定** ]，然後 **新增診斷設定** 。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="顯示診斷設定頁面和要新增之按鈕的螢幕擷取畫面" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. 在接下來的頁面上，填入下列值：
     * **診斷設定名稱** ：提供診斷設定的名稱。
     * **類別細節** ：選擇您想要監視的作業，並核取方塊以啟用這些作業的診斷。 診斷設定可以報告的作業為：
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        如需這些類別及其所含資訊的詳細資訊，請參閱下面的「 [*記錄類別*](#log-categories) 」一節。
     * **目的地詳細資料** ：選擇您要傳送記錄的位置。 您可以選取這三個選項的任意組合：
        - 傳送至 Log Analytics
        - 封存至儲存體帳戶
        - 串流至事件中樞

        如果您的目的地選取專案需要，系統可能會要求您填寫其他詳細資料。  
    
4. 儲存新設定。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="顯示診斷設定頁面的螢幕擷取畫面，其中使用者已填入診斷設定名稱，並針對 [類別目錄詳細資料] 和 [目的地詳細資料] 選取了一些核取方塊。[儲存] 按鈕會反白顯示。" lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

新的設定大約會在 10 分鐘內生效。 之後，記錄會出現在您實例的 [ **診斷設定** ] 頁面上的已設定目標中。 

如需診斷設定及其設定選項的詳細資訊，您可以流覽 [*建立診斷設定，以將平臺記錄和計量傳送至不同的目的地*](../azure-monitor/platform/diagnostic-settings.md)。

## <a name="log-categories"></a>記錄類別

以下是 Azure 數位 Twins 所收集之記錄類別的詳細資料。

| 記錄分類 | 描述 |
| --- | --- |
| ADTModelsOperation | 記錄與模型相關的所有 API 呼叫 |
| ADTQueryOperation | 記錄與查詢相關的所有 API 呼叫 |
| ADTEventRoutesOperation | 記錄與事件路由相關的所有 API 呼叫，以及將事件從 Azure 數位 Twins 輸出到端點服務，例如事件方格、事件中樞和服務匯流排 |
| ADTDigitalTwinsOperation | 記錄與 Azure 數位 Twins 相關的所有 API 呼叫 |

每個記錄類別都包含寫入、讀取、刪除和動作的作業。  這些會對應至 REST API 呼叫，如下所示：

| 事件類型 | REST API 作業 |
| --- | --- |
| 寫入 | PUT 和 PATCH |
| 讀取 | GET |
| 刪除 | 刪除 |
| 動作 | POST |

以下是每個類別中記錄的作業和對應的 [Azure 數位 Twins REST API 呼叫](/rest/api/azure-digitaltwins/) 的完整清單。 

>[!NOTE]
> 每個記錄類別都包含數個作業/REST API 呼叫。 在下表中，每個記錄類別都會對應至其下的所有作業/REST API 呼叫，直到列出下一個記錄類別為止。 

| 記錄分類 | 作業 | REST API 呼叫和其他事件 |
| --- | --- | --- |
| ADTModelsOperation | DigitalTwins/模型/寫入 | 數位對應項模型更新 API |
|  | DigitalTwins/模型/讀取 | 數位對應項模型會依識別碼和清單 Api 取得 |
|  | DigitalTwins/模型/刪除 | 數位對應項模型刪除 API |
|  | DigitalTwins/模型/動作 | 數位對應項模型新增 API |
| ADTQueryOperation | DigitalTwins/query/action | 查詢 Twins API |
| ADTEventRoutesOperation | DigitalTwins/eventroutes/write | 事件路由新增 API |
|  | DigitalTwins/eventroutes/read | 事件路由會依識別碼和清單 Api 取得 |
|  | DigitalTwins/eventroutes/delete | 事件路由刪除 API |
|  | DigitalTwins/eventroutes/action | 嘗試將事件發佈至端點服務時失敗， (不是 API 呼叫)  |
| ADTDigitalTwinsOperation | DigitalTwins/DigitalTwins/write | 數位 Twins 新增、加入關聯性、更新、更新元件 |
|  | DigitalTwins/DigitalTwins/read | 數位 Twins 依識別碼取得、取得元件、依識別碼取得關聯性、列出連入關聯性、清單關聯性 |
|  | DigitalTwins/DigitalTwins/delete | 數位 Twins 刪除、刪除關聯性 |
|  | DigitalTwins/DigitalTwins/action | 數位 Twins 傳送元件遙測，傳送遙測 |

## <a name="log-schemas"></a>記錄架構 

每個記錄類別都有一個架構，可定義該類別中事件的報告方式。 每個個別記錄專案都會儲存為文字，並格式化為 JSON blob。 下列每個記錄類型都會提供記錄檔和範例 JSON 主體中的欄位。 

`ADTDigitalTwinsOperation`、 `ADTModelsOperation` 和 `ADTQueryOperation` 使用一致的 API 記錄架構， `ADTEventRoutesOperation` 有自己的個別架構。

### <a name="api-log-schemas"></a>API 記錄架構

這個記錄架構在 `ADTDigitalTwinsOperation` 、和上是一致的 `ADTModelsOperation` `ADTQueryOperation` 。 它包含對 Azure 數位 Twins 實例進行 API 呼叫的相關資訊。

以下是 API 記錄的欄位和屬性描述。

| 欄位名稱 | 資料類型 | 描述 |
|-----|------|-------------|
| `Time` | Datetime | 此事件發生的日期和時間（UTC） |
| `ResourceID` | String | 事件發生所在資源的 Azure Resource Manager 資源識別碼 |
| `OperationName` | String  | 在事件期間執行的動作類型 |
| `OperationVersion` | String | 在事件期間使用的 API 版本 |
| `Category` | String | 所發出的資源類型 |
| `ResultType` | String | 事件的結果 |
| `ResultSignature` | String | 事件的 Http 狀態碼 |
| `ResultDescription` | String | 事件的其他詳細資料 |
| `DurationMs` | String | 執行事件花費的時間（以毫秒為單位） |
| `CallerIpAddress` | String | 事件的遮罩來源 IP 位址 |
| `CorrelationId` | Guid | 客戶為事件提供的唯一識別碼 |
| `Level` | String | 事件的記錄嚴重性 |
| `Location` | String | 事件發生的區域 |
| `RequestUri` | Uri | 在事件期間使用的端點 |

以下是這些記錄類型的範例 JSON 主體。

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
}
```

### <a name="egress-log-schemas"></a>輸出記錄架構

這是記錄的架構 `ADTEventRoutesOperation` 。 其中包含有關例外狀況的詳細資料，以及連線到 Azure 數位 Twins 實例之輸出端點的 API 作業。

|欄位名稱 | 資料類型 | 描述 |
|-----|------|-------------|
| `Time` | Datetime | 此事件發生的日期和時間（UTC） |
| `ResourceId` | String | 事件發生所在資源的 Azure Resource Manager 資源識別碼 |
| `OperationName` | String  | 在事件期間執行的動作類型 |
| `Category` | String | 所發出的資源類型 |
| `ResultDescription` | String | 事件的其他詳細資料 |
| `Level` | String | 事件的記錄嚴重性 |
| `Location` | String | 事件發生的區域 |
| `EndpointName` | String | 在 Azure 數位 Twins 中建立之輸出端點的名稱 |

以下是這些記錄類型的範例 JSON 主體。

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="view-and-query-logs"></a>查看和查詢記錄

稍早在本文中，您已設定要儲存的記錄類型，並指定其儲存位置。

若要針對問題進行疑難排解，並從這些記錄產生深入解析，您可以產生 **自訂查詢** 。 若要開始使用，您也可以利用服務提供給您的一些範例查詢，以解決客戶對其實例的常見問題。

以下是查詢實例記錄的方式。

1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後流覽至您的 Azure 數位 Twins 實例。 您可以在入口網站的搜尋列中輸入其名稱來尋找它。 

2. 從功能表選取 [ **記錄** ]，以開啟 [記錄查詢] 頁面。 頁面會開啟一個稱為 [ *查詢* ] 的視窗。

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="顯示 Azure 數位 Twins 實例之 [記錄] 頁面的螢幕擷取畫面。它會與查詢視窗重迭，顯示以不同記錄選項命名的預建查詢，例如 DigitalTwin API 延遲和模型 API 延遲。" lightbox="media/troubleshoot-diagnostics/logs.png":::

    這些是針對各種記錄所撰寫的預建範例查詢。 您可以選取其中一個查詢，將它載入查詢編輯器中，然後執行它來查看您實例的這些記錄。

    您也可以關閉 [ *查詢* ] 視窗而不執行任何作業，直接移至 [查詢編輯器] 頁面，您可以在其中撰寫或編輯自訂查詢程式碼。

3. 結束 [ *查詢* ] 視窗之後，您會看到主要的 [查詢編輯器] 頁面。 您可以在這裡查看和編輯範例查詢的文字，或從頭開始撰寫您自己的查詢。
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="顯示 Azure 數位 Twins 實例之 [記錄] 頁面的螢幕擷取畫面。[查詢] 視窗已消失，而是有不同記錄的清單、顯示可編輯查詢程式碼的編輯窗格，以及顯示查詢歷程記錄的窗格。" lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    在左窗格中， 
    - [ *資料表* ] 索引標籤會顯示您可以在查詢中使用的不同 Azure 數位 Twins [記錄類別](#log-categories) 。 
    - [ *查詢* ] 索引標籤包含您可以載入編輯器中的查詢範例。
    - [ *篩選* ] 索引標籤可讓您自訂查詢所傳回之資料的篩選視圖。

如需有關記錄查詢及其撰寫方式的詳細資訊，您可以流覽 [*Azure 監視器中的記錄查詢總覽*](../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>後續步驟

* 如需有關設定診斷的詳細資訊，請參閱 [*收集和取用來自 Azure 資源的記錄資料*](../azure-monitor/platform/platform-logs-overview.md)。
* 如需 Azure 數位 Twins 計量的詳細資訊，請參閱 [*疑難排解：使用 Azure 監視器來查看計量*](troubleshoot-metrics.md)。
* 若要瞭解如何為您的計量啟用警示，請參閱 [*疑難排解：設定警示*](troubleshoot-alerts.md)。