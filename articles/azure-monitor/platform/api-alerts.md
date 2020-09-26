---
title: 使用 Log Analytics 警示 REST API
description: Log Analytics 警示 REST API 可讓您在 Log Analytics 中建立及管理警示，這是 Log Analytics 的一部分。  本文提供此 API 的詳細資料和幾個執行不同作業的範例。
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: dce340db90c1528c46c1be0bc172751a04feaf31
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294070"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>使用 REST API 在 Log Analytics 中建立及管理警示規則 

> [!IMPORTANT]
> 如同 [宣佈](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)，在 *2019 年6月1日* 之後建立的 log analytics 工作區 () 使用目前的 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules/)來管理警示規則。 建議客戶切換至舊版工作區中 [目前的 API](./alerts-log-api-switch.md) ，以充分利用 Azure 監視器 scheduledQueryRules [權益](./alerts-log-api-switch.md#benefits)。 本文說明如何使用舊版 API 管理警示規則。

Log Analytics 警示 REST API 可讓您在 Log Analytics 中建立及管理警示。  本文提供此 API 的詳細資料和幾個執行不同作業的範例。

Log Analytics 搜尋 API 是 RESTful，可透過 Azure Resource Manager REST API 來存取。 在本檔中，您將找到使用  [ARMClient](https://github.com/projectkudu/ARMClient)從 PowerShell 命令列存取 api 的範例，這是可簡化叫用 Azure Resource Manager API 的開放原始碼命令列工具。 使用 ARMClient 和 PowerShell 是存取 Log Analytics 搜尋 API 的許多選項之一。 這些工具可讓您利用 RESTful Azure Resource Manager API 呼叫 Log Analytics 工作區，並在其中執行搜尋命令。 API 會以 JSON 格式向您輸出搜尋結果，讓您以程式設計方式透過許多不同的方法使用搜尋結果。

## <a name="prerequisites"></a>Prerequisites
目前，在 Log Analytics 中只能使用已儲存的搜尋來建立警示。  如需詳細資訊，請參閱 [記錄檔搜尋 REST API](../log-query/log-query-overview.md) 。

## <a name="schedules"></a>排程
一個已儲存的搜尋可以有一或多個排程。 排程中定義搜尋的執行頻率及識別準則的時間間隔。
排程具有下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 間隔 |執行搜尋的頻率。 以分鐘為單位。 |
| QueryTimeSpan |準則評估的時間間隔。 必須等於或大於 Interval。 以分鐘為單位。 |
| 版本 |所使用的 API 版本。  目前，這應該一律設為 1。 |

例如，假設事件查詢的 Interval 是 15 分鐘，而 Timespan 是 30 分鐘。 在此情況下，將會每隔 15 分鐘執行一次查詢，而如果準則在 30 分鐘內連續評估為 true，就會觸發警示。

### <a name="retrieving-schedules"></a>擷取排程
使用 Get 方法來擷取已儲存的搜尋的所有排程。

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20
```

使用 Get 方法並指定排程識別碼，以擷取已儲存的搜尋的特定排程。

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

以下是排程的回應範例。

```json
{
   "value": [{
      "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
      "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
      "properties": {
         "Interval": 15,
         "QueryTimeSpan": 15,
         "Enabled": true,
      }
   }]
}
```

### <a name="creating-a-schedule"></a>建立排程
使用 Put 方法並指定唯一的排程識別碼，以建立新的排程。  兩個排程即使與不同的已儲存搜尋相關聯，也不能有相同的識別碼。  當您在 Log Analytics 主控台中建立排程時，將會建立 GUID 做為排程識別碼。

> [!NOTE]
> Log Analytics API 所建立並儲存的所有搜尋、排程和動作，都必須使用小寫名稱。

```powershell
$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="editing-a-schedule"></a>編輯排程
針對已儲存的相同搜尋，使用 Put 方法並指定現有的排程識別碼，以修改該排程；在下列範例中，排程已停用。 要求的主體必須包含排程的 *etag* 。

```powershell
$scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="deleting-schedules"></a>刪除排程
使用 Delete 方法並指定排程識別碼來刪除排程。

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

## <a name="actions"></a>動作
一個排程可以有多個動作。 一個動作可能定義一或多個處理序來執行，例如傳送郵件或啟動 Runbook，或也可能定義臨界值來判斷搜尋結果是否符合某些準則。  某些動作會同時定義這兩者，以便符合臨界值時執行處理序。

所有動作具有下表中的屬性。  不同類型的警示有不同的額外屬性，如下所述。

| 屬性 | 說明 |
|:--- |:--- |
| `Type` |動作的類型。  目前可能的值為 Alert 和 Webhook。 |
| `Name` |警示的顯示名稱。 |
| `Version` |所使用的 API 版本。  目前，這應該一律設為 1。 |

### <a name="retrieving-actions"></a>擷取動作

使用 Get 方法來擷取排程的所有動作。

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20
```

使用 Put 方法並指定動作識別碼，擷取排程的特定動作。

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20
```

### <a name="creating-or-editing-actions"></a>建立或編輯動作
使用 Put 方法並指定排程特有的動作識別碼，以建立新的動作。  當您在 Log Analytics 主控台中建立動作時，動作識別碼會使用 GUID。

> [!NOTE]
> Log Analytics API 所建立並儲存的所有搜尋、排程和動作，都必須使用小寫名稱。

針對已儲存的相同搜尋，使用 Put 方法並指定現有的動作識別碼，以修改該排程。  要求的主體必須包含排程的 etag。

建立新動作的要求格式依動作類型而不同，下列各節提供這些範例。

### <a name="deleting-actions"></a>刪除動作

使用 Delete 方法並指定動作識別碼來刪除動作。

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20
```

### <a name="alert-actions"></a>警示動作
一個排程應該只有一個警示動作。  警示動作具有下表中的一或多個區段。  以下進一步詳細說明每一個區段。

| 區段 | 描述 | 使用量 |
|:--- |:--- |:--- |
| 閾值 |執行動作的準則。| 將警示延伸至 Azure 之前或之後，都必須為每個警示指定。 |
| 嚴重性 |用來在觸發時將警示分類的標籤。| 將警示延伸至 Azure 之前或之後，都必須為每個警示指定。 |
| 隱藏 |可停止來自警示之通知的選項。 | 將警示延伸至 Azure 之前或之後，可依選擇為每個警示指定。 |
| 動作群組 |已指定所需動作之 Azure ActionGroup 的識別碼，例如 - 電子郵件、簡訊、語音通話、Webhook、自動化 Runbook、ITSM 連接器等。| 將警示延伸至 Azure 之後，就必須指定|
| 自訂動作|修改來自 ActionGroup 之所選動作的標準輸出| 就每個警示而言為選擇性，可在將警示延伸至 Azure 之後使用。 |

### <a name="thresholds"></a>臨界值
一個警示動作應該只有一個臨界值。  當已儲存的搜尋結果符合與該搜尋相關聯動作中的臨界值時，將會執行該動作中的任何其他處理序。  動作也可以只包含臨界值，以便與不包含臨界值的其他類型動作一起搭配使用。

臨界值具有下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| `Operator` |用於比較臨界值的運算子。 <br> gt = 大於 <br>  lt = 小於 |
| `Value` |臨界值。 |

例如，假設事件查詢的間隔是 15 分鐘、時間範圍是 30 分鐘，而臨界值大於 10。 在此情況下，將會每隔 15 分鐘執行一次查詢，而如果傳回 10 個在 30 分鐘內建立的事件，就會觸發警示。

以下是一個只包含臨界值的動作的回應範例。  

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Version": 1
}
```

使用 Put 方法並指定唯一的動作識別碼，以建立排程的新臨界值動作。  

```powershell
$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

使用 Put 方法並指定現有的動作識別碼，以修改排程的臨界值動作。  要求的主體必須包含動作的 etag。

```powershell
$thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

#### <a name="severity"></a>嚴重性
Log Analytics 可讓您將警示分類成數個類別，以便於管理和分級。 已定義的警示嚴重性包括：資訊、警告及嚴重。 這些會以下列方式對應至「Azure 警示」的標準化嚴重性級別：

|Log Analytics 嚴重性等級  |Azure 警示嚴重性等級  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

以下是一個只包含臨界值和嚴重性的動作範例回應。 

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Severity": "critical",
   "Version": 1
}
```

使用 Put 方法搭配唯一動作識別碼，來為排程建立具有嚴重性的新動作。  

```powershell
$thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

使用 Put 方法搭配現有的動作識別碼，以修改排程的嚴重性動作。  要求的主體必須包含動作的 etag。

```powershell
$thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

#### <a name="suppress"></a>隱藏
以 Log Analytics 為基礎的查詢警示會在每次達到或超出閾值時引發。 視查詢中隱含的邏輯而定，這可能導致在一連串間隔都引發警示，因而也不斷傳送通知。 為了避免這樣的情況，使用者可以設定 [隱藏] 選項來指示 Log Analytics 等到達到規定的時間長度之後，才針對該警示規則引發第二次通知。 因此，如果設定隱藏 30 分鐘；警示將會在第一次時引發並傳送所設定的通知。 但是接著會等候 30 分鐘，然後才再次針對該警示規則使用通知。 在過渡期間，警示規則會繼續執行 - 只有通知會被 Log Analytics 依據指定的時間隱藏，不論在此期間內該警示規則引發多少次。

您可以使用 *Throttling* 值來指定Log Analytics 警示規則的「隱藏」屬性，以及使用 *DurationInMinutes* 值來指定隱藏期間。

以下是一個只包含閾值、嚴重性及隱藏屬性之動作的範例回應

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Throttling": {
   "DurationInMinutes": 30
   },
   "Severity": "critical",
   "Version": 1
}
```

使用 Put 方法搭配唯一動作識別碼，來為排程建立具有嚴重性的新動作。  

```powershell
$AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

