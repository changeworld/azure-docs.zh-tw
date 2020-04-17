---
title: 智慧型偵測 - Application Insights 中的失敗異常 | Microsoft Docs
description: 針對 Web 應用程式失敗要求比率的不尋常變化對您發出警示，並提供診斷分析。 不需要設定。
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: a1bce3ab86748d8247a72da3bd70e0f2e8155dbf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536806"
---
# <a name="smart-detection---failure-anomalies"></a>智慧型偵測 - 失敗異常
如果 Web 應用遇到失敗請求率異常上升,[應用程式見解](../../azure-monitor/app/app-insights-overview.md)會自動近乎即時地向您發出警報。 它偵測到回報為失敗的 HTTP 要求率異常提高或相依性呼叫。 對於請求,失敗的請求的回應代碼通常為 400 或更高。 為了説明您對問題進行會診和診斷,警報詳細資訊中提供了故障特徵和相關應用程序數據的分析。 其中也有 Application Insights 入口網站的連結，以供進一步診斷。 不需要設定該功能，因為它是使用機器學習演算法來預測一般失敗率。

此功能適用於任何 Web 應用(託管在雲端中或您自己的伺服器上),這些應用生成應用程式請求或依賴項資料。 例如,如果您有一個工作角色,稱為[TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)或[追蹤相依項()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)。

[為專案設置應用程式見解](../../azure-monitor/app/app-insights-overview.md)后,如果應用生成了一定最小量的數據,則智慧檢測故障異常需要 24 小時才能瞭解應用的正常行為,然後才能打開併發送警報。

下面是一個範例警報:

