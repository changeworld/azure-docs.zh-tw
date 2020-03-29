---
title: 通過 Webhook 發送 Azure 服務運行狀況通知
description: 向現有問題管理系統發送有關服務運行狀況事件的個人化通知。
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062846"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>使用 Webhook 為問題管理系統配置運行狀況通知

本文介紹如何配置 Azure 服務運行狀況警報，以便通過 Webhook 將資料發送到現有通知系統。

您可以佈建服務運行狀況警報，以便在 Azure 服務事件影響您時通過短信或電子郵件通知您。

但是，您可能已經擁有了您喜歡使用的現有外部通知系統。 本文標識了 Webhook 負載的最重要部分。 它還描述了如何創建自訂警報，以便在發生相關服務問題時通知您。

如果要使用預配置的集成，請參閱：
* [使用 ServiceNow 設定警示](service-health-alert-webhook-servicenow.md)
* [使用 PagerDuty 設定警示](service-health-alert-webhook-pagerduty.md)
* [使用 OpsGenie 設定警示](service-health-alert-webhook-opsgenie.md)

**觀看介紹性視頻：**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>使用服務運行狀況 Webhook 負載配置自訂通知
要設置自己的自訂 Webhook 集成，您需要分析通過服務運行狀況通知發送的 JSON 負載。

請參閱[一個示例](../azure-monitor/platform/activity-log-alerts-webhook.md)`ServiceHealth`Webhook 負載。

您可以通過查看`context.eventSource == "ServiceHealth"`來確認它是服務運行狀況警報。 以下屬性最相關：
- **資料.上下文.活動日誌.狀態**
- **資料.上下文.活動日誌.級別**
- **資料.上下文.活動日誌.訂閱 Id**
- **資料.上下文.活動日誌.屬性.標題**
- **資料.上下文.活動日誌.屬性.影響開始時間**
- **資料.上下文.活動日誌.屬性.通信**
- **資料.上下文.活動日誌.屬性.受影響的服務**
- **資料.上下文.活動日誌.屬性.跟蹤Id**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>為事件創建指向服務運行狀況儀表板的連結
您可以通過生成專用 URL 在桌面或行動裝置上創建指向服務運行狀況儀表板的直接連結。 使用此格式使用*跟蹤 Id*和訂閱*Id*的前三位和最後三位數位：

<i> </i>HTTPs：//app.azure.com/h/*&lt;跟蹤&gt;Id*/*&lt;訂閱 Id&gt;的前三位和最後三位數位*

例如，如果您的訂閱*Id*為 bba14129-e895-429b-8809-278e836ecdb3，而您的*跟蹤 Id*為 0DET-URB，則服務運行狀況 URL 為：

HTTPs<i></i>：//app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>使用級別檢測問題的嚴重性
從最低嚴重性到最高嚴重性，有效負載中的**級別**屬性可以是*資訊*、*警告*、*錯誤*或*嚴重*。

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>分析受影響的服務以確定事件範圍
服務運行狀況警報可以通知您跨多個區域和服務的問題。 要獲取完整詳細資訊，您需要分析 的值`impactedServices`。

裡面的內容是一個轉義[的JSON](https://json.org/)字串，當未轉義時，它包含另一個可以定期解析的JSON物件。 例如：

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

成為：

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

此示例顯示：
- "警報&指標"在澳大利亞東部和澳大利亞東南部。
- "應用程式服務"在澳大利亞東南部。

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>通過 HTTP POST 請求測試 Webhook 集成

請遵循下列步驟：

1. 創建要發送的服務運行狀況負載。 有關[Azure 活動日誌警報](../azure-monitor/platform/activity-log-alerts-webhook.md)，請參閱 Webhook 中的服務運行狀況 Webhook 負載示例。

1. 建立 HTTP POST 要求，如下所示：

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   您應該收到"2XX - 成功"回應。

1. 移至 [PagerDuty](https://www.pagerduty.com/)，以確認您的整合已設定成功。

## <a name="next-steps"></a>後續步驟
- 查看[活動日誌警報 Webhook 架構](../azure-monitor/platform/activity-log-alerts-webhook.md)。 
- 瞭解[服務運行狀況通知](../azure-monitor/platform/service-notifications.md)。
- 瞭解有關[操作組](../azure-monitor/platform/action-groups.md)的更多。