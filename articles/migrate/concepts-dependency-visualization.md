---
title: Azure Migrate Server 評估中的相依性分析
description: 說明如何使用相依性分析進行評估，使用 Azure Migrate Server 評估。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024756"
---
# <a name="dependency-analysis"></a>相依性分析

本文描述 Azure Migrate 中的相依性分析：伺服器評估。

## <a name="overview"></a>總覽

相依性分析可協助您識別您想要評估並遷移至 Azure 的內部部署機器之間的相依性。 

- 在 Azure Migrate：伺服器評估中，您會將機器收集到群組中，然後評估該群組。 相依性分析可協助您更精確地將機器分組，並具有高信賴度的評量。
- 相依性分析可讓您識別必須一起遷移的機器。 您可以識別機器是否正在使用中，或者是否可以解除委任，而不是遷移。
- 分析相依性有助於確保不會留下任何內容，並避免在遷移期間發生意外的中斷。
- 如果您不確定機器是否屬於您想要遷移至 Azure 的應用程式部署，則分析特別有用。
- [查看](common-questions-discovery-assessment.md#what-is-dependency-visualization)相依性分析的相關常見問題。

有兩個選項可用於部署相依性分析

- 以代理程式為**基礎**：代理程式相依性分析需要在您要分析的每個內部部署機器上安裝代理程式。
- **無代理**程式：使用無代理程式分析，您不需要在要交叉檢查的電腦上安裝代理程式。 此選項目前為預覽狀態，且僅適用于 VMware Vm。

> [!NOTE]
> Azure Government 不提供以代理程式為基礎的相依性分析。 您可以使用無代理程式相依性分析。

## <a name="agentless-analysis"></a>無代理程式分析

無代理程式相依性分析的運作方式是從已啟用它的機器中，捕獲 TCP 連接資料。 您想要分析的機器上未安裝任何代理程式。

### <a name="collected-data"></a>收集的資料

相依性探索開始之後，設備會每隔五分鐘從機器輪詢資料，以收集資料。 此資料是使用 vSphere Api，透過 vCenter Server 從來賓 Vm 收集而來。 收集的資料會在 Azure Migrate 設備上處理，以推算身分識別資訊，並每隔六小時傳送至 Azure Migrate。

輪詢會從機器收集這種資料： 
- 具有使用中連接之進程的名稱。
- 執行具有使用中連接之進程的應用程式名稱。
- 作用中連接上的目的地埠。

## <a name="agent-based-analysis"></a>以代理程式為基礎的分析

針對以代理程式為基礎的分析，伺服器評估會使用 Azure 監視器中的[服務對應方案](../azure-monitor/insights/service-map.md)來啟用相依性視覺效果和分析。 [Microsoft Monitoring Agent/Log Analytics 代理程式](../azure-monitor/platform/agents-overview.md#log-analytics-agent)和相依性[代理程式](../azure-monitor/platform/agents-overview.md#dependency-agent)必須安裝在您想要分析的每部機器上。

### <a name="collected-data"></a>收集的資料

針對以代理程式為基礎的分析，會收集下列資料：

- 來源電腦伺服器名稱、進程、應用程式名稱。
- 目的地電腦伺服器名稱、進程、應用程式名稱和埠。
- 系統會收集連線、延遲和資料傳輸資訊的數目，並可供 Log Analytics 查詢使用。 


## <a name="compare-agentless-and-agent-based"></a>比較無代理程式和代理程式型

「無代理程式」視覺效果和以代理程式為基礎的視覺效果之間的差異摘要于表格中。

**需求** | **無代理程式** | **以代理程式為基礎**
--- | --- | ---
支援 | 此選項目前為預覽狀態，且僅適用于 VMware Vm。 [檢查](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)支援的作業系統。 | 正式運作（GA）。
代理程式 | 不需要在您要交叉檢查的電腦上安裝代理程式。 | 要在每個您想要分析的內部部署機器上安裝的代理[程式： Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)和[Dependency agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
Log Analytics | 不需要。 | Azure Migrate 使用[Azure 監視器記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服務對應](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解決方案進行相依性分析。 
運作方式 | 會在啟用相依性視覺效果的電腦上，捕獲 TCP 連線資料。 探索之後，它會以五分鐘的間隔收集資料。 | 安裝在電腦上的服務對應代理程式會收集有關 TCP 進程的資料，以及每個進程的輸入/輸出連接。
資料 | 來源電腦伺服器名稱、進程、應用程式名稱。<br/><br/> 目的地電腦伺服器名稱、進程、應用程式名稱和埠。 | 來源電腦伺服器名稱、進程、應用程式名稱。<br/><br/> 目的地電腦伺服器名稱、進程、應用程式名稱和埠。<br/><br/> 系統會收集連線、延遲和資料傳輸資訊的數目，並可供 Log Analytics 查詢使用。 
視覺效果 | 單一伺服器的相依性對應可以在一小時到30天的期間內查看。 | 單一伺服器的相依性對應。<br/><br/> 只能在一小時內查看對應。<br/><br/> 伺服器群組的相依性對應。<br/><br/> 從地圖視圖新增和移除群組中的伺服器。
資料匯出 | 目前無法以表格式格式下載。 | 您可以使用 Log Analytics 來查詢資料。



## <a name="next-steps"></a>後續步驟
- 針對[VMware vm](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)、[實體伺服器](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)和[hyper-v vm](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)，審查設定代理程式式分析的需求。
- 請[參閱](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)VMware vm 的無代理程式分析需求。
- [設定](how-to-create-group-machine-dependencies.md)以代理程式為基礎的相依性視覺效果
- [試用](how-to-create-group-machine-dependencies-agentless.md)VMware vm 的無代理程式相依性視覺效果。
- 查看關於相依性視覺效果的[常見問題](common-questions-discovery-assessment.md#what-is-dependency-visualization)。


