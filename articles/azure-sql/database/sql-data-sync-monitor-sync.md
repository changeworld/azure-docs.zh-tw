---
title: 使用 Azure 監視器記錄監視 SQL 資料同步
description: 瞭解如何使用 Azure 監視器記錄來監視 SQL 資料同步。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 307e501743d01b94cfca3692cc09c05cc90ed3ce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84343229"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>使用 Azure 監視器記錄監視 SQL 資料同步 

以往，若要檢查 SQL 資料同步活動記錄檔並偵測錯誤和警告，您必須在 Azure 入口網站中以手動方式檢查 SQL 資料同步，或使用 PowerShell 或 REST API。 請遵循本文中的步驟，設定自訂解決方案，以改善資料同步的監視體驗。 您可以依據自己的案例來自訂這個解決方案。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

如需 SQL 資料同步的概觀，請參閱[在 Azure 中使用 SQL 資料同步，跨多個雲端與內部部署資料庫同步處理資料](sql-data-sync-data-sql-server-sql-database.md)。

> [!IMPORTANT]
> SQL 資料同步目前**不**支援 Azure SQL 受控執行個體。

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>監視所有同步處理群組的儀表板 

您不需要再逐一查看每個同步處理群組的記錄來尋找問題。 您可以使用自訂的 Azure 監視器 view，在同一個位置監視任何訂用帳戶中的所有同步處理群組。 此檢視會呈現 SQL 資料同步客戶重視的相關資訊。