使用 Put 方法搭配現有的動作識別碼，以修改排程的嚴重性動作。  要求的主體必須包含動作的 etag。

```powershell
$AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

#### <a name="action-groups"></a>動作群組
Azure 中的所有警示都使用「動作群組」作為處理動作的預設機制。 使用「動作群組」時，您可以指定您的動作一次，然後將動作群組與整個 Azure 中的多個警示建立關聯。 這樣就不需要一再地重複宣告相同的動作。 「動作群組」支援多個動作 - 包括電子郵件、、SMS、語音通話、ITSM 連線、自動化 Runbook、Webhook URI 等。 

針對將警示延伸至 Azure 的使用者 - 排程的「動作群組」詳細資料現在應該與閾值一起傳遞，才能建立警示。 必須先在「動作群組」內定義電子郵件詳細資料、Webhook URL、「Runbook 自動化」詳細資料及其他動作，才能建立警示；使用者可以在入口網站中[從 Azure 監視器建立動作群組](./action-groups.md)，或使用[動作群組 API](/rest/api/monitor/actiongroups) 來建立動作群組。

若要將動作群組關聯新增至警示，請在警示定義中指定該動作群組的唯一 Azure Resource Manager 識別碼。 以下提供範例說明：

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ]
   },
   "Severity": "critical",
   "Version": 1
}
```

