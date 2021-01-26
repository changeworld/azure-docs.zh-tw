---
title: 監視 Azure 防火牆記錄和計量
description: 在本文中，您將瞭解如何啟用及管理 Azure 防火牆記錄和計量。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 52c6ef9edfc42bf1ad3b3279e0fa4e19b4cf502c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788259"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>監視 Azure 防火牆記錄和計量

您可以使用防火牆記錄來監視 Azure 防火牆。 您也可以使用活動記錄來稽核 Azure 防火牆資源上的作業。 使用計量，您可以在入口網站中檢視效能計數器。

您可以透過入口網站存取其中一些記錄。 記錄可以傳送至 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)、儲存體和事件中樞，並在 Azure 監視器記錄或不同的工具（例如 Excel 和 Power BI）中進行分析。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先決條件

開始之前，您應該先閱讀 [Azure 防火牆記錄和計量](logs-and-metrics.md) ，以瞭解適用于 azure 防火牆的診斷記錄和計量。

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>透過 Azure 入口網站啟用診斷記錄

在您完成此程序來開啟診斷記錄之後，資料可能需要幾分鐘的時間，才會出現在您的記錄中。 如果您一開始沒有看到任何項目，請在幾分鐘後重新檢查一次。

1. 在 Azure 入口網站中，開啟您的防火牆資源群組，然後選取防火牆。
2. 在 [監視]  下方，選取 [診斷設定]  。

   針對 Azure 防火牆，有四個可用的服務特定記錄：

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. 選取 [新增診斷設定]。 [診斷設定] 頁面中提供診斷記錄的設定。
5. 在此範例中，Azure 監視器記錄會儲存記錄，因此請輸入 **防火牆記錄分析** 作為名稱。
6. 在 [ **記錄**] 底下，選取 [ **>azurefirewallapplicationrule**]、[ **AzureFirewallNetworkRule**]、[ **AzureFirewallThreatIntelLog**] 和 [ **AzureFirewallDnsProxy** ] 以收集記錄。
7. 選取 [ **傳送至 Log Analytics** ] 以設定您的工作區。
8. 選取您的訂用帳戶。
9. 選取 [儲存]。

## <a name="enable-diagnostic-logging-by-using-powershell"></a>使用 PowerShell 啟用診斷記錄

每個 Resource Manager 資源都會自動啟用活動記錄功能。 您必須啟用診斷記錄，才能開始收集可透過這些記錄取得的資料。

若要使用 PowerShell 啟用診斷記錄，請使用下列步驟：

1. 請記下記錄資料儲存所在的 Log Analytics 工作區資源識別碼。 此值的格式為： `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>` 。

   您可以使用訂用帳戶中的任何工作區。 您可以使用 Azure 入口網站來尋找此資訊。 此資訊位於資源 **屬性** 頁面中。

2. 請記下您防火牆的資源識別碼 (將為其啟用記錄功能)。 此值的格式為： `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` 。

   您可以使用入口網站來尋找此資訊。

3. 使用下列 PowerShell Cmdlet 啟用所有記錄和計量的診斷記錄：

   ```powershell
   $diagSettings = @{
      Name = 'toLogAnalytics'
      ResourceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      WorkspaceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      Enabled = $true
   }
   Set-AzDiagnosticSetting  @diagSettings 
   ```

## <a name="enable-diagnostic-logging-by-using-the-azure-cli"></a>使用 Azure CLI 來啟用診斷記錄

每個 Resource Manager 資源都會自動啟用活動記錄功能。 您必須啟用診斷記錄，才能開始收集可透過這些記錄取得的資料。

若要使用 Azure CLI 啟用診斷記錄，請使用下列步驟：

1. 請記下記錄資料儲存所在的 Log Analytics 工作區資源識別碼。 此值的格式為： `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` 。

   您可以使用訂用帳戶中的任何工作區。 您可以使用 Azure 入口網站來尋找此資訊。 此資訊位於資源 **屬性** 頁面中。

2. 請記下您防火牆的資源識別碼 (將為其啟用記錄功能)。 此值的格式為： `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` 。

   您可以使用入口網站來尋找此資訊。

3. 使用下列 Azure CLI 命令啟用所有記錄和計量的診斷記錄：

   ```azurecli-interactive
   az monitor diagnostic-settings create -n 'toLogAnalytics'
      --resource '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      --workspace '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      --logs '[{\"category\":\"AzureFirewallApplicationRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallNetworkRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallDnsProxy\",\"Enabled\":true}]' 
      --metrics '[{\"category\": \"AllMetrics\",\"enabled\": true}]'
   ```

## <a name="view-and-analyze-the-activity-log"></a>檢視和分析活動記錄檔

您可以使用下列任何方法，檢視和分析活動記錄資料：

* **Azure 工具**：透過 Azure PowerShell、Azure CLI、Azure REST API 或 Azure 入口網站，從活動記錄擷取資訊。 [活動作業與 Resource Manager](../azure-resource-manager/management/view-activity-logs.md) 一文會詳述每個方法的逐步指示。
* **Power BI**：如果您還沒有 [Power BI](https://powerbi.microsoft.com/pricing) 帳戶，可以免費試用。 使用 [Power BI 的 Azure 活動記錄內容套件](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)，您可以使用預先設定的儀表板 (可按原樣使用或加以自訂) 來分析資料。
* **Azure Sentinel**：您可以將 Azure 防火牆記錄連線到 Azure Sentinel，讓您可以在活頁簿中查看記錄資料、使用它來建立自訂警示，並將其納入以改善您的調查。 Azure Sentinel 中的 Azure 防火牆資料連線器目前處於公開預覽狀態。 如需詳細資訊，請參閱 [連接 Azure 防火牆的資料](../sentinel/connect-azure-firewall.md)。

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>檢視及分析網路和應用程式規則記錄

[Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)會收集計數器和事件記錄。 它也納入了視覺效果和強大的搜尋功能來分析您的記錄。

如需 Azure 防火牆 log analytics 範例查詢，請參閱 [Azure 防火牆 log analytics](./firewall-workbook.md)範例。

[Azure 防火牆活頁簿](firewall-workbook.md) 為 azure 防火牆資料分析提供了彈性的畫布。 您可以使用它，在 Azure 入口網站中建立豐富的視覺效果報表。 您可以利用多個在 Azure 中部署的防火牆，並將它們結合成整合的互動式體驗。

您也可以連接到儲存體帳戶並擷取存取和效能記錄的 JSON 記錄項目。 下載 JSON 檔案後，可以將它們轉換成 CSV，並在 Excel、PowerBI 或任何其他資料視覺化工具中檢視它們。

> [!TIP]
> 如果您熟悉 Visual Studio 以及在 C# 中變更常數和變數值的基本概念，您可以使用 GitHub 所提供的[記錄檔轉換器工具 (英文)](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## <a name="view-metrics"></a>檢視計量
流覽至 Azure 防火牆，並在 [ **監視** 選取的 **計量**] 下進行。 若要檢視可用的值，請選取 [計量] 下拉式清單。

## <a name="next-steps"></a>後續步驟

既然您已設定防火牆來收集記錄，您可以探索 Azure 監視器記錄以檢視您的資料。

[使用 Azure 防火牆活頁簿監視記錄](firewall-workbook.md)

[Azure 監視器記錄中的網路監視解決方案](../azure-monitor/insights/azure-networking-analytics.md)