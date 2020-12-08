---
title: Azure 監視器概觀 | Microsoft Docs
description: 構成 Azure 服務和應用程式之完整監視策略的各種 Microsoft 服務和功能的概觀。
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: 7c48311612d48ef616e5b4c0eefaaa0ae7bb2e84
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451274"
---
# <a name="azure-monitor-overview"></a>Azure 監視器概觀

Azure 監視器可協助您將應用程式和服務的可用性和效能最大化。 它可提供全方位解決方案，以便收集、分析及處理來自雲端和內部部署環境的遙測資料。 這項資訊可協助您了解您的應用程式表現如何，並主動識別對其造成影響的問題及其所依賴的資源。

有關您可以使用 Azure Monitor 執行之動作的一些範例包括：

- 使用 [Application Insights](app/app-insights-overview.md) 偵測及診斷應用程式與相依性問題。
- 將基礎結構問題與[適用於 VM 的 Azure 監視器](insights/vminsights-overview.md)和[適用於容器的 Azure 監視器](insights/container-insights-overview.md)相互關聯。
- 使用 [Log Analytics](log-query/log-query-overview.md) 向下鑽研您的監視資料，以進行疑難排解與深入診斷。
- 使用[智慧警示](platform/alerts-smartgroups-overview.md)與[自動化動作](platform/alerts-action-rules.md)來支援大規模作業。
- 使用 Azure [儀表板](learn/tutorial-logs-dashboards.md)與 [Workbooks](platform/workbooks-overview.md) 建立視覺效果。
- 使用 [Azure 監視器計量](./platform/data-platform-metrics.md)，從[受監視的資源](./monitor-reference.md)收集資料。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>概觀

下圖提供 Azure 監視器的高階檢視。 圖表的中間是計量與記錄的資料存放區，而這兩者是 Azure 監視器所用資料的兩個基本類型。 左邊的[監視資料來源](platform/data-sources.md)會填入這些[資料存放區](platform/data-platform.md)。 右邊是 Azure 監視器對此收集資料執行的不同功能。 這包括分析、警示，以及串流至外部系統等動作。