![資料同步監視儀表板](./media/sql-data-sync-monitor-sync/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>自動電子郵件通知

您再也不用在 Azure 入口網站中手動檢查記錄檔，也不用透過 PowerShell 或 REST API 來進行。 有了[Azure 監視器記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)，您就可以建立警示，在發生錯誤時直接前往需要查看他們的電子郵件地址。

![資料同步電子郵件通知](./media/sql-data-sync-monitor-sync/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>如何設定這些監視功能？ 

藉由執行下列動作，在不到一小時內執行 SQL 資料同步的自訂 Azure 監視器記錄監視解決方案：

您必須設定三個元件：

-   用來饋送 SQL 資料同步記錄資料以 Azure 監視器記錄的 PowerShell runbook。

-   電子郵件通知的 Azure 監視器警示。

-   用於監視的 Azure 監視器 View。

### <a name="samples-to-download"></a>下載範例

請下載下列兩個範例：

-   [資料同步記錄 PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [資料同步 Azure 監視器視圖](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>必要條件

請確定您已設定下列項目：

-   Azure 自動化帳戶

-   Log Analytics 工作區

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>可取得 SQL 資料同步記錄的 PowerShell Runbook 

使用裝載于 Azure 自動化中的 PowerShell runbook 來提取 SQL 資料同步記錄資料，並將它傳送至 Azure 監視器記錄。 隨附範例指令碼。 必要條件是您必須具備 Azure 自動化帳戶。 然後您需要建立 Runbook 並排程執行。 

### <a name="create-a-runbook"></a>建立 Runbook

如需建立 Runbook 的詳細資訊，請參閱[我的第一個 PowerShell Runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell)。

1.  在您的 Azure 自動化帳戶中，選取 [程序自動化] 下方的 [Runbook]**** 索引標籤。

2.  選取 [Runbook] 頁面左上角的 [**新增 Runbook** ]。

3.  選取 [匯入現有 Runbook]****。

4.  在 [Runbook 檔案]**** 下方，使用指定的 `DataSyncLogPowerShellRunbook` 檔案。 將 [Runbook 類型]**** 設為 `PowerShell`。 為 Runbook 指定名稱。

5.  選取 [建立]。 現在您已有 Runbook。

6.  在您的 Azure 自動化帳戶中，選取 [共用資源] 下方的 [變數]**** 索引標籤。

7.  在 [變數] 頁面上，選取 [新增變數]****。 需要建立一個變數來儲存上次執行 Runbook 的時間。 如果您有多個 Runbook，則每個 Runbook 都需要一個變數。

8.  請將變數的名稱設為 `DataSyncLogLastUpdatedTime`，並將其類型設為 DateTime。

9.  選取 Runbook，然後按一下頁面頂端的 [編輯] 按鈕。

10. 針對您的帳戶和 SQL 資料同步設定，進行所需的變更。 (如需詳細資訊，請參閱範例指令碼。)

    1.  Azure 資訊。

    2.  同步群組資訊。

    3.  Azure 監視器記錄資訊。 在 Azure 入口網站中尋找這項資訊 |設定 |連線的來源。 如需將資料傳送至 Azure 監視器記錄的詳細資訊，請參閱[使用 HTTP 資料收集器 API （預覽）將資料傳送至 Azure 監視器記錄](../../azure-monitor/platform/data-collector-api.md)。

11. 在 [測試] 窗格中執行 Runbook。 檢查並確定已順利完成。

    如果發生錯誤，請確定您已安裝最新的 PowerShell 模組。 您可以在自動化帳戶的 [模組庫]**** 中安裝最新的 PowerShell 模組。

12. 按一下 [**發佈**]

### <a name="schedule-the-runbook"></a>排程 Runbook

若要排程 Runbook：

1.  在 Runbook 中，選取 [資源] 下方的 [排程]**** 索引標籤。

2.  在 [排程] 頁面中，選取 [新增排程]****。

3.  選取 [將**排程連結至您的 runbook**]。

4.  選取 [**建立新的排程]。**

5.  將 [週期]**** 設為 [週期性] 並設定所要的間隔。 在這裡、腳本和 Azure 監視器記錄中使用相同的間隔。

6.  選取 [建立]。

### <a name="check-the-automation"></a>檢查自動化

若要監視您的自動化是否如預期般運作，請在自動化帳戶的 [概觀]**** 下方，找到 [作業統計資料]**** 檢視 (位於 [監視]**** 下方)。 將此檢視釘選到儀表板，以便輕鬆檢視。 Runbook 執行成功時會顯示為「已完成」，執行失敗時會顯示為「失敗」。

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>建立電子郵件通知的 Azure 監視器讀取器警示

若要建立使用 Azure 監視器記錄的警示，請執行下列動作。 必要條件是，您需要有與 Log Analytics 工作區連結的 Azure 監視器記錄。

1.  在 Azure 入口網站中，選取 [記錄搜尋]****。

2.  建立查詢，以選取在所選間隔內的錯誤和警告 (依同步群組)。 例如：

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  執行查詢之後，選取代表**警示**的鈴鐺圖示。

4.  在 [產生警示的依據]**** 下方，選取 [計量量值]****。

    1.  將 [彙總值] 設為 [大於]****。

    2.  在 [大於]**** 之後，輸入要在經過多久閾值後收到通知。 資料同步中應該要有暫時性錯誤。若要減少雜訊，請將閾值設定為5。

5.  在 [動作]**** 下方，將 [電子郵件通知]**** 設為 [是]。 輸入所需的電子郵件收件者。

6.  按一下 [檔案] 。 現在，指定的收件者即可在發生錯誤時，收到電子郵件通知。

## <a name="create-an-azure-monitor-view-for-monitoring"></a>建立用於監視的 Azure 監視器 View

此步驟會建立一個 Azure 監視器視圖，以視覺化方式監視所有指定的同步處理群組。 這個檢視包含數個元件：

-   概觀圖格，其中顯示所有同步群組的錯誤次數、成功次數和警告次數。

-   所有同步群組的圖格，其會顯示每個同步群組的錯誤和警告計數。 此圖格不會顯示沒有問題的群組。

-   每個同步處理群組的磚，其中會顯示錯誤數目、成功次數和警告，以及最近的錯誤訊息。

若要設定 Azure 監視器視圖，請執行下列動作：

1.  在 Log Analytics 工作區的 [首頁] 上，選取左側的加號以開啟 [ **view designer**]。

2.  選取檢視表設計工具頂端列的 [匯入]****。 然後選取 "DataSyncLogOMSView" 範例檔案。

3.  此範例檢視可用來管理兩個同步群組。 請依據您的案例，編輯這個檢視。 按一下 [編輯]**** 並進行下列變更：

    1.  視需要從資源庫建立新的「環圈與清單」物件。

    2.  在每個圖格中，使用您的資訊更新查詢。

        1.  視需要變更每個圖格中的 TimeStamp_t 間隔。

        2.  在每個同步群組的磚上，更新同步處理組名。

    3.  視需要更新每個圖格的標題。

4.  按一下 [儲存]****，即已完成檢視。

## <a name="cost-of-this-solution"></a>解決方案的成本

在大部分情況下，這個解決方案是免費的。

**Azure 自動化：** 根據使用量而定，Azure 自動化帳戶可能會產生費用。 每個月的前 500 分鐘作業執行時間為免費。 在大部分情況下，這個解決方案每個月只會用到 500 分鐘以內的使用量。 若要避免費用，請將 Runbook 排程為間隔兩個小時以上執行。 如需詳細資訊，請參閱[自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

**Azure 監視器記錄：** 根據您的使用量而定，可能會產生與 Azure 監視器記錄相關聯的費用。 免費層包含一天 500 MB 的內嵌資料。 在大部分情況下，這個解決方案一天只會內嵌 500 MB 以內的資料量。 若要減少使用量，可使用 Runbook 隨附的僅限失敗篩選功能。 如果您每天使用超過 500 MB，請升級至付費層，以避免在達到限制時停止分析的風險。 如需詳細資訊，請參閱[Azure 監視器記錄定價](https://azure.microsoft.com/pricing/details/log-analytics/)。

## <a name="code-samples"></a>程式碼範例

您可以從下列位置，下載本文所述的程式碼範例：

-   [資料同步記錄 PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [資料同步 Azure 監視器視圖](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>後續步驟
如需 SQL 資料同步的詳細資訊，請參閱：

-   概觀 - [在 Azure 中使用 SQL 資料同步，跨多個雲端與內部部署資料庫同步處理資料](sql-data-sync-data-sql-server-sql-database.md)
-   設定資料同步
    - 在入口網站中-[教學課程：設定 SQL 資料同步在 Azure SQL Database 和 SQL Server 之間同步處理資料](sql-data-sync-sql-server-configure.md)
    - 透過 PowerShell
        -  [使用 PowerShell 在 Azure SQL Database 中的多個資料庫之間進行同步處理](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [使用 PowerShell 在 Azure SQL Database 中的資料庫和 SQL Server 實例中的資料庫之間進行同步處理](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [適用於 Azure 中 SQL 資料同步的 Data Sync Agent](sql-data-sync-agent-overview.md)
-   最佳做法 - [Azure 中的 SQL 資料同步最佳做法](sql-data-sync-best-practices.md)
-   疑難排解 - [在 Azure 中針對 SQL 資料同步的問題進行疑難排解](sql-data-sync-troubleshoot.md)
-   更新同步結構描述
    -   使用 Transact-sql-自動複寫[Azure 中 SQL 資料同步的架構變更](sql-data-sync-update-sync-schema.md)
    -   使用 PowerShell - [使用 PowerShell 更新現有同步群組中的同步結構描述](scripts/update-sync-schema-in-sync-group.md)

如需 SQL Database 的詳細資訊，請參閱：

-   [SQL Database 概觀](../../sql-database/sql-database-technical-overview.md)
-   [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
