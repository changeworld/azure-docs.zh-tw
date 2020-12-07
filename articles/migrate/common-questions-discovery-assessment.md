---
title: Azure Migrate 中探索、評量和相依性分析的相關問題
description: 取得 Azure Migrate 中探索、評量和相依性分析的常見問題解答。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 4531d68c2fbd0698c33d70a75bb82ac9c7f52f49
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752238"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>探索、評量和相依性分析-常見問題

本文針對 Azure Migrate 中的探索、評量和相依性分析，提供常見問題的解答。 如果您有其他問題，請查看下列資源：

- 關於 Azure Migrate 的[一般問題](resources-faq.md)
- [Azure Migrate 設備](common-questions-appliance.md)的相關問題
- [伺服器遷移](common-questions-server-migration.md)的相關問題
- 在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)中取得問題的解答


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>哪些地理位置支援 Azure Migrate 的探索和評量？

請檢閱[公用](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府雲端](migrate-support-matrix.md#supported-geographies-azure-government)支援的地理位置。


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>我可以使用設備探索多少部 Vm？

您可以使用單一設備探索最多10000個 VMware Vm、最多5000部 Hyper-v Vm，以及最多1000部實體伺服器。 如果您有更多機器，請參閱 [調整 hyper-v 評](scale-hyper-v-assessment.md)量、 [調整 VMware 評](scale-vmware-assessment.md)量，或 [調整實體伺服器評](scale-physical-assessment.md)量。

## <a name="how-do-i-choose-the-assessment-type"></a>如何選擇評量類型？

- 當您想要評估內部部署 [VMware vm](how-to-set-up-appliance-vmware.md)、 [hyper-v vm](how-to-set-up-appliance-hyper-v.md)和 [實體伺服器](how-to-set-up-appliance-physical.md)以遷移至 Azure VM 時，請使用 **Azure VM 評** 量。 [深入了解](concepts-assessment-calculation.md)

- 當您想要使用此評量類型來評估內部部署 [Vmware vm](how-to-set-up-appliance-vmware.md)以遷移至 [Azure VMWARE 解決方案 (AVS)](../azure-vmware/introduction.md)時，請使用 **Azure VMware Solution (AVS)** 評量。 [深入了解](concepts-azure-vmware-solution-assessment-calculation.md)

- 您只能使用與 VMware 機器相同的通用群組來執行這兩種類型的評量。 請注意，如果您是第一次在 Azure Migrate 中執行 AVS 評量，建議您建立新的 VMware 機器群組。
 

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>為什麼我的評量報告中遺失某些/所有 VM 的效能資料？

進行「以效能為基礎的」評量時，當 Azure Migrate 設備無法收集內部部署 VM 的效能資料時，評量報告匯出會顯示 'PercentageOfCoresUtilizedMissing' 或 'PercentageOfMemoryUtilizedMissing'。 請檢查：

- VM 在您建立評量的持續時間內是否開啟電源
- 如果只有記憶體計數器遺失，而且您想要評估 Hyper-V VM，那麼請檢查您是否已在這些 VM 上啟用動態記憶體。 目前有已知問題導致 Azure Migrate 設備無法對此類 VM 收集記憶體使用量。
- 如果所有的效能計數器都遺失，請確定允許埠 443 (HTTPS) 的輸出連線。

注意：如果遺漏任何效能計數器，則 Azure Migrate：伺服器評量會回復為已配置的內部部署核心/記憶體，並據此建議 VM 大小。

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>為何我的評量信賴評等偏低？

系統會根據計算評量所需的[可用資料點](./concepts-assessment-calculation.md#ratings)百分比，為「以效能為基礎的」評量計算信賴評等。 以下是評量會獲得低信賴評等的原因：

- 您未針對正在建立評量的持續時間剖析環境。 例如，如果您要建立將效能持續時間設定為一週的評量，您需要至少等待一週後再開始探索，才能收集到所有資料點。 如果您無法等待該持續時間，請將效能持續時間變更為較短的期間，並「重新計算」評量。
 
- 伺服器評定無法收集評定期間內部分或所有 Vm 的效能資料。 請檢查 VM 在評估期間內是否開啟電源，並允許連接埠 443 上的輸出連線。 針對 Hyper-V VM，如果啟用了動態記憶體，記憶體計數器將會遺失，因而導致信賴評等偏低。 請「重新計算」評量，以反映信賴評等的最新變更。 

- 有少數 VM 是在伺服器評量中的探索啟動後建立的。 例如，如果您要建立過去一個月的效能記錄評量，但是少數虛擬機器在一週前才建立在環境中。 在此情況下，將無法取得新的 VM 在這整段期間內的效能資料，且信賴評等將會偏低。

[深入了解](./concepts-assessment-calculation.md#confidence-ratings-performance-based)信賴評等。

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>我在建立 Azure VMware 解決方案 (AVS) 評量時看不到某些群組

- 您可以在只有 VMware 電腦的群組上進行 AVS 評估。 如果要執行 AVS 評估，請從群組中移除所有非 VMware 機器。
- 如果您是第一次在 Azure Migrate 中執行 AVS 評量，建議您建立新的 VMware 機器群組。

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>我在 Azure Government 中看不到某些 VM 類型

支援評定和遷移的 VM 類型取決於 Azure Government 位置中的可用性。 您可以 [檢查並比較](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) Azure Government 中的 VM 類型。

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>我的 VM 大小已變更。 我可以再次執行評量嗎？

Azure Migrate 設備會持續收集內部部署環境的相關資訊。  評量是內部部署 Vm 的時間點快照集。 如果您在想要評估的 VM 上變更設定，請使用 [重新計算] 選項，以最新的變更來更新評量。

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>如何? 探索多租使用者環境中的 Vm 嗎？

- **VMware**：如果環境跨租使用者共用，而您不想要在另一個租使用者的訂用帳戶中探索租使用者的 vm，請建立 VMware vCenter Server 認證，以便只能存取您想要探索的 vm。 然後，當您在 Azure Migrate 設備中啟動探索時，請使用這些認證。
- **Hyper-v**：探索會使用 hyper-v 主機認證。 如果 Vm 共用相同的 Hyper-v 主機，目前沒有任何方法可分隔探索。  

## <a name="do-i-need-vcenter-server"></a>我需要 vCenter Server 嗎？

是的，Azure Migrate 需要 VMware 環境中的 vCenter Server 來執行探索。 Azure Migrate 不支援探索未受 vCenter Server 管理的 ESXi 主機。

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Azure VM 評量中的調整大小選項有哪些？

使用內部部署的大小調整時，Azure Migrate 不會考慮 VM 效能資料來進行評量。 Azure Migrate 會根據內部部署設定來評估 VM 大小。 使用以效能為基礎的大小調整，調整大小是以使用量資料為基礎。

例如，如果內部部署 VM 有四個核心和 8 GB 的記憶體，50% 的 CPU 使用率和50% 的記憶體使用量：
- 內部部署調整大小會建議有四個核心和 8 GB 記憶體的 Azure VM SKU。
- 以效能為基礎的大小調整會建議 VM SKU 具有兩個核心和 4 GB 的記憶體，因為會考慮使用率百分比。

同樣地，磁片大小調整取決於調整大小準則和儲存體類型：
- 如果調整大小準則是以效能為基礎，而且儲存體類型是自動的，Azure Migrate 會在識別出目標磁片類型 (Standard 或 Premium) 時，將磁片的 IOPS 和輸送量值納入考慮。
- 如果調整大小準則是以效能為基礎，且儲存體類型是 Premium，Azure Migrate 會根據內部部署磁片的大小來建議高階磁片 SKU。 當調整大小為內部部署且儲存體類型為 Standard 或 Premium 時，會將相同的邏輯套用至磁片大小調整。

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>效能歷程記錄和使用率是否會影響 Azure VM 評量中的大小調整？

是，效能歷程記錄和使用率會影響 Azure VM 評量的大小。

### <a name="performance-history"></a>效能歷程記錄

針對僅以效能為基礎的大小調整，Azure Migrate 會收集內部部署機器的效能歷程記錄，然後使用它來建議 Azure 中的 VM 大小和磁片類型：

1. 設備會持續分析內部部署環境，每20秒收集一次即時使用方式資料。
2. 設備會匯總所收集的20秒範例，並每隔15分鐘使用它們來建立單一資料點。
3. 若要建立資料點，設備會從所有20秒範例中選取尖峰值。
4. 設備會將資料點傳送至 Azure。

### <a name="utilization"></a>使用率

當您在 Azure 中建立評估時，視效能持續時間和設定的效能歷程百分位數值而定，Azure Migrate 會計算有效的使用率值，然後使用它來調整大小。

例如，如果您將效能持續時間設定為一天，並將百分位數值設定為第95個百分位數，Azure Migrate 會以遞增順序來排序收集器過去一天所傳送的15分鐘取樣點。 它會挑選第95個百分位數值作為有效使用率。

使用第95個百分位數值可確保忽略極端值。 如果您的 Azure Migrate 使用第99個百分位數，可能會包含極端值。 若要挑選期間的尖峰使用量，但不遺失任何極端值，請將 Azure Migrate 設定為使用第99個百分位數。


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>以匯入為基礎的評量與探索來源為設備的評量有何不同？

以匯入為基礎的 Azure VM 評量是使用 CSV 檔案匯入 Azure Migrate 的機器所建立的評量。 匯入只需要四個欄位：伺服器名稱、核心、記憶體和作業系統。 以下是一些要注意的事項： 
 - 在開機類型參數上以匯入為基礎的評量中，就緒準則較不嚴格。 如果未提供開機類型，則會假設電腦具有 BIOS 開機類型，而且電腦未標示為有條件地 **備** 妥。 在探索來源為設備的評量中，如果遺失開機類型，就會將就緒狀態標示為有 **條件地備** 妥。 這項準備工作的差異在於，在完成以匯入為基礎的評量完成時，使用者可能不會在遷移規劃的早期階段中擁有電腦上的所有資訊。 
 - 以效能為基礎的匯入評量會使用使用者提供的使用率值來進行正確大小的計算。 由於使用率值是由使用者提供，因此在評量屬性中，會停用 **效能歷程記錄** 和 **百分位數使用量** 選項。 在以探索來源作為設備的評量中，選擇的百分位數值會從設備所收集的效能資料中挑選。

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>為什麼以匯入為基礎的 AVS 評定中的建議遷移工具標示為未知？

對於透過 CSV 檔案匯入的機器，AVS 評量中的預設遷移工具不明。 不過，對於 VMware 機器，建議使用 VMware 混合式雲端擴充功能 (HCX) 解決方案。 [深入了解](../azure-vmware/tutorial-deploy-vmware-hcx.md)。


## <a name="what-is-dependency-visualization"></a>什麼是相依性視覺效果？

相依性視覺效果可協助您評估要更安心地遷移的 Vm 群組。 在您執行評量之前，相依性視覺效果會跨檢查電腦相依性。 它有助於確保不會留下任何內容，並可在您遷移至 Azure 時避免非預期的中斷。 Azure Migrate 使用了 Azure 監視器中的 [服務對應] 解決方案來啟用相依性視覺效果。 [深入了解](concepts-dependency-visualization.md)。

> [!NOTE]
> Azure Government 中無法使用以代理程式為基礎的相依性分析。 您可以使用無代理程式相依性分析

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>代理程式型和無代理程式有何不同？

無代理程式視覺效果和以代理程式為基礎的視覺效果之間的差異摘要在表格中。

**需求** | **無代理程式** | **以代理程式為基礎**
--- | --- | ---
支援 | 此選項目前為預覽狀態，且僅適用于 VMware Vm。 [檢查](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) 支援的作業系統。 | 公開上市 (GA) 。
代理程式 | 不需要在您想要交叉檢查的機器上安裝代理程式。 | 要在每個您想要分析的內部部署機器上安裝的代理程式： [Microsoft Monitoring agent (MMA) ](../azure-monitor/platform/agent-windows.md)和 [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。 
先決條件 | [檢查](concepts-dependency-visualization.md#agentless-analysis) 必要條件和部署需求。 | [檢查](concepts-dependency-visualization.md#agent-based-analysis) 必要條件和部署需求。
Log Analytics | 不需要。 | Azure Migrate 會使用 [Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md)中的[服務對應](../azure-monitor/insights/service-map.md)解決方案來實現相依性視覺效果。 [深入了解](concepts-dependency-visualization.md#agent-based-analysis)。
運作方式 | 在啟用相依性視覺效果的電腦上捕獲 TCP 連接資料。 探索之後，會依五分鐘的間隔收集資料。 | 電腦上安裝的服務對應代理程式會收集有關每個處理常式的 TCP 程式和輸入/輸出連接的資料。
資料 | 來源電腦伺服器名稱、進程、應用程式名稱。<br/><br/> 目的地電腦伺服器名稱、進程、應用程式名稱和埠。 | 來源電腦伺服器名稱、進程、應用程式名稱。<br/><br/> 目的地電腦伺服器名稱、進程、應用程式名稱和埠。<br/><br/> 系統會收集並提供 Log Analytics 查詢的連線、延遲和資料傳輸資訊的數目。 
視覺效果 | 單一伺服器的相依性對應可在一小時到30天的期間內查看。 | 單一伺服器的相依性對應。<br/><br/> 您只能在一小時內查看 Map。<br/><br/> 伺服器群組的相依性對應。<br/><br/> 從地圖視圖新增和移除群組中的伺服器。
資料匯出 | 您可以使用 CSV 格式來下載過去30天的資料。 | 您可以使用 Log Analytics 來查詢資料。


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>是否需要部署設備以進行無代理程式相依性分析？

是，必須部署 [Azure Migrate 設備](migrate-appliance.md) 。

## <a name="do-i-pay-for-dependency-visualization"></a>相依性視覺效果需要付費嗎？

不會。 深入瞭解 [Azure Migrate 定價](https://azure.microsoft.com/pricing/details/azure-migrate/)。

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>我要針對以代理程式為基礎的相依性視覺效果安裝什麼？

若要使用以代理程式為基礎的相依性視覺效果，請在您想要評估的每個內部部署機器上，下載並安裝代理程式：

- [Microsoft Monitoring Agent (MMA) ](../azure-monitor/platform/agent-windows.md)
- [相依性代理程式](../azure-monitor/platform/agents-overview.md#dependency-agent)
- 如果您的電腦沒有網際網路連線，請在這些電腦上下載並安裝 Log Analytics 閘道。

只有當您使用以代理程式為基礎的相依性視覺效果時，才需要這些代理程式。

## <a name="can-i-use-an-existing-workspace"></a>我可以使用現有的工作區嗎？

是，如果是以代理程式為基礎的相依性視覺效果，您可以將現有的工作區附加至遷移專案，並將其用於相依性視覺化。 

## <a name="can-i-export-the-dependency-visualization-report"></a>是否可以匯出相依性視覺效果報告？

否，無法匯出以代理程式為基礎的視覺效果中的相依性視覺效果報表。 不過，Azure Migrate 使用服務對應，您可以使用 [服務對應 REST API](/rest/api/servicemap/machines/listconnections) 以 JSON 格式來取得相依性。

## <a name="can-i-automate-agent-installation"></a>我可以自動安裝代理程式嗎？

針對以代理程式為基礎的相依性視覺效果：

- 使用 [腳本來安裝 Dependency agent](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent)。
- 若為 MMA，請 [使用命令列或自動化](../azure-monitor/platform/log-analytics-agent.md#installation-options)，或使用 [腳本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。
- 除了腳本之外，您還可以使用 Microsoft Endpoint Configuration Manager 和 [>intigua](https://www.intigua.com/intigua-for-azure-migration) 之類的部署工具來部署代理程式。

## <a name="what-operating-systems-does-mma-support"></a>MMA 支援哪些作業系統？

- 查看 [MMA 支援的 Windows 作業系統](../azure-monitor/platform/log-analytics-agent.md#installation-options)清單。
- 查看 [MMA 支援的 Linux 作業系統](../azure-monitor/platform/log-analytics-agent.md#installation-options)清單。

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>我可以將相依性視覺化超過一小時嗎？

針對以代理程式為基礎的視覺效果，您可以將相依性視覺化最多一小時。 您可以在歷程記錄中返回一個月到特定日期，但視覺效果的最大持續時間為一小時。 例如，您可以使用相依性對應中的時間持續時間來查看昨天的相依性，但您只能查看一小時的時間範圍內的相依性。 不過，您可以使用 Azure 監視器記錄來 [查詢](./how-to-create-group-machine-dependencies.md) 較長持續時間的相依性資料。

若為無代理程式視覺效果，您可以從1小時到30天之間的持續時間，查看單一伺服器的相依性對應。

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>我可以將超過10個 Vm 的群組相依性視覺化嗎？

您可以針對最多10個 Vm 的群組，將相依性 [視覺化](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) 。 如果您的群組有10部以上的 Vm，建議您將群組分割成較小的群組，然後將相依性視覺化。

## <a name="next-steps"></a>後續步驟

閱讀 [Azure Migrate 總覽](migrate-services-overview.md)。