---
title: Azure 監視器日誌與多租戶應用
description: 使用多租戶 Azure SQL 資料庫 SaaS 應用設置和使用 Azure 監視器日誌
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 7429a9d5e9a803f0e9a6f900c5d81e77e7477a48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214495"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>使用多租戶 SQL 資料庫 SaaS 應用設置和使用 Azure 監視器日誌

在本教程中，您可以設置並使用[Azure 監視器日誌](/azure/log-analytics/log-analytics-overview)來監視彈性池和資料庫。 本教程基於[效能監控和管理教程](saas-dbpertenant-performance-monitoring.md)。 它演示如何使用 Azure 監視器日誌來增強 Azure 門戶中提供的監視和警報。 Azure 監視器日誌支援監視數千個彈性池和數十萬個資料庫。 Azure 監視器日誌提供單個監視解決方案，該解決方案可以跨多個 Azure 訂閱集成不同應用程式和 Azure 服務的監視。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 安裝和配置 Azure 監視器日誌。
> * 使用 Azure 監視器日誌監視池和資料庫。

若要完成本教學課程，請確定已完成下列必要條件：

* 已部署每一租用戶一個資料庫的 Wingtip Tickets SaaS 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索每一租用戶一個資料庫的 Wingtip Tickets SaaS 應用程式](saas-dbpertenant-get-started-deploy.md)。
* 已安裝 Azure PowerShell。 如需詳細資訊，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

如需了解 SaaS 案例和模式的討論，及其對於監視解決方案需求的影響，請參閱[效能監視與管理教學課程](saas-dbpertenant-performance-monitoring.md)。

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>使用 Azure 監視器日誌監視和管理資料庫和彈性池性能

針對 Azure SQL Database，Azure 入口網站中提供了有關資料庫和集區的監視與警示功能。 這個內建的監視與警示功能相當方便，但它也是資源特定的功能。 這意謂著它較不適合用來監視大型安裝項目，或是提供跨資源和訂用帳戶的合併檢視。

對於大容量方案，可以使用 Azure 監視器日誌進行監視和警報。 Azure 監視器是一個單獨的 Azure 服務，用於對從可能的許多服務收集的工作區中的日誌進行分析。 Azure 監視器日誌提供了一個內置的查詢語言和資料視覺化檢視，允許運算元據分析。 「SQL 分析」解決方案提供數個預先定義的彈性集區及資料庫監視與警示檢視和查詢。 Azure 監視器日誌還提供自訂視圖設計器。

OMS 工作區現在稱為 Log Analytics 工作區。 您可以在 Azure 入口網站中開啟 Log Analytics 工作區和分析解決方案。 Azure 入口網站是較新的存取點，但在某些方面可能落後於 Operations Management Suite 入口網站。

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>藉由模擬您租用戶上的工作負載來建立效能診斷資料 

1. 在 PowerShell ISE 中，打開 *。WingtipTicketsSaaS-多租戶Db主\\學習模組\\性能監控和管理\\演示-性能監控和管理.ps1. \\ * 請將此指令碼保持開啟，因為在本教學課程期間，您可能會想要執行數個負載產生案例。
1. 如果您尚未佈建一批租用戶，請這麼做，以便讓監視內容更有趣。 此程序需要幾分鐘的時間。

   a. 設置 **$DemoScenario = 1**，_預配一批租戶_。

   b. 若要執行指令碼並部署額外的 17 個租用戶，請按 F5。

1. 現在，啟動負載產生器，以在所有租用戶上執行模擬負載。

    a. 設置 **$DemoScenario = 2**，_生成正常強度負載（大約 30 DTU）。_

    b. 若要執行指令碼，請按 F5。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>取得每一租用戶一個資料庫的 Wingtip Tickets SaaS 應用程式指令碼

