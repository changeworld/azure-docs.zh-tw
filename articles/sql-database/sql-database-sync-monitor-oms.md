---
title: 使用 Azure 監視器記錄監視 SQL 資料同步
description: 瞭解如何使用 Azure 監視器紀錄監視 Azure SQL 資料同步
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 5f5980f74b24cd972d43e9b05d4a5d623e6e3d2f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383695"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>使用 Azure 監視器記錄監視 SQL 資料同步 

以往，若要檢查 SQL 資料同步活動記錄檔並偵測錯誤和警告，您必須在 Azure 入口網站中以手動方式檢查 SQL 資料同步，或使用 PowerShell 或 REST API。 請遵循本文中的步驟，設定自訂解決方案，以改善資料同步的監視體驗。 您可以依據自己的案例來自訂這個解決方案。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

如需 SQL 資料同步的概觀，請參閱[使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步處理資料](sql-database-sync-data.md)。

> [!IMPORTANT]
> Azure SQL 資料同步此時**不支援**Azure SQL 資料庫託管實例。

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>所有同步群組的監視儀表板 

當您要尋找問題時，再也不用分別查看每個同步群組的記錄。 您可以使用自訂 Azure 監視器檢視在一個位置監視任何訂閱中的所有同步群組。 此檢視會呈現 SQL 資料同步客戶重視的相關資訊。

