---
title: 監視公用基礎負載平衡器的作業、事件和計數器
titleSuffix: Azure Load Balancer
description: 瞭解如何啟用 Azure Load Balancer 的記錄
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
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572774"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>標準 Azure Load Balancer 的 Azure 監視器記錄

您可以在 Azure 中使用不同類型的記錄來管理標準負載平衡器，並對其進行疑難排解。 記錄可以串流至事件中樞或 Log Analytics 工作區。 所有記錄都可以從 Azure blob 儲存體解壓縮，並以不同的工具（例如 Excel 和 Power BI）來查看。  您可以從下列清單進一步了解不同類型的記錄。

* **活動記錄：** 您可以使用 [View 活動記錄來監視資源的動作](../azure-resource-manager/management/view-activity-logs.md) ，以查看提交至您的 Azure 訂用帳戶 () 的所有活動及其狀態。 預設會啟用活動記錄，並可在 Azure 入口網站中查看。 這些記錄適用于基本和標準負載平衡器。
* **Standard Load Balancer 計量：** 您可以使用此記錄來查詢作為標準 Azure Load Balancer 的記錄匯出的度量。 這些記錄僅適用于標準負載平衡器。

> [!IMPORTANT]
> **健康情況探查和 Load Balancer 警示事件記錄檔目前無法運作，而且會列在 [Azure Load Balancer 的已知問題](whats-new.md#known-issues)中。** 

> [!IMPORTANT]
> 記錄僅適用於在資源管理員部署模型中部署的資源。 您無法將記錄使用於傳統部署模型中的資源。 如需這些部署模型的詳細資訊，請參閱[了解 Resource Manager 部署和傳統部署](../azure-resource-manager/management/deployment-models.md)。

## <a name="enable-logging"></a>啟用記錄

每個 Resource Manager 資源都會自動啟用活動記錄功能。 啟用事件和健康情況探查記錄，以開始收集可透過這些記錄取得的資料。 使用下列步驟以啟用記錄功能。

登入 [Azure 入口網站](https://portal.azure.com)。 如果您還沒有負載平衡器，請先 [建立負載平衡器](./quickstart-load-balancer-standard-public-portal.md) 再繼續。

1. 在入口網站中，按一下 [ **資源群組**]。
2. 選取 **\<resource-group-name>** 負載平衡器所在的位置。
3. 選取負載平衡器。
4. 選取 [**活動記錄**  >  **診斷設定**]。
5. 在 [ **診斷設定** ] 窗格中，選取 [ **診斷設定**] 下的 [ **+ 新增診斷設定**]。
6. 在 [**診斷設定** 建立] 窗格的 [**名稱**] 欄位中，輸入 **myLBDiagnostics** 。
7. **診斷設定** 有三個選項。  您可以選擇其中一個、兩個或三個，並針對您的需求進行設定：
   * **封存至儲存體帳戶**
   * **串流至事件中樞**
   * **傳送至 Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>封存至儲存體帳戶
    您將需要已為此進程建立的儲存體帳戶。  若要建立儲存體帳戶，請參閱 [建立儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)

    1. 選取 [封存 **至儲存體帳戶**] 旁的核取方塊。
    2. 選取 [ **設定** ] 以開啟 [ **選取儲存體帳戶** ] 窗格。
    3. 在下拉式清單方塊中，選取您的儲存體帳戶建立所在的 **訂** 用帳戶。
    4. 在下拉式清單方塊中，選取 **儲存體帳戶** 底下的儲存體帳戶名稱。
    5. 選取 [確定]。

    ### <a name="stream-to-an-event-hub"></a>串流至事件中樞
    您將需要已經為此進程建立的事件中樞。  若要建立事件中樞，請參閱 [快速入門：使用 Azure 入口網站建立事件中樞](../event-hubs/event-hubs-create.md)

    1. 選取 [**串流至事件中樞**] 旁的核取方塊
    2. 選取 [ **設定** ] 以開啟 [ **選取事件中樞** ] 窗格。
    3. 在下拉式清單方塊中選取您的事件中樞建立所在的 **訂** 用帳戶。
    4. 在下拉式清單方塊中 **選取 [事件中樞命名空間**]。
    5. 在下拉式清單方塊中 **選取 [事件中樞原則名稱**]。
    6. 選取 [確定]。

    ### <a name="send-to-log-analytics"></a>傳送至 Log Analytics
    您必須已經為此程式建立並設定 log analytics 工作區。  若要建立 Log Analytics 工作區，請參閱[在 Azure 入口網站中建立 Log analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

    1. 選取 [ **傳送至 Log Analytics**] 旁的核取方塊。
    2. 在下拉式清單方塊中選取您的 Log Analytics 工作區所在的 **訂** 用帳戶。
    3. 在下拉式清單方塊中選取 **Log Analytics 工作區** 。


8.  在 [**診斷設定**] 窗格的 [計量 **] 區段下方**，選取 [： **AllMetrics** ] 旁邊的核取方塊。

9. 確認一切看起來正確，然後按一下 [建立 **診斷設定**] 窗格頂端的 [**儲存**]。

## <a name="activity-log"></a>活動記錄檔

預設會產生活動記錄。 您可以 [遵循本文中的指示](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)，將其設定為在訂用帳戶層級上匯出。 若要深入瞭解這些記錄，請閱讀 [View 活動記錄以監視資源的動作](../azure-resource-manager/management/view-activity-logs.md) 文章。

### <a name="view-and-analyze-the-activity-log"></a>檢視和分析活動記錄檔

您可以使用下列任何方法，檢視和分析活動記錄檔資料：

* **Azure 工具：** 透過 Azure PowerShell、Azure 命令列介面 (CLI) 、Azure REST API 或 Azure 入口網站，從活動記錄中取出資訊。 [稽核作業與資源管理員](../azure-resource-manager/management/view-activity-logs.md) 一文會詳述每個方法的逐步指示。
* **Power BI︰** 如果還沒有 [Power BI](https://powerbi.microsoft.com/pricing) 帳戶，您可以免費試用。 您可以使用 [適用于 Power BI 的 Azure Audit Logs 整合](https://powerbi.microsoft.com/integrations/azure-audit-logs/)，利用預先設定的儀表板來分析您的資料，也可以自訂視圖以符合您的需求。

## <a name="metrics-as-logs"></a>以記錄形式的計量
使用計量來記錄 Azure 監視器所提供的匯出功能時，您可以匯出 Load Balancer 計量。 這些計量會產生每個分鐘取樣間隔的記錄專案。

每個資源層級都會啟用計量至記錄匯出。 您可以前往 [診斷設定] 分頁、依資源群組篩選，然後選取您想要啟用計量匯出的 Load Balancer 來啟用這些記錄。 當 Load Balancer 診斷設定] 頁面開啟時，選取 [AllMetrics] 以將合格的計量匯出為記錄。

請參閱本文的「 [限制](#limitations) 」一節，以瞭解計量匯出的限制。

### <a name="view-and-analyze-metrics-as-logs"></a>以記錄形式來查看和分析計量
在 Standard Load Balancer 的診斷設定中啟用 AllMetrics 之後，如果使用事件中樞或 Log Analytics 工作區，這些記錄將會填入 AzureMonitor 資料表中。 如果匯出至儲存體，請連接到您的儲存體帳戶，並取得事件和健康情況探查記錄的 JSON 記錄專案。 下載 JSON 檔案後，您可以將它們轉換成 CSV，並在 Excel、Power BI 或任何其他資料視覺化檢視中查看。 

> [!TIP]
> 如果您熟悉 Visual Studio 以及在 C# 中變更常數和變數值的基本概念，您可以使用 GitHub 所提供的[記錄檔轉換器工具 (英文)](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## <a name="stream-to-an-event-hub"></a>串流至事件中樞
當診斷資訊串流至事件中樞時，它可以用於具有 Azure 監視器整合的協力廠商 SIEM 工具中的集中式記錄分析。 如需詳細資訊，請參閱 [將 Azure 監視資料串流至事件中樞](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>傳送至 Log Analytics
Azure 中的資源可以將其診斷資訊直接傳送至 Log Analytics 工作區，其中可針對疑難排解和分析資訊來執行複雜的查詢。  如需詳細資訊，請參閱 Azure 監視器中的 [收集 Log Analytics 工作區中的 Azure 資源記錄](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="limitations"></a>限制
使用計量來記錄負載平衡器的匯出功能時，目前有下列限制：
* 當匯出為記錄時，目前會使用內部名稱顯示計量，您可以在下表中找到對應
* 不會保留度量的維度。 例如，使用 DipAvailability (健康情況探查狀態) 的計量，您將無法依後端 IP 位址進行分割或查看
* 使用的 SNAT 埠和已配置的 SNAT 埠計量目前無法作為記錄匯出

## <a name="next-steps"></a>後續步驟
* [檢查您 Load Balancer 的可用計量](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [依照 Azure 監視器的指示建立和測試查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* 您可以使用下列連結，在本文中提供意見反應或 Load Balancer 功能
