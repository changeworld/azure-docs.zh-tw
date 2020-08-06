---
title: 部署 Azure 監視器
description: 說明完整執行 Azure 監視器以監視 Azure 訂用帳戶中所有資源所需的不同步驟。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 34a048c702b62caeecaf21e710a9dcd9156e4aea
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801446"
---
# <a name="deploy-azure-monitor"></a>部署 Azure 監視器
啟用 Azure 監視器來監視您所有的 Azure 資源，是設定 Azure 監視器元件和設定 Azure 資源來產生要收集之 Azure 監視器的監視資料的組合。 本文說明使用一般設定來監視您的 Azure 訂用帳戶中的所有資源，以進行完整 Azure 監視器執行所需的不同步驟。 會提供每個步驟的基本描述，以及其他檔的連結，以取得詳細的設定需求。

> [!IMPORTANT]
> Azure 監視器和其設定的功能會根據您的商務需求，與已啟用功能的成本平衡而有所不同。 下列每個步驟都會識別是否有潛在的成本，而且您應該先評估這些成本，再繼續進行該步驟。 如需完整的定價詳細資料，請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="configuration-goals"></a>設定目標
Azure 監視器的完整實施目標，就是從您所有的雲端資源和應用程式收集所有可用的資料，並根據該資料盡可能地在 Azure 監視器中啟用多項功能。

Azure 監視器所收集的資料會傳送至[Azure 監視器計量](platform/data-platform-metrics.md)或[Azure 監視器記錄](platform/data-platform-logs.md)。 每個都會儲存不同種類的資料，並啟用不同種類的分析和警示。 如需不同警示類型的描述，請參閱[比較 Azure 監視器計量和記錄](platform/data-platform.md)，以取得[Microsoft Azure 中警示](platform/alerts-overview.md)的比較和總覽。 

有些資料可以同時傳送至計量和記錄，以便利用不同的功能來使用它。 在這些情況下，您可能需要分別設定每個。 例如，Azure 資源會自動將計量資料傳送至可支援計量瀏覽器和度量警示的計量。 您必須為每個資源建立診斷設定，以將相同的計量資料傳送至記錄，讓您使用 Log Analytics 分析其他記錄資料的效能趨勢。 下列各節會識別資料的傳送位置，並包含將資料傳送到所有可能位置所需的每個步驟。

您可能會有其他需求，例如監視 Azure 外部的資源，以及在 Azure 監視器外部傳送資料。 這類需求可透過此文章中所述的其他功能設定來達成。 遵循每個步驟中的檔連結，以取得其他設定選項。

## <a name="collect-data-from-azure-resources"></a>從 Azure 資源收集資料

> [!NOTE]
> 如需使用 Azure 監視器監視虛擬機器的完整指南，請參閱[使用 Azure 監視器監視 Azure 資源](insights/monitor-azure-resource.md)。

Azure 資源的某些監視可自動使用，而不需要設定，但您必須執行設定步驟來收集其他監視資料。 下表說明從 Azure 資源收集所有可用資料所需的設定步驟，包括將資料傳送至 Azure 監視器計量和 Azure 監視器記錄的步驟。 下列各節將進一步詳細說明每個步驟。

[![部署 Azure 資源監視 ](media/deploy/deploy-azure-resources.png)](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>無組態
當您建立 Azure 訂用帳戶時，不需要進行任何設定，就會啟用 Azure 監視器的下列功能。 此監視沒有相關聯的費用。

[Azure Active Directory 記錄](../active-directory/reports-monitoring/overview-reports.md)-提供登入活動的租使用者層級歷程記錄，以及在 Azure Active Directory 中所做變更的審核記錄。 如需 Azure Active Directory 記錄檔的詳細資訊，以及如何在 Azure 入口網站中加以查看，請參閱[Azure Active Directory 入口網站](../active-directory/reports-monitoring/concept-sign-ins.md)中 Azure Active Directory 入口網站和登入活動報告中的[[審核活動報告](../active-directory/reports-monitoring/concept-audit-logs.md)]。

[活動記錄](platform/platform-logs-overview.md)-可讓您深入瞭解 Azure 中發生的管理群組和訂用帳戶層級事件。 當您建立新的 Azure 資源、修改資源或執行重要的活動時，事件會自動寫入活動記錄中。 您可以在 Azure 入口網站中查看事件，並在建立特定事件時建立活動記錄警示。 如需活動記錄檔的詳細資料，以及如何在 Azure 入口網站中加以查看，請參閱[Azure 活動記錄](platform/activity-log.md)。

[平臺計量](platform/metrics-supported.md)-從 Azure 服務自動收集到[Azure 監視器計量](platform/data-platform-metrics.md)。 這項資料通常會顯示在不同服務之 Azure 入口網站的 [**總覽**] 頁面上。 如需在 Azure 入口網站中分析平臺計量的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](platform/metrics-getting-started.md)。 


