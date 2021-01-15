---
title: 適用于現有 Operations Manager 客戶的 Azure 監視器
description: Operations Manager 的現有使用者指引，以將特定工作負載的監視轉換成 Azure 監視器作為轉換至雲端的一部分。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: 85172e2430a3e65edb0c5ec119c920e2c7d20217
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234757"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>適用于現有 Operations Manager 客戶的 Azure 監視器
本文提供的指導方針適用于目前使用 [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/welcome) 且正在規劃將商務應用程式和其他資源遷移至 Azure 的 [Azure 監視器](overview.md) 。 它會假設您的終極目標是完整轉換至雲端，以 Azure 監視器盡可能地取代 Operations Manager 功能，而不會危及您的業務和 IT 營運需求。 

本文中所做的特定建議將隨著 Azure 監視器和 Operations Manager 新增功能而變更。 不過，基本策略仍維持一致。

> [!IMPORTANT]
> 執行此處所述的數個 Azure 監視器功能需要成本，因此您應該在整個環境中部署之前評估其價值。

## <a name="prerequisites"></a>必要條件
本文假設您已使用 [Operations Manager](https://docs.microsoft.com/system-center/scom) ，且至少對 [Azure 監視器](overview.md)有基本的瞭解。 如需這兩者之間的完整比較，請參閱 [雲端監視指南：監視平臺總覽](/azure/cloud-adoption-framework/manage/monitor/platform-overview)。 該文章詳述與兩者之間的特定功能差異，以協助您瞭解此處所做的一些建議。 


## <a name="general-strategy"></a>一般策略
由於平臺本質上不同，因此沒有可將資產從 Operations Manager 轉換 Azure 監視器的遷移工具。 當您繼續使用 Operations Manager 時，您的遷移會改為構成 [標準的 Azure 監視器實施](deploy.md) 。 當您自訂 Azure 監視器以符合不同應用程式和元件的需求，並隨著其獲得更多功能，您就可以開始在 Operations Manager 淘汰不同的管理元件和代理程式。

本文中建議的一般策略與 [雲端監視指南](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/)中的相同，它會建議 [混合式雲端監視](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) 策略，讓您能夠逐漸轉換到雲端。 雖然有些功能可能會重迭，但此策略可讓您在更熟悉新平臺的情況下維護現有的商務程式。 您只能將其取代為 Azure 監視器的 Operations Manager 功能。 使用多個監視工具會增加複雜度，但可讓您利用 Azure 監視器的能力來監視新一代雲端工作負載，同時保有 Operations Manager 監視可能是內部部署或其他雲端中的伺服器軟體和基礎結構元件的能力。 


## <a name="components-to-monitor"></a>要監視的元件
它有助於將您需要監視的不同工作負載類型分類，以判斷每個工作負載的不同監視策略。 [雲端監視指南：制訂監視策略](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) 可針對您環境中的不同層提供詳細的細目，以在您從舊版企業應用程式到雲端中的新式應用程式時，需要進行監視。

在雲端之前，您使用 Operations Manager 來監視所有的圖層。 當您開始以基礎結構即服務 (IaaS) 進行轉換時，您會繼續使用虛擬機器的 Operations Manager，但開始為您的雲端資源使用 Azure 監視器。 當您使用「平臺即服務」 (PaaS) 來進一步轉換至新式應用程式時，您可以將焦點放在 Azure 監視器，並開始淘汰 Operations Manager 功能。


![雲端模型](https://docs.microsoft.com/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

您可以將這些層級簡化為下列類別，這會在本文的其餘部分中進一步說明。 雖然您環境中的每個監視工作負載可能無法整齊放入其中一個類別，但每個工作負載都應該接近特定類別，才能套用一般建議。

**商務應用程式。** 為您的企業提供特定功能的應用程式。 它們可能是內部或外部，而且通常會使用自訂程式碼在內部開發。 您的繼承應用程式通常會裝載于執行 Windows 或 Linux 的虛擬或實體機器上，而您較新的應用程式將會根據 Azure 中的應用程式服務，例如 Azure Web Apps 和 Azure Functions。

**Azure 服務。** Azure 中的資源，可支援已遷移至雲端的商務應用程式。 這包括 Azure 儲存體、Azure SQL 和 Azure IoT 等服務。 這也包括 Azure 虛擬機器，因為它們會像其他 Azure 服務一樣受到監視，但在這些虛擬機器的客體作業系統上執行的應用程式和軟體需要更多監視超過主機。

**伺服器軟體。** 在虛擬和實體機器上執行的軟體，可支援您的商務應用程式或封裝的應用程式，以提供您企業的一般功能。 範例包括 Internet Information Server (IIS) 、SQL Server、Exchange 和 SharePoint。 這也包括虛擬和實體機器上的 Windows 或 Linux 作業系統。

**本機基礎結構。** 需要監視的內部部署環境特有的元件。 這包括實體伺服器、儲存體和網路元件等資源。 這些是當您移至雲端時，會虛擬化的元件。

## <a name="sample-walkthrough"></a>範例逐步解說
以下是從 Operations Manager 遷移至 Azure 監視器的假設逐步解說。 這並不是用來代表實際遷移的完整複雜度，但至少會提供基本步驟和順序。 下列各節會更詳細地說明每個步驟。

將任何元件移至 Azure 之前，您的環境是以位於內部部署或受控服務提供者的虛擬和實體機器為基礎。 它依賴 Operations Manager 來監視環境中的商務應用程式、伺服器軟體和其他基礎結構元件，例如實體伺服器和網路。 您可以使用適用于伺服器軟體的標準管理套件，例如 IIS、SQL Server 和各種廠商軟體，並針對您的特定需求調整這些管理元件。 您可以為您的商務應用程式和其他元件建立自訂管理元件，這些元件無法使用現有的管理元件進行監視，並設定 Operations Manager 來支援您的商務程式。

您遷移至 Azure 的起點是 IaaS，將支援商務應用程式的虛擬機器移至 Azure。 這些應用程式及其相依的伺服器軟體的監視需求不會變更，且您可以繼續在這些伺服器上使用現有的管理元件 Operations Manager。 

當您建立 Azure 訂用帳戶時，就會立即為您的 Azure 服務啟用 Azure 監視器。 它會自動收集平臺計量和活動記錄檔，並設定要收集的資源記錄，讓您可以使用記錄查詢以互動方式分析所有可用的遙測。 您可以在虛擬機器上啟用適用於 VM 的 Azure 監視器，以分析整個環境的監視資料，並探索電腦與進程之間的關聯性。 您可以在內部部署的實體和虛擬機器上啟用 Azure Arc 的伺服器，藉此擴充您的 Azure 監視器使用。 

您可以為每個商務應用程式啟用 Application Insights。 它會識別每個應用程式的不同元件、開始收集使用量和效能資料，以及識別程式碼中發生的任何錯誤。 您可以建立可用性測試，以主動測試您的外部應用程式，並在任何效能或可用性問題時發出警示。 雖然 Application Insights 為您提供了 Operations Manager 所沒有的強大功能，但仍會繼續依賴您為商務應用程式開發的自訂管理元件，因為它們包含尚未涵蓋 Azure 監視器的監視案例。 

當您熟悉 Azure 監視器時，您會開始建立能夠取代某些管理元件功能的警示規則，並開始發展您的商務程式，以使用新的監視平臺。 這可讓您從 Operations Manager 管理群組開始移除電腦和管理套件。 您可以繼續使用關鍵伺服器軟體和內部部署基礎結構的管理元件，但繼續留意 Azure 監視器中的新功能，讓您淘汰額外的功能。

## <a name="monitor-azure-services"></a>監視 Azure 服務
Azure 服務實際上需要 Azure 監視器來收集遙測資料，而且會在您建立 Azure 訂用帳戶的時候啟用。 系統會自動收集訂用帳戶的 [活動記錄](platform/activity-log.md) ，並會自動從您建立的任何 Azure 資源收集 [平臺計量](platform/data-platform-metrics.md) 。 您可以立即開始使用 [計量瀏覽器](platform/metrics-getting-started.md)，這類似于 Operations 主控台中的效能流覽，但可提供互動式分析和資料的 [advanced](platform/metrics-charts.md) 匯總。 [建立計量警示](platform/alerts-metric.md) ，以在值超過閾值時收到通知，或 [將圖表新增至 Azure 儀表板](platform/metrics-charts.md#pin-charts-to-dashboards) 以供可見度。

[![計量瀏覽器](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

為每個 Azure 資源[建立診斷設定](platform/diagnostic-settings.md)，以傳送計量和[資源記錄](platform/resource-logs.md)，以提供有關每個資源內部作業的詳細資料至 Log Analytics 工作區。 這會提供您資源的所有可用遙測，並可讓您使用 [Log Analytics](log-query/log-analytics-overview.md) ，以互動方式使用在 Operations Manager 中沒有對等專案的 advanced query 語言來分析記錄和效能資料。 您也可以建立 [記錄查詢警示](platform/alerts-log-query.md)，以使用複雜邏輯來判斷警示狀況，並讓多個資源之間的資料相互關聯。

[![記錄分析](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

Azure 監視器中的[深入](monitor-reference.md)解析類似于管理元件，其可為特定的 Azure 服務提供獨特的監視功能。 見解目前適用于數個服務，包括網路、儲存體和容器，而其他服務則會持續新增。

[![深入解析範例](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


深入解析是以 Azure 監視器的活頁 [簿](platform/workbooks-overview.md) 為基礎，這會將計量和記錄查詢結合成豐富的互動式報表。 建立您自己的活頁簿，以結合多個服務的資料，類似于您可能在 Operations 主控台中建立自訂視圖和報表的方式。

### <a name="azure-management-pack"></a>Azure 管理組件 \(英文\)
[Azure 管理元件](https://www.microsoft.com/download/details.aspx?id=50013)可讓 Operations Manager 探索 Azure 資源，並根據一組特定的監視案例來監視其健康情況。 此管理元件會要求您針對 Azure 中的每個資源執行額外的設定，但在您發展商務程式以專注于 Azure 監視器之前，在 Operations 主控台中提供 Azure 資源的一些可見度可能會有所説明。

[![Azure 管理封包](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 如果您想要在 Operations 主控台中查看某些 Azure 資源，並將一些基本警示與現有的程式整合，您可以選擇使用 Azure 管理套件。 它實際上是使用 Azure 監視器所收集的資料。 針對您的 Azure 資源，您應該尋找 Azure 監視器的長期完整監視。 


## <a name="monitor-server-software-and-local-infrastructure"></a>監視伺服器軟體和本機基礎結構
當您將機器移至雲端時，其軟體的監視需求不會變更。 您不再需要監視其實體元件，因為它們已虛擬化，但是無論其環境為何，客體作業系統及其工作負載都有相同的需求。

[適用於 VM 的 Azure 監視器](insights/vminsights-overview.md) 是 Azure 監視器中的主要功能，可監視虛擬機器及其客體作業系統和工作負載。 類似于 Operations Manager，適用於 VM 的 Azure 監視器使用代理程式從虛擬機器的客體作業系統收集資料。 這與管理元件通常用於分析和警示的效能和事件資料相同。 不過，沒有預先存在的規則可識別在這些電腦上執行的商務應用程式和伺服器軟體的問題，併發出警示。 您必須建立自己的警示規則，以主動通知任何偵測到的問題。

[![適用於 VM 的 Azure 監視器效能](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure 監視器也不會測量在虛擬機器上執行的不同應用程式和服務的健全狀況。 當值低於閾值時，計量警示可以自動解決，但 Azure 監視器目前無法為電腦上執行的應用程式和服務定義健全準則，也不會提供健全狀況匯總來將相關元件的健全狀況分組。

> [!NOTE]
> 適用於 VM 的 Azure 監視器的新 [來賓健康情況功能](insights/vminsights-health-overview.md) 現在處於公開預覽狀態，並會根據一組效能計量的健全狀況狀態來發出警示。 這一開始會限制為一組特定的效能計數器，與客體作業系統相關，而不是在虛擬機器中執行的應用程式或其他工作負載。
> 
> [![適用於 VM 的 Azure 監視器來賓健康情況](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

在混合式環境中監視電腦上的軟體，通常會使用適用於 VM 的 Azure 監視器和 Operations Manager 的組合，取決於每部機器的需求，以及您在 Azure 監視器上開發營運程式的成熟度。 Microsoft 管理代理程式 (將 Azure 監視器) 中的 Log Analytics 代理程式，用於這兩個平臺，讓您可以同時監視單一電腦。

> [!NOTE]
> 未來，適用於 VM 的 Azure 監視器會轉換至 [Azure 監視器代理程式，此代理程式](platform/azure-monitor-agent-overview.md)目前處於公開預覽狀態。 它會與 Microsoft Monitoring Agent 相容，因此相同的虛擬機器將繼續由這兩個平臺監視。

繼續使用 Operations Manager，找出 Azure 監視器尚未提供的功能。 這包括重要伺服器軟體（例如 IIS、SQL Server 或 Exchange）的管理元件。 您也可能會有為內部部署基礎結構開發的自訂管理元件，但 Azure 監視器無法達到此目的。 如果已緊密整合到您的作業程式，請繼續使用 Operations Manager，直到您可以轉換來現代化您的服務作業，讓 Azure 監視器和其他 Azure 服務可以擴大或取代。 

使用 Azure 監視器 fo Vm 來增強您目前的監視，即使它不會立即取代 Operations Manager。 Azure 監視器獨有功能的範例包括下列各項：

- 探索並監視虛擬機器與其外部相依性之間的關聯性。
- 在互動式圖表和活頁簿中，跨多部虛擬機器來查看匯總的效能資料。
- 使用 [記錄查詢](log-query/log-query-overview.md) ，以互動方式分析來自您的虛擬機器的遙測資料與其他 Azure 資源的資料。
- 根據跨多個虛擬機器的複雜邏輯建立 [記錄警示規則](platform/alerts-log-query.md) 。

[![適用於 VM 的 Azure 監視器對應](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

除了 Azure 虛擬機器之外，適用於 VM 的 Azure 監視器可以使用 [Azure Arc 啟用的伺服器](../azure-arc/servers/overview.md)來監視內部部署和其他雲端中的機器。 啟用 Arc 的伺服器可讓您管理裝載于 Azure 外部、公司網路或其他雲端提供者的 Windows 和 Linux 機器，與您管理原生 Azure 虛擬機器的方式一致。



## <a name="monitor-business-applications"></a>監視商務應用程式
您通常需要自訂管理元件以利用 Operations Manager，利用安裝在每部虛擬機器上的代理程式來監視您的商務應用程式。 Azure 監視器中的 Application Insights 會監視 web 應用程式是否位於 Azure、其他雲端或內部部署中，以便將其用於所有的應用程式，不論這些應用程式是否已遷移至 Azure。

如果您對商務應用程式的監視僅限於 Operations Manager 中的 [.net 應用程式效能範本]() 所提供的功能，則您很可能會遷移至 Application Insights，而不會遺失任何功能。 事實上，Application Insights 將包含許多額外的功能，包括下列各項：

- 自動探索和監視應用程式元件。
- 收集詳細的應用程式使用方式和效能資料，例如回應時間、失敗率和要求率。
- 收集瀏覽器資料，例如頁面流覽和載入效能。
- 偵測例外狀況，並深入瞭解堆疊追蹤和相關要求。
- 使用 [分散式追蹤](app/distributed-tracing.md) 和 [智慧型偵測](app/proactive-diagnostics.md)等功能來執行 advanced 分析。
- 使用 [計量瀏覽器](platform/metrics-getting-started.md) 以互動方式分析效能資料。
- 使用 [記錄查詢](log-query/log-query-overview.md) 以互動方式分析收集的遙測資料，以及針對 Azure 服務和適用於 VM 的 Azure 監視器所收集的資料。

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

但在某些情況下，您可能需要繼續使用 Application Insights Operations Manager，除非您能夠達成所需的功能。 您可能需要繼續進行 Operations Manager 的範例包括下列各項：

-  [可用性測試](app/monitor-web-app-availability.md)可讓您監視及警示應用程式的可用性和回應性，要求來自 web 測試代理程式 IP 位址的連入要求。 如果您的原則不允許這類存取，您可能需要繼續使用 Operations Manager 中的 [Web 應用程式可用性監視器](/system-center/scom/web-application-availability-monitoring-template) 。
- 在 Operations Manager 您可以設定可用性測試的任何輪詢間隔，其中有許多客戶每隔60-120 秒檢查一次。 Application Insights 有5分鐘的最小輪詢間隔，對某些客戶而言可能太長。
- Operations Manager 的大量監視是透過收集應用程式所產生的事件，以及在本機代理程式上執行腳本來執行。 這些不是 Application Insights 中的標準選項，因此您可以要求自訂工作以達成您的商務需求。 這可能包括使用 Log Analytics 工作區中儲存的事件資料，以及使用 [混合式 runbook 背景工作角色](../automation/automation-hybrid-runbook-worker.md)在虛擬機器來賓中啟動的腳本所建立的自訂警示規則。
- 根據您的應用程式撰寫的語言，您可能會受到限制，而 [您可以搭配 Application Insights 使用此檢測](app/platforms.md)。

遵循本指南的其他章節中的基本策略，繼續針對您的商務應用程式使用 Operations Manager，但利用 Application Insights 所提供的其他功能。 由於您可以使用 Azure 監視器來取代重要的功能，因此您可以開始淘汰自訂管理元件。


## <a name="next-steps"></a>後續步驟

- 如需 Azure 監視器和 System Center Operations Manager 的詳細比較，以及設計和執行混合式監視環境的詳細資訊，請參閱 [雲端監視指南](/azure/cloud-adoption-framework/manage/monitor/) 。
- 深入瞭解如何 [在 Azure 監視器中監視 Azure 資源](insights/monitor-azure-resource.md)。
- 深入瞭解如何 [在 Azure 監視器中監視 Azure 虛擬機器](insights/monitor-vm-azure.md)。
- 閱讀 [適用於 VM 的 Azure 監視器](insights/vminsights-overview.md)的詳細資訊。
- 閱讀 [Application Insights](app/app-insights-overview.md)的詳細資訊。
