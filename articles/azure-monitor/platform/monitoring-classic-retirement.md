---
title: 更新 Azure 監視器中的經典警報&監視
description: 經典監視服務和功能停用的說明，前面顯示在"警報"下的 Azure 門戶中（經典）。
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: dec4d6824644cabf8b1872da207b8554fee0b3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659470"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Azure 監視器中的整合警示和監視取代了傳統警示和監視

Azure 監視器現在已成為整合的完整堆疊監視服務，其現在可跨資源支援「一個計量」和「一個警示」；如需詳細資訊，請參閱[關於新 Azure 監視器的部落格文章](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)。新的 Azure 監視和警示平台經過建置，變得更快速、更聰明，且可擴充，以跟上雲端運算日益擴張的版圖，並和 Microsoft Intelligent Cloud 的原則保持一致。 

隨著新的 Azure 監視和警報平臺的建立，我們將停用 Azure 警報*視圖經典警報*部分中的"經典"監視和警報平臺，**該平臺將在 2019 年 8 月 Azure 公共雲中棄用**。 [Azure 政府雲](../../azure-government/documentation-government-welcome.md)和[Azure 中國 21Vianet](https://docs.azure.cn/)不會受到影響。

> [!NOTE]
> 由於延遲推出遷移工具，經典警報遷移的停用日期從最初宣佈的 2019 年 6 月[30 日延長至 2019 年 8 月 31](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)日。

 ![Azure 入口網站中的傳統警示](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

建議您開始使用警示，並在新的平台中重新建立警示。 對於擁有大量警報的客戶，我們[分階段推出](alerts-understand-migration.md#rollout-phases)一種[自願遷移工具](alerts-using-migration-tool.md)，用於將現有經典警報移動到新警報系統，而不會中斷或增加成本。

> [!IMPORTANT]
> 在 [活動記錄] 上建立的傳統警示規則將不會被淘汰或移轉。 從新的 [Azure 監視器 - 警示] 即可依原樣存取和使用在 [活動記錄] 上建立的所有傳統警示規則。 如需詳細資訊，請參閱[使用 Azure 監視器來建立、檢視及管理活動記錄警示](../../azure-monitor/platform/alerts-activity-log.md)。 同樣地，從新的 [服務健康狀態] 區段也可以依原樣存取和使用 [服務健康狀態] 上的警示。 如需詳細資料，請參閱[服務健康狀態通知的相關警示](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Application Insights 中的整合計量和警示

Azure 監視器的較新計量平台現在可強化來自 Application Insights 的監視功能。 這項變動表示 Application Insights 將會連結至動作群組，從而能夠提供更多的選項，而不只是先前的電子郵件和 Webhook 呼叫。 警示現在可以觸發語音電話、Azure Functions、Logic Apps、簡訊和 ITSM 工具 (例如 ServiceNow 和自動化 Runbook)。 由於有近乎即時的監視和警示功能，新的平台可讓 Application Insights 使用者利用相同技術，來強化其他 Azure 資源的監視功能，並支援 Microsoft 產品的監視功能。

新的 Application Insights 整合監視和警示將會包含：

- **Application Insights 平台計量** - 可提供來自 Application Insights 產品的熱門預建計量。 如需詳細資訊，請參閱這篇關於使用[新 Azure 監視器上的 Application Insights 平台計量](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)的文章。
- **Application Insights 可用性和 Web 測試** - 可讓您能夠評估 Web 應用程式或伺服器的回應能力和可用性。 如需詳細資訊，請參閱這篇關於使用[新 Azure 監視器上的 Application Insights 可用性測試和警示](../../azure-monitor/app/monitor-web-app-availability.md)的文章。
- **Application Insights 自訂計量** - 可讓您定義和發出自己的監視和警示計量。 如需詳細資訊，請參閱這篇關於使用[新 Azure 監視器上的 Application Insights 自訂計量](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)的文章。
- **Application Insights 失敗異常 (智慧偵測的一部分)** - 可在 Web 應用程式的失敗 HTTP 要求或相依性呼叫比率異常增加時，以幾乎即時的方式自動通知您。 有關詳細資訊，請參閱有關使用[智慧檢測 - 故障異常](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics)的這篇文章。

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>其他 Azure 資源的整合計量和警示

自 2018 年 3 月起，新一代的 Azure 資源警示和多維度監視功能便已推出。 現在，較新的計量平台和警示由於有近乎即時的功能，所以執行速度更快。 更重要的是，較新的計量平台警示可提供更多的細微性，因為較新的平台包含維度選項，可讓您配量和篩選至特定的值組合、條件或作業。 和新 Azure 監視器中的所有警示一樣，較新的計量警示由於使用 ActionGroups (讓通知工具不再只有電子郵件或 Webhook，還可使用簡訊、語音、Azure 函式、自動化 Runbook 等等)，所以更具擴充性。 如需詳細資訊，請參閱[使用 Azure 監視器建立、檢視及管理計量警示](../../azure-monitor/platform/alerts-metric.md)。
適用於 Azure 資源的較新計量可透過下列形式來取得：

- **Azure 監視器標準平台計量** - 可提供來自各種 Azure 服務和產品的熱門預先填入計量。 如需詳細資訊，請參閱這篇關於 [Azure 監視器上所支援的計量](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)和 [Azure 監視器上所支援的計量警示](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts)的文章。
- **Azure 監視器自訂計量** - 可提供來自使用者導向來源 (包括 Azure 診斷代理程式) 的計量。 如需詳細資訊，請參閱這篇關於 [Azure 監視器中的自訂計量](../../azure-monitor/platform/metrics-custom-overview.md)的文章。 使用自訂計量，您還可以發佈 [Windows Azure 診斷代理程式](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md)和 [InfluxData Telegraf 代理程式](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md)所收集的計量。

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>淘汰傳統監視和警示平台

如前所述，目前可從 Azure 入口網站的[警示 (傳統) 區段](../../azure-monitor/platform/alerts-classic.overview.md)使用的傳統監視和警示平台，由於已由較新的系統取代，因此將會在未來幾個月內淘汰。
較老的經典監控和警報將于2019年8月31日停用;包括關閉其中的相關 API、Azure 門戶介面和服務。 具體來說，這些功能將會淘汰：

- 目前可透過 Azure 入口網站 [[警示 (傳統)] 區段](../../azure-monitor/platform/alerts-classic.overview.md)使用的 Azure 資源舊版 (傳統) 計量和警示；可以 [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) 資源的形式來存取
- 目前可透過 Azure 入口網站 [[警示 (傳統)] 區段](../../azure-monitor/platform/alerts-classic.overview.md)使用的 Application Insights 舊版 (傳統) 平台與自訂計量及警示；且可以 [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) 資源的形式來存取
- 目前可在 Azure 入口網站以 [Application Insights 中的智慧偵測](../../azure-monitor/app/proactive-diagnostics.md)提供的舊版 (傳統) 失敗異常警示；所設定的警示會顯示於 Azure 入口網站的 [[警示 (傳統)] 區段](../../azure-monitor/platform/alerts-classic.overview.md)

所有經典監視和警報系統（包括[Resource Template](../../azure-monitor/platform/alerts-enable-template.md)相應的[API](https://msdn.microsoft.com/library/azure/dn931945.aspx)API、PowerShell、CLI、Azure[門戶頁面](../../azure-monitor/platform/alerts-classic-portal.md)和資源範本）將一直可用到 2019 年 8 月底。 [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md) [CLI](../../azure-monitor/platform/alerts-classic-portal.md) 

2019 年 8 月底，在 Azure 監視器中：

- 經典監視和警報服務將停用，不再可用於創建新警報規則。
- 2019 年 8 月以後警報（經典）中仍然存在的任何警報規則將繼續執行並觸發通知，但無法修改。
- 從 2019 年 9 月開始，經典監視&警報中可遷移的警報規則將由 Microsoft 自動移動到新 Azure 監視器平臺中的等效程式，分數周進行階段。 此程序無須停機即可順利進行，而且客戶不會遺失任何監視涵蓋範圍。
- 移轉至新警示平台的警示規則將提供與之前一樣的監視涵蓋範圍，但會引發具有新承載的通知。 遷移時，與經典警報規則關聯的任何電子郵件地址、Webhook 終結點或邏輯應用連結都將轉發，但可能無法正確運行，因為警報負載在新平臺中會有所不同。
- 某些[無法自動遷移](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)且需要使用者手動操作的經典警報規則將繼續運行到 2020 年 6 月。

> [!IMPORTANT]
> Microsoft Azure 監視器已分階段推出[工具，以便儘快將其](alerts-using-migration-tool.md)經典警報規則遷移到新平臺。 並強制運行它的所有經典警報規則仍然存在，可以遷移，從 2019 年 9 月開始。 移轉至傳統警示規則之後，客戶必須確實調整使用傳統警示規則的自動化作業，以處理來自 [Application Insights 中的整合計量和警示](#unified-metrics-and-alerts-in-application-insights)或[其他 Azure 資源的整合計量和警示](#unified-metrics-and-alerts-for-other-azure-resources)的新承載。 有關詳細資訊，請參閱[為經典警報規則遷移做好準備](alerts-prepare-migration.md)

本文將持續更新關於新 Azure 監視和警示功能的連結和詳細資料，以及工具的可用性，以協助使用者採用新的 Azure 監視器平台。

## <a name="pricing-for-migrated-alert-rules"></a>遷移警報規則的定價

我們正在推出一個遷移工具，以説明您將 Azure 監視器[經典警報](../../azure-monitor/platform/alerts-classic.overview.md)遷移到新的警報體驗。 遷移的警報規則和相應的遷移操作組（電子郵件、Webhook 或 LogicApp）將保持免費。 您具有經典警報的功能，包括編輯閾值、聚合類型和聚合細微性的功能將繼續在遷移的警報規則中免費提供。 但是，如果您編輯遷移的警報規則以使用任何新的警報平臺功能、通知或操作類型，則將收取相應的費用。 有關警報規則和通知的定價的詳細資訊，請參閱 Azure[監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。

以下是警報規則費用的示例：

- 在新的 Azure 監視器平台上，超出免費單位所建立的任何新的 (非遷移) 警示規則
- 超出 Azure 監視器所含免費單位而擷取和保留的任何資料
- Application Insights 所執行的任何多測試 Web 測試
- 超出 Azure 監視器所含免費單位而儲存的任何自訂計量
- 任何經過編輯以使用較新的指標警報功能（如頻率、多個資源/維度、[動態閾值](alerts-dynamic-thresholds.md)、更改資源/信號等）的遷移警報規則。
- 任何經過編輯以使用較新的通知或操作類型（如 SMS、語音電話和/或 ITSM 集成）的遷移操作組。

## <a name="next-steps"></a>後續步驟

* 了解[新的整合 Azure 監視器](../../azure-monitor/overview.md)。
* 深入了解新的 [Azure 警示](../../azure-monitor/platform/alerts-overview.md)。
