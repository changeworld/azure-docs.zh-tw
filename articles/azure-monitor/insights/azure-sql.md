---
title: Azure 監視器中的 Azure SQL 分析解決方案 |Microsoft Docs
description: Azure SQL 分析解決方案可協助您管理 Azure SQL 資料庫
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 09/19/2020
ms.reviewer: carlrab
ms.openlocfilehash: 0015138f4da9f66e2f9148e468dd1b5543ae0c4b
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397074"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>使用 Azure SQL 分析來監視 Azure SQL Database (預覽)

![Azure SQL 分析符號](./media/azure-sql/azure-sql-symbol.png)

Azure SQL 分析是先進的雲端監視解決方案，可在單一視圖中大規模監視所有 Azure SQL 資料庫的效能，並跨多個訂用帳戶進行監視。 Azure SQL 分析會使用內建的智慧效能疑難排解，來收集關鍵效能計量並將其視覺化。

藉由使用這些收集的計量，您可以建立自訂監視規則和警示。 Azure SQL 分析可協助您找出應用程式堆疊中每個層級的問題。 它會使用 Azure 診斷計量以及 Azure 監視器 views，在單一 Log Analytics 工作區中呈現所有 Azure SQL 資料庫的相關資料。 Azure 監視器可協助您收集、相互關聯並視覺化結構化和非結構化資料。

如需使用 Azure SQL Analytics 解決方案，以及一般使用案例的實際操作概觀，請觀看內嵌影片：

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>連接的來源

Azure SQL 分析是僅限雲端的監視解決方案，支援所有 Azure SQL 資料庫的診斷遙測串流。 由於 Azure SQL 分析不會使用代理程式連線到 Azure 監視器，因此不支援監視內部部署或虛擬機器中裝載的 SQL Server。

