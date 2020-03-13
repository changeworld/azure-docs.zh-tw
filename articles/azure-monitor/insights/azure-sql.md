---
title: Azure 監視器中的 Azure SQL 分析解決方案 |Microsoft Docs
description: Azure SQL 分析解決方案可協助您管理 Azure SQL 資料庫
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275460"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>使用 Azure SQL 分析來監視 Azure SQL Database (預覽)

![Azure SQL 分析符號](./media/azure-sql/azure-sql-symbol.png)

Azure SQL 分析是一種先進的雲端監視解決方案，可讓您在單一視圖中大規模監視所有 Azure SQL 資料庫的效能，以及跨多個訂用帳戶。 Azure SQL 分析會使用內建的智慧功能進行效能疑難排解，以收集關鍵效能計量並加以視覺化。

藉由使用這些收集的計量，您可以建立自訂監視規則和警示。 Azure SQL 分析可協助您找出應用程式堆疊每一層的問題。 它會使用 Azure 診斷計量和 Azure 監視器 views 來呈現單一 Log Analytics 工作區中所有 Azure SQL 資料庫的相關資料。 Azure 監視器可協助您收集、相互關聯並視覺化結構化和非結構化資料。

如需使用 Azure SQL Analytics 解決方案，以及一般使用案例的實際操作概觀，請觀看內嵌影片：

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>連接的來源

Azure SQL 分析是僅限雲端的監視解決方案，可支援所有 Azure SQL 資料庫的診斷遙測串流。 因為 Azure SQL 分析不會使用代理程式連線到 Azure 監視器，所以不支援監視內部部署或虛擬機器中裝載的 SQL Server。

| 連接的來源 | 支援 | 描述 |
| --- | --- | --- |
| [診斷設定](../platform/diagnostic-settings.md) | **是** | Azure 計量和記錄資料會直接傳送至 Azure 監視器的記錄。 |
| [Azure 儲存體帳戶](../platform/collect-azure-metrics-logs.md) | 否 | Azure 監視器不會從儲存體帳戶讀取資料。 |
| [Windows 代理程式](../platform/agent-windows.md) | 否 | Azure SQL 分析不使用直接 Windows 代理程式。 |
| [Linux 代理程式](../learn/quick-collect-linux-computer.md) | 否 | Azure SQL 分析不使用直接 Linux 代理程式。 |
| [System Center Operations Manager 管理群組](../platform/om-agents.md) | 否 | Azure SQL 分析不會使用從 Operations Manager 代理程式到 Azure 監視器的直接連接。 |

## <a name="azure-sql-analytics-options"></a>Azure SQL 分析選項

下表概述兩個 Azure SQL 分析儀表板版本的支援選項，一個用於單一和集區資料庫和彈性集區，另一個用於受控實例和實例資料庫。

| Azure SQL 分析選項 | 描述 | 單一和集區資料庫和彈性集區支援 | 受控實例和實例資料庫支援 |
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

使用[從方案庫新增 Azure 監視器解決方案](../../azure-monitor/insights/solutions.md)中所述的程式，將 Azure SQL 分析（預覽）新增至您的 Log Analytics 工作區。

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>設定 Azure SQL 資料庫以串流診斷遙測

在您的工作區中建立 Azure SQL 分析解決方案之後，您需要設定您想要監視的**每個**資源，以將其診斷遙測串流至 Azure SQL 分析。 遵循此頁面上的詳細指示：

- 為 Azure SQL 資料庫啟用 Azure 診斷，以[將診斷遙測串流至 Azure SQL 分析](../../sql-database/sql-database-metrics-diag-logging.md)。

上述頁面同時提供啟用支援將單一 Azure SQL 分析工作區作為單一窗口來監視多個 Azure 訂用帳戶的操作指示。

## <a name="using-azure-sql-analytics"></a>使用 Azure SQL 分析

當您將 Azure SQL 分析新增至工作區時，[Azure SQL 分析] 磚會加入至您的工作區，並顯示在 [總覽] 中。 選取 [查看摘要] 連結以載入磚內容。

![Azure SQL 分析摘要磚](./media/azure-sql/azure-sql-sol-tile-01.png)

載入之後，磚會顯示單一和集區資料庫、彈性集區、受控實例和受控實例資料庫的數目，Azure SQL 分析接收診斷遙測。

