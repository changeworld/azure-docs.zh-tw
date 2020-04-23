---
title: Azure 移植伺服器來依分析的相依項分析
description: 介紹如何使用 Azure 遷移伺服器評估使用依賴項分析進行評估。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024756"
---
# <a name="dependency-analysis"></a>相依性分析

本文介紹 Azure 遷移:伺服器評估中的依賴項分析。

## <a name="overview"></a>概觀

依賴項分析可説明您確定要評估和遷移到 Azure 的本地電腦之間的依賴關係。 

- 在 Azure 遷移:伺服器評估中,將計算機收集到組中,然後評估該組。 依賴關係分析可説明您更準確地對計算機進行分組,並高度自信地進行評估。
- 依賴項分析使您能夠識別必須一起遷移的計算機。 您可以識別電腦是否正在使用中,或者是否可以停用而不是遷移它們。
- 分析依賴項有助於確保不會留下任何內容,並避免在遷移期間出現意外中斷。
- 如果您不確定電腦是否是要遷移到 Azure 的應用部署的一部分,則分析特別有用。
- [查看](common-questions-discovery-assessment.md#what-is-dependency-visualization)有關依賴項分析的常見問題。

有兩種用於部署相依項分析的選項

- **基於代理**:基於代理的依賴項分析要求在要分析的每個本地電腦上安裝代理。
- **無代理**:使用無代理分析,您無需在要交叉檢查的電腦上安裝代理。 此選項目前處於預覽狀態,僅適用於 VMware VM。

> [!NOTE]
> Azure 政府中不提供基於代理的依賴項分析。 您可以使用無代理依賴項分析。

## <a name="agentless-analysis"></a>沒有代理分析

無代理依賴項分析的工作原理是從啟用 TCP 連接數據的電腦上捕獲 TCP 連接數據。 要分析的電腦上未安裝代理。

### <a name="collected-data"></a>收集的資料

依賴項發現開始后,設備每隔五分鐘輪詢來自計算機的數據以收集數據。 這些數據通過 vCenter 伺服器通過 vSphere API 從來賓 VM 中收集。 收集的數據在 Azure 遷移設備上處理,以推斷標識資訊,並每六小時發送到 Azure 遷移。

輪詢從電腦收集此資料: 
- 具有活動連接的進程的名稱。
- 運行具有活動連接的程序的應用程式的名稱。
- 活動連接上的目標埠。

## <a name="agent-based-analysis"></a>代理伺服器分析

對於基於代理的分析,伺服器評估使用 Azure 監視器中的[服務映射解決方案](../azure-monitor/insights/service-map.md)來啟用依賴項可視化和分析。 [Microsoft 監視代理/日誌分析代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)和[依賴項代理](../azure-monitor/platform/agents-overview.md#dependency-agent)必須安裝在要分析的每台電腦上。

### <a name="collected-data"></a>收集的資料

對於基於代理的分析,將收集以下數據:

- 源電腦伺服器名稱、程序、應用程式名稱。
- 目標計算機伺服器名稱、程序、應用程式名稱和埠。
- 收集連接數、延遲和數據傳輸資訊,並可用於日誌分析查詢。 


## <a name="compare-agentless-and-agent-based"></a>比較無代理與代理伺服器

表中總結了無代理可視化和基於代理的可視化之間的區別。

**需求** | **無代理程式** | **基於代理**
--- | --- | ---
支援 | 此選項目前處於預覽狀態,僅適用於 VMware VM。 [查看](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)支援的作業系統。 | 在一般可用性 (GA) 中。
代理程式 | 無需在要交叉檢查的電腦上安裝代理。 | 要安裝在要分析的每個本地電腦上的[代理伺服器 :Microsoft 監視代理 (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)與[相依項代理](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
Log Analytics | 不需要。 | Azure 遷移使用[Azure 監視器日誌](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服務映射](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解決方案進行分析。 
運作方式 | 在啟用依賴項可視化的電腦上捕獲 TCP 連接數據。 發現后,它每隔五分鐘收集數據。 | 安裝在電腦上的服務映射代理收集有關每個進程的 TCP 進程和入站/出站連接的數據。
資料 | 源電腦伺服器名稱、程序、應用程式名稱。<br/><br/> 目標計算機伺服器名稱、程序、應用程式名稱和埠。 | 源電腦伺服器名稱、程序、應用程式名稱。<br/><br/> 目標計算機伺服器名稱、程序、應用程式名稱和埠。<br/><br/> 收集連接數、延遲和數據傳輸資訊,並可用於日誌分析查詢。 
視覺效果 | 單個伺服器的依賴項映射可在 1 小時到 30 天的持續時間內查看。 | 單個伺服器的依賴項映射。<br/><br/> 地圖只能查看一個多小時。<br/><br/> 一組伺服器的依賴項映射。<br/><br/> 從地圖檢視添加和刪除組中的伺服器。
資料匯出 | 目前無法以表格格式下載。 | 數據可以通過日誌分析進行查詢。



## <a name="next-steps"></a>後續步驟
- 查看為[VMware VM、](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)[物理伺服器](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)和[超](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)VM 設置基於代理的分析的要求。
- [查看](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)VMware VM 無代理分析的要求。
- [設定](how-to-create-group-machine-dependencies.md)基於代理的依賴項視覺化
- [嘗試](how-to-create-group-machine-dependencies-agentless.md)VMware VM 的無代理依賴項可視化。
- 檢視有關相依項視覺化的[常見問題](common-questions-discovery-assessment.md#what-is-dependency-visualization)。