[![](./media/proactive-failure-diagnostics/013.png "Sample smart detection alert showing cluster analysis around failure")](./media/proactive-failure-diagnostics/013.png#lightbox)

警示詳細資訊將告訴您:

* 相較於一般應用程式行為的失敗率。
* 受影響的使用者人數 - 讓您知道應處理的數目。
* 與失敗關聯的特性模式。 在此範例中,有特定的回應代碼、請求名稱(操作)和應用程式版本。 可立即告訴您從何處開始了解程式碼。 其他可能性則可能是特定的瀏覽器或用戶端作業系統。
* 看起來似乎與特色失敗相關聯的例外狀況、記錄追蹤和相依性失敗 (資料庫或其他外部元件)。
* 直接連結到應用程式見解中有關資料搜索的連結。

## <a name="benefits-of-smart-detection"></a>智慧型偵測的優點
一般的 [度量警示](../../azure-monitor/app/alerts.md) 會告訴您可能有問題。 但是智慧檢測會為您啟動診斷工作,執行您自己必須做的大量分析。 您達到幾近封裝完成的結果，幫助您快速取得問題的根源。

## <a name="how-it-works"></a>運作方式
智慧檢測監控從應用接收的數據,尤其是故障率。 此規則會計算 `Successful request` 屬性為 false 的要求數，以及 `Successful call` 屬性為 false 的相依性呼叫數。 對於要求，根據預設，`Successful request == (resultCode < 400)` (除非您撰寫自訂程式碼來[篩選](../../azure-monitor/app/api-filtering-sampling.md#filtering)或產生自己的 [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) 呼叫)。 

您的應用程式效能具有一般的行為模式。 某些要求或相依性呼叫會比其他要求更容易失敗；且整體失敗率可能會隨著負載增加而上移。 「智慧型偵測」會使用機器學習服務來尋找這些異常狀況。

當數據從 Web 應用進入應用程式見解時,智慧檢測會將當前行為與過去幾天的模式進行比較。 如果觀察到與先前效能相比，失敗率異常提高，就會觸發分析。

觸發分析時，服務會對失敗的要求執行叢集分析，以嘗試識別描述失敗特徵之值的模式。 

在上述範例中，分析已發現大部分失敗是關於特定結果碼、要求名稱、伺服器 URL 主機和角色執行個體。 

當使用這些調用檢測服務時,分析器將查找與其標識的群集中的請求關聯的異常和依賴項故障,以及與這些請求關聯的任何跟蹤日誌的示例。

產生的分析報告會以警示寄送給您，除非您已設定不接收該報告。

與[手動設置的警報](../../azure-monitor/app/alerts.md)一樣,您可以檢查已觸發警報的狀態,如果問題已修復,可以解決此問題。 在應用程式見解資源的「警報」頁中配置警報規則。 但與其他警示不同，您並不需要設定「智慧型偵測」。 若有需要，您可以將它停用或變更其目標電子郵件地址。

### <a name="alert-logic-details"></a>警示邏輯詳細資料

這些警示是由我們專屬的機器學習演算法所觸發，因此我們無法共用確切的實作詳細資料。 不過，我們知道您有時需要深入了解基礎邏輯的運作方式。 評估以判斷是否應觸發警示的主要因素如下： 

* 分析要求/相依性在 20 分鐘累積時間範圍內的失敗百分比。
* 比較過去 20 分鐘的失敗百分比與過去 40 分鐘和過去七天的比率，以及尋找超過標準差 X 倍的重大偏差。
* 針對最小失敗百分比使用自動調整限制，此限制會依據要求/相依性的應用程式磁碟區而有所不同。
* 如果問題在 8-24 小時內不再檢測到,則有一種邏輯可以自動解決觸發的警報監視器條件。

## <a name="configure-alerts"></a>設定警示

可以從門戶或使用 Azure 資源管理器禁用智慧檢測警報規則([請參閱範本範例](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config))。

此警報規則與名為「應用程式見解智慧檢測」的關聯操作組一起創建,該[操作組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)包含電子郵件和 Webhook 操作,並可在警報觸發時擴展以觸發其他操作。

> [!NOTE]
> 默認情況下,從此警報規則發送的電子郵件通知將發送給與訂閱的監視讀取器和監視參與者角色關聯的使用者。 有關這方面的更多資訊,[可在此處取得](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification)。
> 從警示規則傳送[此通知](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)。
>

開啟 [警示] 頁面。 故障異常警報規則以及您手動設置的任何警報一起包含在內,您可以看到它當前是否處於警報狀態。

[![](./media/proactive-failure-diagnostics/021.png "On the Application Insights resource page, click 'Alerts' tile, then 'Manage alert rules'")](./media/proactive-failure-diagnostics/021.png#lightbox)

按一下警示來進行設定。

[![](./media/proactive-failure-diagnostics/032.png "Rule configuration screen")](./media/proactive-failure-diagnostics/032.png#lightbox)

請注意,您可以禁用或刪除故障異常警報規則,但不能在同一應用程式見解資源上創建另一個規則。

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>容錯異常警報網鉤負載示例

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

## <a name="triage-and-diagnose-an-alert"></a>會審和診斷警報

發出警示表示系統偵測到要求失敗率異常上升。 原因可能是您的應用程式或其環境有問題。

要進一步調查,請按下「檢視應用程式」 的完整詳細資訊,此頁面中的連結將直接帶到篩選到相關請求、異常、依賴項或追蹤的[搜尋頁面](../../azure-monitor/app/diagnostic-search.md)。 

您還可以打開 Azure[門戶](https://portal.azure.com)、導航到應用的應用程式見解資源,然後打開"失敗"頁。

按下「診斷故障」將説明您獲取更多詳細資訊並解決問題。

[![](./media/proactive-failure-diagnostics/051.png "Diagnostic search")](./media/proactive-failure-diagnostics/051.png#lightbox)

您可以根據要求的百分比及受影響的使用者數目來決定此問題的緊急程度。 在上面的示例中,78.5% 的故障率與 2.2% 的正常比率相比,表明發生了一些不好的事情。 另一方面,只有46個使用者受到影響。 如果是你的應用,您將能夠評估其嚴重程度。

在許多情況下,您將能夠從提供的請求名稱、異常、依賴項失敗和跟蹤數據快速診斷問題。

在此示例中,由於達到請求限制,SQL 資料庫中存在異常。

[![](./media/proactive-failure-diagnostics/052.png "Failed request details")](./media/proactive-failure-diagnostics/052.png#lightbox)

## <a name="review-recent-alerts"></a>檢閱最近的警示

按下「應用程式見解」資源頁中的**警報**以取得最新觸發警報:

[![](./media/proactive-failure-diagnostics/070.png "Alerts summary")](./media/proactive-failure-diagnostics/070.png#lightbox)

## <a name="whats-the-difference-"></a>不同之處在於...
失敗異常的「智慧型偵測」可以與其他相似但不同的 Application Insights 功能互補。

* [指標警報](../../azure-monitor/app/alerts.md)由您設置,可以監視各種指標,如 CPU 佔用率、請求率、頁面載入時間等。 您可以使用它們來向自己發出警告，例如，如果您需要增加更多資源時。 相比之下,與 Web 應用的正常行為相比,智慧檢測故障異常涵蓋一系列關鍵指標(目前僅失敗請求率),旨在在 Web 應用的失敗請求速率提高時以近乎即時的方式通知您。 與指標警報不同,智慧檢測會自動設置和更新閾值,以應對行為中的回應更改。 智慧檢測還可以為您啟動診斷工作,從而節省解決問題的時間。

* [效能異常的智慧型偵測](proactive-performance-diagnostics.md)也會使用機器智慧在您的度量中探索不尋常的模式，且您不需要進行任何設定。 但與失敗異常的「智慧型偵測」不同，效能異常的「智慧型偵測」目的是要尋找您各種使用方式中未獲得正確處理的片段，例如未獲得特定瀏覽器上特定的頁面正確處理。 此分析會每日執行，且如果發現任何結果，它可能不像警示那麼緊急。 相反,對傳入應用程序數據連續執行故障異常分析,如果伺服器故障率大於預期,將在幾分鐘內通知您。

## <a name="if-you-receive-a-smart-detection-alert"></a>如果您收到「智慧型偵測」警示
*為什麼會收到這個警示？*

* 我們偵測到要求失敗率相較於前一個期間正常基準異常上升。 在分析故障和相關應用程序數據后,我們認為存在一個問題,您應該研究。

*收到通知代表一定是有問題嗎？*

* 我們嘗試針對應用程式中斷或效能降低發出警示，但只有您可以完全了解語意和對應用程式或使用者的影響。

*那麼,您正在查看我的應用程序資料?*

* 否。 服務完全是自動的。 只有您會收到通知。 您的資料是 [不公開的](../../azure-monitor/app/data-retention-privacy.md)。

*我是否必須訂閱此警示？*

* 否。 發送請求數據的每個應用程式都有智慧檢測警報規則。

*我是否可以取消訂閱或改為傳送通知給我的同事？*

* 是，請在 [警示] 規則中，按一下 [智慧型偵測] 規則來設定它。 您可以停用警示，或變更警示的收件者。

*我遺失了電子郵件。在入口網站中哪裡可以找到通知？*

* 在 Azure 記錄中。 在 Azure 中，開啟您應用程式的 Application Insights 資源，然後選取 [活動] 記錄。

*部分警示與已知問題相關，我不想接收它們。*

* 您可以使用[警報操作規則](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules)抑制功能。

## <a name="next-steps"></a>後續步驟
這些診斷工具可説明您從應用檢查資料:

* [計量瀏覽器](../../azure-monitor/platform/metrics-charts.md)
* [搜尋資源管理員](../../azure-monitor/app/diagnostic-search.md)
* [分析 - 功能強大的查詢語言](../../azure-monitor/log-query/get-started-portal.md)

智慧檢測是自動的。 但是，或許您會想要再設定一些警示？

* [手動設定的度量警示](../../azure-monitor/app/alerts.md)
* [可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md)
