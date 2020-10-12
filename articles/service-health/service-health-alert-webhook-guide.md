---
title: 透過 webhook 傳送 Azure 服務健康狀態通知
description: 將有關服務健康狀態事件的個人化通知傳送至您現有的問題管理系統。
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 05b0572c89a29fddc881f9977ee437d1319e6254
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518923"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>使用 webhook 來設定問題管理系統的健康情況通知

本文說明如何設定 Azure 服務健康狀態警示，以透過 webhook 將資料傳送至您現有的通知系統。

您可以設定服務健康狀態警示，以在 Azure 服務事件影響您時，透過文字訊息或電子郵件通知您。

但是您可能已經有想要使用的現有外部通知系統。 本文將識別 webhook 承載的最重要部分。 它會說明如何建立自訂警示，以在發生相關服務問題時通知您。

如果您想要使用預先設定的整合，請參閱：
* [使用 ServiceNow 設定警示](service-health-alert-webhook-servicenow.md)
* [使用 PagerDuty 設定警示](service-health-alert-webhook-pagerduty.md)
* [使用 OpsGenie 設定警示](service-health-alert-webhook-opsgenie.md)

**觀看簡介影片：**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>使用服務健康狀態 webhook 承載設定自訂通知
若要設定您自己的自訂 webhook 整合，您需要剖析透過服務健康狀態通知傳送的 JSON 承載。

請參閱 [範例](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` webhook 承載。

您可以查看來確認它是服務健康狀態警示 `context.eventSource == "ServiceHealth"` 。 以下是最相關的屬性：
- **activityLog。狀態**
- **activityLog 層級**
- **activityLog。 subscriptionId**
- **activityLog. title。**
- **activityLog. >impactstarttime。**
- **activityLog 的相關資料。**
- **activityLog. impactedServices。**
- **activityLog. trackingId。**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>建立事件的服務健康狀態儀表板連結
您可以藉由產生特殊的 URL，在桌面或行動裝置上建立服務健康狀態儀表板的直接連結。 使用 *trackingId* 和您的 *subscriptionId* 的前三個和最後三個數字，格式如下：

HTTPs： <i></i> //app.azure.com/h/* &lt; &gt; trackingId* / * &lt; subscriptionId &gt; 的前三個和最後三個數字*

例如，如果您的 *subscriptionId* 是 bba14129-e895-429b-8809-278e836ecdb3，而您的 *trackingId* 是 0DET-URB，則您的服務健康情況 URL 為：

HTTPs： <i></i> //app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>使用層級來偵測問題的嚴重性
從最低到最高的嚴重性，承載中的 **層級** 屬性可能是 *資訊*、 *警告*、 *錯誤*或 *嚴重*。

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>剖析受影響的服務以判斷事件範圍
服務健康狀態警示可通知您跨多個區域和服務的問題。 若要取得完整的詳細資料，您需要剖析的值 `impactedServices` 。

內的內容是一種已被轉義的 [json](https://json.org/) 字串，在非固定的情況下，會包含可定期剖析的另一個 JSON 物件。 例如：

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

 會變成：

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

此範例會顯示下列問題：
- 澳大利亞東部和澳大利亞東南部的「警示 & 計量」。
- 澳大利亞東南部中的「App Service」。

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>透過 HTTP POST 要求測試 webhook 整合

請遵循下列步驟：

1. 建立您想要傳送的服務健康情況承載。 如需 [Azure 活動記錄警示](../azure-monitor/platform/activity-log-alerts-webhook.md)，請參閱 webhook 中的範例服務健康情況 webhook 承載。

1. 建立 HTTP POST 要求，如下所示：

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   您應該會收到「2XX-成功」的回應。

1. 移至 [PagerDuty](https://www.pagerduty.com/)，以確認您的整合已設定成功。

## <a name="next-steps"></a>接下來的步驟
- 檢查 [活動記錄警示 webhook 架構](../azure-monitor/platform/activity-log-alerts-webhook.md)。 
- 深入了解[服務健康狀態通知](./service-notifications.md)。
- 深入了解[動作群組](../azure-monitor/platform/action-groups.md)。
