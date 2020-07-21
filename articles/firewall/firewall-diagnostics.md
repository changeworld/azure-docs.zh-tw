---
title: 監視 Azure 防火牆記錄和計量
description: 在本文中，您會瞭解如何啟用及管理 Azure 防火牆記錄和計量。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 07/10/2020
ms.author: victorh
ms.openlocfilehash: 82202705c5dbd4539eec4775d0844a749fd405f9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536998"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>監視 Azure 防火牆記錄和計量

您可以使用防火牆記錄來監視 Azure 防火牆。 您也可以使用活動記錄來稽核 Azure 防火牆資源上的作業。 使用計量，您可以在入口網站中檢視效能計數器。

您可以透過入口網站存取其中一些記錄。 您可以將記錄傳送到 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)、儲存體和事件中樞，並在 Azure 監視器記錄中或透過不同的工具 (例如 Excel 和 Power BI) 來分析記錄。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先決條件

開始之前，您應該先閱讀[Azure 防火牆記錄和計量](logs-and-metrics.md)，以深入瞭解適用于 azure 防火牆的診斷記錄和計量。

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>透過 Azure 入口網站啟用診斷記錄

在您完成此程序來開啟診斷記錄之後，資料可能需要幾分鐘的時間，才會出現在您的記錄中。 如果您一開始沒有看到任何項目，請在幾分鐘後重新檢查一次。

1. 在 [Azure 入口網站中，開啟您的防火牆資源群組，然後選取 [防火牆]。
2. 在 [監視]**** 下方，選取 [診斷設定]****。

   針對 Azure 防火牆，可以使用兩個服務特定的記錄：

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. 選取 [**新增診斷設定**]。 [診斷設定]**** 頁面中提供診斷記錄的設定。
5. 在此範例中，Azure 監視器記錄會儲存記錄，因此請輸入**防火牆記錄分析**作為名稱。
6. 在 [**記錄**] 下，選取 [ **AzureFirewallApplicationRule**和**AzureFirewallNetworkRule** ] 以收集應用程式和網路規則的記錄。
7. 選取 [**傳送至 Log Analytics** ] 以設定您的工作區。
8. 選取您的訂閱。
9. 選取 [儲存]。

## <a name="enable-logging-with-powershell"></a>使用 PowerShell 啟用記錄

每個 Resource Manager 資源都會自動啟用活動記錄功能。 您必須啟用診斷記錄，才能開始收集可透過這些記錄取得的資料。

若要啟用診斷記錄，請使用下列步驟：

1. 請記下您的儲存體帳戶的資源識別碼 (記錄資料的儲存之處)。 此值的形式為： */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> *。

   您可以使用訂用帳戶中的所有儲存體帳戶。 您可以使用 Azure 入口網站來尋找此資訊。 資訊位於資源的 [屬性]**** 頁面中。

2. 請記下您防火牆的資源識別碼 (將為其啟用記錄功能)。 此值的形式為： */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Network/azureFirewalls/ \<Firewall name\> *。

   您可以使用入口網站來尋找此資訊。

3. 使用下列 Powershell Cmdlet 啟用診斷記錄功能：

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>診斷記錄不需要個別的儲存體帳戶。 將儲存體用於記錄存取和效能會產生服務費用。

## <a name="view-and-analyze-the-activity-log"></a>檢視和分析活動記錄檔

您可以使用下列任何方法，檢視和分析活動記錄資料：

* **Azure 工具**：透過 Azure PowerShell、Azure CLI、Azure REST API 或 Azure 入口網站，從活動記錄擷取資訊。 [活動作業與 Resource Manager](../azure-resource-manager/management/view-activity-logs.md) 一文會詳述每個方法的逐步指示。
* **Power BI**：如果您還沒有[Power BI](https://powerbi.microsoft.com/pricing)帳戶，可以免費試用。 使用 [Power BI 的 Azure 活動記錄內容套件](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)，您可以使用預先設定的儀表板 (可按原樣使用或加以自訂) 來分析資料。

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>檢視及分析網路和應用程式規則記錄

[Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)會收集計數器和事件記錄。 它也納入了視覺效果和強大的搜尋功能來分析您的記錄。

如需 Azure 防火牆 log analytics 範例查詢，請參閱[Azure 防火牆 log analytics 範例](log-analytics-samples.md)。

您也可以連接到儲存體帳戶並擷取存取和效能記錄的 JSON 記錄項目。 下載 JSON 檔案後，可以將它們轉換成 CSV，並在 Excel、PowerBI 或任何其他資料視覺化工具中檢視它們。

> [!TIP]
> 如果您熟悉 Visual Studio 以及在 C# 中變更常數和變數值的基本概念，您可以使用 GitHub 所提供的[記錄檔轉換器工具 (英文)](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## <a name="view-metrics"></a>檢視計量
流覽至 [**監視**] [選取**計量**] 底下的 Azure 防火牆。 若要檢視可用的值，請選取 [計量]**** 下拉式清單。

## <a name="next-steps"></a>後續步驟

既然您已設定防火牆來收集記錄，您可以探索 Azure 監視器記錄以檢視您的資料。

[Azure 監視器記錄中的網路監視解決方案](../azure-monitor/insights/azure-networking-analytics.md)
