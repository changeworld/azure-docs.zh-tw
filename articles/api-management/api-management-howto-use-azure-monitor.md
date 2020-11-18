---
title: 教學課程 - 在 Azure API 管理中監視發佈的原則 | Microsoft Docs
description: 依照本教學課程的步驟，了解如何使用計量、警示、活動記錄和資源記錄，在 Azure API 管理中監視您的 API。
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 2317e61111c3ad328e8f112e7d9567f3f5d47990
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379302"
---
# <a name="tutorial-monitor-published-apis"></a>教學課程：監視發佈的 API

您可以使用 Azure 監視器，對來自 Azure API 管理服務的計量或記錄進行視覺化、查詢、路由、封存及操作。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢視 API 的計量 
> * 設定警示規則 
> * 檢視活動記錄
> * 啟用和檢視資源記錄

## <a name="prerequisites"></a>必要條件

+ 了解 [Azure API 管理術語](api-management-terminology.md)。
+ 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 此外，請完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>檢視 API 的計量

API 管理會每分鐘發出[計量](../azure-monitor/platform/data-platform-metrics.md)，讓您近乎即時地了解 API 的狀態和健康情況。 以下是兩個最常使用的計量。 如需所有可用計量的清單，請參閱[支援的計量](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)。

* **容量** - 協助您決定是否升級/降級 APIM 服務。 計量每分鐘發出，並反映提出報告時的閘道容量。 計量的範圍為 0 到 100，是根據 CPU 和記憶體使用率等閘道資源計算而來。
* **要求** - 協助您分析通過 API 管理服務的 API 流量。 計量會每分鐘發出，並回報具有維度的閘道要求數目，包括回應碼、位置、主機名稱和錯誤。 

> [!IMPORTANT]
> 下列計量已於 2019 年 5 月被取代，將於 2023 日 8 月淘汰：閘道要求總數、成功的閘道要求、未經授權的閘道要求、失敗的閘道要求、其他閘道要求。 請遷移至可提供對等功能的要求計量。

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="API 管理概觀中的計量的螢幕擷取畫面":::

存取計量：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。 在 [概觀] 頁面上，檢閱您 API 的重要計量。
1. 若要詳細調查計量，請從頁面底部附近的功能表中選取 [計量]。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="[監視] 功能表中的計量項目的螢幕擷取畫面":::

1. 從下拉式清單中，選取您想了解的計量。 例如， **要求**。 
1. 該圖表會顯示 API 呼叫的總數。
1. 您可以使用 [要求] 計量的維度來篩選圖表。 例如，選取 [新增篩選]，選取 [後端回應碼類別]，並輸入 500 作為值。 現在，圖表會顯示 API 後端中已失敗的要求數目。   

## <a name="set-up-an-alert-rule"></a>設定警示規則 

您可以根據計量和活動記錄來接收[警示](../azure-monitor/platform/alerts-metric-overview.md)。 Azure 監視器可讓您[將警示設定為](../azure-monitor/platform/alerts-metric.md)在觸發時執行下列動作︰

* 傳送電子郵件通知
* 呼叫 Webhook
* 叫用 Azure 邏輯應用程式

若要根據要求計量來設定範例警示規則：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。
1. 從靠近頁面底部的功能表列中選取 [警示]。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="[監視] 功能表中的 [警示] 選項的螢幕擷取畫面":::

1. 選取 [+ 新增警示規則]。
1. 在 [建立警示規則] 視窗中，按一下 [選取條件]。
1. 在 [設定訊號邏輯] 視窗中：
    1. 在 [訊號類型] 中，選取 [計量]。
    1. 在 [訊號名稱] 中，選取 [要求]。
    1. 在 [依維度分割] 的 [維度名稱] 中，選取 [閘道回應碼類別]。
    1. 在 [維度值] 中，針對用戶端錯誤 (例如未經授權或無效的要求) 選取 [4xx]。
    1. 在 [警示邏輯] 中，指定應觸發警示的閾值，然後選取 [完成]。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="設定訊號邏輯視窗的螢幕擷取畫面":::

