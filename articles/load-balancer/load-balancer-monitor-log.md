---
title: 監視公用基礎負載平衡器的作業、事件和計數器
titleSuffix: Azure Load Balancer
description: 了解如何啟用 Basic Load Balancer 的警示事件和探查健康情況狀態記錄
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 0a21af683d9fa7849d3e96c545983c9f40a8d4c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935324"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>公用基礎負載平衡器的 Azure 監視器記錄

>[!IMPORTANT]
>Azure Load Balancer 支援兩種不同類型：基本和標準。 本文討論基本 Load Balancer。 如需有關 Standard Load Balancer 的詳細資訊，請參閱 [Standard Load Balancer 概觀](load-balancer-standard-overview.md)，其中會透過 Azure 監視器中的多維度計量來公開遙測資料。

您可以在 Azure 中使用不同類型的記錄來管理 Basic Load Balancer，並對其進行疑難排解。 透過入口網站可以存取其中一些記錄。 可以將日誌資料流到事件中心或日誌分析工作區。 所有日誌都可以從 Azure Blob 存儲中提取，並在不同的工具（如 Excel 和 Power BI）中查看。  您可以從下列清單進一步了解不同類型的記錄。

* **活動日誌：** 可以使用["查看活動日誌"監視資源上的操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)，以查看提交到 Azure 訂閱的所有活動及其狀態。 預設情況下啟用活動日誌，可以在 Azure 門戶中查看。
* **警示事件記錄：** 您可以使用此記錄來檢視負載平衡器所引發的警示。 系統每五分鐘會收集一次負載平衡器的狀態。 只有在引發負載平衡器警示事件時，才會寫入此記錄檔。
* **健康狀態探查記錄︰** 您可以使用此記錄來檢視健康狀態探查所偵測到的問題，例如後端集區中因為健康狀態探查失敗而未從負載平衡器接收要求的執行個體數目。 健康狀態探查狀態發生變更時會寫入此記錄。

> [!IMPORTANT]
> Azure 監視器日誌目前僅適用于公共基本負載等化器。 記錄僅適用於在資源管理員部署模型中部署的資源。 您無法將記錄使用於傳統部署模型中的資源。 如需這些部署模型的詳細資訊，請參閱[了解 Resource Manager 部署和傳統部署](../azure-resource-manager/management/deployment-models.md)。

## <a name="enable-logging"></a>啟用記錄

每個 Resource Manager 資源都會自動啟用活動記錄功能。 啟用事件和運行狀況探測日誌記錄，以開始收集通過這些日誌提供的資料。 使用下列步驟以啟用記錄功能。