![Azure 監視器概觀](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>監視資料平台

Azure 監視器所收集的所有資料均符合下列兩個基本類型之一：[計量和記錄](platform/data-platform.md)。 [計量](platform/data-platform-metrics.md)為數值，可描述系統在特定時間點的某個方面。 它們屬於輕量型，而且能夠支援近乎即時的案例。 [記錄](platform/data-platform-logs.md)包含不同類型的資料，而資料會針對每個類型組織成不同的屬性集。 除了效能資料，還會將事件和追蹤之類的遙測資料儲存為記錄，讓它能夠全部組合在一起進行分析。

對於許多 Azure 資源，您會在 Azure 入口網站的 [概觀] 頁面當中看到 Azure 監視器所收集的資料。 比方說，查看一下任何虛擬機器，您會看到數個顯示效能計量的圖表。 按一下任何圖表，以在 Azure 入口網站的[計量瀏覽器](platform/metrics-charts.md)中開啟資料，可讓您將一段時間的多個計量值繪製成圖表。  您可以互動方式檢視圖表，或將其釘選到儀表板，利用其他視覺效果進行檢視。

![圖表顯示流入「計量瀏覽器」的計量資料，以便在視覺效果中使用。](media/overview/metrics.png)

可以使用[查詢](log-query/log-query-overview.md)分析 Azure 監視器收集的記錄資料，以快速擷取、彙總和分析收集的資料。  您可以使用 Azure 入口網站中的 [Log Analytics](./log-query/log-query-overview.md) 來建立及測試查詢。 然後，您可以使用不同的工具直接分析資料，或儲存查詢以便搭配[視覺效果](visualizations.md)或[警示規則](platform/alerts-overview.md)使用。

Azure 監視器使用的 [Kusto 查詢語言](/azure/kusto/query/)版本適合用於簡單的記錄查詢，但也包含進階功能，例如彙總、聯結和智慧分析。 您可以使用[多個課程](log-query/get-started-queries.md)，快速了解查詢語言。  我們會為已經熟悉 [SQL](log-query/sql-cheatsheet.md) 和 [Splunk](log-query/splunk-cheatsheet.md) 的使用者提供特別指引。

![圖表顯示流入 Log Analytics 以便進行分析的記錄資料。](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Azure 監視器會收集哪些資料？

Azure 監視器可以從[多種來源](monitor-reference.md)收集資料。 此範圍包括您的應用程式、其倚賴的任何作業系統和服務，乃至於平台本身。 Azure 監視器會從下列各層收集資料：

- **應用程式監視資料：** 您所撰寫程式碼的效能和功能相關資料 (不論其平台為何)。
- **客體 OS 監視資料：** 有關應用程式執行所在作業系統的資料。 這可以在 Azure、其他雲端或內部部署中執行。 
- **Azure 資源監視資料：** 有關 Azure 資源作業的資料。
- **Azure 訂用帳戶監視資料：** 有關 Azure 訂用帳戶作業和管理的資料，以及有關 Azure 本身健康情況和作業的資料。 
- **Azure 租用戶監視資料：** 租用戶層級 Azure 服務的作業相關資料，例如 Azure Active Directory。

當您建立 Azure 訂用帳戶並開始新增資源 (例如虛擬機器和 Web 應用程式) 時，Azure 監視器就會開始收集資料。  [活動記錄](platform/platform-logs-overview.md)會記錄資源的建立或修改時間。 [計量](platform/data-platform.md)會告訴您如何執行資源以及它所取用的資源。 

[啟用診斷](platform/platform-logs-overview.md)，將您收集到的資料擴充至資源的內部作業。  [新增代理程式](platform/agents-overview.md)以計算資源，從其客體作業系統收集遙測資料。 

為您的 [Application Insights](app/app-insights-overview.md) 啟用監視功能，讓 Application Insights 收集各種詳細資訊，包括頁面檢視、應用程式要求和例外狀況。 設定[可用性測試](app/monitor-web-app-availability.md)來模擬使用者流量，進一步確認您應用程式的可用性。

### <a name="custom-sources"></a>自訂來源

Azure 監視器可以使用[資料收集器 API](platform/data-collector-api.md)，從任何 REST 用戶端收集記錄資料。 這可讓您建立自訂監視案例，並且將監視延伸到不會透過其他來源公開遙測的資源。

## <a name="insights"></a>深入解析
如果監視資料可以提高您對於運算環境作業的可見性，監視資料才有用處。 [Insights](monitor-reference.md#insights-and-core-solutions) 可為特定 Azure 服務提供自訂的監視體驗。 這只需要最基本的設定，且能夠提升您對重要資源作業的可見度。

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) 可監視 Web 應用程式的可用性、效能及使用情況 (不論應用程式是裝載在雲端還是內部部署環境)。 它會利用 Azure 監視器中強大的資料分析平台，為您提供應用程式作業的深入解析以及診斷錯誤。 如此，您即可快速診斷錯誤，而無須等待使用者回報錯誤。 Application Insights 包含各種開發工具的連接點，並與 Visual Studio 整合以支援您的 DevOps 程序。

![應用程式情資](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>適用於容器的 Azure 監視器
[適用於容器的 Azure 監視器](insights/container-insights-overview.md)可監視部署至 Azure Kubernetes Service (AKS) 上所裝載受控 Kubernetes 叢集的容器工作負載效能。 它可透過計量 API 從 Kubernetes 中提供的控制器、節點與容器收集計量，為您提供效能可見性。 容器記錄也會一併收集。  在您從 Kubernetes 叢集啟用監視之後，系統會透過適用於 Linux 的 Log Analytics 代理程式容器化版本自動收集這些計量和記錄。

![容器健康情況](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器
[適用於 VM 的 Azure 監視器](insights/vminsights-overview.md)可大規模監視您的 Azure 虛擬機器 (VM)。 此服務會分析 Windows 和 Linux VM 的效能和健康情況，並識別其不同的程序以及對外部程序的相互關聯相依性。 此解決方案包含針對在內部部署環境或其他雲端提供者上裝載的 VM，監視效能和應用程式相依性的支援。  


![VM Insights](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>回應重大情況
有效的監視解決方案除了可讓您以互動方式分析監視資料，還必須能夠主動回應在它所收集資料中找到的重大情況。 可以是傳送文字或郵件給負責調查問題的系統管理員。 或者，您可啟動自動化程序，嘗試更正錯誤情況。


### <a name="alerts"></a>警示
[Azure 監視器中的警示](platform/alerts-overview.md)會主動通知您重大情況，並可能嘗試採取矯正措施。 以計量為基礎的警示規則會根據數值提供近乎即時的警示。 以記錄為基礎的規則允許使用跨多個資料來源的複雜邏輯。

Azure 監視器中的警示規則會使用[動作群組](platform/action-groups.md)，其中包含幾組獨特的收件人以及多個規則可共用的動作。 根據您的需求，動作群組可以執行一些動作，例如使用 Webhook 讓警示啟動外部動作，或與 ITSM 工具整合。

![螢幕擷取畫面顯示 Azure 監視器中的警示，其中包含嚴重性、警示總計和其他資訊。](media/overview/alerts.png)

### <a name="autoscale"></a>Autoscale
自動調整可讓您執行適當數量的資源來處理應用程式的負載。 建立規則，並使用 Azure 監視器所收集的計量，以判斷何時應在負載增加時自動新增資源。 移除閒置的資源可節省成本。 您可指定執行個體的數目上限和下限，以及何時要增加或減少資源的邏輯。

![圖表顯示自動調整功能，其中有數個伺服器位在標示「處理器時間 > 80%」的線條上，兩部伺服器標示為最小值、三部伺服器標示為目前的容量，以及五部伺服器標示為最大值。](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>將監視資料視覺化
[視覺效果](visualizations.md) (例如圖表和資料表) 是摘要監視資料以及向不同對象呈現資料的有效工具。 Azure 監視器有自己的功能可將監視資料視覺化，並運用其他 Azure 服務向不同的對象發佈資料。

### <a name="dashboards"></a>儀表板
[Azure 儀表板](../azure-portal/azure-portal-dashboards.md)可讓您將不同類型的資料結合至 [Azure 入口網站](https://portal.azure.com)的單一窗格中。 您可以選擇性地與其他 Azure 使用者共用儀表板。 請將任何記錄查詢或計量圖表的輸出新增至 Azure 儀表板。 例如，您可以建立一個儀表板，將顯示計量圖表、活動記錄表、來自 Application Insights 的使用情況圖表，以及記錄查詢輸出的圖格結合在一起。

![螢幕擷取畫面顯示 Azure 儀表板，其中包括應用程式和安全性磚，以及其他可自訂的資訊。](media/overview/dashboard.png)

### <a name="workbooks"></a>活頁簿
[活頁簿](platform/workbooks-overview.md)提供彈性的畫布用以進行資料分析，以及在 Azure 入口網站中建立豐富的視覺效果報表。 活頁簿可讓您從 Azure 中深入了解多個資料來源，並將其合併為整合的互動式體驗。 您可以使用 Insights 隨附的活頁簿，或從預先定義的範本建立自己的活頁簿。


![活頁簿範例](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) 是一項商務分析服務，可提供跨多種資料來源的互動式視覺效果。 這是將資料提供給組織內、外部人員使用的有效方法。 您可以將 Power BI 設定為[從 Azure 監視器自動匯入記錄資料](./platform/powerbi.md)，以便利用這些額外的視覺效果。


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>整合並匯出資料
您通常需要整合 Azure 監視器與其他系統，以及建置自訂解決方案來使用您的監視資料。 其他 Azure 服務會與 Azure 監視器搭配運作來提供這項整合。

### <a name="event-hub"></a>事件中樞
[Azure 事件中樞](../event-hubs/index.yml)是串流平台和事件擷取服務。 此服務可以使用任何即時分析提供者或批次/儲存體配接器來轉換和儲存資料。 使用事件中樞[將 Azure 監視器資料串流處理](platform/stream-monitoring-data-event-hubs.md)至合作夥伴 SIEM 和監視工具。


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) 是一項服務，可讓您透過工作流程來整合不同的系統和服務，從而實現工作與商務程序的自動化。 Azure 監視器中有活動可用來讀取和寫入計量和記錄。 這可讓您建置與各種其他系統整合的工作流程。


### <a name="api"></a>API
除了存取所產生的警示，還有多個 API 可供讀取和寫入 Azure 監視器中的計量和記錄。 您也可以設定和擷取警示。 這可為您提供基本上無限制的可能性，來建置可與 Azure 監視器整合的自訂解決方案。

## <a name="next-steps"></a>後續步驟
深入了解：

* Azure 監視器所收集資料的[計量和記錄](platform/data-platform.md)。
* [資料來源](platform/data-sources.md)，以了解應用程式的不同元件如何傳送遙測。
* [記錄查詢](log-query/log-query-overview.md)，以分析所收集的資料。
* 用於監視雲端應用程式與服務的[最佳做法](/azure/architecture/best-practices/monitoring) \(部分機器翻譯\)。