1. 選取現有的動作群組或建立新的群組。 在下列範例中，會建立新的動作群組。 系統會將通知電子郵件傳送至 admin@contoso.com。 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="新動作群組的通知的螢幕擷取畫面":::

1. 輸入警示規則的名稱和說明，然後選取嚴重性層級。 
1. 選取 [建立警示規則]。
1. 接著，在沒有 API 金鑰的情況下呼叫 Conference API，藉以測試警示規則。 例如：

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    系統會根據評估期間觸發警示，並將電子郵件傳送至 admin@contoso.com。 

    警示也會出現在 API 管理執行個體的 [警示] 頁面上。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="入口網站中的警示的螢幕擷取畫面":::

## <a name="activity-logs"></a>活動記錄

活動記錄可讓您深入了解 API 管理服務上所執行的作業。 您可以使用活動記錄來判斷 API 管理服務上所執行之任何寫入作業 (PUT、POST、DELETE) 的「內容、對象和時間」。

> [!NOTE]
> 活動記錄不會納入讀取 (GET) 作業，也不會納入透過 Azure 入口網站或原始管理 API 所執行的作業。

您可以在 API 管理服務中存取活動記錄，或在 Azure 監視器中存取所有 Azure 資源的記錄。 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="入口網站中的活動記錄的螢幕擷取畫面":::

若要檢視活動記錄：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。

1. 選取 [活動記錄]。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="[監視] 功能表中的活動記錄項目的螢幕擷取畫面":::
1. 選取所需的篩選範圍，然後選取 [套用]。

## <a name="resource-logs"></a>資源記錄

資源記錄可提供豐富的作業與錯誤資訊，這些資訊對於稽核和疑難排解用途來說很重要。 資源記錄與活動記錄不同。 活動記錄可讓您深入了解在 Azure 資源上執行的作業。 資源記錄能讓您了解資源執行的作業。

若要設定資源記錄：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。
2. 選取 [診斷設定]。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="[監視] 功能表中的診斷設定項目的螢幕擷取畫面":::

1. 選取 [+新增診斷設定]。
1. 選取您要收集的記錄或計量。

   您可以將資源記錄連同計量封存至儲存體帳戶、將其串流至事件中樞，或將其傳送至 Log Analytics 工作區。 

如需詳細資訊，請參閱[建立診斷設定以將平台記錄和計量傳送至不同目的地](../azure-monitor/platform/diagnostic-settings.md)。

## <a name="view-diagnostic-data-in-azure-monitor"></a>在 Azure 監視器中檢視診斷資料

如果您在 Log Analytics 工作區中啟用收集 GatewayLogs 或計量的功能，則可能需要幾分鐘的時間，資料才會出現在 Azure 監視器中。 若要檢視資料：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。
1. 從靠近頁面底部的功能表中，選取 [記錄]。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="[監視] 功能表中的記錄項目的螢幕擷取畫面":::

執行查詢以檢視資料。 您可以使用數個隨附的[範例查詢](../azure-monitor/log-query/saved-queries.md)，或執行您自己的查詢。 例如，下列查詢會從 GatewayLogs 資料表中擷取最近 24 小時的資料：

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

如需與使用 API 管理的資源記錄有關的詳細資訊，請參閱：

* [開始使用 Azure 監視器 Log Analytics](../azure-monitor/log-query/get-started-portal.md)，或參閱 [Log Analytics 示範環境](https://portal.loganalytics.io/demo)。

* [Azure 監視器中的記錄查詢概觀](../azure-monitor/log-query/log-query-overview.md)。

下列 JSON 會針對成功的 API 要求，指出 GatewayLogs 中的範例項目。 如需詳細資訊，請參閱[結構描述參考](gateway-log-schema-reference.md)。 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 檢視 API 的計量
> * 設定警示規則 
> * 檢視活動記錄
> * 啟用和檢視資源記錄


前進到下一個教學課程：

> [!div class="nextstepaction"]
> [追蹤呼叫](api-management-howto-api-inspector.md)
