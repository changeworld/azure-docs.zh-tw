---
title: Azure Application Insights 中記錄型和預先彙總的計量 | Microsoft Docs
description: 為何在 Azure Application Insights 中使用記錄型和預先彙總的計量
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 30487eebed361e5b010df023a9b1a44f96590b14
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271075"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Application Insights 中記錄型和預先彙總的計量

本文介紹基於日誌的"傳統"應用程式見解指標與當前處於公共預覽中的預聚合指標之間的區別。 這兩種類型的計量均可供 Application Insights 使用者使用，而且各自在監視應用程式健康情況、診斷和分析方面帶來獨特的價值。 檢測應用程式的開發人員可以根據應用程式的大小、預期的遙測數量，以及計量精確度和警示的商務需求，決定哪種類型的計量最適合於特定案例。

## <a name="log-based-metrics"></a>記錄型計量

直到最近,應用程式監視應用程式見解中的遙測數據模型僅基於少量預定義的事件類型,如請求、異常、依賴項調用、頁面檢視等。開發人員可以使用 SDK 手動發出這些事件(透過編寫顯式調用 SDK 的代碼),也可以依賴自動檢測事件自動集合。 在任一情況下，Application Insights 後端會將所有收集的事件儲存為記錄，而 Azure 入口網站中的 Application Insights 刀鋒視窗會作為分析與診斷工具，以視覺化方式呈現記錄中以事件為基礎的資料。

使用記錄來保留一組完整的事件，可以帶來絕佳的分析與診斷價值。 例如，您可以取得特定 URL 的確切要求計數，以及進行這些呼叫的不同使用者數目。 您也可以取得詳細的診斷追蹤，包括任何使用者工作階段的例外狀況和相依性呼叫。 擁有此類型的資訊可大幅改善應用程式健康狀態和使用量的可見性，進而縮短診斷應用程式問題所需的時間。

同時,對於生成大量遙測的應用程序來說,收集一組完整的事件可能不切實際(甚至是不可能的)。 在事件數量太高的情況下，Application Insights 會實作數種遙測數量縮減技術，例如[取樣](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)和[篩選](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling)，以減少所收集和預存的事件數目。 不幸的是，降低的預存事件數目也會降低計量的精確度，其在幕後必須執行記錄中所儲事件的查詢階段彙總。

> [!NOTE]
> 在 Application Insights 中，以事件的查詢階段彙總為基礎的計量和以記錄中儲存的量測為基礎的計量都稱為記錄型計量。 這些計量通常有許多來自事件屬性的維度，使其更適合用於分析，但是取樣和篩選會對這些計量的精確度造成負面影響。

## <a name="pre-aggregated-metrics"></a>預先彙總的計量

除了基於日誌的指標外,2018 年末,應用程式洞察團隊還公開預覽了存儲在針對時間序列優化的專用存儲庫中的指標。 新的計量不再保存為具有許多屬性的個別事件。 相反地，它們會儲存為預先彙總的時間序列，而且只有重要維度。 這可讓新的計量在查詢階段表現優異：擷取資料更快速地發生，而且需要較少的計算能力。 因此造就新的案例，例如[近乎即時的計量維度警示](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)、更有回應的[儀表板](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)等。

> [!IMPORTANT]
> 記錄型和預先彙總的計量兩者共存於 Application Insights 中。 為了區分兩者,在應用程式見解 UX 中,預聚合指標現在稱為"標準指標(預覽"),而事件中的傳統指標則重命名為"基於日誌的指標"。

在遙測數量縮減技術開始運作之前，較新的 SDK (適用於 .NET 的[Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK 或更新版本) 會預先彙總收集期間的計量。 這意味著在使用最新的應用程式見解 SDK 時,新指標的準確性不受採樣和篩選的影響。

對於未實現預聚合的 SDK(即舊版本的應用程式見解 SDK 或瀏覽器檢測),應用程式見解後端仍通過聚合應用程式見解事件集合終結點接收的事件來填充新指標。 這意味著,雖然您無法從通過線傳輸的數據量減少中獲益,但您仍可以使用預聚合指標,並使用在收集過程中未預聚合指標的 SDK 體驗更好的性能和支援近即時維度警報。

值得一提的是收集端點會在擷取取樣前預先彙總事件，這表示不論您使用哪個版本的 SDK 搭配您的應用程式，[擷取取樣](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)決不會影響預先彙總的計量精確度。  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>使用預先彙總搭配 Application Insights 自訂計量

您可以使用預先彙總搭配自訂計量。 有兩個主要優勢：能夠設定自訂計量的維度和發出警示，以及減少從 SDK 傳送至 Application Insights 收集端點的資料量。

[從 Application Insights SDK 傳送自訂計量的方法](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)有好幾種。 如果您的 SDK 版本提供 [GetMetric 和 TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) 方法，這是傳送自訂計量慣用的方式，因為在此情況下，預先彙總會發生於 SDK 內部，不僅可減少 Azure 中儲存的資料量，也可減少從 SDK 傳輸至 Application Insights 的資料量。 否則，使用 [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) 方法，該方法會在資料擷取期間預先彙總計量事件。

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>自訂計量維度和預先彙總

您使用 [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) 或 [GetMetric 和 TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) API 呼叫傳送的所有計量，都會自動儲存在記錄和計量存放區中。 不過，雖然記錄型自訂計量版本一律會保留所有維度，但是預先彙總的計量版本儲存時預設不含任何維度。 您可以通過選擇「啟用自訂指標維度上的警報」,打開[使用和估計成本](https://docs.microsoft.com/azure/application-insights/app-insights-pricing)選項卡上自訂指標維度的集合: 

![使用量和估計成本](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>自訂計量維度的收集為何預設會關閉？

自訂計量維度的集合預設會關閉，因為未來儲存具有維度的自訂計量將與 Application Insights 分開計費，而儲存無維度的自訂計量仍為免費 (達到配額前)。 您可以在我們的官方[定價頁面](https://azure.microsoft.com/pricing/details/monitor/)上了解即將推出的定價模型變更。

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>建立圖表並探索記錄型和標準預先彙總的計量

使用[Azure 監視器指標資源管理器](../platform/metrics-getting-started.md)從預聚合和基於日誌的指標繪製圖表,並使用圖表創作儀錶板。 選取所需的 Application Insights 資源之後，使用命名空間選擇器在標準 (預覽) 與記錄型計量之間切換，或選取自訂計量命名空間：

![計量命名空間](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>應用程式洞察指標的定價模型

將指標引入到應用程式見解中(無論是基於日誌的還是預聚合的)都將根據引入數據的大小生成成本,[此處](https://docs.microsoft.com/azure/azure-monitor/app/pricing#pricing-model)所述。 您的自定義指標(包括其所有維度)始終存儲在應用程式見解日誌存儲中;因此,您的自定義指標(包括其所有維度)始終存儲在"應用程式見解"日誌存儲中。此外,默認情況下,自定義指標的預聚合版本(沒有維度)將轉發到指標存儲。

選擇[「啟用自訂指標維度上的警報](#custom-metrics-dimensions-and-pre-aggregation)」選項以在指標存儲中存儲預聚合指標的所有維度,可以根據[自訂指標定價](https://azure.microsoft.com/pricing/details/monitor/)生成**額外**成本。

## <a name="next-steps"></a>後續步驟

* [近乎即時警示](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric 和 TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)