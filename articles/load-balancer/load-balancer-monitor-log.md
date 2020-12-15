---
title: 監視公用負載平衡器的作業、事件和計數器
titleSuffix: Azure Load Balancer
description: 瞭解如何啟用 Azure Load Balancer 的記錄。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: fcfd3da30ef9ace723b4204f5924591b1e2717f8
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503160"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Azure Standard Load Balancer 的 Azure 監視器記錄

您可以使用不同類型的 Azure 監視器記錄來管理和疑難排解 Azure Standard Load Balancer。 記錄可以串流至事件中樞或 Log Analytics 工作區。 您可以從 Azure Blob 儲存體解壓縮所有記錄，並在 Excel 和 Power BI 等工具中加以查看。 

記錄類型為：

* **活動記錄：** 您可以查看所有提交至您 Azure 訂用帳戶的活動及其狀態。 如需詳細資訊，請參閱 [查看活動記錄以監視資源上的動作](../azure-resource-manager/management/view-activity-logs.md)。 預設會啟用活動記錄，並可在 Azure 入口網站中查看。 這些記錄適用于 Azure 基本 Load Balancer 和 Standard Load Balancer。
* **Standard Load Balancer 計量：** 您可以使用此記錄來查詢匯出為 Standard Load Balancer 記錄的度量。 這些記錄僅適用于 Standard Load Balancer。

