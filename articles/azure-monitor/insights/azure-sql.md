---
title: Azure 監視器中的 Azure SQL 分析解決方案 |微軟文檔
description: Azure SQL 分析解決方案可協助您管理 Azure SQL 資料庫
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275460"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>使用 Azure SQL 分析來監視 Azure SQL Database (預覽)

![Azure SQL 分析符號](./media/azure-sql/azure-sql-symbol.png)

Azure SQL 分析是一種高級雲監視解決方案，用於大規模監視所有 Azure SQL 資料庫的性能，並在單個視圖中跨多個訂閱進行監視。 Azure SQL 分析使用用於性能故障排除的內置智慧收集和視覺化關鍵性能指標。

通過使用這些收集的指標，您可以創建自訂監視規則和警報。 Azure SQL 分析可説明您識別應用程式堆疊每一層的問題。 它使用 Azure 診斷指標和 Azure 監視器視圖在單個日誌分析工作區中顯示有關所有 Azure SQL 資料庫的資料。 Azure 監視器可説明您收集、關聯和視覺化結構化和非結構化資料。

如需使用 Azure SQL Analytics 解決方案，以及一般使用案例的實際操作概觀，請觀看內嵌影片：

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>連接的來源

Azure SQL 分析是一種僅雲監視解決方案，支援對所有 Azure SQL 資料庫的診斷遙測流。 由於 Azure SQL 分析不使用代理連接到 Azure 監視器，因此不支援監視本地託管或在虛擬機器中託管的 SQL Server。

| 連接的來源 | 支援 | 描述 |
| --- | --- | --- |
| [診斷設定](../platform/diagnostic-settings.md) | **是** | Azure 指標和日誌資料由 Azure 直接發送到 Azure 監視器日誌。 |
| [Azure 存儲帳戶](../platform/collect-azure-metrics-logs.md) | 否 | Azure 監視器不會從存儲帳戶讀取資料。 |
| [Windows 代理程式](../platform/agent-windows.md) | 否 | Azure SQL 分析不使用直接 Windows 代理。 |
| [Linux 代理程式](../learn/quick-collect-linux-computer.md) | 否 | Azure SQL 分析不使用直接 Linux 代理。 |
| [System Center Operations Manager 管理群組](../platform/om-agents.md) | 否 | Azure SQL 分析不使用從操作管理器代理到 Azure 監視器的直接連接。 |

## <a name="azure-sql-analytics-options"></a>Azure SQL 分析選項

下表概述了 Azure SQL Analytics 儀表板的兩個版本的支援選項，一個用於單個和池化資料庫和彈性池，另一個用於託管實例和實例資料庫。

| Azure SQL 分析選項 | 描述 | 支援單一和池資料庫和彈性池 | 託管實例和實例資料庫支援 |
| --- | ------- | ----- | ----- |
| 資源 (依類型) | 可計算所有受監視資源的檢視方塊。 | 是 | 是 |
| 深入解析 | 可透過階層的方式，向下鑽研至 Intelligent Insights 乃至效能。 | 是 | 是 |
| Errors | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 錯誤。 | 是 | 是 |
| 逾時 | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 逾時。 | 是 | 否 |
| 封鎖 | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 封鎖。 | 是 | 否 |
| 資料庫等候 | 可透過階層的方式，向下鑽研至資料庫層級的 SQL 等候統計資料。 包含總等候時間及每種等候類型等候時間的摘要。 |是 | 否 |
| 查詢持續時間 | 可透過階層的方式，向下鑽研至查詢執行統計資料，例如查詢持續時間、CPU 使用量、資料 IO 使用量、記錄 IO 使用量。 | 是 | 是 |
| 查詢等候 | 可透過階層的方式，依等候類別，向下鑽研至查詢等候統計資料。 | 是 | 是 |

## <a name="configuration"></a>組態

使用解決方案[庫中添加 Azure 監視器解決方案](../../azure-monitor/insights/solutions.md)中描述的過程將 Azure SQL 分析（預覽）添加到日誌分析工作區。

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>將 Azure SQL 資料庫配置為流診斷遙測

在工作區中創建 Azure SQL 分析解決方案後，需要配置要監視**的每個**資源，以便將其診斷遙測流到 Azure SQL 分析。 遵循此頁面上的詳細指示：

- 為 Azure SQL 資料庫啟用 Azure 診斷，以[將診斷遙測串流至 Azure SQL 分析](../../sql-database/sql-database-metrics-diag-logging.md)。

上述頁面同時提供啟用支援將單一 Azure SQL 分析工作區作為單一窗口來監視多個 Azure 訂用帳戶的操作指示。

## <a name="using-azure-sql-analytics"></a>使用 Azure SQL 分析

將 Azure SQL 分析添加到工作區時，Azure SQL 分析磁貼將添加到工作區，並顯示在"概述"中。 選擇"查看摘要"連結以載入磁貼內容。

