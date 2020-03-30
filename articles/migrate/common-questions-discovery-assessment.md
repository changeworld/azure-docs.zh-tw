---
title: 有關 Azure 遷移中的發現、評估和依賴關係分析的問題
description: 獲取有關 Azure 遷移中發現、評估和依賴項分析的常見問題的解答。
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7a63271811053ee2da79f134ac117559e31b0fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460801"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>發現、評估和依賴分析 - 常見問題

本文回答了有關 Azure 遷移中的發現、評估和依賴項分析的常見問題。 如果您有其他問題，請檢查以下資源：

- 有關 Azure 遷移的[一般問題](resources-faq.md)
- 有關[Azure 遷移設備的問題](common-questions-appliance.md)
- 有關[伺服器遷移](common-questions-server-migration.md)的問題
- 在[Azure 遷移論壇](https://aka.ms/AzureMigrateForum)中回答問題

## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>使用設備可以發現多少個 VM？

通過使用單個設備，您可以發現多達 10，000 個 VMware VM、多達 5，000 台 Hyper-VM 和多達 250 台物理伺服器。 如果您有更多電腦，請閱讀有關縮放[Hyper-V 評估](scale-hyper-v-assessment.md)、[縮放 VMware 評估](scale-vmware-assessment.md)或[縮放物理伺服器評估](scale-physical-assessment.md)。

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>我的 VM 大小已更改。 我可以再次進行評估嗎？

Azure 遷移設備不斷收集有關本地環境的資訊。  評估是本地 VM 的時間點快照。 如果更改要評估的 VM 上的設置，請使用重新計算選項使用最新更改更新評估。

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>如何在多租戶環境中發現 VM？

- **VMware**：如果環境在租戶之間共用，並且您不希望在另一個租戶的訂閱中發現租戶的 VM，請創建 VMware vCenter Server 憑據，該憑據只能訪問要發現的 VM。 然後，在 Azure 遷移設備中開始發現時使用這些憑據。
- **Hyper-V**： 發現使用超 V 主機憑據。 如果 VM 共用同一個 Hyper-V 主機，則當前無法分離發現。  

## <a name="do-i-need-vcenter-server"></a>我需要 vCenter 伺服器嗎？

是的，Azure 遷移需要 VMware 環境中的 vCenter 伺服器來執行發現。 Azure 遷移不支援發現 vCenter 伺服器未管理的 ESXi 主機。

## <a name="what-are-the-sizing-options"></a>大小調整選項是什麼？

使用本地大小調整時，Azure 遷移不考慮 VM 效能資料進行評估。 Azure 遷移根據本地配置評估 VM 大小。 使用基於性能的尺寸調整，大小調整基於利用率資料。

例如，如果本地 VM 具有四個內核和 8 GB 記憶體，CPU 利用率為 50%，記憶體利用率為 50%：
- 作為本地大小調整將建議具有四個內核和 8 GB 記憶體的 Azure VM SKU。
- 基於性能的尺寸調整將建議具有兩個內核和 4 GB 記憶體的 VM SKU，因為考慮了利用率百分比。

同樣，磁片大小調整取決於大小調整條件和存儲類型：
- 如果大小調整條件基於性能，並且存儲類型是自動的，則 Azure 遷移在標識目標磁片類型（標準磁片或高級磁片）時，會考慮磁片的 IOPS 和輸送量值。
- 如果大小調整條件基於性能，並且存儲類型為高級，則 Azure 遷移建議根據本地磁片的大小使用高級磁片 SKU。 當大小調整為本地大小調整且存儲類型為"標準"或"高級"時，相同的邏輯應用於磁片大小調整。

## <a name="does-performance-history-and-utilization-affect-sizing"></a>性能歷史記錄和利用率是否影響大小調整？

是，性能歷史記錄和利用率會影響 Azure 遷移中大小調整。

### <a name="performance-history"></a>效能歷程記錄

對於僅針對基於性能的大小調整，Azure 遷移會收集本地電腦的性能歷史記錄，然後使用它在 Azure 中推薦 VM 大小和磁片類型：

1. 設備持續對本地環境進行設定檔，以便每 20 秒收集一次即時利用率資料。
1. 產品將收集的 20 秒樣本匯總一次，並使用它們每 15 分鐘創建一個資料點。
1. 要創建資料點，設備將從所有 20 秒的樣本中選擇峰值。
1. 設備將資料點發送到 Azure。

### <a name="utilization"></a>利用

在 Azure 中創建評估時，根據性能持續時間和設置的性能歷史記錄百分位數值，Azure 遷移將計算有效利用率值，然後使用它進行大小調整。

例如，如果將性能持續時間設置為一天，百分位數值設置為第 95 百分位，Azure 遷移會按昇冪對收集器過去一天發送的 15 分鐘採樣點進行排序。 它選擇第 95 個百分位值作為有效利用。

使用第 95 個百分位值可確保忽略異常值。 如果 Azure 遷移使用第 99 個百分位數，則可能包含異常值。 要選擇期間的峰值使用值而不錯過任何異常值，將 Azure 遷移設置為使用第 99 個百分位數。

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>基於導入的評估與使用發現源作為設備的評估有什麼不同？

基於導入的評估是使用 CSV 檔導入 Azure 遷移的電腦創建的評估。 導入只需四個欄位：伺服器名稱、內核、記憶體和作業系統。 以下是需要注意的一些事項： 
 - 在基於導入的對引導類型參數的評估中，就緒條件不太嚴格。 如果未提供引導類型，則假定電腦具有 BIOS 啟動類型，並且電腦未標記為 **"有條件就緒**"。 在使用發現源作為設備進行的評估中，如果缺少啟動類型，就緒狀態將標記為 **"有條件就緒**"。 此就緒性計算差異是因為，在基於導入的評估完成時，使用者可能沒有有關遷移規劃早期階段電腦的所有資訊。 
 - 基於性能的導入評估使用使用者提供的利用率值進行正確的大小調整計算。 由於利用率值由使用者提供，因此在評估屬性中禁用**性能歷史記錄**和**百分位數利用率**選項。 在使用發現源作為設備進行評估時，所選百分位數值將從設備收集的效能資料中選取。

## <a name="what-is-dependency-visualization"></a>什麼是相依性視覺效果？

依賴項視覺化可説明您更自信地評估要遷移的 VM 組。 依賴項視覺化在運行評估之前交叉檢查電腦依賴項。 它有助於確保不會留下任何內容，並且有助於避免遷移到 Azure 時意外中斷。 Azure Migrate 使用了 Azure 監視器中的 [服務對應] 解決方案來啟用相依性視覺效果。 [深入了解](concepts-dependency-visualization.md)。

> [!NOTE]
> 依賴項視覺化在 Azure 政府中不可用。

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>基於代理和無代理之間的區別是什麼？

表中總結了無代理視覺化和基於代理的視覺化之間的區別。

**要求** | **無代理程式** | **基於代理**
--- | --- | ---
支援 | 此選項當前處於預覽狀態，僅適用于 VMware VM。 [查看](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)支援的作業系統。 | 在一般可用性 （GA） 中。
代理程式 | 無需在要交叉檢查的電腦上安裝代理。 | 要安裝在要分析的每個本地電腦上的代理[：Microsoft 監視代理 （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)和[依賴項代理](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
Prerequisites | [查看](concepts-dependency-visualization.md#agentless-analysis)先決條件和部署要求。 | [查看](concepts-dependency-visualization.md#agent-based-analysis)先決條件和部署要求。
Log Analytics | 不需要。 | Azure 遷移使用[Azure 監視器日誌](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服務映射](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解決方案進行依賴項視覺化。 [深入了解](concepts-dependency-visualization.md#agent-based-analysis)。
運作方式 | 在啟用依賴項視覺化的電腦上捕獲 TCP 連接資料。 發現後，它每隔五分鐘收集資料。 | 安裝在電腦上的服務映射代理收集有關每個進程的 TCP 進程和入站/出站連接的資料。
資料 | 源電腦伺服器名稱、進程、應用程式名稱。<br/><br/> 目的電腦伺服器名稱、進程、應用程式名稱和埠。 | 源電腦伺服器名稱、進程、應用程式名稱。<br/><br/> 目的電腦伺服器名稱、進程、應用程式名稱和埠。<br/><br/> 收集連接數、延遲和資料傳輸資訊，並可用於日誌分析查詢。 
視覺效果 | 單個伺服器的依賴項映射可在 1 小時到 30 天的持續時間內查看。 | 單個伺服器的依賴項映射。<br/><br/> 地圖只能查看一個多小時。<br/><br/> 一組伺服器的依賴項映射。<br/><br/> 從地圖視圖添加和刪除組中的伺服器。
資料匯出 | 當前無法以表格格式下載。 | 資料可以通過日誌分析進行查詢。

## <a name="do-i-pay-for-dependency-visualization"></a>我為依賴項視覺化付費嗎？

否。 瞭解有關[Azure 遷移定價](https://azure.microsoft.com/pricing/details/azure-migrate/)的更多詳細資訊。

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>對於基於代理的依賴項視覺化，我應安裝什麼？

要使用基於代理的依賴項視覺化，請在要評估的每個本地電腦上下載和安裝代理：

- [微軟監控代理（MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [相依性代理程式](../azure-monitor/platform/agents-overview.md#dependency-agent)
- 如果您有沒有互聯網連接的電腦，請下載並安裝日誌分析閘道。

僅當使用基於代理的依賴項視覺化效果時，才需要這些代理。

## <a name="can-i-use-an-existing-workspace"></a>我可以使用現有的工作區嗎？

是的，對於基於代理的依賴項視覺化，您可以將現有工作區附加到遷移專案，並將其用於依賴項視覺化。 

## <a name="can-i-export-the-dependency-visualization-report"></a>是否可以匯出相依性視覺效果報告？

否，無法匯出基於代理的視覺化中的依賴項視覺化報表。 但是，Azure 遷移使用服務映射，您可以使用[服務映射 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)以 JSON 格式檢索依賴項。

## <a name="can-i-automate-agent-installation"></a>我可以自動安裝代理嗎？

對於基於代理的依賴項視覺化：

- 使用[腳本安裝依賴項代理](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)。
- 對於 MMA，[請使用命令列或自動化](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)，或使用[腳本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。
- 除了腳本之外，您還可以使用部署工具（如 Microsoft 終結點組態管理員和[Intigua）](https://www.intigua.com/getting-started-intigua-for-azure-migration)來部署代理。

## <a name="what-operating-systems-does-mma-support"></a>MMA 支援哪些作業系統？

- 查看 MMA[支援的 Windows 作業系統](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)的清單。
- 查看 MMA[支援的 Linux 作業系統](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)的清單。

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>我可以視覺化依賴項超過一小時嗎？

對於基於代理的視覺化，您可以視覺化依賴項長達一個小時。 您可以追溯到歷史記錄中的特定日期長達一個月，但視覺化的最大持續時間為 1 小時。 例如，可以使用依賴項映射中的工期來查看昨天的依賴項，但只能查看一小時視窗的依賴項。 但是，可以使用 Azure 監視器日誌[查詢依賴項資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)的時間較長。

對於無代理視覺化，您可以查看單個伺服器的依賴項映射，持續時間為 1 小時到 30 天。

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>是否可以視覺化超過 10 個 VM 的組的依賴項？

您可以視覺化最多具有 10 個 VM 的組[的依賴項](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果組具有 10 個以上 VM，我們建議您將組拆分為較小的組，然後視覺化依賴項。

## <a name="next-steps"></a>後續步驟

閱讀[Azure 遷移概述](migrate-services-overview.md)。
