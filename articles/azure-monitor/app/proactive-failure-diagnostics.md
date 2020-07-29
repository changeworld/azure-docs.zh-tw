---
title: 智慧型偵測 - Application Insights 中的失敗異常 | Microsoft Docs
description: 針對 Web 應用程式失敗要求比率的不尋常變化對您發出警示，並提供診斷分析。 不需要設定。
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 30bb95c4c47c02fe6b3d31d6e6763656aa96fdec
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87315932"
---
# <a name="smart-detection---failure-anomalies"></a>智慧型偵測 - 失敗異常
當 Web 應用程式的失敗要求比率異常增加時，[Application Insights](./app-insights-overview.md) 會以幾乎即時的方式自動向您發出警示。 它偵測到回報為失敗的 HTTP 要求率異常提高或相依性呼叫。 對於要求，失敗的要求通常會有 400 或更高的回應碼。 為了協助您分級並診斷問題，警示詳細資料中會提供失敗的特性分析與相關應用程式資料。 其中也有 Application Insights 入口網站的連結，以供進一步診斷。 不需要設定該功能，因為它是使用機器學習演算法來預測一般失敗率。

這項功能適用於任何 Web 應用程式 (裝載於雲端或您自己的伺服器上)，其會產生應用程式要求或相依性資料。 例如，如果您的背景工作角色會呼叫 [TrackRequest()](./api-custom-events-metrics.md#trackrequest) 或 [TrackDependency()](./api-custom-events-metrics.md#trackdependency)。

設定[專案的 Application Insights](./app-insights-overview.md)之後，如果您的應用程式產生某些最少量的資料，錯誤異常的「智慧偵測」需先花費 24 小時來了解您應用程式的正常行為，然後才會啟動而能夠傳送警示。

以下是警示範例：

[![](./media/proactive-failure-diagnostics/013.png "Sample smart detection alert showing cluster analysis around failure")](./media/proactive-failure-diagnostics/013.png#lightbox)

警示詳細資料會告訴您：

* 相較於一般應用程式行為的失敗率。
* 受影響的使用者人數 - 讓您知道應處理的數目。
* 與失敗關聯的特性模式。 在此範例中，有特定的回應碼、要求名稱 (作業) 和應用程式版本。 可立即告訴您從何處開始了解程式碼。 其他可能性則可能是特定的瀏覽器或用戶端作業系統。
* 看起來似乎與特色失敗相關聯的例外狀況、記錄追蹤和相依性失敗 (資料庫或其他外部元件)。
* 直接連結到 Application Insights 的資料上相關搜尋。

## <a name="benefits-of-smart-detection"></a>智慧型偵測的優點
一般的 [度量警示](../platform/alerts-log.md) 會告訴您可能有問題。 但「智慧偵測」會為您啟動診斷工作，執行許多您原本必須自己執行的分析。 您達到幾近封裝完成的結果，幫助您快速取得問題的根源。

## <a name="how-it-works"></a>運作方式
「智慧偵測」會監視從應用程式收到的資料，特別是失敗率的資料。 此規則會計算 `Successful request` 屬性為 false 的要求數，以及 `Successful call` 屬性為 false 的相依性呼叫數。 對於要求，根據預設，`Successful request == (resultCode < 400)` (除非您撰寫自訂程式碼來[篩選](./api-filtering-sampling.md#filtering)或產生自己的 [TrackRequest](./api-custom-events-metrics.md#trackrequest) 呼叫)。 

您的應用程式效能具有一般的行為模式。 某些要求或相依性呼叫會比其他要求更容易失敗；且整體失敗率可能會隨著負載增加而上移。 「智慧型偵測」會使用機器學習服務來尋找這些異常狀況。

當資料從您的 Web 應用程式進入 Application Insights 時，「智慧偵測」會將目前的行為與過去幾天觀察到的模式做比較。 如果觀察到與先前效能相比，失敗率異常提高，就會觸發分析。

觸發分析時，服務會對失敗的要求執行叢集分析，以嘗試識別描述失敗特徵之值的模式。 

在上述範例中，分析已發現大部分失敗是關於特定結果碼、要求名稱、伺服器 URL 主機和角色執行個體。 

當您的服務使用這些呼叫進行檢測時，分析器會尋找與已識別叢集中要求建立關聯的例外狀況及相依性失敗，同時也包含與那些要求建立關聯的任何追蹤記錄範例。

產生的分析報告會以警示寄送給您，除非您已設定不接收該報告。

和[您手動設定的警示](../platform/alerts-log.md)一樣，您也可以檢查所引發警示的狀態，如果問題已修正，就可以解決此警示。 在 Application Insights 資源的 [警示] 頁面中設定警示規則。 但與其他警示不同，您並不需要設定「智慧型偵測」。 若有需要，您可以將它停用或變更其目標電子郵件地址。

### <a name="alert-logic-details"></a>警示邏輯詳細資料

這些警示是由我們專屬的機器學習演算法所觸發，因此我們無法共用確切的實作詳細資料。 不過，我們知道您有時需要深入了解基礎邏輯的運作方式。 評估以判斷是否應觸發警示的主要因素如下： 

* 分析要求/相依性在 20 分鐘累積時間範圍內的失敗百分比。
* 比較過去 20 分鐘的失敗百分比與過去 40 分鐘和過去七天的比率，以及尋找超過標準差 X 倍的重大偏差。
* 針對最小失敗百分比使用自動調整限制，此限制會依據要求/相依性的應用程式磁碟區而有所不同。
* 有邏輯可在 8-24 小時不再偵測到問題時，自動解決所引發警示的監視條件。

## <a name="configure-alerts"></a>設定警示

您可以從入口網站或使用 Azure Resource Manager ([請參閱範本範例](./proactive-arm-config.md)) 來停用智慧偵測警示規則。

建立此警示規則時，會使用名為「Application Insights 智慧偵測」的相關聯[動作群組](../platform/action-groups.md)，其中包含電子郵件和 Webhook 動作，並可加以擴充以在引發警示時觸發其他動作。

> [!NOTE]
> 從這項警示規則傳送的電子郵件通知，現在預設會傳送給與訂用帳戶的監視讀者和監視參與者角色相關聯的使用者。 如需這方面的詳細資訊，請參閱[這裡](./proactive-email-notification.md)。
> 從這項警示規則傳送過來的通知會遵循[一般警示結構描述](../platform/alerts-common-schema.md)。
>

開啟 [警示] 頁面。 「失敗異常」警示規則已包含在任何您已手動設定的警示中，您可以查看它目前是否處於警示狀態。

[![](./media/proactive-failure-diagnostics/021.png "On the Application Insights resource page, click 'Alerts' tile, then 'Manage alert rules'")](./media/proactive-failure-diagnostics/021.png#lightbox)

按一下警示來進行設定。

[![](./media/proactive-failure-diagnostics/032.png "Rule configuration screen")](./media/proactive-failure-diagnostics/032.png#lightbox)

請注意，您可以停用或刪除失敗異常警示規則，但無法在相同的 Application Insights 資源上建立另一個警示規則。

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>失敗異常警示 Webhook 承載的範例

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [["2019-10-30T05:20:00Z",
            0],
            ["2019-10-30T05:40:00Z",
            100],
            ["2019-10-30T06:00:00Z",
            0],
            ["2019-10-30T06:20:00Z",
            0],
            ["2019-10-30T06:40:00Z",
            100],
            ["2019-10-30T07:00:00Z",
            0],
            ["2019-10-30T07:20:00Z",
            0],
            ["2019-10-30T07:40:00Z",
            100],
            ["2019-10-30T08:00:00Z",
            0],
            ["2019-10-30T08:20:00Z",
            0],
            ["2019-10-30T08:40:00Z",
            100],
            ["2019-10-30T17:00:00Z",
            0],
            ["2019-10-30T17:20:00Z",
            0],
            ["2019-10-30T09:00:00Z",
            0],
            ["2019-10-30T09:20:00Z",
            0],
            ["2019-10-30T09:40:00Z",
            100],
            ["2019-10-30T10:00:00Z",
            0],
            ["2019-10-30T10:20:00Z",
            0],
            ["2019-10-30T10:40:00Z",
            100],
            ["2019-10-30T11:00:00Z",
            0],
            ["2019-10-30T11:20:00Z",
            0],
            ["2019-10-30T11:40:00Z",
            100],
            ["2019-10-30T12:00:00Z",
            0],
            ["2019-10-30T12:20:00Z",
            0],
            ["2019-10-30T12:40:00Z",
            100],
            ["2019-10-30T13:00:00Z",
            0],
            ["2019-10-30T13:20:00Z",
            0],
            ["2019-10-30T13:40:00Z",
            100],
            ["2019-10-30T14:00:00Z",
            0],
            ["2019-10-30T14:20:00Z",
            0],
            ["2019-10-30T14:40:00Z",
            100],
            ["2019-10-30T15:00:00Z",
            0],
            ["2019-10-30T15:20:00Z",
            0],
            ["2019-10-30T15:40:00Z",
            100],
            ["2019-10-30T16:00:00Z",
            0],
            ["2019-10-30T16:20:00Z",
            0],
            ["2019-10-30T16:40:00Z",
            100],
            ["2019-10-30T17:30:00Z",
            50]],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [{
                "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
            }],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [{
                "rootJsonNode": null,
                "sectionName": null,
                "displayControls": [{
                    "property": "DetectionSummary",
                    "displayName": "What was detected?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "FormattedOccurenceTime",
                    "displayName": "When did this occur?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "DetectedFailureRate",
                    "displayName": "Detected failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "NormalFailureRate",
                    "displayName": "Normal failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "chartType": "Line",
                    "xAxisType": "Date",
                    "yAxisType": "Percentage",
                    "xAxisName": "",
                    "yAxisName": "",
                    "property": "FailureRateChart",
                    "displayName": "Failure rate over last 12 hours",
                    "type": "Chart",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "defaultLoad": true,
                    "displayConfig": [{
                        "rootJsonNode": null,
                        "sectionName": null,
                        "displayControls": [{
                            "showHeader": false,
                            "columns": [{
                                "property": "Name",
                                "displayName": "Name"
                            },
                            {
                                "property": "Value",
                                "displayName": "Value"
                            }],
                            "property": "tables[0].rows[0][0]",
                            "displayName": "All of the failed requests had these characteristics:",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": true
                        }]
                    }],
                    "property": "ArmSystemEventsRequest",
                    "displayName": "",
                    "type": "ARMRequest",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "showHeader": false,
                    "columns": [{
                        "property": "Link",
                        "displayName": "Link"
                    }],
                    "property": "LinksTable",
                    "displayName": "Links",
                    "type": "Table",
                    "isOptional": false,
                    "isPropertySerialized": false
                }]
            }]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>針對警示進行分級和診斷

發出警示表示系統偵測到要求失敗率異常上升。 原因可能是您的應用程式或其環境有問題。

若要進一步調查，請按一下 [在 Application Insights 中檢視完整詳細資料]。此頁面中的連結會直接帶您前往[搜尋頁面](./diagnostic-search.md)，其已經過篩選而會顯示相關的要求、例外狀況、相依性或追蹤。 

您也可以開啟 [Azure 入口網站](https://portal.azure.com)，瀏覽至應用程式的 Application Insights 資源，並開啟 [失敗] 頁面。

按一下 [診斷失敗] 可協助您取得更多詳細資料並解決問題。

[![](./media/proactive-failure-diagnostics/051.png "Diagnostic search")](./media/proactive-failure-diagnostics/051.png#lightbox)

您可以根據要求的百分比及受影響的使用者數目來決定此問題的緊急程度。 在上述範例中，78.5% 的失敗率與正常比率 2.2% 相比，表示有問題。 從另一方面來看，只有 46 位使用者受到影響。 如果是您的應用程式，您可以評估嚴重程度。

在多數情況下，您可以根據要求名稱、例外狀況、相依性失敗及提供的追蹤資料來快速診斷問題。

在此範例中，由於達到要求限制，SQL Database 發生例外狀況。

[![](./media/proactive-failure-diagnostics/052.png "Failed request details")](./media/proactive-failure-diagnostics/052.png#lightbox)

## <a name="review-recent-alerts"></a>檢閱最近的警示

按一下 [Application Insights 資源] 頁面中的 [警示]，以取得最新引發的警示：

[![](./media/proactive-failure-diagnostics/070.png "Alerts summary")](./media/proactive-failure-diagnostics/070.png#lightbox)

## <a name="whats-the-difference-"></a>不同之處在於...
失敗異常的「智慧型偵測」可以與其他相似但不同的 Application Insights 功能互補。

* 計量[警示](../platform/alerts-log.md)是由您設定，而且可以監視各種不同的度量，例如 CPU 使用量、要求率、頁面載入時間等等。 您可以使用它們來向自己發出警告，例如，如果您需要增加更多資源時。 對比之下，失敗異常的「智慧偵測」只涵蓋小範圍的重要計量 (目前僅包含要求失敗率)，其設計目的是要在 Web 應用程式的要求失敗率與其正常行為相比有所增加時，能以幾乎即時的方式通知您。 不同於計量警示，智慧偵測會自動設定並更新行為中回應變更的臨界值。 智慧偵測也會為您啟動診斷工作，讓您省下解決問題的時間。

* [效能異常的智慧型偵測](proactive-performance-diagnostics.md)也會使用機器智慧在您的度量中探索不尋常的模式，且您不需要進行任何設定。 但與失敗異常的「智慧型偵測」不同，效能異常的「智慧型偵測」目的是要尋找您各種使用方式中未獲得正確處理的片段，例如未獲得特定瀏覽器上特定的頁面正確處理。 此分析會每日執行，且如果發現任何結果，它可能不像警示那麼緊急。 對比之下，失敗異常的分析則是會對傳入的應用程式資料持續執行，且如果伺服器失敗率超出預期，您就會在幾分鐘內收到通知。

## <a name="if-you-receive-a-smart-detection-alert"></a>如果您收到「智慧型偵測」警示
*為什麼會收到這個警示？*

* 我們偵測到要求失敗率相較於前一個期間正常基準異常上升。 在分析失敗和關聯的應用程式資料後，我們認為您應該注意發生的問題。

*收到通知代表一定是有問題嗎？*

* 我們嘗試針對應用程式中斷或效能降低發出警示，但只有您可以完全了解語意和對應用程式或使用者的影響。

*那麼，您會查看我的應用程式資料嗎？*

* 否。 服務完全是自動的。 只有您會收到通知。 您的資料是 [不公開的](./data-retention-privacy.md)。

*我是否必須訂閱此警示？*

* 否。 每個傳送要求資料的應用程式都有「智慧偵測」警示規則。

*我是否可以取消訂閱或改為傳送通知給我的同事？*

* 是，請在 [警示] 規則中，按一下 [智慧型偵測] 規則來設定它。 您可以停用警示，或變更警示的收件者。

*我遺失了電子郵件。在入口網站中哪裡可以找到通知？*

* 在 Azure 記錄中。 在 Azure 中，開啟您應用程式的 Application Insights 資源，然後選取 [活動] 記錄。

*部分警示與已知問題相關，我不想接收它們。*

* 您可以使用[警示動作規則](../platform/alerts-action-rules.md)歸併功能。

## <a name="next-steps"></a>後續步驟
這些診斷工具可協助您檢查來自您的應用程式的資料︰

* [計量瀏覽器](../platform/metrics-charts.md)
* [搜尋總管](./diagnostic-search.md)
* [分析 - 功能強大的查詢語言](../log-query/get-started-portal.md)

智慧偵測會自動進行。 但是，或許您會想要再設定一些警示？

* [手動設定的度量警示](../platform/alerts-log.md)
* [可用性 Web 測試](./monitor-web-app-availability.md)