![Azure SQL 分析摘要磁貼](./media/azure-sql/azure-sql-sol-tile-01.png)

載入後，磁貼將顯示 Azure SQL 分析從中接收診斷遙測的單個和池資料庫、彈性池、託管實例和託管實例資料庫的數量。

![Azure SQL 分析圖格](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics 提供了兩個單獨的視圖， 一個用於監視單個資料庫和池資料庫和彈性池， 另一個用於監視託管實例和實例資料庫的視圖。

要查看單個資料庫和池資料庫和彈性池的 Azure SQL 分析監視儀表板，請按一下磁貼的上半部分。 要查看託管實例和實例資料庫的 Azure SQL 分析監視儀表板，請按一下磁貼的下半部分。

### <a name="viewing-azure-sql-analytics-data"></a>檢視 Azure SQL 分析資料

儀表板包含透過不同檢視方塊監視之所有資料庫的概觀。 要使不同視角正常工作，必須啟用正確的指標或 SQL 資源上的日誌，以便資料流到日誌分析工作區。

如果某些指標或日誌未資料流到 Azure 監視器中，則 Azure SQL 分析中的磁貼不會填充監視資訊。

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>單個和池資料庫和彈性池視圖

選取適用於資料庫的 [Azure SQL 分析] 圖格後，便會顯示監視儀表板。

![Azure SQL 分析概觀](./media/azure-sql/azure-sql-sol-overview.png)

選取任何磚，以便在特定的檢視方塊中開啟向下鑽研報表。 一旦選取檢視方塊，向下鑽研報表隨即開啟。

![Azure SQL 分析逾時](./media/azure-sql/azure-sql-sol-metrics.png)

此視圖中的每個透視圖都提供訂閱、伺服器、彈性池和資料庫級別的摘要。 此外，每個檢視方塊都會在右側顯示檢視方塊專屬的報表。 從清單中選取訂用帳戶、伺服器、集區或資料庫可繼續往下鑽研。

### <a name="managed-instance-and-instances-databases-view"></a>託管實例和實例資料庫檢視

選取適用於資料庫的 [Azure SQL 分析] 圖格後，便會顯示監視儀表板。

![Azure SQL 分析概觀](./media/azure-sql/azure-sql-sol-overview-mi.png)

選取任何磚，以便在特定的檢視方塊中開啟向下鑽研報表。 一旦選取檢視方塊，向下鑽研報表隨即開啟。

選擇託管實例視圖，顯示有關託管實例利用率、它包含的資料庫的詳細資訊以及跨實例執行的查詢的遙測資料。

![Azure SQL 分析逾時](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights 報表

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) 可讓您了解所有 Azure SQL 資料庫的效能情況。 收集的所有 Intelligent Insights 都可以透過 Insights 檢視方塊視覺化及存取。

![Azure SQL 分析見解](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>彈性池和資料庫報告

彈性池和資料庫都有其自己的特定報告，這些報表顯示在指定時間內為資源收集的所有資料。

![Azure SQL 分析資料庫](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL 彈性集區](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>查詢報表

通過查詢持續時間和查詢等待透視，可以通過查詢報表關聯任何查詢的性能。 此報表會比較不同資料庫上的查詢效能，並可讓您輕鬆地找出所選查詢執行速度良好與緩慢的資料庫。

![Azure SQL 分析查詢](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>權限

若要使用 Azure SQL 分析，使用者需至少在 Azure 中取得「讀者」角色使用權限。 不過此角色並無法讓使用者取得觀看查詢文字，或執行任何自動調整動作的權限。 Azure 中允許最大程度使用 Azure SQL 分析的更多寬鬆角色是擁有者、參與者、SQL DB 參與者或 SQL 伺服器參與者。 您也可能會考慮在入口網站中建立自訂角色，並將使用權限設定為僅限 Azure SQL 分析，不可存取管理其他資源。

### <a name="creating-a-custom-role-in-portal"></a>在入口網站中建立自訂角色

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

我們了解到某些組織會在 Azure 中實施嚴格的使用權限控制，因此請參閱下列 PowerShell 指令碼，它可以在 Azure 入口網站中建立自訂角色「SQL 分析監視人員」，此角色僅擁有使用完整 Azure SQL 分析所需之最低限度讀取和寫入使用權限。

在下列指令碼中，請將 “{SubscriptionId}" 取代為您的 Azure 訂用帳戶識別碼，接著以擁有者或參與者角色身分登入 Azure 並執行指令碼。

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

新角色建立後，請將這個角色指派給需要 Azure SQL 分析使用權限的每一位使用者。

## <a name="analyze-data-and-create-alerts"></a>分析資料並建立警示

Azure SQL 分析中的資料分析以 [Log Analytics 語言](../log-query/get-started-queries.md)為基礎，提供您自訂查詢和報告功能。 請參閱[可用的計量和記錄](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)，內有針對收集自資料庫資源可自訂查詢的資料的說明。

Azure SQL 分析中的自動警報基於編寫日誌分析查詢，該查詢在滿足條件時觸發警報。 在日誌分析查詢上找到以下幾個示例，可在 Azure SQL 分析中設置警報。

### <a name="creating-alerts-for-azure-sql-database"></a>建立 Azure SQL Database 的警示

您可以使用來自 Azure SQL Database 資源的資料，輕鬆[建立警示](../platform/alerts-metric.md)。 以下是您可以搭配記錄警示使用的一些實用[記錄查詢](../log-query/log-query-overview.md)：

#### <a name="high-cpu-on-azure-sql-database"></a>Azure SQL Database 上的高 CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - 設置此警報的預先要求是受監視的資料庫將基本指標資料流到 Azure SQL 分析。
> - 請將 MetricName 值 cpu_percent 更換為 dtu_consumption_percent，即可改為取得高 DTU 結果。

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Azure SQL Database 彈性集區上的高 CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - 設置此警報的預先要求是受監視的資料庫將基本指標資料流到 Azure SQL 分析。
> - 請將 MetricName 值 cpu_percent 更換為 dtu_consumption_percent，即可改為取得高 DTU 結果。

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>過去 1 小時的平均 Azure SQL Database 儲存體高於 95%

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - 設置此警報的預先要求是受監視的資料庫將基本指標資料流到 Azure SQL 分析。
> - 這項查詢需要將警示規則設定為會在有查詢結果 (> 0 個結果) 時引發警示，這表示某些資料庫上有此情況。 其輸出中會列出所定義 time_range 內高於 storage_threshold 的資料庫資源。
> - 其輸出中會列出所定義 time_range 內高於 storage_threshold 的資料庫資源。

#### <a name="alert-on-intelligent-insights"></a>針對 Intelligent insights 的警示

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - 設置此警報的預先要求是受監視的資料庫將 SQLInsights 診斷日誌資料流到 Azure SQL 分析。
> - 這項查詢需要將警示規則設定為利用和 alert_run_interval 相同的頻率來執行，以避免產生重複結果。 請將此規則設定為會在有查詢結果 (> 0 個結果) 時引發警示。
> - 自訂alert_run_interval指定時間範圍，以檢查該條件是否發生在配置為將 SQLInsights 日誌資料流到 Azure SQL 分析的資料庫上。
> - 自訂 insights_string 以擷取深入解析根本原因分析文字的輸出。 這與 Azure SQL 分析 UI 中顯示的文本相同，可以從現有見解中使用。 或者，您也可以使用下列查詢來查看訂用帳戶所產生的所有深入解析文字。 請使用查詢的輸出來搜集可供對深入解析設定警示的不同字串。

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>為託管實例創建警報

#### <a name="managed-instance-storage-is-above-90"></a>託管實例存儲超過 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - 設置此警報的預先要求是，受監視的實例已啟用了 Azure SQL 分析的資源使用方式統計日誌流。
> - 此查詢需要設置警報規則，以便當查詢存在結果（> 0 結果）時觸發警報，表示託管實例上存在此情況。 輸出是託管實例上的存儲百分比消耗。

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>託管實例 CPU 平均消耗量在過去 1 小時中超過 95%

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - 設置此警報的預先要求是受監視的託管實例已啟用了 Azure SQL 分析的資源使用方式統計日誌流。
> - 此查詢需要設置警報規則，以便當查詢存在結果（> 0 結果）時觸發警報，表示託管實例上存在此情況。 輸出是託管實例上定義期間的平均 CPU 利用率百分比消耗。

### <a name="pricing"></a>定價

雖然 Azure SQL 分析可免費使用，但使用每月分配的免費資料引入單位的診斷遙測資料，請參閱[日誌分析定價](https://azure.microsoft.com/pricing/details/monitor)。 所提供的免費資料擷取單位可讓您每個月免費監視多個資料庫。 具有更重工作負載的更活動資料庫可引入更多資料，而不是使用空閒資料庫。 通過在 Azure SQL 分析的導航功能表上選擇 OMS 工作區，然後選擇"使用方式"和"估計成本"，可以輕鬆地監視 Azure SQL 分析中的資料引入消耗。

## <a name="next-steps"></a>後續步驟

- 使用 Azure 監視器中的[日誌查詢](../log-query/log-query-overview.md)查看詳細的 Azure SQL 資料。
- [建立您自己的儀表板](../learn/tutorial-logs-dashboards.md)來顯示 Azure SQL 資料。
- 在特定的 Azure SQL 事件發生時[建立警示](../platform/alerts-overview.md)。
