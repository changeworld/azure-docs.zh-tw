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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
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
|**AlertDisplayName**|String|警示的顯示名稱。|
|**AlertType**|String|警示的類型。 相同類型的警示應具有相同的值。 此欄位是代表警示類型的索引鍵字串，而不是警示實例的索引鍵。 相同偵測邏輯/分析的所有警示實例，其警示類型應具有相同的值。|
|**CompromisedEntity**|String|與此警示最相關之資源的顯示名稱。|
|**描述**|String|警示的描述。|
|**EndTimeUtc**|Datetime|警示中包含的最後一個事件或活動的時間。  欄位應該是符合 ISO8601 格式的字串，包括 UTC 時區資訊。|
|**實體**|IEnumerable （IEntity）|與警示相關的實體清單。 這份清單可以保存各種類型的實體。 實體類型可以是 [實體] 區段中所定義的任何類型。 您也可以傳送不在下列清單中的實體，但不保證會處理它們（警示將不會驗證新類型的實體）。|
|**ExtendedProperties**|字典（字串、字串）|提供者可以（選擇性）在此包含自訂欄位。|
|**Intent**|列舉|警示背後的終止鏈相關意圖。 如需支援的值清單，以及 Azure 資訊安全中心支援的終止鏈意圖的說明，請參閱[意圖](../articles/security-center/alerts-reference.md#intentions)。<br/>此欄位可能會有多個值（以逗號分隔）。|
|**IsIncident**|Bool|此欄位會決定警示是否為事件（數個警示的複合群組）或單一警示。 欄位的預設值為「false」（表示這是單一警示）。|
|**ProcessingEndTime**|Datetime|在保留警示的原始產品中，使用者可以存取警示的時間。|
|**ProductName**|String|發佈此警示的產品名稱（Azure 資訊安全中心、Azure ATP、Microsoft Defender ATP、O365 ATP、MCAS 等等）。|
|**RemediationSteps**|名單<String>|要採取以補救警示的手動動作專案。|
|**ResourceIdentifiers**|清單（資源識別碼）|此警示的資源識別碼，可用來將警示導向至正確的產品公開群組（租使用者、工作區、訂用帳戶等）。 每個警示可以有多個不同類型的識別碼。|
|**嚴重性**|列舉|提供者所報告的警示嚴重性。 可能的值： [資訊]、[低]、[中] 和 [高]。|
|**StartTimeUtc**|Datetime|警示中包含的第一個事件或活動的時間。 欄位應該是符合 ISO8601 格式的字串，包括 UTC 時區資訊。|
|**狀態**|列舉|警示的生命週期狀態。<br/>支援的狀態包括：新增、已解決、已解除、未知。<br/>指定支援選項以外值的警示會被指派狀態「不明」。<br/>未指定值的警示會指派狀態 [新增]。|
|**SystemAlertId**|String|警示識別碼。|
|**TimeGenerated**|Datetime|警示提供者產生警示的時間。 如果內部警示提供者未回報，產品可以選擇指派其收到的時間供產品處理。  欄位應該是符合 ISO8601 格式的字串，包括 UTC 時區資訊。|
|**VendorName**|String|引發警示的廠商名稱。|
|||