登錄到 Azure[門戶](https://portal.azure.com)。 如果您還沒有負載平衡器，請先 [建立負載平衡器](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) 再繼續。

1. 在門戶中，按一下 **"資源組**"。
2. 選擇負載平衡器所在的**\<資源組名稱>。**
3. 選擇負載等化器。
4. 選擇 **"監視** > **診斷設置**"。
5. 在 **"診斷設置"** 窗格中，在 **"診斷設置"** 下，選擇 **"添加診斷"設置**。
6. 在 **"診斷設置**創建"窗格中，在 **"名稱"** 欄位中輸入 **"我的LB診斷**"。
7. 對於**診斷設置**，有三個選項。  您可以選擇一個、兩個或全部三個，並根據您的要求配置每個：
   * **存檔到存儲帳戶**
   * **資料流到事件中心**
   * **傳送至 Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>封存至儲存體帳戶
    您需要為此過程創建的存儲帳戶。  要創建存儲帳戶[，請參閱創建存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. 選擇 **"存檔"** 旁邊的核取方塊。
    2. 選擇 **"配置**"以打開 **"選擇存儲帳戶窗格**"。
    3. 選擇下拉清單中創建存儲帳戶的**訂閱**。
    4. 在下拉清單中的 **"存儲帳戶"** 下選擇存儲帳戶的名稱。
    5. 選取 [確定]。

    ### <a name="stream-to-an-event-hub"></a>串流至事件中樞
    您需要為此進程創建一個事件中心。  要創建事件中心，請參閱[快速入門：使用 Azure 門戶創建事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. 選擇 **"流"旁邊的核取方塊到事件中心**
    2. 選擇 **"配置**"以打開 **"選擇事件中心"** 窗格。
    3. 選擇在下拉清單中創建事件中心的位置的**訂閱**。
    4. 在下拉清單中**選擇事件中心命名空間**。
    5. 在下拉清單中**選擇事件中心策略名稱**。
    6. 選取 [確定]。

    ### <a name="send-to-log-analytics"></a>傳送至 Log Analytics
    您需要已經為此過程創建和配置日誌分析工作區。  要創建日誌分析工作區，請參閱[在 Azure 門戶中創建日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. 選擇 **"發送到日誌分析**"旁邊的核取方塊。
    2. 選擇日誌分析工作區在下拉清單中的 **"訂閱**"。
    3. 在下拉清單中選擇**日誌分析工作區**。


8. 在 **"診斷設置"** 窗格中的 **"LOG"** 部分下方，選擇兩者旁邊的核取方塊：
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  在 **"診斷設置"** 窗格中的 **"METRIC"** 部分下方，選擇以下核取方塊：
   * **所有指標**

11. 驗證一切看起來正確，然後按一下"創建**診斷設置"** 窗格頂部的 **"保存**"。

## <a name="activity-log"></a>活動記錄檔

預設情況下組建活動日誌。 記錄會在 Azure 的 [事件記錄] 存放區中保留 90 天。 通過閱讀["查看活動日誌"來監視資源文章的操作，](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)瞭解有關這些日誌的更多內容。

## <a name="archive-to-storage-account-logs"></a>存檔到存儲帳戶日誌

### <a name="alert-event-log"></a>警示事件記錄檔

您必須對每一個負載平衡器進行啟用，才會產生此記錄檔。 事件會以 JSON 格式記錄，並儲存在您啟用記錄時所指定的儲存體帳戶中。 下面的示例是事件。

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

JSON 輸出顯示*事件名稱*屬性，該屬性將描述負載等化器創建警報的原因。 在這種情況下，生成的警報是由於源 IP NAT 限制 （SNAT） 導致的 TCP 埠耗盡。

### <a name="health-probe-log"></a>健全狀況探查記錄檔

如果您已如上所述對每一個負載平衡器進行啟用，才會產生此記錄檔。 資料會儲存在您啟用記錄時所指定的儲存體帳戶中。 系統會建立名為 'insights-logs-loadbalancerprobehealthstatus' 的容器，並記錄下列資料：

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

JSON 輸出在屬性欄位中顯示了探查健全狀況狀態的基本資訊。 *dipDownCount*屬性顯示後端上的實例總數，由於探測回應失敗，這些實例未接收網路流量。

### <a name="view-and-analyze-the-activity-log"></a>檢視和分析活動記錄檔

您可以使用下列任何方法，檢視和分析活動記錄檔資料：

* **Azure 工具：** 通過 Azure PowerShell、Azure 命令列介面 （CLI）、Azure REST API 或 Azure 門戶從活動日誌中檢索資訊。 [稽核作業與資源管理員](../azure-resource-manager/management/view-activity-logs.md) 一文會詳述每個方法的逐步指示。
* **Power BI︰** 如果還沒有 [Power BI](https:// .microsoft.com/pricing) 帳戶，您可以免費試用。 使用 [Power BI 的 Azure 稽核記錄內容套件](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)，您可以使用預先設定的儀表板來分析資料，或根據您的需求自訂檢視。

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>檢視和分析健全狀況探查與事件記錄檔

連接到您的存儲帳戶並檢索事件和運行狀況探測日誌的 JSON 日誌條目。 下載 JSON 檔後，您可以將它們轉換為 CSV，並在 Excel、Power BI 或任何其他資料視覺化檢視中查看。

> [!TIP]
> 如果您熟悉 Visual Studio 以及在 C# 中變更常數和變數值的基本概念，您可以使用 GitHub 所提供的[記錄檔轉換器工具 (英文)](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## <a name="stream-to-an-event-hub"></a>串流至事件中樞
將診斷資訊資料流到事件中心時，可以使用 Azure 監視器集成的協力廠商 SIEM 工具進行集中式日誌分析。 有關詳細資訊，請參閱將[Azure 監視資料流程式傳輸到事件中心](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>傳送至 Log Analytics
Azure 中的資源可以直接將其診斷資訊發送到日誌分析工作區，在那裡可以根據資訊運行複雜的查詢以進行故障排除和分析。  有關詳細資訊，請參閱在[Azure 監視器中的日誌分析工作區中收集 Azure 資源日誌](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>後續步驟

[了解負載平衡器偵查](load-balancer-custom-probe-overview.md)
