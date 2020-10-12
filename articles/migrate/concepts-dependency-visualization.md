---
title: Azure Migrate 伺服器評量中的相依性分析
description: 說明如何使用 Azure Migrate 伺服器評量的相依性分析進行評量。
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: a284d549f13595e0ce8a5d06cc017602e559b648
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530245"
---
# <a name="dependency-analysis"></a>相依性分析

本文說明 Azure Migrate：伺服器評量中的相依性分析。


相依性分析會識別探索到的內部部署機器之間的相依性。 它提供下列優點： 

- 您可以更有信心地將機器收集成群組以進行評量。
- 您可以識別必須一起遷移的電腦。 如果您不確定哪些電腦是您想要遷移至 Azure 的應用程式部署的一部分，這會特別有用。
- 您可以識別電腦是否正在使用中，以及哪些電腦可以解除委任而不是遷移。
- 分析相依性有助於確保不會留下任何東西，因此可避免在遷移後意外中斷。
- [查看](common-questions-discovery-assessment.md#what-is-dependency-visualization) 相依性分析的相關常見問題。


## <a name="analysis-types"></a>分析類型

有兩個選項可用於部署相依性分析

**選項** | **詳細資料** | **公用雲端** | **Azure Government**
----  |---- | ---- 
**無代理程式** | 使用 vSphere Api 輪詢 VMware Vm 的資料。<br/><br/> 您不需要在 Vm 上安裝代理程式。<br/><br/> 此選項目前僅供適用于 VMware Vm 的預覽。 | 支援。 | 支援。
**以代理程式為基礎的分析** | 使用 Azure 監視器中的 [服務對應解決方案](../azure-monitor/insights/service-map.md) ，以啟用相依性視覺效果和分析。<br/><br/> 您必須在您想要分析的每個內部部署機器上安裝代理程式。 | 支援 | 不支援。


## <a name="agentless-analysis"></a>無代理程式分析

無代理程式相依性分析的運作方式是從已啟用它的電腦中，捕捉 TCP 連接資料。 Vm 上未安裝任何代理程式。 具有相同來源伺服器和進程，以及目的地伺服器、進程和埠的連接，會以邏輯方式分組為相依性。 您可以在地圖視圖中將已捕捉的相依性資料視覺化，或將它匯出為 CSV。 您想要分析的機器上未安裝任何代理程式。

### <a name="dependency-data"></a>相依性資料

開始探索相依性資料之後，輪詢會開始：

- Azure Migrate 設備會每隔五分鐘從電腦輪詢 TCP 連線資料，以收集資料。
- 您可以使用 vSphere Api，透過 vCenter Server 從來賓 Vm 收集資料。
- 輪詢會收集下列資料：

    - 具有使用中連接的處理常式名稱。
    - 執行具有使用中連接之進程的應用程式名稱。
    - 使用中連接上的目的地埠。

- 收集的資料會在 Azure Migrate 設備上處理，以推算身分識別資訊，並每隔6小時傳送給 Azure Migrate


## <a name="agent-based-analysis"></a>以代理程式為基礎的分析

針對以代理程式為基礎的分析，伺服器評量會使用 Azure 監視器中的 [服務對應](../azure-monitor/insights/service-map.md) 解決方案。 您要在每個想要分析的機器上安裝 [Microsoft Monitoring Agent/Log Analytics 代理程式](../azure-monitor/platform/agents-overview.md#log-analytics-agent) 和 [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。

### <a name="dependency-data"></a>相依性資料

以代理程式為基礎的分析提供下列資料：

- 來源電腦伺服器名稱、進程、應用程式名稱。
- 目的地電腦伺服器名稱、進程、應用程式名稱和埠。
- 系統會收集並提供 Log Analytics 查詢的連線、延遲和資料傳輸資訊的數目。 



## <a name="compare-agentless-and-agent-based"></a>比較無代理程式和代理程式型

無代理程式視覺效果和以代理程式為基礎的視覺效果之間的差異摘要在表格中。

**需求** | **無代理程式** | **以代理程式為基礎**
--- | --- | ---
**支援** | 僅適用于 VMware Vm 的預覽版。 [檢查](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) 支援的作業系統。 | 公開上市 (GA) 。
**代理程式** | 您想要分析的機器上不需要代理程式。 | 每個您想要分析的內部部署電腦上都需要代理程式。
**Log Analytics** | 不需要。 | Azure Migrate 在[Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md)檔中使用[服務對應](../azure-monitor/insights/service-map.md)方案進行相依性分析。<br/><br/> 您可以將 Log Analytics 工作區與 Azure Migrate 專案產生關聯。 此工作區必須位於「美國東部」、「東南亞」或「西歐」區域。 此工作區必須位於[支援服務對應](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)的區域中。
**處理** | 捕捉 TCP 連接資料。 探索之後，會依五分鐘的間隔收集資料。 | 電腦上安裝的服務對應代理程式會收集 TCP 處理常式的相關資料，以及每個處理常式的輸入/輸出連接。
**Data** | 來源電腦伺服器名稱、進程、應用程式名稱。<br/><br/> 目的地電腦伺服器名稱、進程、應用程式名稱和埠。 | 來源電腦伺服器名稱、進程、應用程式名稱。<br/><br/> 目的地電腦伺服器名稱、進程、應用程式名稱和埠。<br/><br/> 系統會收集並提供 Log Analytics 查詢的連線、延遲和資料傳輸資訊的數目。 
**視覺效果** | 單一伺服器的相依性對應可在一小時到30天的期間內查看。 | 單一伺服器的相依性對應。<br/><br/> 伺服器群組的相依性對應。<br/><br/>  您只能在一小時內查看 Map。<br/><br/> 從地圖視圖新增和移除群組中的伺服器。
資料匯出 | 您可以使用 CSV 格式來下載過去30天的資料。 | 您可以使用 Log Analytics 來查詢資料。



## <a name="next-steps"></a>後續步驟

- [設定](how-to-create-group-machine-dependencies.md) 以代理程式為基礎的相依性視覺效果。
- [試用](how-to-create-group-machine-dependencies-agentless.md) VMware vm 的無代理程式相依性視覺效果。
- 查看相依性視覺效果的[相關常見問題。](common-questions-discovery-assessment.md#what-is-dependency-visualization)
