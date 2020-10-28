---
title: 搭配多租用戶應用程式使用 Azure 監視器記錄
description: 搭配多租用戶 Azure SQL Database SaaS 應用程式來設定及使用 Azure 監視器記錄
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: a078ba6147d4d874a890f406563111b6fdb82ed6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780898"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-azure-sql-database-saas-app"></a>搭配多租用戶 Azure SQL Database SaaS 應用程式來設定及使用 Azure 監視器記錄
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在本教學課程中，您會設定及使用 [Azure 監視器記錄](../../azure-monitor/log-query/log-query-overview.md)來監視彈性集區和資料庫。 本教學課程是以[效能監視與管理教學課程](saas-dbpertenant-performance-monitoring.md)為基礎。 其中示範如何使用 Azure 監視器記錄來增強 Azure 入口網站中所提供的監視和警示功能。 Azure 監視器記錄支援監視數千個彈性集區和數十萬個資料庫。 Azure 監視器記錄也提供單一監視解決方案，可以跨多個 Azure 訂用帳戶，整合不同應用程式和 Azure 服務的監視。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 安裝和設定 Azure 監視器記錄。
> * 使用 Azure 監視器記錄來監視集區和資料庫。

若要完成本教學課程，請確定已完成下列必要條件：

* 已部署每一租用戶一個資料庫的 Wingtip Tickets SaaS 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索每一租用戶一個資料庫的 Wingtip Tickets SaaS 應用程式](./saas-dbpertenant-get-started-deploy.md)。
* 已安裝 Azure PowerShell。 如需詳細資訊，請參閱[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。

如需了解 SaaS 案例和模式的討論，及其對於監視解決方案需求的影響，請參閱[效能監視與管理教學課程](saas-dbpertenant-performance-monitoring.md)。

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>使用 Azure 監視器記錄來監視及管理資料庫和彈性集區的效能

針對 Azure SQL Database，Azure 入口網站中提供了有關資料庫和集區的監視與警示功能。 這個內建的監視與警示功能相當方便，但它也是資源特定的功能。 這意謂著它較不適合用來監視大型安裝項目，或是提供跨資源和訂用帳戶的合併檢視。

針對數量龐大的情況，您可以使用 Azure 監視器記錄來進行監視和警示。 Azure 監視器是個別的 Azure 服務，可讓您分析工作區中所收集的記錄，而這些記錄可能來自許多服務。 Azure 監視器記錄提供內建的查詢語言和資料視覺化工具，可進行操作資料分析。 「SQL 分析」解決方案提供數個預先定義的彈性集區及資料庫監視與警示檢視和查詢。 Azure 監視器記錄也會提供自訂的檢視設計工具。

OMS 工作區現在稱為 Log Analytics 工作區。 您可以在 Azure 入口網站中開啟 Log Analytics 工作區和分析解決方案。 Azure 入口網站是較新的存取點，但在某些方面可能落後於 Operations Management Suite 入口網站。

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>藉由模擬您租用戶上的工作負載來建立效能診斷資料 

1. 在 PowerShell ISE 中，開啟 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1* 。 請將此指令碼保持開啟，因為在本教學課程期間，您可能會想要執行數個負載產生案例。
1. 如果您尚未佈建一批租用戶，請這麼做，以便讓監視內容更有趣。 此程序需要幾分鐘的時間。

   a. 設定 **$DemoScenario = 1** ，佈建一批租用戶。

   b. 若要執行指令碼並部署額外的 17 個租用戶，請按 F5。

1. 現在，啟動負載產生器，以在所有租用戶上執行模擬負載。

    a. 設定 **$DemoScenario = 2** ，產生一般強度負載 (約為 30 DTU)。

    b. 若要執行指令碼，請按 F5。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>取得每一租用戶一個資料庫的 Wingtip Tickets SaaS 應用程式指令碼

[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 存放庫中提供了 Wingtip Tickets SaaS 多租用戶資料庫指令碼和應用程式原始程式碼。 如需有關下載 Wingtip Tickets PowerShell 指令碼及將其解除封鎖的步驟，請參閱[一般指引](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>安裝及設定 Log Analytics 工作區和 Azure SQL 分析解決方案

Azure 監視器是一項必須設定的個別服務。 Azure 監視器記錄會將記錄資料、遙測及計量收集至 Log Analytics 工作區中。 就像 Azure 中的其他資源一樣，Log Analytics 工作區是必須建立的資源。 工作區不一定要建立在與其監視之應用程式相同的資源群組中。 不過，這麼做通常最有意義。 針對 Wingtip Tickets 應用程式，請使用單一資源群組，以確保工作區會隨著應用程式一起刪除。

1. 在 PowerShell ISE 中，開啟 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\Demo-LogAnalytics.ps1* 。
1. 若要執行指令碼，請按 F5。

現在，您可以在 Azure 入口網站中開啟 Azure 監視器記錄。 需要幾分鐘的時間才能將遙測資料收集至 Log Analytics 工作區中並顯示出來。 您讓系統收集診斷資料的時間越長，體驗就會更有趣。 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>使用 Log Analytics 工作區和 SQL 分析解決方案來監視集區和資料庫


在此練習中，開啟 Azure 入口網站中的 Log Analytics 工作區，以查看針對資料庫和集區蒐集的遙測資料。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。 選取 [所有服務] 以開啟 Log Analytics 工作區。 然後搜尋 Log Analytics。

   ![開啟 Log Analytics 工作區](./media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. 選取名為 wtploganalytics-&lt;user&gt; 的工作區。

1. 選取 [概觀] 以在 Azure 入口網站中開啟記錄分析解決方案。

   ![概觀](./media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > 解決方案可能需要幾分鐘的時間才會變成作用中。 

1. 選取 [Azure SQL 分析]  圖格來開啟它。

    ![[概觀] 圖格](./media/saas-dbpertenant-log-analytics/overview.png)

1. 解決方案中的檢視會橫向捲動，其中底部會有自己的內部捲軸。 請視需要重新整理頁面。

1. 若要瀏覽摘要頁面，請選取圖格或個別的資料庫，以開啟向下鑽研的總管頁面。

    ![記錄分析儀表板](./media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. 變更篩選設定以修改時間範圍。 針對本教學課程，請選取 [過去 1 小時]  。

    ![時間篩選器](./media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. 選取個別資料庫，以探索該資料庫的查詢使用情況和計量。

    ![資料庫分析](./media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. 若要查看使用計量，請向右捲動分析頁面。
 
     ![資料庫計量](./media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. 向左捲動分析頁面，然後選取 [資源資訊]  清單中的伺服器圖格。  

    ![[資源資訊] 清單](./media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    隨即會開啟一個頁面，當中顯示該伺服器上的集區和資料庫。

    ![具有集區和資料庫的伺服器](./media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. 選取一個集區。 在開啟的集區頁面上，向右捲動到看到集區計量。 

    ![集區計量](./media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. 回到 Log Analytics 工作區，選取 [OMS 入口網站]  以在該處開啟工作區。

    ![Log Analytics 工作區](./media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

在 Log Analytics 工作區中，您可以進一步探索記錄和計量資料。 

Azure 監視器記錄中的監視和警示是以工作區中的資料查詢為基礎，不同於 Azure 入口網站中每個資源上定義的警示。 由於警示是以查詢為基礎，因此您可以定義可查看所有資料庫的單一警示，而不是為每個資料庫定義一個警示。 查詢僅受限於工作區中可用的資料。

如需有關如何使用 Azure 監視器記錄來查詢及設定警示的詳細資訊，請參閱[使用 Azure 監視器記錄中的警示規則](../../azure-monitor/platform/alerts-metric.md)。

SQL Database 的 Azure 監視器記錄會根據工作區中的資料量收費。 在本教學課程中，您已建立一個免費工作區，其每天的限制為 500 MB。 在達到該限制之後，就不會再將資料新增至該工作區。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 安裝和設定 Azure 監視器記錄。
> * 使用 Azure 監視器記錄來監視集區和資料庫。

請嘗試[租用戶分析教學課程](saas-dbpertenant-log-analytics.md)。

## <a name="additional-resources"></a>其他資源

* [以每一租用戶一個資料庫的初始 Tickets SaaS 應用程式部署為基礎的其他教學課程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure 監視器記錄](../../azure-monitor/insights/azure-sql.md)