---
title: 包含檔案
description: 包含檔案
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272871"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>架構的資料模型

|欄位|資料類型|描述|
|----|----|----|
|**警報顯示名稱**|String|警報的顯示名稱。|
|**AlertType**|String|警報的類型。 相同類型的警報應具有相同的值。 此欄位是表示警報類型的鍵控字串，而不是警報實例的類型。 來自同一檢測邏輯/分析的所有警報實例都應具有相同的警報類型的值。|
|**洩露的實體**|String|與此警報最相關的資源的顯示名稱。|
|**描述**|String|警示的描述。|
|**結束時間Utc**|Datetime|警報中包含的最後一個事件或活動的時間。  該欄位應為符合 ISO8601 格式（包括 UTC 時區資訊）的字串。|
|**實體**|IE500（IEntity）|與警報相關的實體清單。 此清單可以容納不同類型的實體的混合。 實體類型可以是"實體"部分中定義的任何類型。 也可以發送不在下面清單中的實體，但不保證它們將被處理（警報不會失敗，使用新類型的實體進行驗證）。|
|**擴充屬性**|字典（字串，字串）|提供程式可能（可選）在此處包含自訂欄位。|
|**意圖**|例舉|警報後面的終止鏈相關意圖。 有關支援的值的清單以及 Azure 安全中心支援的終止鏈意圖的說明，請參閱[意圖](../articles/security-center/alerts-reference.md#intentions)。<br/>此欄位可能具有多個值（用逗號分隔）。|
|**正在發生**|Bool|此欄位確定警報是事件（多個警報的複合分組）還是單個警報。 欄位的預設值為"false"（表示它是單個警報）。|
|**ProcessingEndTime**|Datetime|持有警報的原始產品中的最終使用者可訪問警報的時間。|
|**產品名稱**|String|發佈此警報的產品的名稱（Azure 安全中心、Azure ATP、微軟防禦者 ATP、O365 ATP、MCAS 等）。|
|**補救步驟**|清單<String>|要執行手動操作項來修復警報。|
|**資源識別碼**|清單（資源識別碼）|可用於將警報定向到正確的產品暴露組（租戶、工作區、訂閱等）的資源識別碼。 每個警報可以有多個不同類型的識別碼。|
|**嚴重性**|例舉|提供程式報告的警報的嚴重性。 可能的值：資訊、低、中和高。|
|**開始時間Utc**|Datetime|警報中包含的第一個事件或活動的時間。 該欄位應為符合 ISO8601 格式（包括 UTC 時區資訊）的字串。|
|**狀態**|例舉|警報的生命週期狀態。<br/>支援的狀態為：新建、已解決、已取消、未知。<br/>指定受支援選項以外的值的警報將分配狀態為"未知"。<br/>未指定值的警報將分配狀態為"新建"。|
|**系統警報 Id**|String|警報識別碼。|
|**TimeGenerated**|Datetime|警報提供程式生成警報的時間。 如果未由內部警報提供程式報告，則產品可以選擇分配產品接收的時間進行處理。  該欄位應為符合 ISO8601 格式（包括 UTC 時區資訊）的字串。|
|**供應商名稱**|String|引發警報的供應商的名稱。|
|||