翼尖門票 SaaS 多租戶資料庫腳本和應用程式原始程式碼可在[翼尖TicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub存儲庫中提供。 如需有關下載 Wingtip Tickets PowerShell 指令碼及將其解除封鎖的步驟，請參閱[一般指引](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>安裝和配置日誌分析工作區和 Azure SQL 分析解決方案

Azure 監視器是必須配置的單獨服務。 Azure 監視器日誌在日誌分析工作區中收集日誌資料、遙測和指標。 就像 Azure 中的其他資源一樣，Log Analytics 工作區是必須建立的資源。 工作區不一定要建立在與其監視之應用程式相同的資源群組中。 不過，這麼做通常最有意義。 針對 Wingtip Tickets 應用程式，請使用單一資源群組，以確保工作區會隨著應用程式一起刪除。

1. 在 PowerShell ISE 中，打開 *。WingtipTicketsSaaS-多租戶Db\\主\\學習模組性能監控和管理\\日誌分析\\演示-日誌分析.ps1 . \\ *
1. 若要執行指令碼，請按 F5。

現在，您可以在 Azure 門戶中打開 Azure 監視器日誌。 需要幾分鐘的時間才能將遙測資料收集至 Log Analytics 工作區中並顯示出來。 您讓系統收集診斷資料的時間越長，體驗就會更有趣。 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>使用日誌分析工作區和 SQL 分析解決方案監視池和資料庫


在本練習中，在 Azure 門戶中打開日誌分析工作區，查看為資料庫和池收集的遙測資料。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。 選擇**所有服務**以打開日誌分析工作區。 然後搜尋 Log Analytics。

   ![打開日誌分析工作區](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. 選取名為 wtploganalytics-&lt;user&gt;__ 的工作區。

1. 選擇 **"概述"** 以在 Azure 門戶中打開日誌分析解決方案。

   ![總覽](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > 解決方案可能需要幾分鐘的時間才會變成作用中。 

1. 選取 [Azure SQL 分析]**** 圖格來開啟它。

    ![[概觀] 圖格](media/saas-dbpertenant-log-analytics/overview.png)

1. 解決方案中的檢視會橫向捲動，其中底部會有自己的內部捲軸。 請視需要重新整理頁面。

1. 若要瀏覽摘要頁面，請選取圖格或個別的資料庫，以開啟向下鑽研的總管頁面。

    ![日誌分析儀表板](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. 變更篩選設定以修改時間範圍。 針對本教學課程，請選取 [過去 1 小時]****。

    ![時間篩選器](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. 選取個別資料庫，以探索該資料庫的查詢使用情況和計量。

    ![資料庫分析](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. 若要查看使用計量，請向右捲動分析頁面。
 
     ![資料庫計量](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. 向左捲動分析頁面，然後選取 [資源資訊]**** 清單中的伺服器圖格。  

    ![[資源資訊] 清單](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    隨即會開啟一個頁面，當中顯示該伺服器上的集區和資料庫。

    ![具有集區和資料庫的伺服器](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. 選取一個集區。 在開啟的集區頁面上，向右捲動到看到集區計量。 

    ![集區計量](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. 回到 Log Analytics 工作區，選取 [OMS 入口網站]**** 以在該處開啟工作區。

    ![Log Analytics 工作區](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

在 Log Analytics 工作區中，您可以進一步探索記錄和計量資料。 

Azure 監視器日誌中的監視和警報基於對工作區中資料的查詢，這與 Azure 門戶中每個資源上定義的警報不同。 由於警示是以查詢為基礎，因此您可以定義可查看所有資料庫的單一警示，而不是為每個資料庫定義一個警示。 查詢僅受限於工作區中可用的資料。

有關如何使用 Azure 監視器日誌查詢和設置警報的詳細資訊，請參閱在 Azure[監視器日誌中使用警報規則](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)。

Azure 監視器基於工作區中的資料卷記錄 SQL 資料庫費用。 在本教學課程中，您已建立一個免費工作區，其每天的限制為 500 MB。 在達到該限制之後，就不會再將資料新增至該工作區。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 安裝和配置 Azure 監視器日誌。
> * 使用 Azure 監視器日誌監視池和資料庫。

請嘗試[租用戶分析教學課程](saas-dbpertenant-log-analytics.md)。

## <a name="additional-resources"></a>其他資源

* [以每一租用戶一個資料庫的初始 Tickets SaaS 應用程式部署為基礎的其他教學課程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure 監視器記錄](../azure-monitor/insights/azure-sql.md)