### <a name="create-log-analytics-workspace"></a>建立 Log Analytics 工作區
您需要至少一個 Log Analytics 工作區來啟用[Azure 監視器記錄](platform/data-platform-logs.md)，這是將這類資料收集為來自 azure 資源的記錄、從 azure 虛擬機器的客體作業系統收集資料，以及大部分 Azure 監視器的深入解析所需的記錄。 Azure Sentinel 和 Azure 資訊安全中心等其他服務也會使用 Log Analytics 工作區，而且可以共用您用於 Azure 監視器的相同帳戶。 您可以從單一工作區開始，以支援此監視，但請參閱[設計您的 Azure 監視器記錄部署](platform/design-logs-deployment.md)，以取得何時使用多個工作區的指引。

建立 Log Analytics 工作區不會產生任何費用，但一旦您設定要將資料收集到其中，可能會有費用。 如需詳細資訊，請參閱[使用 Azure 監視器記錄來管理使用量和成本](platform/manage-cost-storage.md)。  

請參閱在[Azure 入口網站中建立 Log analytics 工作區](learn/quick-create-workspace.md)，以建立初始的 log analytics 工作區。 請參閱[管理 Azure 監視器中記錄資料和工作區的存取](platform/manage-access.md)權以設定存取權。 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>建立診斷設定以收集租使用者和訂用客戶紀錄
雖然您租使用者的[Azure Active Directory 記錄](../active-directory/reports-monitoring/overview-reports.md)和訂用帳戶的[活動記錄](platform/platform-logs-overview.md)會自動收集，但將它們傳送至 log analytics 工作區可讓您使用 log analytics 中的記錄查詢，以其他記錄資料來分析這些事件。 這也可讓您建立記錄查詢警示，這是警示 Azure Active Directory 記錄的唯一方式，並提供比活動記錄警示更複雜的邏輯。

將活動記錄傳送至工作區不會產生任何費用，但是 Azure Active Directory 記錄檔的資料內嵌和保留費用。 

請參閱[整合 Azure AD 記錄與 Azure 監視器記錄](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)，並[建立診斷設定以將平臺記錄和計量傳送至不同的目的地](platform/diagnostic-settings.md)，以建立租使用者和訂用帳戶的診斷設定，以將記錄專案傳送至您的 log Analytics 工作區。 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>建立診斷設定以收集資源記錄和平臺計量
Azure 中的資源會自動產生[資源記錄](platform/platform-logs-overview.md)，以提供資源內所執行作業的詳細資料。 與平臺計量不同的是，您必須設定要收集的資源記錄。 建立診斷設定，將它們傳送至 Log Analytics 工作區，以將它們與 Azure 監視器記錄所使用的其他資料結合。 相同的診斷設定也可以用來將大部分資源的平臺計量傳送至相同的工作區，讓您能夠使用記錄查詢與其他收集的資料來分析度量資料。

此集合會有成本，因此在跨大量資源執行之前，請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。 另請參閱[使用 Azure 監視器記錄來管理使用量和成本](platform/manage-cost-storage.md)，以取得優化記錄收集成本的詳細資料。

