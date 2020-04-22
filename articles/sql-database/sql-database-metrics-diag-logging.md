---
title: 設定指標與資源紀錄的串流式匯出
description: 瞭解如何配置指標和資源日誌的流式匯出,包括從 Azure SQL 資料庫到首選目標的智慧診斷分析,以存儲有關資源利用率和查詢執行統計資訊的資訊。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/06/2020
ms.openlocfilehash: 288d4e4d0c5faa6bb2b51451fb36bbb6d666c9eb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683186"
---
# <a name="configure-streaming-export-of-azure-sql-database-diagnostic-telemetry"></a>設定 Azure SQL 資料庫診斷遙測的串流式匯出

在本文中,您將瞭解 Azure SQL 資料庫的性能指標和資源日誌,這些指標和資源日誌可以匯出到多個目標之一進行分析。 您將瞭解如何透過 Azure 門戶、PowerShell、Azure CLI、REST API 和 Azure 資源管理器範本配置此診斷遙測的流式匯出。

您還將瞭解可以流式傳輸此診斷遙測的目標以及如何在這些選項中進行選擇。 您的目的地選項包括:

- [紀錄分析與 SQL 分析](#stream-into-sql-analytics)
- [事件中樞](#stream-into-event-hubs)
- [Azure 儲存體](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export-for-azure-sql-database"></a>用於 Azure SQL 資料庫匯出的診斷遙測

可以導出的診斷遙測中最重要的是智慧見解 (SQLInsights) 日誌。 [智慧見解](sql-database-intelligent-insights.md)使用內建智慧通過人工智慧持續監控資料庫使用方式,並檢測導致性能不佳的破壞性事件。 檢測到後,將執行詳細的分析,生成智能見解日誌,並智慧評估問題。 此評估包含資料庫效能問題的根本原因分析，並在可能的情況下提供效能改進建議。 您需要設定此日誌的流式匯出才能查看其內容。

除了流式傳輸智慧見解日誌的匯出外,您還可以匯出各種性能指標和其他 SQL 資料庫日誌。 下表描述了性能指標和資源日誌,您可以配置這些指標和資源日誌,以便流式傳輸匯出到多個目標之一。 此診斷遙測可以配置為單個資料庫、彈性池和池資料庫以及託管實例和實例資料庫。

| 資料庫的診斷遙測 | 單一資料庫和集區資料庫支援 | 託管實例資料庫支援 |
| :------------------- | ----- | ----- |
| [基本指標](#basic-metrics):包含 DTU/CPU 百分比、DTU/CPU 限制、物理數據讀取百分比、日誌寫入百分比、成功/失敗/按防火牆連接阻止、會話百分比、輔助百分比、存儲、存儲百分比和 XTP 存儲百分比。 | 是 | 否 |
| [實例和應用程式高級](#advanced-metrics):包含 tempdb 系統資料庫數據和日誌檔大小以及使用的 tempdb 百分比日誌檔。 | 是 | 否 |
| [查詢存儲運行時統計資訊](#query-store-runtime-statistics):包含有關查詢運行時統計資訊的資訊,如 CPU 使用方式和查詢持續時間統計資訊。 | 是 | 是 |
| [查詢存儲等待統計資訊](#query-store-wait-statistics):包含有關查詢等待統計資訊(查詢等待等待的內容)的資訊,例如 CPU、LOG 和鎖定。 | 是 | 是 |
| [錯誤](#errors-dataset):包含有關資料庫上的 SQL 錯誤的資訊。 | 是 | 是 |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset)：包含和資料庫花費在不同等候類型的等候時間長度有關的資訊。 | 是 | 否 |
| [超時](#time-outs-dataset):包含有關資料庫上的超時的資訊。 | 是 | 否 |
| [塊](#blockings-dataset):包含有關在資料庫上阻止事件的資訊。 | 是 | 否 |
| [死鎖](#deadlocks-dataset):包含有關資料庫上的死鎖事件的資訊。 | 是 | 否 |
| [自動調優](#automatic-tuning-dataset):包含有關資料庫自動調優建議的資訊。 | 是 | 否 |
| [SQLInsights:](#intelligent-insights-dataset)包含對資料庫性能的智慧見解。 若要深入了解，請參閱 [Intelligent Insights](sql-database-intelligent-insights.md)。 | 是 | 是 |

> [!NOTE]
> 無法為**系統資料庫**配置診斷設定,例如主資料庫、msdb 資料庫、模型資料庫、資源和 tempdb 資料庫。

## <a name="streaming-export-destinations"></a>串流式匯出目標

此診斷遙測可以流式傳輸到以下 Azure 資源之一進行分析。

- **[紀錄分析工作區](#stream-into-sql-analytics)**:

  流式傳輸到[日誌分析工作區的數據](../azure-monitor/platform/resource-logs-collect-workspace.md)可由[SQL 分析](../azure-monitor/insights/azure-sql.md)使用。 SQL Analytics 是一種僅雲的監視解決方案,提供對資料庫的智慧監視,包括性能報告、警報和緩解建議。 串流式傳輸到日誌分析工作區的資料可以使用收集的其他監視資料進行分析,還使您能夠利用其他 Azure 監視器功能(如警報和視覺化)
- **[Azure 事件中心](#stream-into-event-hubs)**:

  串流式傳輸到[Azure 事件中心](../azure-monitor/platform/resource-logs-stream-event-hubs.md)的資料提供以下功能:

  - **將日誌流式傳輸到第三方日誌記錄和遙測系統**:將所有指標和資源日誌流式傳輸到單個事件中心,以將日誌數據傳送到第三方 SIEM 或日誌分析工具。
  - **構建自定義遙測和日誌記錄平臺**:事件中心的高度可擴展的發佈-訂閱特性允許您靈活地將指標和資源日誌引入自定義遙測平臺。 有關詳細資訊[,請參閱在 Azure 事件中心上設計和調整全域縮放遙測平臺的尺寸](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。
  - **通過將資料流式傳輸到 Power BI 來查看服務運行狀況**:使用事件中心、流分析和 Power BI 將診斷數據轉換為有關 Azure 服務的近乎即時的見解。 有關此解決方案的詳細資訊[,請參閱流分析和 Power BI:有關流資料的即時分析儀表板](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)。
- **[Azure 儲存](#stream-into-azure-storage)**:

  通過流式傳輸到[Azure 儲存](../azure-monitor/platform/resource-logs-collect-storage.md)的數據,您可以存檔大量診斷遙測,但成本只是前兩個流式處理選項的一小部分。

流式傳輸到這些目標之一的診斷遙測可用於測量資源利用率和查詢執行統計資訊,以便更輕鬆地進行性能監視。

![架構](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>啟用與設定診斷遙測的串流匯出

您可以使用以下方法之一啟用和管理指標和診斷遙測日誌記錄:

- Azure 入口網站
- PowerShell
- Azure CLI
- Azure 監視器 REST API
- Azure Resource Manager 範本

> [!NOTE]
> 要啟用安全遙測的審核日誌流,請參閱在[Azure 監視器日誌和 Azure 事件中心中為資料庫設定審核日誌和審核日誌](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242)。 [Set up auditing for your database](sql-database-auditing.md#setup-auditing)

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>設定診斷遙測的流匯出

您可以使用 Azure 門戶中的 **「診斷設定」** 功能表來啟用和設定診斷遙測流。 此外,您可以使用 PowerShell、Azure CLI、REST [API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)和[資源管理器範本](../azure-monitor/platform/diagnostic-settings-template.md)來配置診斷遙測流。 您可以設置以下目標以流式傳輸診斷遙測:Azure 存儲、Azure 事件中心和 Azure 監視器日誌。

> [!IMPORTANT]
> 默認情況下,未啟用診斷遙測的流式匯出。

選擇以下選項卡之一,用於在 Azure 門戶中配置診斷遙測的流式匯出以及使用 PowerShell 和 Azure CLI 實現相同的腳本的分步指南。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/azure-portal)

### <a name="elastic-pools"></a>彈性集區

您可以設置彈性池資源以收集以下診斷遙測數據:

| 資源 | 監視遙測 |
| :------------------- | ------------------- |
| **彈性池** | [基本指標](sql-database-metrics-diag-logging.md#basic-metrics)包含 eDTU/CPU 百分比、eDTU/CPU 限制、物理數據讀取百分比、日誌寫入百分比、會話百分比、輔助百分比、存儲百分比、存儲百分比、存儲限制和 XTP 儲存百分比。 |

要為彈性池和池資料庫配置診斷遙測流,需要單獨配置每個資料庫:

- 為彈性池啟用診斷遙測流
- 為彈性池中的每個資料庫啟用診斷遙測流

彈性池容器具有其自己的遙測數據,與每個單獨的池資料庫的遙測數據分開。

要為彈性池資源啟用診斷遙測流,請按照以下步驟操作:

1. 轉到 Azure 門戶中的**彈性池**資源。
2. 選取 [診斷設定]****。
3. 如果沒有先前的設定存在，請選取 [開啟診斷]****，或者選取 [編輯設定]**** 來編輯先前的設定。

   ![啟用彈性集區的診斷功能](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. 輸入供您自己參考的設定名稱。
5. 為串流式診斷資料選擇目標資源:**存檔到儲存帳戶**,**或是寄****送到紀錄分析**。
6. 對於日誌分析,選擇 **「設定**」並創建新工作區,選擇 **「創建新工作區**」或「選擇現有工作區」。
7. 選擇彈性池診斷遙測的複選框:**基本**指標。
   ![設定彈性集區的診斷功能](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

8. 選取 [儲存]  。
9. 此外,通過下一節中描述的步驟,為要監視的彈性池中的每個資料庫配置診斷遙測流。

> [!IMPORTANT]
> 除了為彈性池配置診斷遙測外,還需要為彈性池中的每個資料庫配置診斷遙測。

### <a name="single-or-pooled-database"></a>單資料庫或池資料庫

您可以設定單個或池資料庫資源來收集以下診斷遙測:

| 資源 | 監視遙測 |
| :------------------- | ------------------- |
| **單資料庫或池資料庫** | [基本指標](sql-database-metrics-diag-logging.md#basic-metrics)包含 DTU 百分比、使用的 DTU、DTU 限制、CPU 百分比、物理數據讀取百分比、日誌寫入百分比、成功/失敗/阻止防火牆連接、工作階段百分比、輔助百分比、存儲、儲存百分比、XTP 存儲百分比和死鎖。 |

要為單個或池資料庫啟用診斷遙測流,請按照以下步驟操作:

1. 轉到 Azure **SQL 資料庫**資源。
2. 選取 [診斷設定]****。
3. 如果沒有先前的設定存在，請選取 [開啟診斷]****，或者選取 [編輯設定]**** 來編輯先前的設定。 您最多可以創建三個並行連接以流診斷遙測。
4. 選擇 **「新增診斷設定**」以設定診斷資料的並行流到多個資源。

   ![開啟單資料庫與函式資料庫的診斷](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

5. 輸入供您自己參考的設定名稱。
6. 為串流式診斷資料選擇目標資源:**存檔到儲存帳戶**,**或是寄****送到紀錄分析**。
7. 對於此事件的標準監視器,選擇以下複取框進行資料庫診斷紀錄遙測 **:SQLInsights,****自動調優**,**查詢儲存執行時統計資訊**,**查詢儲存等待統計資訊**,**錯誤**,**資料庫等待統計資訊**,**逾時**、**區****塊與死鎖**。
8. 有關基於一分鐘的高級監控體驗,請選擇**基本**指標的複選框。

   ![設定單一、集區式或執行個體資料庫的診斷](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
9. 選取 [儲存]  。
10. 對要監視的每個資料庫重複這些步驟。

> [!TIP]
> 對要監視的每個單個資料庫和池資料庫重複這些步驟。

### <a name="managed-instance"></a>受控執行個體

您可以設定託管實例資源以收集以下診斷遙測資料:

| 資源 | 監視遙測 |
| :------------------- | ------------------- |
| **受控執行個體** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) 包含 V 核心計數、平均 CPU 百分比、IO 要求、讀取/寫入的位元組、保留的儲存空間，以及使用的儲存空間。 |

要為託管實體與實體資料庫設定診斷遙測流,您需要單獨設定每個資料庫:

- 為託管實例啟用診斷遙測流
- 開啟每個實體資料庫的診斷遙測流

託管實例容器具有與每個實例資料庫的遙測數據不同的自己的遙測數據。

要為託管實例資源啟用診斷遙測流,請按照以下步驟操作:

1. 轉到 Azure 門戶中的**託管實例**資源。
2. 選取 [診斷設定]****。
3. 如果沒有先前的設定存在，請選取 [開啟診斷]****，或者選取 [編輯設定]**** 來編輯先前的設定。

   ![啟用受控執行個體的診斷](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. 輸入供您自己參考的設定名稱。
5. 為串流式診斷資料選擇目標資源:**存檔到儲存帳戶**,**或是寄****送到紀錄分析**。
6. 對於日誌分析,選擇 **「設定**」並創建新工作區,選擇 **「創建新工作區**」或使用現有工作區。
7. 選擇實體診斷遙測的複選盒:**資源使用情況統計**。

   ![設定受控執行個體的診斷](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

8. 選取 [儲存]  。
9. 此外,請按照下一節中描述的步驟,為要監視的託管實例中每個實例資料庫配置診斷遙測流。

> [!IMPORTANT]
> 除了配置託管實例的診斷遙測外,還需要為每個實例資料庫配置診斷遙測。

### <a name="instance-database"></a>執行個體資料庫

您可以設定實體資料庫資源來收集以下診斷遙測資料:

| 資源 | 監視遙測 |
| :------------------- | ------------------- |
| **執行個體資料庫** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) 包含 V 核心計數、平均 CPU 百分比、IO 要求、讀取/寫入的位元組、保留的儲存空間，以及使用的儲存空間。 |

要啟用實例資料庫的診斷遙測流,請按照以下步驟操作:

1. 轉到託管實例中的**實例資料庫**資源。
2. 選取 [診斷設定]****。
3. 如果沒有先前的設定存在，請選取 [開啟診斷]****，或者選取 [編輯設定]**** 來編輯先前的設定。
   - 您最多可以創建三 (3) 個並行連接來流診斷遙測。
   - 選取 [+新增診斷設定]**** 建立診斷資料到多個資源的多個平行串流處理。

   ![啟用執行個體資料庫的診斷](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. 輸入供您自己參考的設定名稱。
5. 為串流式診斷資料選擇目標資源:**存檔到儲存帳戶**,**或是寄****送到紀錄分析**。
6. 選擇資料庫診斷遙測的選取框 **:SQLInsights、****查詢儲存執行時統計資訊**,**查詢儲存的統計資訊**與**錯誤**。
   ![設定執行個體資料庫的診斷](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
7. 選取 [儲存]  。
8. 對要監視的每個實例資料庫重複這些步驟。

> [!TIP]
> 對要監視的每個實例資料庫重複這些步驟。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組,但所有後續開發都針對 Az.Sql 模組。 有關這些 cmdlet,請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的參數基本相同。

您可以使用 PowerShell 啟用計量和診斷記錄功能。

- 要在儲存帳戶中啟用指標和資源紀錄的儲存,請使用此命令:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  儲存體帳戶識別碼是目的地儲存體帳戶的資源識別碼。

- 要啟用指標和資源日誌流式傳輸到事件中心,請使用此命令:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Azure 服務匯流排規則識別碼是此格式的字串︰

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- 要啟用將指標和資源紀錄傳送到紀錄分析工作區,請使用以下指令:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- 您可以使用下列命令取得 Log Analytics 工作區的資源識別碼：

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

您可以結合這些參數讓多個輸出選項。

**若要設定多個 Azure 資源**

若要支援多個訂用帳戶，從[使用 PowerShell 啟用 Azure 資源計量記錄](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/)使用 PowerShell 指令碼。

在執行指令碼 `Enable-AzureRMDiagnostics.ps1` 將診斷資料從多個資源傳送至工作區時，提供工作區資源識別碼 \<$WSID\> 做為參數。

- 若要取得您診斷資料目的地的工作區識別碼 \<$WSID\>，請使用下列指令碼：

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  以訂用帳戶識別碼取代 \<subID\>，以資源群組的名稱取代 \<RG_NAME\>，並且以工作區名稱取代 \<WS_NAME\>。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以使用 Azure CLI 啟用計量和診斷記錄功能。

> [!IMPORTANT]
> Azure CLI v1.0 支援啟用診斷日誌記錄的腳本。 Azure CLI v2.0 此時不受支援。

- 要在儲存帳戶中啟用指標和資源紀錄的儲存,請使用以下命令:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  儲存體帳戶識別碼是目的地儲存體帳戶的資源識別碼。

- 要啟用指標和資源日誌流式傳輸到事件中心,請使用以下命令:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  服務匯流排規則識別碼是此格式的字串︰

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- 要啟用將指標和資源紀錄傳送到紀錄分析工作區,請使用以下指令:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

您可以結合這些參數讓多個輸出選項。

---

## <a name="stream-into-sql-analytics"></a>流式傳輸到 SQL 分析

Azure SQL 分析可以使用流式傳輸到日誌分析工作區的 SQL 資料庫指標和資源日誌。 Azure SQL Analytics 是一種雲端解決方案,用於監控單個資料庫、彈性池和池資料庫以及託管實例和實例資料庫在規模和跨多個訂閱的性能。 可協助您收集 Azure SQL Database 效能計量，並以視覺效果方式呈現，而且有內建智慧可以執行效能疑難排解。

![Azure SQL 分析概觀](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>安裝概觀

可以通過執行以下步驟來監視 Azure SQL 資料庫的集合與 Azure SQL 分析:

1. 從 Azure Marketplace 建立 Azure SQL 分析解決方案。
2. 在解決方案中創建日誌分析工作區。
3. 配置資料庫以將診斷遙測流式傳輸到工作區中。

您可以使用 Azure 門戶中的診斷設定選項卡中的內建 **「發送到日誌分析」** 選項來設定此診斷遙測的流式匯出。 還可以透過使用[透過 PowerShell cmdlet、Azure](sql-database-metrics-diag-logging.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry) [CLI、Azure](sql-database-metrics-diag-logging.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry)[監視器 REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)或[資源管理員範本](../azure-monitor/platform/diagnostic-settings-template.md)的診斷設定,將流式處理到日誌分析工作區中。

### <a name="create-an-azure-sql-analytics-resource"></a>建立 Azure SQL 分析資源

1. 在 Azure Marketplace 中搜尋 Azure SQL 分析並加以選取。

   ![在入口網站中搜尋 Azure SQL 分析](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. 在解決方案的 [概觀] 畫面上選取 [建立]****。

3. 在 Azure SQL 分析表單中填入所需的其他資訊：工作區名稱、訂用帳戶、資源群組、位置及定價層。

   ![在入口網站中設定 Azure SQL 分析](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. 選取 [確定]**** 確認，然後選取 [建立]****。

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>將資源設定為紀錄指標和資源紀錄

您需要為單個和池資料庫、彈性池、託管實例和實例資料庫單獨配置診斷遙測流。 配置資源記錄指標位置的最簡單方法是使用 Azure 門戶。 有關詳細步驟,請參閱[配置診斷遙測的流匯出](sql-database-metrics-diag-logging.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry)。

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>使用 Azure SQL 分析進行監視和警示

您可以將 SQL 分析用作分層儀錶板來查看 SQL 資料庫資源。

- 要瞭解如何使用 Azure SQL 分析,請參閱[使用 SQL 分析監視 SQL 資料庫](../log-analytics/log-analytics-azure-sql.md)。
- 要瞭解如何在 SQL Analytics 中設定警報,請參閱[為資料庫、彈性池和託管實例建立警報](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)。

## <a name="stream-into-event-hubs"></a>串流至事件中樞

通過使用 Azure 門戶中的內建**流到事件中心**選項,可以將 SQL 資料庫指標和資源日誌流式傳輸到事件中心。 還可以透過使用透過 PowerShell cmdlet、Azure CLI 或 Azure 監視器 REST API 的診斷設定啟用服務總線規則 ID。 請確定事件中樞與您的資料庫和伺服器位於相同的區域。

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>如何處理事件中心的指標和資源紀錄

所選的資料串流到事件中樞之後，您很快就能啟用進階監視案例。 事件中樞是作為事件管線的大門。 資料收集到事件中樞之後，這些資料可以透過即時分析提供者或儲存體配接器來轉換和儲存。 事件中樞會讓事件串流的產生從這些事件的取用分離。 如此一來，事件消費者可以在自己的排程存取事件。 如需事件中樞的詳細資訊，請參閱：

- [什麼是 Azure 事件中心?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [開始使用事件中心](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

您可以在事件中樞使用串流的計量：

- **以將熱路徑資料串流式傳輸到 Power BI 來檢視服務執行狀況**

  您可以使用事件中樞、串流分析和 PowerBI，輕鬆快速地將計量和診斷資料轉換為 Azure 服務上的深入解析。 如需如何設定事件中樞、使用串流分析處理資料，以及使用 PowerBI 作為輸出的概觀，請參閱[串流分析和 Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)。

- **將紀錄串流式傳輸到第三方紀錄記錄和遙測流**

  通過使用事件中心流,您可以將指標和資源日誌放入各種第三方監視和日誌分析解決方案中。

- **建置自訂遙測和紀錄記錄平臺**

  您是否已建立自訂的遙測平台，或正考慮建置一個？ 事件中心的高度可擴展的發佈訂閱特性允許您靈活地引入指標和資源日誌。 請參閱 [Dan Rosanova 指南，以在全球級別的遙測平台中使用事件中樞](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="stream-into-azure-storage"></a>串流到 Azure 儲存體中

通過使用 Azure 門戶中的內置**存檔到儲存帳戶**選項,可以在 Azure 存儲中存儲指標和資源日誌。 還可以透過使用透過 PowerShell cmdlet、Azure CLI 或 Azure 監視器 REST API 的診斷設定啟用儲存。

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>儲存帳號中的指標和資源紀錄的架構

設置指標和資源日誌收集後,會在第一行數據可用時選擇的存儲帳戶中創建存儲容器。 Blob 的結構為：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

或者，形式更簡單：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如,基本指標的 blob 名稱可能是:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

儲存彈性集區的資料所用的 Blob 名稱，例如：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>資料保留原則和價格

如果您選取事件中樞或儲存體帳戶，您可以指定保留原則。 此原則會刪除早於選取時間期間的資料。 如果您指定 Log Analytics，則保留原則取決於所選的定價層。 在這種情況下，所提供的免費資料擷取單位可以每個月免費監視多個資料庫。 任何超過可用單位的診斷遙測消耗都可能產生成本。

> [!IMPORTANT]
> 具有更重工作負載的活動資料庫比空閒資料庫引入的數據更多。 有關詳細資訊,請參閱[日誌分析定價](https://azure.microsoft.com/pricing/details/monitor/)。

如果使用 Azure SQL 分析,則可以通過在 Azure SQL 分析的導航功能表上選擇**OMS 工作區**,然後選擇 **「使用方式**」和 **「估計成本**」來監視數據引入消耗。

## <a name="metrics-and-logs-available"></a>可用的計量和記錄

本文的這一部分記錄了可用於單個資料庫、池資料庫、彈性池、託管實例和實例資料庫的監視遙測。 在 SQL Analytics 中收集的監視遙測可用於使用[Azure 監視器日誌查詢](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)語言進行您自己的自定義分析和應用程式開發。

### <a name="basic-metrics"></a>基本指標

有關按資源進行基本指標的詳細資訊,請參閱下表。

> [!NOTE]
> 基本指標選項以前稱為"全部指標」。 所做的更改僅對命名,並且對監視的指標沒有更改。 啟動此更改是為了以後引入其他指標類別。

#### <a name="basic-metrics-for-elastic-pools"></a>彈性池的基本指標

|**Resource**|**計量**|
|---|---|
|彈性集區|eDTU 百分比、使用的 eDTU、eDTU 限制、CPU 百分比、實體資料讀取百分比、記錄寫入百分比、工作階段百分比、背景工作百分比、儲存體、儲存體百分比、儲存體限制、XTP 儲存體百分比 |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>單個資料庫和池資料庫的基本指標

|**Resource**|**計量**|
|---|---|
|單資料庫和池資料庫|DTU 百分比、使用的 DTU、DTU 限制、CPU 百分比、實體資料讀取百分比、記錄寫入百分比、成功/失敗/防火牆封鎖的連線、工作階段百分比、背景工作百分比、儲存體、儲存體百分比、XTP 儲存體百分比和死結 |

### <a name="advanced-metrics"></a>進階指標

有關高級指標的詳細資訊,請參閱下表。

|**計量**|**計量顯示名稱**|**說明**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|SQL 伺服器行程核心百分比|SQL Server 進程的 CPU 使用率百分比,由作業系統測量。|
|sqlserver_process_memory_percent<sup>1</sup> |SQL 伺服器行程記憶體百分比|SQL Server 進程的記憶體使用百分比,由作業系統測量。|
|tempdb_data_size<sup>2</sup>| Tempdb 資料檔案大小千位元組 |Tempdb 資料檔大小千位元組。|
|tempdb_log_size<sup>2</sup>| Tempdb 紀錄檔案大小千位元組 |Tempdb 紀錄檔大小千位元組。|
|tempdb_log_used_percent<sup>2</sup>| 使用 Tempdb 百分比紀錄 |使用百分比日誌。|

<sup>1</sup>此指標可用於使用 vCore 購買模型的資料庫,該模型具有 2 個 vCore 和更高版本,或者 200 DTU 和更高版本可用於基於 DTU 的採購模型。 

<sup>2</sup>此指標可用於使用 vCore 購買模型的資料庫,該模型具有 2 個 vCore 和更高版本,或者 200 DTU 和更高版本可用於基於 DTU 的採購模型。 此指標當前不適用於超大規模資料庫或數據倉庫。

### <a name="basic-logs"></a>基本紀錄

下表記錄了可用於所有日誌的遙測的詳細資訊。 請參閱[支持的診斷遙測數據](#diagnostic-telemetry-for-export-for-azure-sql-database),以瞭解特定資料庫風格支援哪些日誌 - Azure SQL 單、池或實例資料庫。

#### <a name="resource-usage-stats-for-managed-instances"></a>託管實例的資源使用方式統計資訊

|屬性|描述|
|---|---|
|TenantId|您的租用戶識別碼 |
|SourceSystem|一律：Azure|
|TimeGenerated [UTC]|記錄檔記錄時的時間戳記 |
|類型|一律：AzureDiagnostics |
|ResourceProvider|資源提供者名稱。 一律：MICROSOFT.SQL |
|類別|類別名稱。 一律：ResourceUsageStats |
|資源|資源名稱 |
|ResourceType|資源類型名稱。 一律：MANAGEDINSTANCES |
|SubscriptionId|資料庫的訂用帳戶 GUID |
|ResourceGroup|資料庫的資源群組名稱 |
|LogicalServerName_s|受控執行個體的名稱 |
|ResourceId|資源 URI |
|SKU_s|受控執行個體產品 SKU |
|virtual_core_count_s|可用的虛擬核心數目 |
|avg_cpu_percent_s|CPU 百分比平均 |
|reserved_storage_mb_s|受控執行個體上的保留儲存體容量 |
|storage_space_used_mb_s|受控執行個體上已使用儲存體 |
|io_requests_s|IOPS 計數 |
|io_bytes_read_s|讀取的 IOPS 位元組 |
|io_bytes_written_s|寫入的 IOPS 位元組 |

#### <a name="query-store-runtime-statistics"></a>查詢存放區執行階段統計資料

|屬性|描述|
|---|---|
|TenantId|您的租用戶識別碼 |
|SourceSystem|一律：Azure |
|TimeGenerated [UTC]|記錄檔記錄時的時間戳記 |
|類型|一律：AzureDiagnostics |
|ResourceProvider|資源提供者名稱。 一律：MICROSOFT.SQL |
|類別|類別名稱。 一律：QueryStoreRuntimeStatistics |
|OperationName|作業名稱。 一律：QueryStoreRuntimeStatisticsEvent |
|資源|資源名稱 |
|ResourceType|資源類型名稱。 一律：SERVERS/DATABASES |
|SubscriptionId|資料庫的訂用帳戶 GUID |
|ResourceGroup|資料庫的資源群組名稱 |
|LogicalServerName_s|資料庫伺服器的名稱 |
|ElasticPoolName_s|資料庫的彈性集區名稱 (如果有) |
|DatabaseName_s|資料庫名稱 |
|ResourceId|資源 URI |
|query_hash_s|查詢雜湊 |
|query_plan_hash_s|查詢計劃雜湊 |
|statement_sql_handle_s|陳述式 SQL 控制代碼 |
|interval_start_time_d|間隔的開始 datetimeoffset 刻度數目從 1900-1-1 起 |
|interval_end_time_d|間隔的結束 datetimeoffset 刻度數目從 1900-1-1 起 |
|logical_io_writes_d|邏輯 IO 寫入總數 |
|max_logical_io_writes_d|每次執行的邏輯 IO 寫入次數上限 |
|physical_io_reads_d|實體 IO 讀取總數 |
|max_physical_io_reads_d|每次執行的邏輯 IO 讀取次數上限 |
|logical_io_reads_d|邏輯 IO 讀取總數 |
|max_logical_io_reads_d|每次執行的邏輯 IO 讀取次數上限 |
|execution_type_d|執行類型 |
|count_executions_d|查詢的執行次數 |
|cpu_time_d|查詢所耗用的總 CPU 時間 (毫秒) |
|max_cpu_time_d|單次執行可耗用的 CPU 時間上限 (毫秒) |
|dop_d|平行處理原則的程度總和 |
|max_dop_d|單次執行所用之平行處理原則的最大程度 |
|rowcount_d|傳回的資料列總數 |
|max_rowcount_d|單次執行傳回的資料列數目上限 |
|query_max_used_memory_d|使用的記憶體總量 (KB) |
|max_query_max_used_memory_d|單次執行所使用的記憶體數量上限 (KB) |
|duration_d|總執行時間 (毫秒) |
|max_duration_d|單次執行的執行時間上限 |
|num_physical_io_reads_d|實體讀取總數 |
|max_num_physical_io_reads_d|每次執行的實體讀取次數上限 |
|log_bytes_used_d|使用的記錄檔位元組總數 |
|max_log_bytes_used_d|每次執行所使用的記錄檔位元組數量上限 |
|query_id_d|查詢存放區中查詢的識別碼 |
|plan_id_d|查詢存放區中計劃的識別碼 |

瞭解有關[查詢存儲運行時統計資訊數據](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)的更多。

#### <a name="query-store-wait-statistics"></a>查詢存放區等候統計資料

|屬性|描述|
|---|---|
|TenantId|您的租用戶識別碼 |
|SourceSystem|一律：Azure |
|TimeGenerated [UTC]|記錄檔記錄時的時間戳記 |
|類型|一律：AzureDiagnostics |
|ResourceProvider|資源提供者名稱。 一律：MICROSOFT.SQL |
|類別|類別名稱。 一律：QueryStoreWaitStatistics |
|OperationName|作業名稱。 一律：QueryStoreWaitStatisticsEvent |
|資源|資源名稱 |
|ResourceType|資源類型名稱。 一律：SERVERS/DATABASES |
|SubscriptionId|資料庫的訂用帳戶 GUID |
|ResourceGroup|資料庫的資源群組名稱 |
|LogicalServerName_s|資料庫伺服器的名稱 |
|ElasticPoolName_s|資料庫的彈性集區名稱 (如果有) |
|DatabaseName_s|資料庫名稱 |
|ResourceId|資源 URI |
|wait_category_s|等候的類別 |
|is_parameterizable_s|查詢是否可參數化 |
|statement_type_s|陳述式類型 |
|statement_key_hash_s|陳述式索引鍵雜湊 |
|exec_type_d|執行類型 |
|total_query_wait_time_ms_d|特定等候類別的查詢等候總時間 |
|max_query_wait_time_ms_d|特定等候類別個別執行時的查詢等候時間上限 |
|query_param_type_d|0 |
|query_hash_s|查詢存放區中的查詢雜湊 |
|query_plan_hash_s|查詢存放區中的查詢計劃 |
|statement_sql_handle_s|查詢存放區中的陳述式控制代碼 |
|interval_start_time_d|間隔的開始 datetimeoffset 刻度數目從 1900-1-1 起 |
|interval_end_time_d|間隔的結束 datetimeoffset 刻度數目從 1900-1-1 起 |
|count_executions_d|查詢的執行計數 |
|query_id_d|查詢存放區中查詢的識別碼 |
|plan_id_d|查詢存放區中計劃的識別碼 |

深入了解[查詢存放區等候統計資料](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)。

#### <a name="errors-dataset"></a>錯誤資料集

|屬性|描述|
|---|---|
|TenantId|您的租用戶識別碼 |
|SourceSystem|一律：Azure |
|TimeGenerated [UTC]|記錄檔記錄時的時間戳記 |
|類型|一律：AzureDiagnostics |
|ResourceProvider|資源提供者名稱。 一律：MICROSOFT.SQL |
|類別|類別名稱。 一律：Errors |
|OperationName|作業名稱。 一律：ErrorEvent |
|資源|資源名稱 |
|ResourceType|資源類型名稱。 一律：SERVERS/DATABASES |
|SubscriptionId|資料庫的訂用帳戶 GUID |
|ResourceGroup|資料庫的資源群組名稱 |
|LogicalServerName_s|資料庫伺服器的名稱 |
|ElasticPoolName_s|資料庫的彈性集區名稱 (如果有) |
|DatabaseName_s|資料庫名稱 |
|ResourceId|資源 URI |
|訊息|純文字的錯誤訊息 |
|user_defined_b|錯誤是否為使用者定義的位元 |
|error_number_d|錯誤碼 |
|Severity|錯誤的嚴重性 |
|state_d|錯誤的狀態 |
|query_hash_s|失敗查詢的查詢雜湊 (如果有) |
|query_plan_hash_s|失敗查詢的查詢計劃雜湊 (如果有) |

深入了解 [SQL Server 錯誤訊息](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15)。

#### <a name="database-wait-statistics-dataset"></a>資料庫等候統計資料資料集

|屬性|描述|
|---|---|
|TenantId|您的租用戶識別碼 |
|SourceSystem|一律：Azure |
|TimeGenerated [UTC]|記錄檔記錄時的時間戳記 |
|類型|一律：AzureDiagnostics |
|ResourceProvider|資源提供者名稱。 一律：MICROSOFT.SQL |
|類別|類別名稱。 一律：DatabaseWaitStatistics |
|OperationName|作業名稱。 一律：DatabaseWaitStatisticsEvent |
|資源|資源名稱 |
|ResourceType|資源類型名稱。 一律：SERVERS/DATABASES |
|SubscriptionId|資料庫的訂用帳戶 GUID |
|ResourceGroup|資料庫的資源群組名稱 |
|LogicalServerName_s|資料庫伺服器的名稱 |
|ElasticPoolName_s|資料庫的彈性集區名稱 (如果有) |
|DatabaseName_s|資料庫名稱 |
|ResourceId|資源 URI |
|wait_type_s|等候類型的名稱 |
|start_utc_date_t [UTC]|測量的時段開始時間 |
|end_utc_date_t [UTC]|測量的時段結束時間 |
|delta_max_wait_time_ms_d|每次執行的等候時間上限 |
|delta_signal_wait_time_ms_d|訊號總等候時間 |
|delta_wait_time_ms_d|期間內的總等候時間 |
|delta_waiting_tasks_count_d|等候工作數目 |

深入了解[資料庫等候統計資料](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)。

#### <a name="time-outs-dataset"></a>逾時資料集

|屬性|描述|
|---|---|
|TenantId|您的租用戶識別碼 |
|SourceSystem|一律：Azure |
|TimeGenerated [UTC]|記錄檔記錄時的時間戳記 |
|類型|一律：AzureDiagnostics |
|ResourceProvider|資源提供者名稱。 一律：MICROSOFT.SQL |
|類別|類別名稱。 一律：Timeouts |
|OperationName|作業名稱。 一律：TimeoutEvent |
|資源|資源名稱 |
|ResourceType|資源類型名稱。 一律：SERVERS/DATABASES |
|SubscriptionId|資料庫的訂用帳戶 GUID |
|ResourceGroup|資料庫的資源群組名稱 |
|LogicalServerName_s|資料庫伺服器的名稱 |
|ElasticPoolName_s|資料庫的彈性集區名稱 (如果有) |
|DatabaseName_s|資料庫名稱 |
|ResourceId|資源 URI |
|error_state_d|錯誤狀態碼 |
|query_hash_s|查詢雜湊 (如果有) |
|query_plan_hash_s|查詢計劃雜湊 (如果有) |

#### <a name="blockings-dataset"></a>封鎖資料集

|屬性|描述|
|---|---|
|TenantId|您的租用戶識別碼 |
|SourceSystem|一律：Azure |
|TimeGenerated [UTC]|記錄檔記錄時的時間戳記 |
|類型|一律：AzureDiagnostics |
|ResourceProvider|資源提供者名稱。 一律：MICROSOFT.SQL |
|類別|類別名稱。 一律：Blocks |
|OperationName|作業名稱。 一律：BlockEvent |
|資源|資源名稱 |
|ResourceType|資源類型名稱。 一律：SERVERS/DATABASES |
|SubscriptionId|資料庫的訂用帳戶 GUID |
|ResourceGroup|資料庫的資源群組名稱 |
|LogicalServerName_s|資料庫伺服器的名稱 |
|ElasticPoolName_s|資料庫的彈性集區名稱 (如果有) |
|DatabaseName_s|資料庫名稱 |
|ResourceId|資源 URI |
|lock_mode_s|查詢所使用的鎖定模式 |
|resource_owner_type_s|鎖定擁有者 |
|blocked_process_filtered_s|已封鎖的處理序報告 XML |
|duration_d|鎖定的持續時間 (微秒) |

#### <a name="deadlocks-dataset"></a>死結 (Deadlock) 資料集

|屬性|描述|
|---|---|
|TenantId|您的租用戶識別碼 |
|SourceSystem|一律：Azure |
|TimeGenerated [UTC] |記錄檔記錄時的時間戳記 |
|類型|一律：AzureDiagnostics |
|ResourceProvider|資源提供者名稱。 一律：MICROSOFT.SQL |
|類別|類別名稱。 一律：Deadlocks |
|OperationName|作業名稱。 一律：DeadlockEvent |
|資源|資源名稱 |
|ResourceType|資源類型名稱。 一律：SERVERS/DATABASES |
|SubscriptionId|資料庫的訂用帳戶 GUID |
|ResourceGroup|資料庫的資源群組名稱 |
|LogicalServerName_s|資料庫伺服器的名稱 |
|ElasticPoolName_s|資料庫的彈性集區名稱 (如果有) |
|DatabaseName_s|資料庫名稱 |
|ResourceId|資源 URI |
|deadlock_xml_s|死結報表 XML |

#### <a name="automatic-tuning-dataset"></a>自動調整資料集

|屬性|描述|
|---|---|
|TenantId|您的租用戶識別碼 |
|SourceSystem|一律：Azure |
|TimeGenerated [UTC]|記錄檔記錄時的時間戳記 |
|類型|一律：AzureDiagnostics |
|ResourceProvider|資源提供者名稱。 一律：MICROSOFT.SQL |
|類別|類別名稱。 一律：AutomaticTuning |
|資源|資源名稱 |
|ResourceType|資源類型名稱。 一律：SERVERS/DATABASES |
|SubscriptionId|資料庫的訂用帳戶 GUID |
|ResourceGroup|資料庫的資源群組名稱 |
|LogicalServerName_s|資料庫伺服器的名稱 |
|LogicalDatabaseName_s|資料庫名稱 |
|ElasticPoolName_s|資料庫的彈性集區名稱 (如果有) |
|DatabaseName_s|資料庫名稱 |
|ResourceId|資源 URI |
|RecommendationHash_s|自動調整建議的唯一雜湊 |
|OptionName_s|自動調整作業 |
|Schema_s|資料庫結構描述 |
|Table_s|受影響的資料表 |
|IndexName_s|索引名稱 |
|IndexColumns_s|資料行名稱 |
|IncludedColumns_s|包含的資料行 |
|EstimatedImpact_s|自動調整建議 JSON 的預估影響 |
|Event_s|自動調整事件的類型 |
|Timestamp_t|上一次更新的時間戳記 |

#### <a name="intelligent-insights-dataset"></a>Intelligent Insights 資料集

深入了解 [Intelligent Insights 記錄格式](sql-database-intelligent-insights-use-diagnostics-log.md)。

## <a name="next-steps"></a>後續步驟

若要了解如何啟用記錄，並了解各種 Azure 服務支援的計量和記錄類別，請參閱：

- [Microsoft Azure 中的度量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Azure 平台記錄概觀](../azure-monitor/platform/platform-logs-overview.md)

若要了解事件中樞，請閱讀：

- [Azure 事件中樞是什麼？](../event-hubs/event-hubs-what-is-event-hubs.md)
- [開始使用事件中心](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

要瞭解如何根據日誌分析的遙測數據設置警報,請參閱:

- [為 SQL 資料庫與託管實範例建立警示](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
