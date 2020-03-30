---
title: 在 Azure 監視器中查看 Azure 活動日誌事件
description: 在 Azure 監視器中查看 Azure 活動日誌，然後使用 PowerShell、CLI 和 REST API 進行檢索。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 9df7593a9fd191d3a734fba5e81fb1aecba08345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275044"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>查看和檢索 Azure 活動日誌事件

[Azure 活動日誌](platform-logs-overview.md)提供對 Azure 中發生的訂閱級事件的見解。 本文詳細介紹了查看和檢索活動日誌事件的不同方法。

## <a name="azure-portal"></a>Azure 入口網站
查看 Azure 門戶中的 **"監視器"** 功能表中所有資源的活動日誌。 從該資源功能表中的 **"活動日誌"** 選項查看特定資源的活動日誌。

![查看活動日誌](./media/activity-logs-overview/view-activity-log.png)

您可以按以下欄位篩選活動日誌事件：

* **時間跨度**：事件的開始和結束時間。
* **類別**：[活動日誌 中的類別中](activity-log-view.md#categories-in-the-activity-log)所述的事件類別。
* **訂閱**：一個或多個 Azure 訂閱名稱。
* **資源組**：所選訂閱中的一個或多個資源組。
* **資源（名稱）：**- 特定資源的名稱。
* **資源類型**：資源類型，例如_Microsoft.計算/虛擬機器_。
* **操作名稱**- Azure 資源管理器操作的名稱，例如_Microsoft.SQL/伺服器/寫入_。
* **嚴重性**：事件的嚴重性級別。 可用值為 _"資訊"、"__警告_"、"_錯誤_"、"_嚴重_"。
* **由 啟動的事件 ：** 執行操作的使用者。
* **打開搜索**：打開文本搜索框，該框在所有事件中的所有欄位中搜索該字串。

## <a name="categories-in-the-activity-log"></a>活動日誌中的類別
活動日誌中的每個事件都有下表仲介紹的特定類別。 如需這些類別結構描述的完整詳細資料，請參閱 [Azure 活動記錄事件結構描述](activity-log-schema.md)。 

| 類別 | 描述 |
|:---|:---|
| 管理 | 包含通過資源管理器執行的所有創建、更新、刪除和操作操作的記錄。 管理事件的示例包括_創建虛擬機器_和_刪除網路安全性群組_。<br><br>使用者或應用程式使用資源管理器執行的每個操作都建模為對特定資源類型的操作。 如果操作類型為 _"寫入_"、_刪除_或_操作_，則該操作的開始和成功或失敗的記錄將記錄在"管理"類別中。 管理事件還包括對訂閱中基於角色的存取控制的任何更改。 |
| 服務健康情況 | 包含 Azure 中發生的任何服務運行狀況事件的記錄。 美國東部服務運行狀況事件 SQL Azure 的一個示例_遇到停機_。 <br><br>服務健康事件有六個品種：_行動要求_，_輔助恢復_，_事件_，_維護_，_資訊_，或_安全_。 僅當訂閱中的資源受事件影響時，才會創建這些事件。
| 資源健全狀況 | 包含 Azure 資源發生的任何資源運行狀況事件的記錄。 資源運行狀況事件的一個示例是_虛擬機器運行狀況狀態更改為不可用_。<br><br>資源運行狀況事件可以表示四種運行狀況之一：_可用_、_不可用_、_降級_和_未知_。 此外，資源運行狀況事件可以歸類為_平臺啟動_或_使用者啟動_。 |
| 警示 | 包含 Azure 警報的啟動記錄。 警報事件的一個示例是 _，在過去 5 分鐘中，myVM 上的 CPU % 超過 80。_|
| Autoscale | 包含與自動縮放引擎操作相關的任何事件的記錄，這些事件基於您在訂閱中定義的任何自動縮放設置。 自動縮放事件的一個示例是_自動縮放向上操作失敗_。 |
| 建議 | 包含 Azure 顧問的建議事件。 |
| 安全性 | 包含 Azure 安全中心生成的任何警報的記錄。 安全事件的一個示例是_執行了可疑雙擴展檔_。 |
| 原則 | 包含 Azure 策略執行的所有效果操作操作的記錄。 策略事件的示例包括_審核_和_拒絕_。 原則所採取的每個動作會模型化為資源上的作業。 |

## <a name="view-change-history"></a>檢視變更記錄

查看活動日誌時，它可以説明查看該事件期間發生的更改。 您可以使用**更改歷史記錄**查看此資訊。 從要深入瞭解的活動日誌中選擇事件。 選擇 **"更改歷史記錄（預覽）"** 選項卡以查看與該事件相關的任何更改。

![變更事件的歷史記錄清單](media/activity-logs-overview/change-history-event.png)

如果事件有任何關聯的更改，您將看到您可以選擇的更改清單。 這將打開**更改歷史記錄（預覽）** 頁面。 在此頁上，您可以看到對資源的更改。 從以下示例中可以看出，我們不僅能夠看到 VM 更改了大小，而且可以看到更改之前以前的 VM 大小以及更改了哪些大小。

![更改顯示差異的歷史記錄頁](media/activity-logs-overview/change-history-event-details.png)

要瞭解有關更改歷史記錄的更多資訊，請參閱[獲取資源更改](../../governance/resource-graph/how-to/get-resource-changes.md)。






## <a name="powershell"></a>PowerShell
使用[獲取-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) Cmdlet 從 PowerShell 檢索活動日誌。 下面是一些常見的示例。

> [!NOTE]
> `Get-AzLog` 只提供 15 天的歷程記錄。 使用 **-MaxEvents**參數查詢超過 15 天的最後 N 個事件。 要訪問超過 15 天的事件，請使用 REST API 或 SDK。 如果您未包含 **StartTime**，則預設值是 **EndTime** 減去一小時。 如果您未包含 **EndTime**，則預設值是目前的時間。 所有時間都是採用 UTC 格式。


獲取在特定日期時間之後創建的日誌條目：

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

獲取日期時間範圍之間的日誌條目：

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

取得特定資源群組中的記錄檔項目︰

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

在日期時間範圍之間從特定資來源提供者獲取日誌條目：

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

獲取具有特定調用方的日誌條目：

```powershell
Get-AzLog -Caller 'myname@company.com'
```

獲取最後 1000 個事件：

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
使用[az 監視器活動日誌](cli-samples.md#view-activity-log-for-a-subscription)從 CLI 檢索活動日誌。 下面是一些常見的示例。


查看所有可用選項。

```azurecli
az monitor activity-log list -h
```

取得特定資源群組中的記錄檔項目︰

```azurecli
az monitor activity-log list --resource-group <group name>
```

獲取具有特定調用方的日誌條目：

```azurecli
az monitor activity-log list --caller myname@company.com
```

按資源類型的調用方獲取日誌，在日期範圍內：

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
使用[Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/)從 REST 用戶端檢索活動日誌。 下面是一些常見的示例。

使用篩選器獲取活動日誌：

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

使用篩選器獲取活動日誌並選擇：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

獲取活動日誌，選擇：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

獲取活動日誌，無需篩選或選擇：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="activity-logs-analytics-monitoring-solution"></a>活動日誌分析監視解決方案
Azure 日誌分析監視解決方案包括多個日誌查詢和視圖，用於分析日誌分析工作區中的活動日誌記錄。

### <a name="prerequisites"></a>Prerequisites
您必須創建診斷設置才能將訂閱的活動日誌發送到日誌分析工作區。 請參閱[在 Azure 監視器 中的日誌分析工作區中收集 Azure 平臺日誌](resource-logs-collect-workspace.md)。

### <a name="install-the-solution"></a>安裝解決方案
使用[安裝監視解決方案](../insights/solutions.md#install-a-monitoring-solution)中的過程安裝**活動日誌分析**解決方案。 無需其他配置。

### <a name="use-the-solution"></a>使用解決方案
按一下**活動日誌**頁頂部的**日誌**以打開訂閱[的活動日誌分析監視解決方案](activity-log-collect.md)。 或者訪問 Azure 門戶中的訂閱**監視器**功能表中的所有監視解決方案。 在 **"見解"** 部分中選擇 **"更多**"以使用解決方案磁貼打開 **"概述"** 頁。 Azure**活動日誌**磁貼顯示工作區中**AzureActivity**記錄數的計數。

![Azure 活動記錄圖格](media/collect-activity-logs/azure-activity-logs-tile.png)


按一下**Azure 活動日誌**磁貼以打開**Azure 活動日誌**視圖。 視圖包括下表中的視覺化部分。 每個部件最多列出 10 個與指定時間範圍的零件條件相匹配的專案。 可以通過按一下"查看零件底部**的所有記錄**"來運行返回所有匹配記錄的日誌查詢。

![Azure 活動記錄儀表板](media/collect-activity-logs/activity-log-dash.png)

| 視覺化部分 | 描述 |
| --- | --- |
| Azure 活動記錄項目 | 顯示所選日期範圍的頂級 Azure 活動日誌條目記錄總計的橫條圖，並顯示前 10 個活動調用方的清單。 按一下長條圖即可執行 `AzureActivity` 的記錄搜尋。 按一下調用方項以運行日誌搜索，返回該專案的所有活動日誌條目。 |
| 依狀態列出的活動記錄 | 顯示所選日期範圍的 Azure 活動日誌狀態的環圈圖和前十個狀態記錄的清單。 按一下圖表可運行 的`AzureActivity | summarize AggregatedValue = count() by ActivityStatus`日誌查詢。 按一下狀態項以運行日誌搜索，返回該狀態記錄的所有活動日誌條目。 |
| 依資源列出的活動記錄 | 使用活動日誌顯示資源總數，並列出每個資源具有記錄計數的前十個資源。 按一下總計區域即可執行 `AzureActivity | summarize AggregatedValue = count() by Resource` 的記錄搜尋，這會顯示解決方案可用的所有 Azure 資源。 按一下資源以運行日誌查詢，返回該資源的所有活動記錄。 |
| 依資源提供者列出的活動記錄 | 顯示組建活動日誌的資來源提供者的總數，並列出前十名。 按一下總面積以運行 的`AzureActivity | summarize AggregatedValue = count() by ResourceProvider`日誌查詢，該查詢顯示所有 Azure 資來源提供者。 按一下資來源提供者以運行日誌查詢，返回提供程式的所有活動記錄。 |




## <a name="next-steps"></a>後續步驟

* [閱讀平臺日誌概述](platform-logs-overview.md)
* [創建診斷設置以將活動日誌發送到其他目標](diagnostic-settings.md)
