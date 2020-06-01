---
title: 什麼是 Azure Application Insights？ | Microsoft Docs
description: 即時 Web 應用程式的應用程式效能管理和使用量追蹤。  偵測、分級和診斷問題，了解人們如何使用您的應用程式。
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: 6630de971dd9cc7ece2e0f87821db5fd62c39af0
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828724"
---
# <a name="what-is-application-insights"></a>什麼是 Application Insights？
Application Insights (一項 [Azure 監視器](../overview.md)功能) 是開發人員和 DevOps 專業人員所適用的可延伸「應用程式效能管理」(APM) 服務。 您可以使用它來監視即時應用程式。 其會自動偵測效能異常，其中包括強大的分析工具可協助診斷問題，並了解使用者實際上如何運用您的應用程式。  它是設計來協助您持續改善效能和可用性。 其適用於各種不同平台上的應用程式，包括裝載在內部部署、混合式或任何公用雲端的 .NET、Node.js 和 JAVA 和 Python。 它可與您的 DevOps 程序整合，並有各種開發工具的連接點。 透過與 Visual Studio App Center 整合，還可以從行動應用程式監視並分析遙測資料。

## <a name="how-does-application-insights-work"></a>Application Insights 的運作方式
您可以在您的應用程式中安裝小型檢測套件 (SDK)，或是在[支援](../../azure-monitor/app/platforms.md)時以無程式碼方式啟用 Application Insights。 此檢測會監視您的應用程式，並使用我們稱之為檢測金鑰的唯一 GUID，將遙測資料導向至 Azure Application Insights 資源。

您不僅可以檢測 Web 服務應用程式，也可以檢測任何背景元件以及網頁本身中的 JavaScript。 應用程式及其元件可以在任何地方執行 - 不一定要裝載於 Azure 中。

![您應用程式中的 Application Insights 檢測功能會將遙測傳送到 Application Insights 資源。](./media/app-insights-overview/diagram.png)

此外，您可以從主機環境 (例如效能計數器、Azure 診斷或 Docker 記錄) 提取遙測資料。 您也可以設定會定期將綜合要求傳送至 Web 服務的 Web 測試。

上述所有遙測串流都會整合到 Azure 監視器中。 在 Azure 入口網站中，您可以將強大的分析和搜尋工具套用於未經處理的資料。

### <a name="whats-the-overhead"></a>負荷為何？
對您的應用程式效能的影響很小。 追蹤呼叫不具阻擋性，而且會在個別的執行緒中分批傳送。

## <a name="what-does-application-insights-monitor"></a>Application Insights 可監視什麼？

Application Insights 是以開發小組為目標，以協助您了解您的應用程式的執行和使用情況。 它可監視︰

* **要求率、回應時間和失敗率** - 找出哪些頁面在每天哪些時段最受歡迎，以及使用者位於何處。 查看哪些頁面的表現最好。 如果您的回應時間和失敗率隨著要求增加而提高，您或許有資源配置問題。 
* **相依比率、回應時間和失敗率** - 找出外部服務是否會使您降低效能。
* **例外狀況**：分析彙總的統計資料，或挑選特定執行個體並深入了解堆疊追蹤和相關要求。 伺服器和瀏覽器例外狀況都會報告。
* **頁面檢視和載入效能** - 由使用者的瀏覽器報告。
* 來自網頁的 **AJAX 呼叫** - 比率、回應時間和失敗率。
* **使用者和工作階段計數**。
* 你的Windows 或 Linux 伺服器中的**效能計數器**，例如 CPU、記憶體和網路使用量。 
* 來自 Docker 或 Azure 的**主機診斷**。 
* 來自您應用程式的**診斷追蹤記錄** - 讓您使追蹤事件與要求相互關聯。
* 您在用戶端或伺服器程式碼中自行撰寫的**自訂事件和計量**，可追蹤商業事件，例如售出的項目或獲勝的遊戲。

## <a name="where-do-i-see-my-telemetry"></a>哪裡可以查看我的遙測？

有許多方式可以探索您的資料。 請參閱下列文章：