使用 Put 方法搭配唯一動作識別碼，來為排程關聯已經存在的「動作群組」。  以下是使用方式的範例說明。

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

使用 Put 方法搭配現有的動作識別碼，來為排程修改已關聯的「動作群組」。  要求的主體必須包含動作的 etag。

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

#### <a name="customize-actions"></a>自訂動作
動作預設會依循通知的標準範本和格式。 但是使用者可以自訂一些動作，即使這些動作是由「動作群組」所控制。 目前，可以自訂「電子郵件主旨」和「Webhook 承載」。

##### <a name="customize-e-mail-subject-for-action-group"></a>自訂動作群組的電子郵件主旨
警示的電子郵件主旨預設為：`<WorkspaceName>` 的警示通知 `<AlertName>`。 但是您可以自訂此主旨，以便使用特定的文字或標籤 - 這樣可讓您在「收件匣」中輕鬆採用篩選規則。 自訂電子郵件標頭詳細資料必須一併傳送 ActionGroup 詳細資料，如以下範例所示。

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
      "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
}
```

使用 Put 方法搭配唯一動作識別碼，來為排程關聯含有自訂值的已存在「動作群組」。  以下是使用方式的範例說明。

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

使用 Put 方法搭配現有的動作識別碼，來為排程修改已關聯的「動作群組」。  要求的主體必須包含動作的 etag。

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

##### <a name="customize-webhook-payload-for-action-group"></a>自訂動作群組的 Webhook 承載
根據預設，透過「動作群組」傳送來進行記錄分析的 Webhook 會具有固定的結構。 但是使用者可以藉由使用所支援的特定變數來自訂 JSON 承載，以符合 Webhook 端點的需求。 如需詳細資訊，請參閱[記錄警示規則的 Webhook 動作](./alerts-log-webhook.md)。 

自訂 Webhook 詳細資料必須一併傳送 ActionGroup 詳細資料，並且將會套用至動作群組內所指定的所有 Webhook URI；如以下範例所示。

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
   "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
   "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
},
```

使用 Put 方法搭配唯一動作識別碼，來為排程關聯含有自訂值的已存在「動作群組」。  以下是使用方式的範例說明。

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

使用 Put 方法搭配現有的動作識別碼，來為排程修改已關聯的「動作群組」。  要求的主體必須包含動作的 etag。

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

## <a name="next-steps"></a>後續步驟

* 在 Log Analytics 中使用 [REST API 執行記錄檔搜尋](../log-query/log-query-overview.md) 。
* 瞭解 [Azure 監視器中的記錄警示](./alerts-unified-log.md)
* 如何 [建立、編輯或管理 Azure 監視器中的記錄警示規則](./alerts-log.md)

