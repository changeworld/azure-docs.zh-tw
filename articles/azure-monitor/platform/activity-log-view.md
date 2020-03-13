---
title: 在 Azure 監視器中查看 Azure 活動記錄事件
description: 在 Azure 監視器中查看 Azure 活動記錄，並使用 PowerShell、CLI 和 REST API 抓取。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 9df7593a9fd191d3a734fba5e81fb1aecba08345
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275044"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>查看和取出 Azure 活動記錄事件

[Azure 活動記錄](platform-logs-overview.md)可讓您深入瞭解 azure 中發生的訂用帳戶層級事件。 本文提供查看和抓取活動記錄事件之不同方法的詳細資料。

## <a name="azure-portal"></a>Azure 入口網站
從 Azure 入口網站中的 **監視** 功能表，查看所有資源的活動記錄。 從該資源功能表中的 [**活動記錄**] 選項，查看特定資源的活動記錄檔。

![查看活動記錄](./media/activity-logs-overview/view-activity-log.png)

您可以依下欄欄位篩選活動記錄事件：

* **Timespan**：事件的開始和結束時間。
* **Category**：[活動記錄中分類中](activity-log-view.md#categories-in-the-activity-log)所述的事件類別目錄。
* **訂**用帳戶：一或多個 Azure 訂用帳戶名稱。
* **資源群組**：所選訂用帳戶內的一或多個資源群組。
* **資源（名稱）** ：-特定資源的名稱。
* **資源類型**：資源的類型，例如_Microsoft. Compute/virtualmachines_。
* 作業**名稱**-Azure Resource Manager 作業的名稱，例如， _Microsoft .sql/servers/Write_。
* **嚴重性**：事件的嚴重性層級。 可用的值為_資訊_、_警告_、_錯誤_、_嚴重_。
* **事件起始者**：執行作業的使用者。
* **開啟搜尋**：開啟 [文字搜尋] 方塊，在所有事件的所有欄位中搜尋該字串。

## <a name="categories-in-the-activity-log"></a>活動記錄中的類別
活動記錄中的每個事件都具有下表中所述的特定類別。 如需這些類別結構描述的完整詳細資料，請參閱 [Azure 活動記錄事件結構描述](activity-log-schema.md)。 

| 類別 | 描述 |
|:---|:---|
| 管理 | 包含透過 Resource Manager 執行的所有建立、更新、刪除和動作作業的記錄。 系統管理事件的範例包括 [_建立虛擬機器_] 和 [_刪除網路安全性群組_]。<br><br>使用 Resource Manager 的使用者或應用程式所採取的每個動作，都會在特定資源類型上模型化為作業。 如果作業類型為 [_寫入_]、[_刪除_] 或 [_動作_]，該作業的 [啟動] 和 [成功] 或 [失敗] 記錄都會記錄在 [系統管理] 類別中。 系統管理事件也包括對訂用帳戶中的角色型存取控制所做的任何變更。 |
| 服務健康情況 | 包含 Azure 中發生之任何服務健康狀態事件的記錄。 _美國東部的 SQL Azure_服務健康狀態事件的範例是發生停機。 <br><br>服務健康狀態事件分為六種：_需要採取動作_、_協助_復原、_事件_、_維護_、_資訊_或_安全性_。 只有當您的訂用帳戶中有會受到事件影響的資源時，才會建立這些事件。
| 資源健全狀況 | 包含 Azure 資源已發生之任何資源健康狀態事件的記錄。 資源健康狀態事件的一個範例是_虛擬機器健全狀況狀態已變更為 [無法使用_]。<br><br>資源健康狀態事件可以代表四種健康狀態之一： [_可用_]、[_無法使用_]、[已_降級_] 和 [_未知_]。 此外，資源健康狀態事件可以分類為已_起始平臺_或_使用者起始_。 |
| 警示 | 包含 Azure 警示的啟用記錄。 警示事件的範例是_myVM 上的 CPU% 在過去5分鐘內已超過 80_。|
| Autoscale | 包含根據您在訂用帳戶中定義的自動調整規模設定，與自動調整引擎作業相關之任何事件的記錄。 自動調整規模事件的一個範例是 [_自動調整相應增加] 動作失敗_。 |
| 建議 | 包含來自 Azure Advisor 的建議事件。 |
| 安全性 | 包含 Azure 資訊安全中心所產生之任何警示的記錄。 安全性事件的範例是_執行可疑的雙重擴充_檔案。 |
| 原則 | 包含 Azure 原則所執行之所有效果動作作業的記錄。 原則事件的範例包括_Audit_和_Deny_。 原則所採取的每個動作會模型化為資源上的作業。 |

## <a name="view-change-history"></a>檢視變更記錄

在檢查活動記錄檔時，它可以協助您查看該事件期間發生的變更。 您可以使用 [變更歷程**記錄**] 來查看此資訊。 從您想要更深入瞭解的活動記錄中，選取事件。 選取 [**變更歷程記錄（預覽）** ] 索引標籤，以查看與該事件相關聯的任何變更。

![變更事件的歷程記錄清單](media/activity-logs-overview/change-history-event.png)

如果事件有任何相關聯的變更，您將會看到一份您可以選取的變更清單。 這會開啟 [**變更歷程記錄（預覽）** ] 頁面。 在此頁面上，您會看到資源的變更。 如您在下列範例中所見，我們不僅可以查看 VM 的大小變更，還能看到先前的 VM 大小在變更之前的大小，以及已變更的內容。

![顯示差異的變更歷程記錄頁面](media/activity-logs-overview/change-history-event-details.png)

若要深入瞭解變更歷程記錄，請參閱[取得資源變更](../../governance/resource-graph/how-to/get-resource-changes.md)。






## <a name="powershell"></a>PowerShell
使用[AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) Cmdlet 從 PowerShell 取出活動記錄。 以下是一些常見的範例。

> [!NOTE]
> `Get-AzLog` 只提供 15 天的歷程記錄。 使用 **-MaxEvents**參數來查詢超過15天的最後 N 個事件。 若要存取15天之前的事件，請使用 REST API 或 SDK。 如果您未包含 **StartTime**，則預設值是 **EndTime** 減去一小時。 如果您未包含 **EndTime**，則預設值是目前的時間。 所有時間都是採用 UTC 格式。


取得在特定日期時間之後建立的記錄檔專案：

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

取得日期時間範圍之間的記錄專案：

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

取得特定資源群組中的記錄檔項目︰

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

從日期時間範圍之間的特定資源提供者取得記錄專案：

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

取得具有特定呼叫者的記錄專案：

```powershell
Get-AzLog -Caller 'myname@company.com'
```

取得最後1000個事件：

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
使用[az monitor activity-log](cli-samples.md#view-activity-log-for-a-subscription)從 CLI 取出活動記錄。 以下是一些常見的範例。


查看所有可用的選項。

```azurecli
az monitor activity-log list -h
```

取得特定資源群組中的記錄檔項目︰

```azurecli
az monitor activity-log list --resource-group <group name>
```

取得具有特定呼叫者的記錄專案：

```azurecli
az monitor activity-log list --caller myname@company.com
```

在日期範圍內，依資源類型的呼叫者取得記錄：

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
使用[Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/) ，從 REST 用戶端抓取活動記錄。 以下是一些常見的範例。

取得具有篩選的活動記錄：

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

使用篩選取得活動記錄並選取：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

使用 select 取得活動記錄：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

取得沒有篩選或選取的活動記錄：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="activity-logs-analytics-monitoring-solution"></a>活動記錄分析監視解決方案
Azure Log Analytics 監視解決方案包含多個記錄查詢和視圖，可用於分析 Log Analytics 工作區中的活動記錄檔記錄。

### <a name="prerequisites"></a>Prerequisites
您必須建立診斷設定，以將訂用帳戶的活動記錄傳送至 Log Analytics 工作區。 請參閱[在 Azure 監視器中收集 Log Analytics 工作區中的 Azure 平臺記錄](resource-logs-collect-workspace.md)。

### <a name="install-the-solution"></a>安裝解決方案
使用[安裝監視解決方案](../insights/solutions.md#install-a-monitoring-solution)中的程式來安裝**活動記錄分析**解決方案。 不需要進行其他設定。

### <a name="use-the-solution"></a>使用解決方案
按一下 [**活動記錄**] 頁面頂端的 [**記錄**]，開啟訂用帳戶的[活動記錄分析監視解決方案](activity-log-collect.md)。 或者，在 Azure 入口網站的 訂用帳戶**監視器** 功能表中存取所有監視解決方案。 在 [**深入**解析] 區段中選取 [**更多**]，以開啟具有方案磚的 [**總覽**] 頁面。 [ **Azure 活動記錄**] 圖格會顯示工作區中**AzureActivity**記錄的數目。

![Azure 活動記錄圖格](media/collect-activity-logs/azure-activity-logs-tile.png)


按一下 [ **Azure 活動記錄**] 圖格，以開啟 [ **azure 活動記錄**] 視圖。 此視圖包含下表中的視覺效果元件。 每個部分會列出最多10個專案，符合所指定時間範圍內的部分準則。 您可以按一下元件底部的 [**查看全部**]，來執行記錄查詢，以傳回所有相符的記錄。

![Azure 活動記錄儀表板](media/collect-activity-logs/activity-log-dash.png)

| 視覺效果元件 | 描述 |
| --- | --- |
| Azure 活動記錄項目 | 針對您所選取的日期範圍，顯示最上層 Azure 活動記錄專案記錄總計的橫條圖，並顯示前10個活動呼叫者的清單。 按一下長條圖即可執行 `AzureActivity` 的記錄搜尋。 按一下呼叫者專案來執行記錄搜尋，以傳回該專案的所有活動記錄專案。 |
| 依狀態列出的活動記錄 | 顯示所選日期範圍的 Azure 活動記錄狀態的環圈圖，以及前十筆狀態記錄的清單。 按一下圖表以執行 `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`的記錄查詢。 按一下狀態專案以執行記錄搜尋，以傳回該狀態記錄的所有活動記錄專案。 |
| 依資源列出的活動記錄 | 顯示具有活動記錄的總資源數，並列出每個資源具有記錄計數的前10個資源。 按一下總計區域即可執行 `AzureActivity | summarize AggregatedValue = count() by Resource` 的記錄搜尋，這會顯示解決方案可用的所有 Azure 資源。 按一下資源以執行記錄查詢，以傳回該資源的所有活動記錄。 |
| 依資源提供者列出的活動記錄 | 顯示產生活動記錄的資源提供者總數，並列出前十個。 按一下 [總計] 區域，以執行 `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`的記錄查詢，這會顯示所有的 Azure 資源提供者。 按一下資源提供者以執行記錄查詢，以傳回提供者的所有活動記錄。 |




## <a name="next-steps"></a>後續步驟

* [閱讀平臺記錄的總覽](platform-logs-overview.md)
* [建立診斷設定以將活動記錄傳送至其他目的地](diagnostic-settings.md)
