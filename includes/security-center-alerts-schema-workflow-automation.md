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
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400998"
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
|**AlertType**|String|警示的類型。 相同類型的警示應該具有相同的值。 此欄位是代表警示類型，而不是警示實例的索引字串。 相同偵測邏輯/分析中的所有警示實例都應具有相同的警示類型值。|
|**CompromisedEntity**|String|與此警示最相關的資源顯示名稱。|
|**說明**|String|警示的描述。|
|**EndTimeUtc**|Datetime|警示中包含的最後一個事件或活動的時間。  此欄位應該是符合 ISO8601 格式的字串，包括 UTC 時區資訊。|
|**實體**|IEnumerable (IEntity) |與警示相關的實體清單。 這份清單可保存各種不同類型的實體。 實體類型可以是在 [實體] 區段中定義的任何類型。 您也可以傳送不在下列清單中的實體，但不保證會進行處理， (警示將無法通過新的實體類型) 驗證。|
|**ExtendedProperties**|字典 (字串，字串) |提供者可能 (選擇性) 在此包含自訂欄位。|
|**意圖**|列舉|警示後方的終止鏈相關意圖。 如需支援值的清單，以及 Azure 資訊安全中心支援的終止鏈意圖的說明，請參閱 [意圖](../articles/security-center/alerts-reference.md#intentions)。<br/>這個欄位可能會有多個值 (以逗號) 分隔。|
|**IsIncident**|Bool|此欄位會判斷警示是否為事件 (多個警示的複合群組) 或單一警示。 此欄位的預設值為 ' false ' (表示它是單一警示) 。|
|**ProcessingEndTime**|Datetime|包含警示之原始產品中的使用者可存取警示的時間。|
|**ProductName**|String|發佈此警示的產品名稱 (Azure 資訊安全中心、Azure ATP、Microsoft Defender ATP、MCAS 等) 。|
|**RemediationSteps**|清單<String>|修復警示所要採取的手動動作專案。|
|**ResourceIdentifiers**|列出 (資源識別碼) |此警示的資源識別碼可用來將警示導向至正確的產品暴露群組， (租使用者、工作區、訂用帳戶等 ) 。 每個警示可以有不同類型的多個識別符。|
|**嚴重性**|列舉|提供者所報告之警示的嚴重性。 可能的值：資訊、低、中和高。|
|**StartTimeUtc**|Datetime|警示中包含的第一個事件或活動的時間。 此欄位應該是符合 ISO8601 格式的字串，包括 UTC 時區資訊。|
|**狀態**|列舉|警示的生命週期狀態。<br/>支援的狀態為：新增、已解決、已關閉、不明。<br/>指定不是支援的選項之值的警示會指派狀態「未知」。<br/>未指定值的警示會指派為 [新增] 狀態。|
|**SystemAlertId**|String|警示識別碼。|
|**TimeGenerated**|Datetime|警示提供者產生警示的時間。 如果內部警示提供者未回報，產品可以選擇將其接收的時間指派給產品進行處理。  此欄位應該是符合 ISO8601 格式的字串，包括 UTC 時區資訊。|
|**VendorName**|String|引發警示之廠商的名稱。|
|||