|  |  |
| --- | --- |
| [**智慧型偵測和手動警示**](../../azure-monitor/app/proactive-diagnostics.md)<br/>如果發生常見模式之外的一些狀況，自動警示會適應您的應用程式在遙測和觸發下的正常模式。 您也可以在自訂或標準計量的特定層級上[設定警示](../../azure-monitor/platform/alerts-log.md)。 |![警示範例](./media/app-insights-overview/alerts-tn.png) |
| [**應用程式對應**](../../azure-monitor/app/app-map.md)<br/>探索應用程式的元件，包含重要計量和警示。 |![應用程式對應](./media/app-insights-overview/appmap-tn.png)  |
| [**分析工具**](../../azure-monitor/app/profiler.md)<br/>檢查取樣要求的執行設定檔。 |![分析工具](./media/app-insights-overview/profiler.png) |
| [**使用量分析**](../../azure-monitor/app/usage-overview.md)<br/>分析使用者區隔和保留期。|![保留期工具](./media/app-insights-overview/retention.png) |
| [**執行個體資料的診斷搜尋**](../../azure-monitor/app/diagnostic-search.md)<br/>搜尋和篩選事件，例如要求、例外狀況、相依性呼叫、記錄追蹤，以及頁面檢視。  |![搜尋遙測](./media/app-insights-overview/search-tn.png) |
| [**彙總資料的計量瀏覽器**](../../azure-monitor/platform/metrics-charts.md)<br/>瀏覽、篩選和分割彙總的資料，例如，要求、錯誤和例外狀況的比率；回應時間、頁面載入時間。 |![計量](./media/app-insights-overview/metrics-tn.png) |
| [**儀表板**](../../azure-monitor/app/overview-dashboard.md)<br/>來自多個資源的交互式資料並與其他人員共用。 非常適用於多元件的應用程式，以及小組聊天室中的連續顯示。 |![儀表板範例](./media/app-insights-overview/dashboard-tn.png) |
| [**即時計量串流**](../../azure-monitor/app/live-stream.md)<br/>當您部署新的組建時，請觀看這些近乎即時的效能指標，以確定一切如預期運作。 |![即時計量範例](./media/app-insights-overview/live-metrics-tn.png) |
| [**分析**](../../azure-monitor/app/analytics.md)<br/>使用這個功能強大的查詢語言，回答有關您應用程式效能和使用方式的艱難問題。 |![分析範例](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](../../azure-monitor/app/visual-studio.md)<br/>查看程式碼中的效能資料。 從堆疊追蹤移至程式碼。|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**快照集偵錯工具**](../../azure-monitor/app/snapshot-debugger.md)<br/>使用參數值，對取樣自即時作業的快照集進行偵錯。|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](../../azure-monitor/app/export-power-bi.md )<br/>整合使用量計量和其他商業智慧。| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>撰寫程式碼，對您的計量和未經處理資料執行查詢。| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**連續匯出**](../../azure-monitor/app/export-telemetry.md)<br/>立即將送達的未經處理資料大量匯出至儲存體。 |![匯出](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>如何使用 Application Insights？

### <a name="monitor"></a>監視
在應用程式中安裝 Application Insights、設定[可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md)，以及︰

* 查看小組聊天室的預設[應用程式儀表板](../../azure-monitor/app/overview-dashboard.md)，以持續關注相依項目、頁面載入和 AJAX 呼叫的載入、回應性和效能。
* 探索哪些是最慢和最失敗的要求。
* 在部署新版本時觀看[即時串流](../../azure-monitor/app/live-stream.md)，立即知曉任何效能降低情形。

### <a name="detect-diagnose"></a>偵測、診斷
當您收到警示或發現問題︰

* 評估有多少使用者受到影響。
* 將失敗與例外狀況、相依項目呼叫和追蹤相互關聯。
* 檢查分析工具、快照集、堆疊傾印和追蹤記錄。

### <a name="build-measure-learn"></a>建置、測量、學習
[測量所部署之各個新功能的效果](../../azure-monitor/app/usage-overview.md)。

* 計劃測量客戶使用新 UX 或商務功能的情況。
* 在程式碼中撰寫自訂遙測。
* 讓下一個開發週期以遙測的真憑實據做為根據。

## <a name="get-started"></a>開始使用
Application Insights 是 Microsoft Azure 中裝載的多項服務之一，而遙測資料會送至該處進行分析及呈現。 所以在執行任何動作前，您需要 [Microsoft Azure](https://azure.com)訂用帳戶。 您可免費註冊，而且如果選擇 Application Insights 的基本[價格方案](https://azure.microsoft.com/pricing/details/application-insights/)，在您的應用程式成長到有大量使用量之前，不會有任何變更。 如果您的組織已經有訂用帳戶，他們可能會將您的 Microsoft 帳戶新增至其中。

有數種方式可以開始使用。 從最適合您的方式著手。 您可以稍後新增其他帳戶。

* **在執行階段：檢測伺服器上的 Web 應用程式。** 適用於已部署的應用程式。 避免對程式碼進行任何更新。
  * [**Azure Web Apps 上裝載的 ASP.NET 或 ASP.NET Core 應用程式**](../../azure-monitor/app/azure-web-apps.md)
  * [**裝載在 Azure VM 或 Azure 虛擬機器擴展集上 IIS 中的 ASP.NET 應用程式**](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * [**裝載在 IIS 內部部署 VM 中的 ASP.NET 應用程式**](../../azure-monitor/app/monitor-performance-live-website-now.md)
* **在開發階段：將 Application Insights 加入至您的程式碼。** 可讓您自訂遙測收集並傳送其他遙測。
  * [ASP.NET 應用程式](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Core 應用程式](../../azure-monitor/app/asp-net-core.md)
  * [.NET 主控台應用程式](../../azure-monitor/app/console.md)
  * [Java](../../azure-monitor/app/java-get-started.md)
  * [Node.js](../../azure-monitor/app/nodejs.md)
  * [Python](../../azure-monitor/app/opencensus-python.md)
  * [其他平台](../../azure-monitor/app/platforms.md)
* **[檢測您的網頁](../../azure-monitor/app/javascript.md)** 的頁面檢視、AJAX 和其他用戶端遙測。
* 藉由與 Visual Studio App Center 整合，來 **[分析行動應用程式使用方式](../../azure-monitor/learn/mobile-center-quickstart.md)** 。
* **[可用性集合](../../azure-monitor/app/monitor-web-app-availability.md)** - 定期從我們的伺服器 ping 您的網站。

## <a name="next-steps"></a>後續步驟
在執行階段開始使用︰

* [由 Azure VM 和 Azure 虛擬機器擴展集 IIS 所裝載的應用程式](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS 伺服器](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

在開發階段開始使用︰

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)


## <a name="support-and-feedback"></a>支援與意見反應
* 疑難排解與問題：
  * [疑難排解][qna]
  * [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-monitor.html)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* 您的建議：
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* 部落格：
  * [Application Insights 部落格](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../../azure-monitor/learn/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ../../azure-monitor/app/javascript.md
[desktop]: ../../azure-monitor/app/windows-desktop.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[ios]: ../../azure-monitor/learn/mobile-center-quickstart.md
[java]: ../../azure-monitor/app/java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ../../azure-monitor/app/platforms.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