| 連接的來源 | 支援 | 描述 |
| --- | --- | --- |
| [診斷設定](../platform/diagnostic-settings.md) | **是** | Azure 會將 azure 計量和記錄資料直接傳送給 Azure 監視器的記錄。 |
| [Azure 儲存體帳戶](../platform/resource-logs.md#send-to-log-analytics-workspace) | 否 | Azure 監視器不會從儲存體帳戶讀取資料。 |
| [Windows 代理程式](../platform/agent-windows.md) | 否 | Azure SQL 分析不會使用 Direct Windows 代理程式。 |
| [Linux 代理程式](../learn/quick-collect-linux-computer.md) | 否 | Azure SQL 分析不會使用直接 Linux 代理程式。 |
| [System Center Operations Manager 管理群組](../platform/om-agents.md) | 否 | Azure SQL 分析不會使用從 Operations Manager 代理程式到 Azure 監視器的直接連接。 |

## <a name="azure-sql-analytics-options"></a>Azure SQL 分析選項

下表概述 Azure SQL 分析儀表板的兩個版本支援的選項，一個適用于 Azure SQL Database，另一個適用于 Azure SQL 受控執行個體資料庫。

| Azure SQL 分析選項 | 描述 | SQL Database 支援 | SQL 受控執行個體支援 |
| --- | ------- | ----- | ----- |
| 資源 (依類型) | 可計算所有受監視資源的檢視方塊。 | 是 | 是 |
| 深入解析 | 可透過階層的方式，向下鑽研至 Intelligent Insights 乃至效能。 | 是 | 是 |
| Errors | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 錯誤。 | 是 | 是 |
| 逾時 | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 逾時。 | 是 | 否 |
| 封鎖 | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 封鎖。 | 是 | 否 |
| 資料庫等候 | 可透過階層的方式，向下鑽研至資料庫層級的 SQL 等候統計資料。 包含總等候時間及每種等候類型等候時間的摘要。 |是 | 否 |
| 查詢持續時間 | 可透過階層的方式，向下鑽研至查詢執行統計資料，例如查詢持續時間、CPU 使用量、資料 IO 使用量、記錄 IO 使用量。 | 是 | 是 |
| 查詢等候 | 可透過階層的方式，依等候類別，向下鑽研至查詢等候統計資料。 | 是 | 是 |

## <a name="configuration"></a>設定

使用 [從方案庫新增 Azure 監視器解決方案](./solutions.md) 中所述的程式，將 Azure SQL 分析 (Preview) 新增至 Log Analytics 工作區。

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>設定 Azure SQL Database 以串流診斷遙測

在您的工作區中建立 Azure SQL 分析解決方案之後，您必須設定要監視的 **每個** 資源，以將其診斷遙測串流至 Azure SQL 分析。 遵循此頁面上的詳細指示：

- 為您的資料庫啟用 Azure 診斷，以將 [診斷遙測串流至 Azure SQL 分析](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)。

上述頁面同時提供啟用支援將單一 Azure SQL 分析工作區作為單一窗口來監視多個 Azure 訂用帳戶的操作指示。

## <a name="using-azure-sql-analytics"></a>使用 Azure SQL 分析

當您將 Azure SQL 分析新增至工作區時，Azure SQL 分析圖格會新增至您的工作區，並顯示在 [總覽] 中。 選取 [視圖摘要] 連結以載入磚內容。

![Azure SQL 分析摘要磚](./media/azure-sql/azure-sql-sol-tile-01.png)

載入之後，圖格會顯示 SQL Database 中的資料庫和彈性集區數目，以及 SQL 受控執行個體中的實例和實例資料庫，Azure SQL 分析接收診斷遙測。

![Azure SQL 分析圖格](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL 分析提供兩個不同的視圖，一個用於監視 SQL Database，另一個則是監視 SQL 受控執行個體的觀點。

若要查看 SQL Database 的 Azure SQL 分析監視儀表板，請按一下磚的上半部。 若要查看 SQL 受控執行個體的 Azure SQL 分析監視儀表板，請按一下磚的下半部。

### <a name="viewing-azure-sql-analytics-data"></a>檢視 Azure SQL 分析資料

儀表板包含透過不同檢視方塊監視之所有資料庫的概觀。 若要讓不同的透視圖運作，您必須在 SQL 資源上啟用適當的計量或記錄，以串流至 Log Analytics 工作區。

如果某些計量或記錄未串流到 Azure 監視器中，Azure SQL 分析中的圖格就不會填入監視資訊。

### <a name="sql-database-view"></a>SQL Database 視圖

選取適用於資料庫的 [Azure SQL 分析] 圖格後，便會顯示監視儀表板。

![顯示監視儀表板的螢幕擷取畫面。](./media/azure-sql/azure-sql-sol-overview.png)

選取任何磚，以便在特定的檢視方塊中開啟向下鑽研報表。 一旦選取檢視方塊，向下鑽研報表隨即開啟。

![顯示向下切入至特定觀點之報表的螢幕擷取畫面。](./media/azure-sql/azure-sql-sol-metrics.png)

此視圖中的每個觀點都會提供訂用帳戶、伺服器、彈性集區和資料庫層級的摘要。 此外，每個檢視方塊都會在右側顯示檢視方塊專屬的報表。 從清單中選取訂用帳戶、伺服器、集區或資料庫可繼續往下鑽研。

### <a name="sql-managed-instance-view"></a>SQL 受控執行個體 view

選取適用於資料庫的 [Azure SQL 分析] 圖格後，便會顯示監視儀表板。

![Azure SQL 分析概觀](./media/azure-sql/azure-sql-sol-overview-mi.png)

選取任何磚，以便在特定的檢視方塊中開啟向下鑽研報表。 一旦選取檢視方塊，向下鑽研報表隨即開啟。

選取 [SQL 受控執行個體] 視圖，會顯示在受控實例上執行的查詢實例使用率、實例資料庫和遙測資料的詳細資料。

![Azure SQL 分析逾時](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights 報表

Azure SQL Database [Intelligent Insights](../../azure-sql/database/intelligent-insights-overview.md) 可讓您了解所有 Azure SQL 資料庫的效能情況。 收集的所有 Intelligent Insights 都可以透過 Insights 檢視方塊視覺化及存取。

![Azure SQL 分析見解](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>彈性集區和資料庫報表

彈性集區和資料庫都有自己的特定報表，可顯示在指定時間內針對資源收集的所有資料。

![Azure SQL 分析資料庫](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL 彈性集區](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>查詢報表

透過查詢持續時間和查詢等候的觀點，您可以透過查詢報告將任何查詢的效能相互關聯。 此報表會比較不同資料庫上的查詢效能，並可讓您輕鬆地找出所選查詢執行速度良好與緩慢的資料庫。

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

Azure SQL 分析中的資料分析以 [Log Analytics 語言](../log-query/get-started-queries.md)為基礎，提供您自訂查詢和報告功能。 請參閱[可用的計量和記錄](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available)，內有針對收集自資料庫資源可自訂查詢的資料的說明。

Azure SQL 分析中的自動化警示是以撰寫 Log Analytics 查詢為基礎，而該查詢會在符合條件時觸發警示。 您可以在下列幾個有關 Log Analytics 查詢的範例中，找到可在 Azure SQL 分析中設定警示的範例。

### <a name="creating-alerts-for-azure-sql-database"></a>建立 Azure SQL Database 的警示

您可以使用來自 Azure SQL Database 資源的資料，輕鬆[建立警示](../platform/alerts-metric.md)。 以下是您可以搭配記錄警示使用的一些實用[記錄查詢](../log-query/log-query-overview.md)：

#### <a name="high-cpu"></a>高 CPU

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

#### <a name="high-cpu-on-elastic-pools"></a>彈性集區上的高 CPU

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

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>過去1小時內平均高於95% 的儲存體

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

> [!IMPORTANT]
> 如果資料庫執行良好且未產生任何 Intelligent Insights，此查詢將會失敗，並出現錯誤訊息：無法解析名為 ' rootCauseAnalysis_s ' 的純量運算式。 對於資料庫沒有智慧型深入解析的所有案例，都必須要有這種行為。

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
> - 自訂 alert_run_interval 來指定時間範圍，以檢查在設定為將 SQLInsights 記錄串流至 Azure SQL 分析的資料庫上是否發生此情況。
> - 自訂 insights_string 以擷取深入解析根本原因分析文字的輸出。 這是在 Azure SQL 分析的 UI 中顯示的文字，您可以從現有的深入解析中使用這些文字。 或者，您也可以使用下列查詢來查看訂用帳戶所產生的所有深入解析文字。 請使用查詢的輸出來搜集可供對深入解析設定警示的不同字串。

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>建立 SQL 受控執行個體的警示

#### <a name="storage-is-above-90"></a>儲存體超過90%

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
> - 設定此警示的必要條件是受監視的受控實例已啟用 ResourceUsageStats 記錄的串流，以 Azure SQL 分析。
> - 此查詢需要設定警示規則，以在 ( # A0 0 結果) 自查詢中有結果時引發警示，以指出該條件存在於受控實例上。 輸出是受控實例上的儲存體百分比耗用量。

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>過去1小時內的 CPU 平均耗用量高於95%

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
> - 設定此警示的必要條件是受監視的受控實例已啟用 ResourceUsageStats 記錄的串流，以 Azure SQL 分析。
> - 此查詢需要設定警示規則，以在 ( # A0 0 結果) 自查詢中有結果時引發警示，以指出該條件存在於受控實例上。 輸出是受控實例上所定義期間內的平均 CPU 使用率百分比耗用量。

### <a name="pricing"></a>定價

雖然 Azure SQL 分析可免費使用，但在每個月所配置之資料內嵌的免費單位上使用的診斷遙測使用量也適用，請參閱 [Log Analytics 定價](https://azure.microsoft.com/pricing/details/monitor)。 所提供的免費資料擷取單位可讓您每個月免費監視多個資料庫。 具有較繁重工作負載的更多作用中資料庫會內嵌更多資料與閒置的資料庫。 您可以藉由在 Azure SQL 分析的導覽功能表中選取 [OMS 工作區]，然後選取 [使用量和估計成本]，輕鬆地監視 Azure SQL 分析中的資料內嵌耗用量。

## <a name="next-steps"></a>後續步驟

- 使用 Azure 監視器中的 [記錄查詢](../log-query/log-query-overview.md) ，以查看詳細的 Azure SQL 資料。
- [建立您自己的儀表板](../learn/tutorial-logs-dashboards.md)來顯示 Azure SQL 資料。
- 在特定的 Azure SQL 事件發生時[建立警示](../platform/alerts-overview.md)。