![資料同步監視儀表板](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>自動電子郵件通知

您再也不用在 Azure 入口網站中手動檢查記錄檔，也不用透過 PowerShell 或 REST API 來進行。 使用[Azure 監視器日誌](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview),您可以建立直接轉到發生錯誤時需要查看這些日誌的人的電子郵寄地址的警報。

![資料同步電子郵件通知](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>如何設定這些監視功能？ 

以執行以下操作,在不到一小時內為 SQL 資料同步實現自訂 Azure 監視器監視解決方案:

您必須設定三個元件：

-   用於將 SQL 資料同步日誌數據饋送到 Azure 監視器日誌的 PowerShell 執行簿。

-   電子郵件通知的 Azure 監視器警報。

-   用於監視的 Azure 監視器檢視。

### <a name="samples-to-download"></a>下載範例

請下載下列兩個範例：

-   [資料同步記錄 PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [資料同步 Azure 監視器檢視](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Prerequisites

請確定您已設定下列項目：

-   Azure 自動化帳戶

-   Log Analytics 工作區

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>可取得 SQL 資料同步記錄的 PowerShell Runbook 

使用 Azure 自動化中託管的 PowerShell 執行簿提取 SQL 資料同步日誌數據並將其發送到 Azure 監視器日誌。 隨附範例指令碼。 必要條件是您必須具備 Azure 自動化帳戶。 然後您需要建立 Runbook 並排程執行。 

### <a name="create-a-runbook"></a>建立 Runbook

如需建立 Runbook 的詳細資訊，請參閱[我的第一個 PowerShell Runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell)。

1.  在您的 Azure 自動化帳戶中，選取 [程序自動化] 下方的 [Runbook]**** 索引標籤。

2.  選取 [Runbook] 頁面左上角的 [新增 Runbook]****。

3.  選取 [匯入現有 Runbook]****。

4.  在 [Runbook 檔案]**** 下方，使用指定的 `DataSyncLogPowerShellRunbook` 檔案。 將 [Runbook 類型]**** 設為 `PowerShell`。 為 Runbook 指定名稱。

5.  選取 [建立]  。 現在您已有 Runbook。

6.  在您的 Azure 自動化帳戶中，選取 [共用資源] 下方的 [變數]**** 索引標籤。

7.  在 [變數] 頁面上，選取 [新增變數]****。 需要建立一個變數來儲存上次執行 Runbook 的時間。 如果您有多個 Runbook，則每個 Runbook 都需要一個變數。

8.  請將變數的名稱設為 `DataSyncLogLastUpdatedTime`，並將其類型設為 DateTime。

9.  選取 Runbook，然後按一下頁面頂端的 [編輯] 按鈕。

10. 針對您的帳戶和 SQL 資料同步設定，進行所需的變更。 (如需詳細資訊，請參閱範例指令碼。)

    1.  Azure 資訊。

    2.  同步群組資訊。

    3.  Azure 監視器記錄資訊。 在 Azure 入口網站 | 設定 | 連接的來源中，尋找這項資訊。 有關將資料傳送到 Azure 監視器紀錄的詳細資訊,請參閱[使用 HTTP 資料收集器 API(預覽)將資料傳送到 Azure 監視器紀錄](../azure-monitor/platform/data-collector-api.md)。

11. 在 [測試] 窗格中執行 Runbook。 檢查並確定已順利完成。

    如果發生錯誤，請確定您已安裝最新的 PowerShell 模組。 您可以在自動化帳戶的 [模組庫]**** 中安裝最新的 PowerShell 模組。

12. 單擊 **"發佈"**

### <a name="schedule-the-runbook"></a>排程 Runbook

若要排程 Runbook：

1.  在 Runbook 中，選取 [資源] 下方的 [排程]**** 索引標籤。

2.  在 [排程] 頁面中，選取 [新增排程]****。

3.  選擇**要連結到您的 Runbook**。

4.  選擇 **「創建新計畫」。**

5.  將 [週期]**** 設為 [週期性] 並設定所要的間隔。 在此處、腳本和 Azure 監視器日誌中使用相同的間隔。

6.  選取 [建立]  。

### <a name="check-the-automation"></a>檢查自動化

若要監視您的自動化是否如預期般運作，請在自動化帳戶的 [概觀]**** 下方，找到 [作業統計資料]**** 檢視 (位於 [監視]**** 下方)。 將此檢視釘選到儀表板，以便輕鬆檢視。 Runbook 執行成功時會顯示為「已完成」，執行失敗時會顯示為「失敗」。

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>為電子郵件通知建立 Azure 監視器讀取器警報

要建立使用 Azure 監視器日誌的警報,請執行以下操作。 作為先決條件,需要將 Azure 監視器日誌與日誌分析工作區連結。

1.  在 Azure 入口網站中，選取 [記錄搜尋]****。

2.  建立查詢，以選取在所選間隔內的錯誤和警告 (依同步群組)。 例如：

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  執行查詢之後，選取代表**警示**的鈴鐺圖示。

4.  在 [產生警示的依據]**** 下方，選取 [計量量值]****。

    1.  將 [彙總值] 設為 [大於]****。

    2.  在 [大於]**** 之後，輸入要在經過多久閾值後收到通知。 數據同步中需要暫時性錯誤。要降低噪音,請將閾值設置為 5。

5.  在 [動作]**** 下方，將 [電子郵件通知]**** 設為 [是]。 輸入所需的電子郵件收件者。

6.  按一下 [檔案]  。 現在，指定的收件者即可在發生錯誤時，收到電子郵件通知。

## <a name="create-an-azure-monitor-view-for-monitoring"></a>建立監控的 Azure 監視器檢視

此步驟創建 Azure 監視器檢視以直覺地監視所有指定的同步組。 這個檢視包含數個元件：

-   概觀圖格，其中顯示所有同步群組的錯誤次數、成功次數和警告次數。

-   所有同步群組的圖格，其會顯示每個同步群組的錯誤和警告計數。 此圖格不會顯示沒有問題的群組。

-   每個同步群組的圖格，其會顯示錯誤次數、成功次數和警告次數，以及最近的錯誤訊息。

要配置 Azure 監視器檢視,請執行以下操作:

1.  在紀錄分析工作區首頁上,選擇左側的加號以開啟**檢視設計器**。

2.  選取檢視表設計工具頂端列的 [匯入]****。 然後選取 "DataSyncLogOMSView" 範例檔案。

3.  此範例檢視可用來管理兩個同步群組。 請依據您的案例，編輯這個檢視。 按一下 [編輯]**** 並進行下列變更：

    1.  視需要從資源庫建立新的「環圈與清單」物件。

    2.  在每個圖格中，使用您的資訊更新查詢。

        1.  視需要變更每個圖格中的 TimeStamp_t 間隔。

        2.  在每個同步群組圖格中，更新同步群組的名稱。

    3.  視需要更新每個圖格的標題。

4.  按一下 [儲存]****，即已完成檢視。

## <a name="cost-of-this-solution"></a>解決方案的成本

在大部分情況下，這個解決方案是免費的。

**Azure 自動化：** 根據使用量而定，Azure 自動化帳戶可能會產生費用。 每個月的前 500 分鐘作業執行時間為免費。 在大部分情況下，這個解決方案每個月只會用到 500 分鐘以內的使用量。 若要避免費用，請將 Runbook 排程為間隔兩個小時以上執行。 如需詳細資訊，請參閱[自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

**Azure 監視器日誌:** 根據使用方式,可能與 Azure 監視器日誌相關聯的成本。 免費層包含一天 500 MB 的內嵌資料。 在大部分情況下，這個解決方案一天只會內嵌 500 MB 以內的資料量。 若要減少使用量，可使用 Runbook 隨附的僅限失敗篩選功能。 如果您一天使用超過 500 MB，請升級到付費層，以避免因達到限制而停止分析的風險。 有關詳細資訊,請參閱[Azure 監視器日誌定價](https://azure.microsoft.com/pricing/details/log-analytics/)。

## <a name="code-samples"></a>程式碼範例

您可以從下列位置，下載本文所述的程式碼範例：

-   [資料同步記錄 PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [資料同步 Azure 監視器檢視](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>後續步驟
如需 SQL 資料同步的詳細資訊，請參閱：

-   概觀 - [使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步資料](sql-database-sync-data.md)
-   設定資料同步
    - 在入口網站中 - [教學課程：設定 SQL 資料同步以同步處理 Azure SQL Database 與內部部署 SQL Server 之間的資料](sql-database-get-started-sql-data-sync.md)
    - 透過 PowerShell
        -  [使用 PowerShell 在多個 Azure SQL 資料庫之間同步](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [使用 PowerShell 設定「資料同步」在內部部署的 Azure SQL Database 和 SQL Server 之間進行同步處理](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [適用於 Azure SQL Data Sync 的 Data Sync Agent](sql-database-data-sync-agent.md)
-   最佳做法 - [Azure SQL 資料同步最佳做法](sql-database-best-practices-data-sync.md)
-   疑難排解 - [為 Azure SQL 資料同步的問題進行疑難排解](sql-database-troubleshoot-data-sync.md)
-   更新同步結構描述
    -   使用 Transact-SQL - [在 Azure SQL 資料同步中自動執行結構描述變更複寫](sql-database-update-sync-schema.md)
    -   使用 PowerShell - [使用 PowerShell 更新現有同步群組中的同步結構描述](scripts/sql-database-sync-update-schema.md)

如需 SQL Database 的詳細資訊，請參閱：

-   [SQL 資料庫概述](sql-database-technical-overview.md)
-   [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
