---
title: 設定串流匯出計量和資源記錄
description: 瞭解如何設定串流匯出計量和資源記錄，包括從 Azure SQL Database 和 Azure SQL 受控執行個體到您所選目的地的智慧型診斷分析，以儲存資源使用率和查詢執行統計資料的相關資訊。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/06/2020
ms.openlocfilehash: efb99e23466e4615dfa1f4a429addcd8c4ac68f5
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085594"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>設定 Azure SQL Database 和 SQL 受控執行個體診斷遙測的串流匯出
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

在本文中，您將瞭解 Azure SQL Database 的效能計量和資源記錄檔，您可以將其匯出至數個目的地的其中一個以進行分析。 您將瞭解如何透過 Azure 入口網站、PowerShell、Azure CLI、REST API 和 Azure Resource Manager 範本，設定此診斷遙測的串流匯出。

您也將瞭解可以串流此診斷遙測的目的地，以及如何在這些選擇中進行選擇。 您的目的地選項包括：

- [Log Analytics 和 SQL 分析](#stream-into-sql-analytics)
- [事件中樞](#stream-into-event-hubs)
- [Azure 儲存體](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>匯出的診斷遙測

在您可以匯出的診斷遙測中，最重要的是 Intelligent Insights （SQLInsights）記錄檔。 [Intelligent Insights](intelligent-insights-overview.md)使用內建智慧功能，透過人工智慧持續監視資料庫使用量，並偵測導致效能不佳的干擾性事件。 一旦偵測到，就會執行詳細的分析，產生具有問題智慧型評估的 Intelligent Insights 記錄。 此評估包含資料庫效能問題的根本原因分析，並在可能的情況下提供效能改進建議。 您必須設定此記錄檔的串流匯出以查看其內容。

除了串流 Intelligent Insights 記錄的匯出之外，您也可以匯出各種效能計量和額外的資料庫記錄。 下表說明您可以設定的效能計量和資源記錄，以便將串流匯出至數個目的地的其中一個。 可以針對單一資料庫、彈性集區和集區資料庫，以及受控實例和實例資料庫，設定此診斷遙測。

| 資料庫的診斷遙測 | 支援 Azure SQL Database | Azure SQL 受控執行個體支援 |
| :------------------- | ----- | ----- |
| [基本計量](#basic-metrics)：包含 DTU/cpu 百分比、DTU/cpu 限制、實體資料讀取百分比、記錄寫入百分比、成功/失敗/防火牆連線、會話百分比、背景工作百分比、儲存體、儲存體百分比和 XTP 儲存體百分比。 | 是 | 否 |
| [實例和應用程式 Advanced](#advanced-metrics)：包含 tempdb 系統資料庫資料和記錄檔大小，以及所使用的 tempdb 百分比記錄檔。 | 是 | 否 |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics)：包含查詢執行時間統計資料的相關資訊，例如 CPU 使用率和查詢持續時間統計資料。 | 是 | 是 |
| [QueryStoreWaitStatistics](#query-store-wait-statistics)：包含查詢等候統計資料的相關資訊（您的查詢等候的時間），例如 CPU、記錄和鎖定。 | 是 | 是 |
| [錯誤](#errors-dataset)：包含有關資料庫上 SQL 錯誤的資訊。 | 是 | 是 |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset)：包含和資料庫花費在不同等候類型的等候時間長度有關的資訊。 | 是 | 否 |
| [超時](#time-outs-dataset)：包含資料庫上之超時的相關資訊。 | 是 | 否 |
| [封鎖](#blockings-dataset)：包含有關資料庫上封鎖事件的資訊。 | 是 | 否 |
| [鎖死](#deadlocks-dataset)：包含有關資料庫上之鎖死事件的資訊。 | 是 | 否 |
| [AutomaticTuning](#automatic-tuning-dataset)：包含資料庫之自動調整建議的相關資訊。 | 是 | 否 |
| [SQLInsights](#intelligent-insights-dataset)：包含資料庫的效能 Intelligent Insights。 若要深入了解，請參閱 [Intelligent Insights](intelligent-insights-overview.md)。 | 是 | 是 |

> [!NOTE]
> 無法為**系統資料庫**設定診斷設定，例如 master、msdb、model、resource 和 tempdb 資料庫。

## <a name="streaming-export-destinations"></a>串流匯出目的地

此診斷遙測可以串流處理至下列其中一個 Azure 資源進行分析。

- **[Log Analytics 工作區](#stream-into-sql-analytics)**：

  串流至[Log Analytics 工作區](../../azure-monitor/platform/resource-logs-collect-workspace.md)的資料可由[SQL 分析](../../azure-monitor/insights/azure-sql.md)使用。 SQL 分析是僅限雲端的監視解決方案，可提供您資料庫的智慧型監視，其中包含效能報告、警示和緩和建議。 串流至 Log Analytics 工作區的資料可以利用所收集的其他監視資料進行分析，也能讓您利用其他 Azure 監視器功能，例如警示和視覺效果
- **[Azure 事件中樞](#stream-into-event-hubs)**：

  資料流程處理至[Azure 事件中樞](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)的資料提供下列功能：

  - **將記錄串流至協力廠商記錄和遙測系統**：將您所有的計量和資源記錄串流至單一事件中樞，以將記錄資料傳送至協力廠商 SIEM 或 log analytics 工具。
  - **建立自訂遙測和記錄平臺**：事件中樞的高擴充性發佈訂閱特性，可讓您將計量和資源記錄彈性地內嵌到自訂遙測平臺中。 如需詳細資訊，請參閱[Azure 事件中樞上的全域調整遙測平臺的設計和調整大小](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。
  - 藉**由將資料串流至 Power BI 來查看服務健全狀況**：使用事件中樞、串流分析和 Power BI，將您的診斷資料轉換為 Azure 服務上近乎即時的深入解析。 如需此解決方案的詳細資訊，請參閱[串流分析和 Power BI：串流資料的即時分析儀表板](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)。
- **[Azure 儲存體](#stream-into-azure-storage)**：

  串流至[Azure 儲存體](../../azure-monitor/platform/resource-logs-collect-storage.md)的資料可讓您封存大量的診斷遙測，以取得前兩個數據流選項成本的一小部分。

此診斷遙測串流至其中一個目的地，可用來測量資源使用率和查詢執行統計資料，以更輕鬆地監視效能。

![架構](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>啟用和設定診斷遙測的串流匯出

您可以使用下列其中一種方法來啟用及管理計量和診斷遙測記錄：

- Azure 入口網站
- PowerShell
- Azure CLI
- Azure 監視器 REST API
- Azure Resource Manager 範本

> [!NOTE]
> 若要啟用安全性遙測的審核記錄串流，請參閱[在 Azure 監視器記錄檔和 Azure 事件中樞中](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242)[設定資料庫的審核](../../sql-database/sql-database-auditing.md#setup-auditing)和審核記錄。

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>設定診斷遙測的串流匯出

您可以使用 [Azure 入口網站中的 [**診斷設定**] 功能表來啟用和設定診斷遙測的串流。 此外，您可以使用 PowerShell、Azure CLI、 [REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)和[Resource Manager 範本](../../azure-monitor/platform/diagnostic-settings-template.md)來設定診斷遙測的串流。 您可以設定下列目的地以串流診斷遙測： Azure 儲存體、Azure 事件中樞和 Azure 監視器記錄檔。

> [!IMPORTANT]
> 診斷遙測的資料流程匯出預設不會啟用。

選取下列其中一個索引標籤，以取得在 Azure 入口網站中設定診斷遙測串流匯出的逐步指引，以及針對使用 PowerShell 和 Azure CLI 完成相同工作的腳本。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Azure SQL Database 中的彈性集區

您可以設定彈性集區資源，以收集下列診斷遙測：

| 資源 | 監視遙測 |
| :------------------- | ------------------- |
| **彈性集區** | [基本計量](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics)包含 EDTU/cpu 百分比、EDTU/cpu 限制、實體資料讀取百分比、記錄寫入百分比、會話百分比、背景工作百分比、儲存體、儲存體百分比、儲存體限制，以及 XTP 儲存體百分比。 |

若要設定彈性集區和集區資料庫的診斷遙測串流，您需要分別個別設定：

- 啟用彈性集區診斷遙測的串流處理
- 針對彈性集區中的每個資料庫啟用診斷遙測的串流處理

彈性集區容器有自己的遙測，與每個個別集區資料庫的遙測分開。

若要啟用彈性集區資源診斷遙測的串流處理，請遵循下列步驟：

1. 移至 Azure 入口網站中的**彈性集**區資源。
2. 選取 [診斷設定]。
3. 如果沒有先前的設定存在，請選取 [開啟診斷]****，或者選取 [編輯設定]**** 來編輯先前的設定。

   ![啟用彈性集區的診斷功能](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. 輸入供您自己參考的設定名稱。
5. 選取串流診斷資料的目的地資源： [封存**至儲存體帳戶**]、[**串流至事件中樞**] 或 [**傳送至 Log Analytics**]。
6. 針對 log analytics，選取 [ **+ 建立新的工作區**]，或選取現有的工作區來**設定**並建立新的工作區。
7. 選取彈性集區診斷遙測的核取方塊： [**基本**計量]。
   ![設定彈性集區的診斷功能](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. 選取 [儲存]。
9. 此外，請遵循下一節所述的步驟，為您要監視的彈性集區中的每個資料庫設定診斷遙測的串流。

> [!IMPORTANT]
> 除了設定彈性集區的診斷遙測以外，您還需要為彈性集區中的每個資料庫設定診斷遙測。

### <a name="databases-in-azure-sql-database"></a>Azure SQL Database 中的資料庫

您可以設定資料庫資源來收集下列診斷遙測：

| 資源 | 監視遙測 |
| :------------------- | ------------------- |
| **單一或集區資料庫** | [基本計量](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics)包含 dtu 百分比、使用的 DTU、dtu 限制、CPU 百分比、實體資料讀取百分比、記錄寫入百分比、成功/失敗/防火牆連線、會話百分比、背景工作百分比、儲存體、儲存體百分比、XTP 儲存體百分比和鎖死。 |

若要啟用單一或集區資料庫之診斷遙測的串流處理，請遵循下列步驟：

1. 移至 Azure **SQL database**資源。
2. 選取 [診斷設定]。
3. 如果沒有先前的設定存在，請選取 [開啟診斷]****，或者選取 [編輯設定]**** 來編輯先前的設定。 您可以建立最多三個平行連線來串流處理診斷遙測。
4. 選取 [**新增診斷設定**]，以設定將診斷資料平行串流處理至多個資源。

   ![啟用單一和集區資料庫的診斷功能](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. 輸入供您自己參考的設定名稱。
6. 選取串流診斷資料的目的地資源： [封存**至儲存體帳戶**]、[**串流至事件中樞**] 或 [**傳送至 Log Analytics**]。
7. 針對標準的事件監視體驗，請選取下列資料庫診斷記錄遙測的核取方塊： **SQLInsights**、 **AutomaticTuning**、 **QueryStoreRuntimeStatistics**、 **QueryStoreWaitStatistics**、**錯誤**、 **DatabaseWaitStatistics**、**超時**、**區塊**和**鎖死**。
8. 如需以一分鐘為基礎的先進監視體驗，請選取 [**基本**計量] 的核取方塊。

   ![設定 Azure SQL Database 的診斷](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. 選取 [儲存]。
10. 針對您想要監視的每個資料庫重複這些步驟。

> [!TIP]
> 針對您想要監視的每個單一和集區資料庫重複這些步驟。

### <a name="instances-in-azure-sql-managed-instance"></a>Azure SQL 受控執行個體中的實例

您可以設定受控實例資源，以收集下列診斷遙測：

| 資源 | 監視遙測 |
| :------------------- | ------------------- |
| **受控實例** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) 包含 V 核心計數、平均 CPU 百分比、IO 要求、讀取/寫入的位元組、保留的儲存空間，以及使用的儲存空間。 |

若要設定受控實例和實例資料庫的診斷遙測串流，您必須分別設定每個資料：

- 啟用受控實例診斷遙測的串流處理
- 針對每個實例資料庫啟用診斷遙測的串流處理

受控實例容器有自己的遙測，與每個實例資料庫的遙測分開。

若要啟用受控實例資源診斷遙測的串流處理，請遵循下列步驟：

1. 移至 Azure 入口網站中的**受控實例**資源。
2. 選取 [診斷設定]。
3. 如果沒有先前的設定存在，請選取 [開啟診斷]****，或者選取 [編輯設定]**** 來編輯先前的設定。

   ![啟用受控執行個體的診斷](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. 輸入供您自己參考的設定名稱。
5. 選取串流診斷資料的目的地資源： [封存**至儲存體帳戶**]、[**串流至事件中樞**] 或 [**傳送至 Log Analytics**]。
6. 針對 log analytics，選取 [ **+ 建立新的工作區**]，或使用現有的工作區，以選取 [**設定**並建立新的工作區]。
7. 選取 [實例診斷遙測： **ResourceUsageStats**] 的核取方塊。

   ![設定受控執行個體的診斷](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. 選取 [儲存]。
9. 此外，請遵循下一節所述的步驟，為您要監視的受控實例中的每個實例資料庫設定診斷遙測的串流。

> [!IMPORTANT]
> 除了設定受控實例的診斷遙測以外，您還需要為每個實例資料庫設定診斷遙測。

### <a name="databases-in-azure-sql-managed-instance"></a>Azure SQL 受控執行個體中的資料庫

您可以設定實例資料庫資源，以收集下列診斷遙測：

| 資源 | 監視遙測 |
| :------------------- | ------------------- |
| **執行個體資料庫** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) 包含 V 核心計數、平均 CPU 百分比、IO 要求、讀取/寫入的位元組、保留的儲存空間，以及使用的儲存空間。 |

若要啟用實例資料庫診斷遙測的串流處理，請遵循下列步驟：

1. 移至受控實例中的**實例資料庫**資源。
2. 選取 [診斷設定]。
3. 如果沒有先前的設定存在，請選取 [開啟診斷]****，或者選取 [編輯設定]**** 來編輯先前的設定。
   - 您最多可建立三（3）個平行連線來串流處理診斷遙測。
   - 選取 [+新增診斷設定]**** 建立診斷資料到多個資源的多個平行串流處理。

   ![啟用執行個體資料庫的診斷](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. 輸入供您自己參考的設定名稱。
5. 選取串流診斷資料的目的地資源： [封存**至儲存體帳戶**]、[**串流至事件中樞**] 或 [**傳送至 Log Analytics**]。
6. 選取資料庫診斷遙測的核取方塊： [ **SQLInsights**]、[ **QueryStoreRuntimeStatistics**]、[ **QueryStoreWaitStatistics**] 和 [**錯誤**]。
   ![設定執行個體資料庫的診斷](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. 選取 [儲存]。
8. 針對您想要監視的每個實例資料庫重複這些步驟。

> [!TIP]
> 針對您想要監視的每個實例資料庫重複這些步驟。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager 模組仍受到支援，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

您可以使用 PowerShell 啟用計量和診斷記錄功能。

- 若要在儲存體帳戶中啟用計量和資源記錄的儲存體，請使用此命令：

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  儲存體帳戶識別碼是目的地儲存體帳戶的資源識別碼。

- 若要啟用將計量和資源記錄串流至事件中樞，請使用此命令：

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Azure 服務匯流排規則識別碼是此格式的字串︰

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- 若要啟用將計量和資源記錄傳送到 Log Analytics 工作區，請使用此命令：

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

\<$WSID\>執行腳本將 `Enable-AzureRMDiagnostics.ps1` 診斷資料從多個資源傳送至工作區時，請提供工作區資源識別碼做為參數。

- 若要取得 \<$WSID\> 診斷資料之目的地的工作區識別碼，請使用下列腳本：

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  請以訂用帳戶 \<subID\> 識別碼取代、 \<RG_NAME\> 以資源組名取代，並以 \<WS_NAME\> 工作區名稱取代。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以使用 Azure CLI 啟用計量和診斷記錄功能。

> [!IMPORTANT]
> Azure CLI v1.0 支援啟用診斷記錄的腳本。 目前不支援 Azure CLI v2.0。

- 若要在儲存體帳戶中啟用計量和資源記錄的儲存體，請使用此命令：

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  儲存體帳戶識別碼是目的地儲存體帳戶的資源識別碼。

- 若要啟用將計量和資源記錄串流至事件中樞，請使用此命令：

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  服務匯流排規則識別碼是此格式的字串︰

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- 若要啟用將計量和資源記錄傳送到 Log Analytics 工作區，請使用此命令：

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

您可以結合這些參數讓多個輸出選項。

---

## <a name="stream-into-sql-analytics"></a>串流至 SQL 分析

串流至 Log Analytics 工作區的 Azure SQL Database 和 Azure SQL 受控執行個體計量和資源記錄可供 Azure SQL 分析使用。 Azure SQL 分析是一種雲端解決方案，可監視單一資料庫、彈性集區和集區資料庫的效能，以及大規模且跨多個訂用帳戶的受控實例和實例資料庫。 它可協助您收集和視覺化效能計量，並具有內建的效能疑難排解功能。

![Azure SQL 分析概觀](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>安裝概觀

您可以執行下列步驟，使用 Azure SQL 分析監視資料庫和資料庫集合的集合：

1. 從 Azure Marketplace 建立 Azure SQL 分析解決方案。
2. 在解決方案中建立 Log Analytics 工作區。
3. 將資料庫設定為將診斷遙測串流至工作區。

您可以在 Azure 入口網站的 [診斷設定] 索引標籤中，使用內建的 [**傳送至 Log Analytics** ] 選項，設定此診斷遙測的串流匯出。 您也可以透過[PowerShell Cmdlet](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry)、 [Azure CLI](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry)、 [Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)或[Resource Manager 範本](../../azure-monitor/platform/diagnostic-settings-template.md)，使用診斷設定來啟用 Log Analytics 工作區的串流。

### <a name="create-an-azure-sql-analytics-resource"></a>建立 Azure SQL 分析資源

1. 在 Azure Marketplace 中搜尋 Azure SQL 分析並加以選取。

   ![在入口網站中搜尋 Azure SQL 分析](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. 在解決方案的 [概觀] 畫面上選取 [建立]****。

3. 在 Azure SQL 分析表單中填入所需的其他資訊：工作區名稱、訂用帳戶、資源群組、位置及定價層。

   ![在入口網站中設定 Azure SQL 分析](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. 選取 [確定]**** 確認，然後選取 [建立]****。

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>設定資源以記錄計量和資源記錄

您需要針對單一和集區資料庫、彈性集區、受控實例和實例資料庫，分別設定診斷遙測串流。 設定資源記錄計量的最簡單方式是使用 Azure 入口網站。 如需詳細步驟，請參閱[設定診斷遙測的串流匯出](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry)。

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>使用 Azure SQL 分析進行監視和警示

您可以使用 SQL 分析做為階層式儀表板來查看您的資料庫資源。

- 若要瞭解如何使用 Azure SQL 分析，請參閱[使用 SQL 分析進行監視](../../azure-monitor/insights/azure-sql.md)。
- 若要瞭解如何在 SQL 分析中設定的警示，請參閱[建立資料庫、彈性集區和受控實例的警示](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)。

## <a name="stream-into-event-hubs"></a>串流至事件中樞

您可以使用 Azure 入口網站中的內建 [**串流至事件中樞**] 選項，將 Azure SQL Database 和 Azure SQL 受控執行個體計量和資源記錄串流至事件中樞。 您也可以透過 PowerShell Cmdlet、Azure CLI 或 Azure 監視器 REST API，使用診斷設定來啟用服務匯流排規則識別碼。 請確定事件中樞與您的資料庫和伺服器位於相同的區域。

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>事件中樞中的計量和資源記錄檔

所選的資料串流到事件中樞之後，您很快就能啟用進階監視案例。 事件中樞是作為事件管線的大門。 資料收集到事件中樞之後，這些資料可以透過即時分析提供者或儲存體配接器來轉換和儲存。 事件中樞會讓事件串流的產生從這些事件的取用分離。 如此一來，事件消費者可以在自己的排程存取事件。 如需事件中樞的詳細資訊，請參閱：

- [什麼是 Azure 事件中樞？](../../event-hubs/event-hubs-about.md)
- [開始使用事件中心](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

您可以在事件中樞使用串流的計量：

- **藉由將熱路徑資料串流至 Power BI 來查看服務健全狀況**

  您可以使用事件中樞、串流分析和 PowerBI，輕鬆快速地將計量和診斷資料轉換為 Azure 服務上的深入解析。 如需如何設定事件中樞、使用串流分析處理資料，以及使用 PowerBI 作為輸出的概觀，請參閱[串流分析和 Power BI](../../stream-analytics/stream-analytics-power-bi-dashboard.md)。

- **將記錄串流至協力廠商記錄和遙測串流**

  藉由使用事件中樞串流，您可以將計量和資源記錄檔放入各種協力廠商監視和記錄分析解決方案中。

- **建立自訂遙測和記錄平臺**

  您是否已建立自訂的遙測平台，或正考慮建置一個？ 事件中樞可高度擴充的發佈-訂閱特性，可讓您靈活地內嵌計量和資源記錄。 請參閱 [Dan Rosanova 指南，以在全球級別的遙測平台中使用事件中樞](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="stream-into-azure-storage"></a>串流到 Azure 儲存體中

您可以使用 Azure 入口網站中的內建 [封存**至儲存體帳戶**] 選項，將計量和資源記錄儲存在 Azure 儲存體中。 您也可以透過 PowerShell Cmdlet、Azure CLI 或 Azure 監視器 REST API，使用診斷設定來啟用儲存體。

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>儲存體帳戶中計量和資源記錄的架構

設定計量和資源記錄集合之後，當您的第一個資料列可供使用時，會在您選取的儲存體帳戶中建立儲存體容器。 Blob 的結構為：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

或者，形式更簡單：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，基本計量的 blob 名稱可能是：

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

儲存彈性集區的資料所用的 Blob 名稱，例如：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>資料保留原則和價格

如果您選取事件中樞或儲存體帳戶，您可以指定保留原則。 此原則會刪除早於選取時間期間的資料。 如果您指定 Log Analytics，則保留原則取決於所選的定價層。 在這種情況下，所提供的免費資料擷取單位可以每個月免費監視多個資料庫。 任何超過免費單位的診斷遙測消耗可能會產生成本。

> [!IMPORTANT]
> 具有較繁重工作負載的作用中資料庫會內嵌比閒置資料庫更多的資料。 如需詳細資訊，請參閱[Log analytics 定價](https://azure.microsoft.com/pricing/details/monitor/)。

如果您使用 Azure SQL 分析，您可以在 Azure SQL 分析的導覽功能表上選取 [ **OMS 工作區**]，然後選取 [**使用量**和**估計成本**]，來監視您的資料內嵌耗用量。

## <a name="metrics-and-logs-available"></a>可用的計量和記錄

適用于單一資料庫、集區資料庫、彈性集區、受控實例和實例資料庫的監視遙測，記載于此文章的這一節。 您可以使用[Azure 監視器記錄查詢](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)語言，將 SQL 分析中收集的監視遙測用於您自己的自訂分析和應用程式開發。

### <a name="basic-metrics"></a>基本計量

如需有關資源的基本計量詳細資料，請參閱下表。

> [!NOTE]
> [基本計量] 選項先前稱為 [所有計量]。 所做的變更僅限於命名，而且不會變更受監視的計量。 已起始這項變更，以允許未來引進額外的計量類別。

#### <a name="basic-metrics-for-elastic-pools"></a>彈性集區的基本計量

|**Resource**|**計量**|
|---|---|
|彈性集區|eDTU 百分比、使用的 eDTU、eDTU 限制、CPU 百分比、實體資料讀取百分比、記錄寫入百分比、工作階段百分比、背景工作百分比、儲存體、儲存體百分比、儲存體限制、XTP 儲存體百分比 |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>單一和集區資料庫的基本計量

|**Resource**|**計量**|
|---|---|
|單一和集區資料庫|DTU 百分比、使用的 DTU、DTU 限制、CPU 百分比、實體資料讀取百分比、記錄寫入百分比、成功/失敗/防火牆封鎖的連線、工作階段百分比、背景工作百分比、儲存體、儲存體百分比、XTP 儲存體百分比和死結 |

### <a name="advanced-metrics"></a>Advanced 計量

如需有關 advanced 計量的詳細資訊，請參閱下表。

|**計量**|**計量顯示名稱**|**描述**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|SQL 進程核心百分比|SQL 進程的 CPU 使用量百分比，以作業系統來測量。|
|sqlserver_process_memory_percent<sup>1</sup> |SQL 進程記憶體百分比|SQL 進程的記憶體使用量百分比，以作業系統來測量。|
|tempdb_data_size<sup>2</sup>| TempDB 資料檔案大小 (KB) |Tempdb 資料檔案大小（Kb）。|
|tempdb_log_size<sup>2</sup>| Tempdb 記錄檔大小 (KB) |Tempdb 記錄檔大小（Kb）。|
|tempdb_log_used_percent<sup>2</sup>| Tempdb 使用的記錄百分比 |使用的 Tempdb 百分比記錄。|

<sup>1</sup>此計量適用于使用具有2虛擬核心和更高版本的 vCore 購買模型，或 200 dtu 和更新版本（適用于以 DTU 為基礎的購買模型）的資料庫。

<sup>2</sup>此計量適用于使用具有2虛擬核心和更高版本的 vCore 購買模型，或 200 dtu 和更新版本（適用于以 DTU 為基礎的購買模型）的資料庫。 此度量目前不適用於超大規模資料庫資料庫或資料倉儲。

### <a name="basic-logs"></a>基本記錄

下表記載所有記錄可用的遙測詳細資料。 如需詳細資訊，請參閱[支援的診斷遙測](#diagnostic-telemetry-for-export)。

#### <a name="resource-usage-stats-for-managed-instances"></a>受控實例的資源使用狀況統計資料

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
|SKU_s|SQL 受控執行個體產品 SKU |
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

深入瞭解[查詢存放區執行時間統計資料](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)。

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

深入瞭解[SQL 錯誤訊息](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15)。

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

深入了解 [Intelligent Insights 記錄格式](intelligent-insights-use-diagnostics-log.md)。

## <a name="next-steps"></a>後續步驟

若要了解如何啟用記錄，並了解各種 Azure 服務支援的計量和記錄類別，請參閱：

- [Microsoft Azure 中的度量概觀](../../azure-monitor/platform/data-platform.md)
- [Azure 平台記錄概觀](../../azure-monitor/platform/platform-logs-overview.md)

若要了解事件中樞，請閱讀：

- [Azure 事件中樞是什麼？](../../event-hubs/event-hubs-about.md)
- [開始使用事件中心](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

若要瞭解如何根據 log analytics 的遙測來設定警示，請參閱：

- [建立 Azure SQL Database 和 Azure SQL 受控執行個體的警示](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