若要建立 Azure 資源的診斷設定，請參閱[建立診斷設定以收集 azure 中的資源記錄和計量](platform/diagnostic-settings.md#create-in-azure-portal)。 由於必須為每個 Azure 資源建立診斷設定，請參閱[大規模部署 Azure 監視器](deploy-scale.md)，以取得使用[azure 原則](../governance/policy/overview.md)在每次建立 Azure 資源時自動建立設定的詳細資訊。

### <a name="enable-insights-and-solutions"></a>啟用深入解析和解決方案
深入解析和解決方案提供特定服務或解決方案的特殊監視。 深入解析會使用較新的 Azure 監視器功能（例如活頁簿），因此，如果您的服務可以使用深入解析，就應該使用此見解。 它們會自動在每個 Azure 訂用帳戶中提供，但可能需要部分設定以取得完整功能。 它們通常會使用您先前設定的平臺計量和資源記錄檔，而且可能會收集其他資料。

解決方案必須加入至每個訂用帳戶，並以獨佔方式使用 Azure 監視器記錄中的資料，而且可能會收集其他記錄資料。

解決方案或深入解析不會產生任何費用，但您可能需要支付所收集的任何資料。

如需 Azure 監視器中可用的深入解析和解決方案清單，請參閱[Azure 監視器會監視哪些專案？](monitor-reference.md) 。 如需任何唯一的設定或定價資訊，請參閱每個的檔。 

## <a name="collect-data-from-virtual-machines"></a>從虛擬機器收集資料

> [!NOTE]
> 如需使用 Azure 監視器監視虛擬機器的完整指南，請參閱[使用 Azure 監視器監視 Azure 虛擬機器](insights/monitor-vm-azure.md)。 

虛擬機器會產生類似于其他 Azure 資源的資料，但您需要代理程式來收集來自客體作業系統的資料。 如需 Azure 監視器使用之代理程式的比較，請參閱[Azure 監視器代理](platform/agents-overview.md)程式的總覽。 

[適用於 VM 的 Azure 監視器](insights/vminsights-overview.md)會使用 Log Analytics 代理程式和相依性代理程式，從虛擬機器的客體作業系統收集資料，因此您可以在此深入解析的執行過程中部署這些代理程式。 這會讓 Log Analytics 代理程式適用于使用它的其他服務，例如 Azure 資訊安全中心。


[![部署 AZURE VM ](media/deploy/deploy-azure-vm.png)](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-azure-monitor-for-vms"></a>設定適用於 VM 的 Azure 監視器的工作區
適用於 VM 的 Azure 監視器需要 Log Analytics 工作區，這通常會與用來收集其他 Azure 資源的資料相同。 啟用任何虛擬機器之前，您必須將適用於 VM 的 Azure 監視器所需的解決方案新增至工作區。

如需設定 Log Analytics 工作區以進行適用於 VM 的 Azure 監視器的詳細資訊，請參閱[Configure Log analytics 工作區以取得適用於 VM 的 Azure 監視器](insights/vminsights-configure-workspace.md)。

### <a name="enable-azure-monitor-for-vms-on-each-virtual-machine"></a>在每部虛擬機器上啟用適用於 VM 的 Azure 監視器
設定好工作區之後，您可以藉由安裝 Log Analytics 代理程式和相依性代理程式來啟用每部虛擬機器。 安裝這些代理程式的方法有多種，包括 Azure 原則，可讓您在每個虛擬機器建立時自動進行設定。 適用於 VM 的 Azure 監視器所收集的效能資料和進程詳細資料會儲存在 Azure 監視器記錄中。

如需將代理程式部署至虛擬機器並啟用監視的選項，請參閱[啟用適用於 VM 的 Azure 監視器總覽](insights/vminsights-enable-overview.md)。

### <a name="configure-workspace-to-collect-events"></a>設定工作區以收集事件
適用於 VM 的 Azure 監視器會從每部虛擬機器的客體作業系統收集效能資料，以及處理常式的詳細資訊和相依性。 Log Analytics 代理程式也可以從來賓收集記錄，包括來自 Windows 的事件記錄檔，以及來自 Linux 的 syslog。 它會從所連線的 Log Analytics 工作區，抓取這些記錄的設定。 您只需要設定工作區一次，每次代理程式連線時，它就會下載所有設定變更。 

如需設定 Log Analytics 工作區以從代理程式虛擬機器收集其他資料的詳細資訊，請參閱[Azure 監視器中的代理程式資料來源](platform/agent-data-sources.md)。

> [!NOTE]
> 您也可以設定工作區來收集效能計數器，但這很可能會因為適用於 VM 的 Azure 監視器所收集的效能資料而重複。 工作區所收集的效能資料將會儲存在*Perf*資料表中，而適用於 VM 的 Azure 監視器所收集的效能資料則儲存在*InsightsMetrics*資料表中。 只有當您需要適用於 VM 的 Azure 監視器尚未收集的計數器時，才會在工作區中設定效能集合。

### <a name="diagnostic-extension-and-telegraf-agent"></a>診斷擴充功能和 Telegraf 代理程式
適用於 VM 的 Azure 監視器使用 Log Analytics 代理程式，它會將效能資料傳送至 Log Analytics 工作區，但不會 Azure 監視器計量。 將此資料傳送至計量，可讓它以計量瀏覽器進行分析，並與度量警示搭配使用。 這需要 Windows 上的診斷延伸模組和 Linux 上的 Telegraf 代理程式。

如需有關安裝和設定這些代理程式的詳細資訊，請參閱[安裝和設定 Windows Azure 診斷擴充功能 (WAD) ](platform/diagnostics-extension-windows-install.md)並[收集 Linux VM 的自訂計量](platform/collect-custom-metrics-linux-telegraf.md)。


## <a name="monitor-applications"></a>監視應用程式
Azure 監視器會使用[Application Insights](app/app-insights-overview.md)監視您的自訂應用程式，您必須針對每個要監視的應用程式進行設定。 設定程式會根據受監視的應用程式類型，以及您想要執行的監視類型而有所不同。 視功能而定，Application Insights 所收集的資料會儲存在 Azure 監視器計量、Azure 監視器記錄和 Azure blob 儲存體中。 效能資料會同時儲存在 Azure 監視器計量和 Azure 監視器記錄中，不需要額外的設定。

### <a name="create-an-application-resource"></a>建立應用程式資源
您必須針對要監視的每個應用程式，在 Application Insights 中建立資源。 Application Insights 所收集的記錄資料會儲存在 Azure 監視器記錄中，但與您的 Log Analytics 工作區不同，如[如何在 Azure 監視器記錄中的資料結構化？](platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured)中所述。 目前處於預覽狀態，是能夠直接將應用程式資料儲存在 Log Analytics 工作區中，並搭配您的其他資料。 這會簡化您的設定，並允許您的應用程式利用 Log Analytics 工作區的所有功能。

 當您建立應用程式時，您必須選取要使用傳統還是以工作區為基礎的 (預覽) 。 請參閱[建立 Application Insights 資源](app/create-new-resource.md)來建立傳統應用程式。 請參閱以[工作區為基礎的 Application Insights 資源 (預覽) ](app/create-workspace-resource.md)來建立以工作區為基礎的應用程式。

### <a name="configure-codeless-or-code-based-monitoring"></a>設定無程式碼或以程式碼為基礎的監視
若要啟用應用程式的監視，您必須決定是否要使用無程式碼或以程式碼為基礎的監視。 設定程式會根據這項決定和您要監視的應用程式類型而有所不同。

**無程式碼監視**最容易執行，而且可以在程式碼開發後進行設定。 您的程式碼不需要任何更新。 如需有關啟用監視的詳細資訊，請參閱下列資源（視您的應用程式而定）。

- [裝載于 Azure Web Apps 上的應用程式](app/azure-web-apps.md)
- [Java 應用程式](app/java-in-process-agent.md)
- [裝載在 Azure VM 或 Azure 虛擬機器擴展集上 IIS 中的 ASP.NET 應用程式](app/azure-vm-vmss-apps.md)
- [裝載在 IIS 內部部署 VM 中的 ASP.NET 應用程式](app/monitor-performance-live-website-now.md)


以**程式碼為基礎的監視**會更容易自訂並收集額外的遙測，但它需要在 Application Insights SDK NuGet 套件上，將相依性新增至您的程式碼。 如需根據您的應用程式啟用監視的詳細資訊，請參閱下列資源。

- [ASP.NET 應用程式](app/asp-net.md)
- [ASP.NET Core 應用程式](app/asp-net-core.md)
- [.NET 主控台應用程式](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [其他平台](app/platforms.md)

### <a name="configure-availability-testing"></a>設定可用性測試
Application Insights 中的可用性測試是週期性測試，可從世界各地的定期監視應用程式的可用性和回應性。 您可以免費建立簡單的 ping 測試，或建立一連串的 web 要求來模擬具有相關成本的使用者交易。 

請參閱[監視任何網站的可用性](app/monitor-web-app-availability.md)以取得不同測試類型的摘要，以及建立它們的詳細資料。

### <a name="configure-profiler"></a>設定 Profiler
Application Insights 中的 Profiler 會提供 .NET 應用程式的效能追蹤。 它可協助您識別在處理特定 web 要求時所花費時間最長的「熱」程式碼路徑。 設定 profiler 的程式會根據應用程式的類型而有所不同。 

如需設定 Profiler 的詳細資訊，請參閱[使用 Application Insights 分析 Azure 中的生產應用程式](app/profiler-overview.md)。

### <a name="configure-snapshot-debugger"></a>設定快照偵錯工具
Application Insights 中的快照偵錯工具會監視來自 .NET 應用程式的例外狀況遙測，並收集前擲回例外狀況的快照集，讓您擁有診斷生產環境中問題所需的資訊。 設定快照偵錯工具的程式會根據應用程式的類型而有所不同。 

如需設定快照偵錯工具的詳細資訊，請參閱在[.net 應用程式中的例外](app/snapshot-debugger.md)狀況的 Debug 錯快照


## <a name="visualize-data"></a>顯現資料
深入解析和解決方案會包含自己的活頁簿和資料分析視圖。 除了這些功能之外，您還可以建立自己的[視覺效果](visualizations.md)，包括 Azure 監視器資料和儀表板的活頁簿，以結合 Azure 監視器資料與 Azure 中其他服務的資料。


### <a name="create-workbooks"></a>建立活頁簿
Azure 監視器中的活頁[簿](platform/workbooks-overview.md)可讓您在 Azure 入口網站中建立豐富的視覺效果報表。 您可以將不同的資料集與 Azure 監視器計量和 Azure 監視器記錄結合，以建立統一的互動體驗。 您可以在 [Azure 監視器] 功能表的 [活頁**簿**] 索引標籤中，存取活頁簿的資源庫。 

如需建立自訂活頁簿的詳細資訊，請參閱[Azure 監視器活頁簿](platform/workbooks-overview.md)。

### <a name="create-dashboards"></a>建立儀表板
[Azure 儀表板](../azure-portal/azure-portal-dashboards.md)是 azure 的主要儀表板管理技術，可讓您結合 Azure 監視器資料與其他服務的資料，在您的 Azure 基礎結構上提供單一的半透明窗格。 如需建立包含來自 Azure 監視器記錄之資料的儀表板詳細資料，請參閱[建立和共用 Log Analytics 資料的儀表板](learn/tutorial-logs-dashboards.md)。 

如需建立包含來自 Application Insights 之資料的儀表板詳細資訊，請參閱[使用 Azure 應用程式 Insights 建立自訂 KPI 儀表板](learn/tutorial-app-dashboards.md)。 

## <a name="alerts"></a>警示
Azure 監視器中的警示會主動通知您監視資料中所識別的重要資料或模式。 有些深入解析會在沒有設定的情況下產生警示。 針對其他案例，您必須建立包含要分析之資料的[警示規則](platform/alerts-overview.md)，以及何時產生警示的準則，以及定義產生警示時要採取之動作的動作群組。 


### <a name="create-action-groups"></a>建立動作群組
[動作群組](platform/action-groups.md)是通知喜好設定的集合，由警示規則用來決定觸發警示時要執行的動作。 動作範例包括傳送郵件或文字、呼叫 webhook，或將資料傳送至 ITSM 工具。 每個警示規則都需要至少一個動作群組，而單一動作群組可供多個警示規則使用。

如需建立動作群組的詳細資訊，以及可包含的各種動作的說明，請參閱在[Azure 入口網站中建立和管理動作群組](platform/action-groups.md)。


### <a name="create-alert-rules"></a>建立警示規則
有多種類型的警示規則是由其使用的資料類型所定義。 每個都有不同的功能和不同的成本。 您應遵循的基本策略是使用警示規則類型，其成本最低，可提供您所需的邏輯。

- [活動記錄規則](platform/activity-log-alerts.md)。 針對符合指定條件的新活動記錄事件建立警示。 這些警示不會產生任何費用，因此應該是您的第一個選擇。 如需建立活動記錄警示的詳細資料，請參閱[使用 Azure 監視器建立、查看和管理活動記錄警示](platform/alerts-activity-log.md)。
- [度量警示規則](platform/alerts-metric-overview.md)。 建立警示以回應超過閾值的一個或多個度量值。 計量警示是可設定狀態的，這表示當值低於閾值時，警示會自動關閉，而且只會在狀態變更時傳送通知。 計量警示會產生成本，但這比記錄警示明顯少。 如需建立計量警示的詳細資訊，請參閱[使用 Azure 監視器建立、查看和管理計量警示](platform/alerts-metric.md)。
- [記錄警示規則](platform/alerts-unified-log.md)。 當排程查詢的結果符合指定的準則時，會建立警示。 它們是最昂貴的警示規則，但它們允許最複雜的條件。 如需有關建立記錄查詢警示的詳細資訊，請參閱[使用 Azure 監視器建立、查看和記錄管理警示](platform/alerts-log.md)。
- [應用程式警示](app/monitor-web-app-availability.md)可讓您執行 web 應用程式的主動式效能和可用性測試。 您可以免費執行簡單的 ping 測試，但需要花費較複雜的測試。 如需不同測試的說明，以及建立它們的詳細資料，請參閱[監視任何網站的可用性](app/monitor-web-app-availability.md)。


## <a name="next-steps"></a>後續步驟

- 請參閱[使用 Azure 原則大規模部署 Azure 監視器](deploy-scale.md)。