> [!IMPORTANT]
> 健康情況探查和 Load Balancer 警示事件記錄檔目前無法運作，而且會列在 [Azure Load Balancer 的已知問題](whats-new.md#known-issues)中。 

> [!IMPORTANT]
> 記錄僅適用於在 Azure Resource Manager 部署模型中部署的資源。 您無法將記錄用於傳統部署模型中的資源。 如需這些部署模型的詳細資訊，請參閱[了解 Resource Manager 部署和傳統部署](../azure-resource-manager/management/deployment-models.md)。

## <a name="enable-logging"></a>啟用記錄

每個 Resource Manager 資源都會自動啟用活動記錄功能。 啟用事件和健康情況探查記錄，以開始收集可透過這些記錄取得的資料。 使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 如果您還沒有負載平衡器，請先 [建立負載平衡器](./quickstart-load-balancer-standard-public-portal.md) 再繼續。
1. 在入口網站中，選取 [ **資源群組**]。
2. 選取 **\<resource-group-name>** 負載平衡器所在的位置。
3. 選取負載平衡器。
4. 選取 [**活動記錄**  >  **診斷設定**]。
5. 在 [ **診斷設定** ] 窗格中，選取 [ **診斷設定**] 下的 [ **+ 新增診斷設定**]。
6. 在 [**診斷設定** 建立] 窗格的 [**名稱**] 方塊中，輸入 **myLBDiagnostics** 。
7. **診斷設定** 有三個選項。 您可以選擇其中一個、兩個或全部三個，並針對您的需求進行設定：

   * **封存至儲存體帳戶**。 您將需要已為此進程建立的儲存體帳戶。 若要建立儲存體帳戶，請參閱[如何建立儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)。
     1. 選取 [封存 **至儲存體帳戶** ] 核取方塊。
     2. 選取 [ **設定** ] 以開啟 [ **選取儲存體帳戶** ] 窗格。
     3. 在 [ **訂** 用帳戶] 下拉式清單中，選取您的儲存體帳戶建立所在的訂用帳戶。
     4. 在 [ **儲存體帳戶** ] 下拉式清單中，選取您的儲存體帳戶名稱。
     5. 選取 [確定]。

   * **串流處理至事件中樞**。 您將需要已經為此進程建立的事件中樞。 若要建立事件中樞，請參閱 [快速入門：使用 Azure 入口網站建立事件中樞](../event-hubs/event-hubs-create.md)。
     1. 選取 [串流至事件中樞] 核取方塊。
     2. 選取 [ **設定** ] 以開啟 [ **選取事件中樞** ] 窗格。
     3. 在 [ **訂** 用帳戶] 下拉式清單中，選取您的事件中樞建立所在的訂用帳戶。
     4. 在 [ **選取事件中樞命名空間** ] 下拉式清單中，選取命名空間。
     5. 在 [ **選取事件中樞原則名稱** ] 下拉式清單中，選取名稱。
     6. 選取 [確定]。

   * **傳送至 Log Analytics**。 您必須已經為此程式建立並設定 log analytics 工作區。 若要建立 Log Analytics 工作區，請參閱 [Azure 入口網站中的建立 Log analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。
     1. 選取 [傳送至 Log Analytics] 核取方塊。
     2. 在 [ **訂** 用帳戶] 下拉式清單中，選取您的 Log Analytics 工作區所在的訂用帳戶。
     3. 在 [ **Log Analytics 工作區** ] 下拉式清單中，選取工作區。

8. 在 [**診斷設定**] 窗格的 [**度量**] 區段中，選取 [ **AllMetrics** ] 核取方塊。

9. 確認一切看起來正確，然後選取 [**診斷設定** 建立] 窗格頂端的 [**儲存**]。

## <a name="view-and-analyze-the-activity-log"></a>檢視和分析活動記錄檔

預設會產生活動記錄。 您可以 [遵循本文中的指示](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)，將其設定為在訂用帳戶層級上匯出。 若要深入瞭解這些記錄，請閱讀 [View 活動記錄以監視資源的動作](../azure-resource-manager/management/view-activity-logs.md) 文章。

您可以使用下列其中一種方法來查看和分析活動記錄資料：

* **Azure 工具：** 透過 Azure PowerShell、Azure CLI、Azure REST API 或 Azure 入口網站，從活動記錄中取出資訊。 [使用 Resource Manager 的「審核作業](../azure-resource-manager/management/view-activity-logs.md)」一文會提供每個方法的逐步指示。
* **Power BI︰** 如果還沒有 [Power BI](https://powerbi.microsoft.com/pricing) 帳戶，您可以免費試用。 藉由使用 [適用于 Power BI 的 Azure Audit Logs 整合](https://powerbi.microsoft.com/integrations/azure-audit-logs/)，您可以使用預先設定的儀表板來分析您的資料。 或者，您也可以自訂視圖以符合您的需求。

## <a name="view-and-analyze-metrics-as-logs"></a>以記錄形式來查看和分析計量
藉由使用 Azure 監視器中的匯出功能，您可以匯出 Load Balancer 計量。 這些計量會產生每一分鐘取樣間隔的記錄專案。

每個資源層級都會啟用計量至記錄匯出。 若要啟用這些記錄：

1. 移至 [ **診斷設定** ] 窗格。
1. 依資源群組篩選，然後選取您想要為其啟用計量匯出的 Load Balancer 實例。 
1. Load Balancer 的 [診斷設定] 頁面開啟時，請選取 [ **AllMetrics** ]，將合格的計量匯出為記錄檔。

如需計量匯出的限制，請參閱本文的「 [限制](#limitations) 」一節。

在 Standard Load Balancer 的診斷設定中啟用 **AllMetrics** 之後，如果您使用的是事件中樞或 Log Analytics 工作區，這些記錄將會填入 **AzureMonitor** 資料表中。 

如果您要匯出至儲存體，請連接到您的儲存體帳戶，並取得事件和健康情況探查記錄的 JSON 記錄專案。 下載 JSON 檔案之後，您可以將它們轉換成 CSV，並在 Excel、Power BI 或任何其他資料視覺化檢視中加以查看。 

> [!TIP]
> 如果您很熟悉如何在 c # 中變更常數和變數值的 Visual Studio 和基本概念，您可以使用 GitHub 提供的 [記錄檔轉換器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter) 。

## <a name="stream-to-an-event-hub"></a>串流至事件中樞
當診斷資訊串流至事件中樞時，您可以將它用於具有 Azure 監視器整合的合作夥伴 SIEM 工具中的集中式記錄分析。 如需詳細資訊，請參閱 [將 Azure 監視資料串流至事件中樞](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)。

## <a name="send-to-log-analytics"></a>傳送至 Log Analytics
您可以將 Azure 中資源的診斷資訊直接傳送至 Log Analytics 工作區。 在該工作區中，您可以對資訊執行複雜的查詢，以進行疑難排解和分析。 如需詳細資訊，請參閱 [在 Azure 監視器中收集 Log Analytics 工作區中的 Azure 資源記錄](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)。

## <a name="limitations"></a>限制
Azure Load Balancer 的計量對記錄匯出功能有下列限制：
* 當匯出為記錄時，計量目前會透過內部名稱顯示。 您可以在下表中找到對應。
* 不會保留度量的維度。 例如，使用 **DipAvailability** (健康情況探查狀態) 之類的計量，您將無法依後端 IP 位址進行分割或查看。
* 使用的 SNAT 埠和已配置 SNAT 埠的計量目前無法匯出為記錄。

## <a name="next-steps"></a>後續步驟
* [檢查負載平衡器的可用計量](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [遵循 Azure 監視器指示來建立及測試查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