![Azure SQL 分析圖格](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL 分析提供兩個不同的視圖，一個用於監視單一資料庫和集區資料庫和彈性集區，另一個則用於監視受管理的實例和實例資料庫。

若要查看單一和集區資料庫和彈性集區的 Azure SQL 分析監視儀表板，請按一下磚的上半部。 若要查看受控實例和實例資料庫的 Azure SQL 分析監視 儀表板，請按一下磚的下半部。

### <a name="viewing-azure-sql-analytics-data"></a>檢視 Azure SQL 分析資料

儀表板包含透過不同檢視方塊監視之所有資料庫的概觀。 若要讓不同的觀點正常執行，您必須在要串流至 Log Analytics 工作區的 SQL 資源上啟用適當的計量或記錄。

如果某些計量或記錄未串流到 Azure 監視器，Azure SQL 分析中的磚不會填入監視資訊。

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>單一和集區資料庫和彈性集區視圖

選取適用於資料庫的 [Azure SQL 分析] 圖格後，便會顯示監視儀表板。

![Azure SQL 分析概觀](./media/azure-sql/azure-sql-sol-overview.png)

選取任何磚，以便在特定的檢視方塊中開啟向下鑽研報表。 一旦選取檢視方塊，向下鑽研報表隨即開啟。

![Azure SQL 分析逾時](./media/azure-sql/azure-sql-sol-metrics.png)

此視圖中的每個觀點都會提供訂用帳戶、伺服器、彈性集區和資料庫層級的摘要。 此外，每個檢視方塊都會在右側顯示檢視方塊專屬的報表。 從清單中選取訂用帳戶、伺服器、集區或資料庫可繼續往下鑽研。

### <a name="managed-instance-and-instances-databases-view"></a>受控實例和實例資料庫檢視

選取適用於資料庫的 [Azure SQL 分析] 圖格後，便會顯示監視儀表板。

![Azure SQL 分析概觀](./media/azure-sql/azure-sql-sol-overview-mi.png)

選取任何磚，以便在特定的檢視方塊中開啟向下鑽研報表。 一旦選取檢視方塊，向下鑽研報表隨即開啟。

選取 [受控實例] 視圖，會顯示受控實例使用率的詳細資料、其包含的資料庫，以及在實例上執行之查詢的遙測。

![Azure SQL 分析逾時](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights 報表

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) 可讓您了解所有 Azure SQL 資料庫的效能情況。 收集的所有 Intelligent Insights 都可以透過 Insights 檢視方塊視覺化及存取。

![Azure SQL 分析見解](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>彈性集區和資料庫報表

彈性集區和資料庫都有自己的特定報表，可顯示在指定的時間內為資源收集的所有資料。

![Azure SQL 分析資料庫](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL 彈性集區](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>查詢報表

透過查詢持續時間和查詢等候的角度，您可以將任何查詢的效能與查詢報表相互關聯。 此報表會比較不同資料庫上的查詢效能，並可讓您輕鬆地找出所選查詢執行速度良好與緩慢的資料庫。

![Azure SQL 分析查詢](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>權限

若要使用 Azure SQL 分析，使用者需至少在 Azure 中取得「讀者」角色使用權限。 不過此角色並無法讓使用者取得觀看查詢文字，或執行任何自動調整動作的權限。 Azure 中更寬鬆的角色，可讓您充分利用 Azure SQL 分析，包括擁有者、參與者、SQL DB 參與者或 SQL Server 參與者。 您也可能會考慮在入口網站中建立自訂角色，並將使用權限設定為僅限 Azure SQL 分析，不可存取管理其他資源。

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

Azure SQL 分析中的自動化警示是以撰寫 Log Analytics 查詢為基礎，它會在符合條件時觸發警示。 請在下列幾個有關 Log Analytics 查詢的範例中尋找可在 Azure SQL 分析中設定警示的範例。

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
> - 設定此警示的必要條件是受監視的資料庫會將基本計量串流至 Azure SQL 分析。
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
> - 設定此警示的必要條件是受監視的資料庫會將基本計量串流至 Azure SQL 分析。
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
> - 設定此警示的必要條件是受監視的資料庫會將基本計量串流至 Azure SQL 分析。
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
> - 設定此警示的必要條件是受監視的資料庫會將 SQLInsights 診斷記錄串流至 Azure SQL 分析。
> - 這項查詢需要將警示規則設定為利用和 alert_run_interval 相同的頻率來執行，以避免產生重複結果。 請將此規則設定為會在有查詢結果 (> 0 個結果) 時引發警示。
> - 自訂 alert_run_interval 來指定時間範圍，以檢查設定為將 SQLInsights 記錄串流至 Azure SQL 分析的資料庫是否發生條件。
> - 自訂 insights_string 以擷取深入解析根本原因分析文字的輸出。 這是在 Azure SQL 分析的 UI 中顯示的相同文字，您可以從現有的深入解析中使用。 或者，您也可以使用下列查詢來查看訂用帳戶所產生的所有深入解析文字。 請使用查詢的輸出來搜集可供對深入解析設定警示的不同字串。

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>建立受控實例的警示

#### <a name="managed-instance-storage-is-above-90"></a>受控實例儲存體高於90%

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
> - 設定此警示的必要條件是，監視受控實例已啟用 ResourceUsageStats 記錄的串流以 Azure SQL 分析。
> - 此查詢需要設定警示規則，以便在從查詢存在結果（> 0 結果）時引發警示，表示該條件存在於受控實例上。 輸出是受控實例上的儲存體耗用量百分比。

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>過去1小時內的受控實例 CPU 平均耗用量高於95%

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
> - 設定此警示的必要條件是受監視的受控實例已啟用 ResourceUsageStats 記錄的串流以 Azure SQL 分析。
> - 此查詢需要設定警示規則，以便在從查詢存在結果（> 0 結果）時引發警示，表示該條件存在於受控實例上。 輸出是受控實例上所定義期間的平均 CPU 使用率百分比耗用量。

### <a name="pricing"></a>價格

雖然 Azure SQL 分析可免費使用，但超過每個月所配置的免費資料提取單位數，才會耗用診斷遙測，請參閱[Log Analytics 定價](https://azure.microsoft.com/pricing/details/monitor)。 所提供的免費資料擷取單位可讓您每個月免費監視多個資料庫。 較繁重工作負載的更多作用中資料庫會內嵌更多資料與閒置資料庫。 您可以在 Azure SQL 分析的導覽功能表上選取 [OMS 工作區]，然後選取 [使用量和估計成本]，輕鬆地監視 Azure SQL 分析中的資料內嵌耗用量。

## <a name="next-steps"></a>後續步驟

- 使用 Azure 監視器中的[記錄查詢](../log-query/log-query-overview.md)來查看詳細的 Azure SQL 資料。
- [建立您自己的儀表板](../learn/tutorial-logs-dashboards.md)來顯示 Azure SQL 資料。
- 在特定的 Azure SQL 事件發生時[建立警示](../platform/alerts-overview.md)。
