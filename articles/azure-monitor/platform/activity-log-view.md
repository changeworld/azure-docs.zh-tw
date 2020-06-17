---
title: 在 Azure 監視器中檢視 Azure 活動記錄
description: 在 Azure 監視器中檢視 Azure 活動記錄，並使用 PowerShell、CLI 和 REST API 擷取記錄。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3327f0bdaff641055cf84ab205d847f0fb73bfe8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834606"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>檢視及擷取 Azure 活動記錄事件

[Azure 活動記錄](platform-logs-overview.md)可讓您深入了解 Azure 中發生的訂用帳戶層級事件。 本文提供各種用於檢視和擷取活動記錄事件的方法詳細資訊。

## <a name="azure-portal"></a>Azure 入口網站
從 Azure 入口網站的 [監視] 功能表，檢視所有資源的活動記錄。 從該資源功能表中的 [活動記錄] 選項，檢視特定資源的活動記錄。

![檢視活動記錄](./media/activity-logs-overview/view-activity-log.png)

您可以依照下列欄位來篩選活動記錄：

* **時間範圍**：事件的開始和結束時間。
* **類別**：如[活動記錄中的類別](activity-log-view.md#categories-in-the-activity-log)所述的事件類別。
* 訂用帳戶：一或多個 Azure 訂用帳戶名稱。
* **資源群組**：所選訂用帳戶中的一或多個資源群組。
* **資源 (名稱)** ：特定資源的名稱。
* **資源類型**：資源的類型，例如 Microsoft.Compute/virtualmachines。
* **作業名稱**：Azure Resource Manager 作業的名稱，例如 Microsoft.SQL/servers/Write。
* **嚴重性**：事件的嚴重性層級。 可用的值為 [資訊]、[警告]、[錯誤]、[重大]。
* **事件起始者**：執行作業的使用者。
* **開啟搜尋**：開啟文字搜尋方塊，以在所有事件的所有欄位中搜尋該字串。

## <a name="categories-in-the-activity-log"></a>活動記錄中的類別
活動記錄中的每個事件都具有下表所述的特定類別。 如需這些類別結構描述的完整詳細資料，請參閱 [Azure 活動記錄事件結構描述](activity-log-schema.md)。 

| 類別 | 描述 |
|:---|:---|
| 管理 | 包含透過 Resource Manager 執行的所有建立、更新、刪除和動作作業記錄。 系統管理事件的範例包括「建立虛擬機器」和「刪除網路安全性群組」。<br><br>使用 Resource Manager 的使用者或應用程式所採取的每個動作，都會在特定資源類型上模型化為作業。 如果作業類型為「寫入」、「刪除」或「動作」，則該作業的啟動及成功或失敗記錄皆會記錄在「系統管理」類別。 系統管理事件也包含訂用帳戶中角色型存取控制的所有變更。 |
| 服務健康情況 | 包含所有在 Azure 中發生的服務健康情況事件記錄。 「位於美國東部的 SQL Azure 面臨停機」服務健康狀態事件範例。 <br><br>服務健康情況事件有六個種類：需要採取動作、輔助復原、事件、維護、資訊或_安全性_。 只有訂用帳戶中有會受到事件影響的資源時，才會建立這些事件。
| 資源健全狀況 | 包含 Azure 資源已發生的任何資源健康情況事件記錄。 「虛擬機器健全狀態已變成無法使用」是資源健康情況事件的範例之一。<br><br>資源健康情況事件可以代表下列四種健全狀態之一：「可用」、「無法使用」、「已降級」與「未知」。 此外，資源健康情況事件可分類為「平台起始」或「使用者起始」。 |
| 警示 | 包含 Azure 警示的啟用記錄。 「myVM 上的 CPU% 在過去 5 分鐘內已超過 80」是警示事件的範例之一。|
| Autoscale | 包含所有與自動調整引擎 (以訂用帳戶中定義的自動調整設定為基礎) 作業相關的所有事件記錄。 「自動調整擴大動作失敗」是自動調整事件的範例之一。 |
| 建議 | 包含來自 Azure Advisor 的建議事件。 |
| 安全性 | 包含 Azure 資訊安全中心所產生的任何警示記錄。 「已執行可疑的雙重擴充檔案」是安全性事件的範例之一。 |
| 原則 | 包含 Azure 原則所執行的所有效果動作作業記錄。 原則事件的範例包括「稽核」和「拒絕」。 原則所採取的每個動作會模型化為資源上的作業。 |

## <a name="view-change-history"></a>檢視變更歷程記錄

在檢閱活動記錄時，這有助於查看該事件期間發生的變更。 您可以使用 [變更歷程記錄] 來檢視此資訊。 從您想要更深入了解的活動記錄中選取事件。 選取 [變更歷程記錄 (預覽)] 索引標籤，以檢視與該事件相關聯的任何變更。

![變更事件的歷程記錄清單](media/activity-logs-overview/change-history-event.png)

如果事件有任何相關聯的變更，您將會看到一份可選取的變更清單。 這可開啟 [變更歷程記錄 (預覽)] 頁面。 在此頁面上，您會看到資源的變更。 如您在下列範例中所見，我們不僅能看見 VM 的大小變更，還能看見變更前的先前 VM 大小以及變更後的大小。

![顯示差異的變更歷程記錄頁面](media/activity-logs-overview/change-history-event-details.png)

若要深入了解變更歷程記錄，請參閱[取得資源變更](../../governance/resource-graph/how-to/get-resource-changes.md)。






## <a name="powershell"></a>PowerShell
使用 [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) Cmdlet，從 PowerShell 擷取活動記錄。 以下是一些常見的範例。

> [!NOTE]
> `Get-AzLog` 只提供 15 天的歷程記錄。 使用 **-MaxRecord** 參數可查詢 15 天以前的最後 N 個事件。 若要存取 15 天前的事件，請使用 REST API 或 SDK。 如果您未包含 **StartTime**，則預設值是 **EndTime** 減去一小時。 如果您未包含 **EndTime**，則預設值是目前的時間。 所有時間都是採用 UTC 格式。


取得在特定日期時間之後建立的記錄項目：

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

取得某個日期時間範圍內的記錄項目︰

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

取得特定資源群組中的記錄檔項目︰

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

從特定資源提供者取得某個日期時間範圍內的記錄項目︰

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

取得包含特定呼叫者的記錄項目︰

```powershell
Get-AzLog -Caller 'myname@company.com'
```

取得最後 1000 個事件：

```powershell
Get-AzLog -MaxRecord 1000
```


## <a name="cli"></a>CLI
使用 [az monitor activity-log](../samples/cli-samples.md#view-activity-log-for-a-subscription) 從 CLI 擷取活動記錄。 以下是一些常見的範例。


檢視所有可用的選項。

```azurecli
az monitor activity-log list -h
```

取得特定資源群組中的記錄檔項目︰

```azurecli
az monitor activity-log list --resource-group <group name>
```

取得包含特定呼叫者的記錄項目︰

```azurecli
az monitor activity-log list --caller myname@company.com
```

取得呼叫者在某個日期範圍內的資源類型記錄：

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
使用 [Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/)，從 REST 用戶端擷取活動記錄。 以下是一些常見的範例。

利用 filter 取得活動記錄：

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

利用 filter 和 select 取得活動記錄：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

利用 select 取得活動記錄：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

不利用 filter 或 select 取得活動記錄：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>後續步驟

* [閱讀平台記錄的概觀](platform-logs-overview.md)
* [建立診斷設定以將活動記錄傳送至其他目的地](diagnostic-settings.md)
