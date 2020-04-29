---
title: 透過 webhook 傳送 Azure 服務健康狀態通知
description: 將有關服務健康狀態事件的個人化通知傳送到您現有的問題管理系統。
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062846"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>使用 webhook 設定問題管理系統的健康情況通知

本文說明如何設定 Azure 服務健康狀態警示，以便透過 webhook 將資料傳送到您現有的通知系統。

您可以設定服務健康狀態警示，以便在 Azure 服務事件影響您時，以文字訊息或電子郵件通知您。

但您可能已經有想要使用的現有外部通知系統。 本文會識別 webhook 承載中最重要的部分。 並說明如何建立自訂警示，以在發生相關服務問題時通知您。

如果您想要使用預先設定的整合，請參閱：
* [使用 ServiceNow 設定警示](service-health-alert-webhook-servicenow.md)
* [使用 PagerDuty 設定警示](service-health-alert-webhook-pagerduty.md)
* [使用 OpsGenie 設定警示](service-health-alert-webhook-opsgenie.md)

**觀看簡介影片：**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>使用服務健康狀態 webhook 承載來設定自訂通知
若要設定您自己的自訂 webhook 整合，您必須剖析透過服務健康狀態通知傳送的 JSON 承載。

請參閱[範例](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` webhook 承載。

您可以藉由查看來確認它是服務健康狀態警示`context.eventSource == "ServiceHealth"`。 以下是最相關的屬性：
- **activityLog。狀態**
- **activityLog. level**
- **activityLog. subscriptionId**
- **activityLog. properties. title**
- **activityLog. properties. Impactstarttime 開始**
- **activityLog. 屬性。通訊**
- **activityLog. properties. impactedServices**
- **activityLog. properties. trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>建立事件服務健康狀態儀表板的連結
您可以藉由產生特殊的 URL，在桌上型電腦或行動裝置上建立服務健康狀態儀表板的直接連結。 使用*trackingId*和您的*subscriptionId*的前三個和最後三個數字，格式如下：

HTTPs<i></i>：//*&lt;app.azure.com/h/&gt;trackingId*/*&lt;subscriptionId&gt;的前三個和最後三個數字*

例如，如果您的*subscriptionId*是 bba14129-e895-429b-8809-278e836ecdb3，而您的*trackingId*是 0DET-URB，則您的服務健康狀態 URL 為：

HTTPs<i></i>：//app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>使用層級來偵測問題的嚴重性
從最低到最高嚴重性，裝載中的**level**屬性可以是*資訊*、*警告*、*錯誤*或*重大*。

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>剖析受影響的服務以判斷事件範圍
服務健康狀態警示可以通知您跨多個區域和服務的問題。 若要取得完整的詳細資料，您必須剖析的`impactedServices`值。

內的內容是一個已轉義的[json](https://json.org/)字串，如果未經過轉義，則會包含可定期剖析的另一個 json 物件。 例如：

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

滿

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

這個範例會顯示下列問題：
- [澳大利亞東部和澳大利亞東南部中的警示 & 計量]。
- 澳大利亞東南部中的「App Service」。

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>透過 HTTP POST 要求測試 webhook 整合

請遵循下列步驟：

1. 建立您想要傳送的服務健康情況承載。 如需[Azure 活動記錄警示](../azure-monitor/platform/activity-log-alerts-webhook.md)，請參閱 webhook 上的服務健康情況 webhook 承載範例。

1. 建立 HTTP POST 要求，如下所示：

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   您應該會收到「2XX-成功」回應。

1. 移至 [PagerDuty](https://www.pagerduty.com/)，以確認您的整合已設定成功。

## <a name="next-steps"></a>後續步驟
- 檢查[活動記錄警示 webhook 架構](../azure-monitor/platform/activity-log-alerts-webhook.md)。 
- 瞭解[服務健康狀態通知](../azure-monitor/platform/service-notifications.md)。
- 深入瞭解[動作群組](../azure-monitor/platform/action-groups.md)。