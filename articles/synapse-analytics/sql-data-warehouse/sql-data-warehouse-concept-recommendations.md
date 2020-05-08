---
title: Synapse SQL 建議
description: 瞭解 Synapse SQL 建議及其產生方式
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 546945d70554adbb28f19a3153faa67495e55f04
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607739"
---
# <a name="synapse-sql-recommendations"></a>Synapse SQL 建議

本文說明透過 Azure Advisor 提供的 Synapse SQL 建議。  

SQL 分析會提供建議，以確保您的資料倉儲工作負載一致地針對效能進行優化。 建議與[Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)緊密整合，以直接在[Azure 入口網站](https://aka.ms/Azureadvisor)內提供最佳作法。 SQL 分析會針對您的作用中工作負載，以每日步調收集遙測和呈現建議。 以下概述支援的建議案例，以及如何套用建議的動作。

您今天可以[檢查您的建議](https://aka.ms/Azureadvisor)！ 

## <a name="data-skew"></a>資料扭曲

當執行工作負載時，資料扭曲可能會造成額外的資料移動或資源瓶頸。 下列文件說明如何識別資料扭曲，並透過選取最佳的散發索引鍵來防止發生資料扭曲。

- [識別並移除扭曲](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>沒有或過期的統計資料

具有較佳的統計資料可能會嚴重影響查詢效能，因為它會導致 SQL 查詢最佳化工具產生次佳的查詢計劃。 下列文件說明建立及更新統計資料的最佳做法：

- [建立及更新資料表統計資料](sql-data-warehouse-tables-statistics.md)

若要依照這些建議查看受影響的資料表清單，請執行下列[t-sql 腳本](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)。 Advisor 會持續執行相同的 T-SQL 指令碼，以產生這些建議。

## <a name="replicate-tables"></a>複寫資料表

針對複寫的資料表建議，Advisor 會根據下列實體特性偵測資料表候選項目：

- 複寫的資料表大小
- Number of columns
- 資料表散發類型
- 資料分割數目

Advisor 會持續運用工作負載型啟發學習法 (例如資料表存取頻率、平均傳回的資料列數，以及與資料倉儲大小和活動相關的閾值) 來確保產生高品質的建議。

下一節說明您可以在每個複寫資料表建議的 Azure 入口網站中找到的工作負載型啟發學習法：

- 掃描平均 - 過去七天內，針對每個資料表存取，從資料表傳回的資料列平均百分比
- 經常讀取、無更新 - 指出資料表在過去七天有顯示存取活動但沒有更新
- 讀取/更新比例 - 過去七天內，資料表的存取頻率相對於其更新時間的比例
- 活動 - 根據存取活動評估使用狀況。 在過去七天內，此活動會比較資料倉儲上與平均資料表存取活動相關的資料表存取活動。

目前 Advisor 一次最多只會顯示四個複寫的資料表候選項目，並以叢集資料行存放區索引排列最高活動優先順序。

> [!IMPORTANT]
> 複寫的資料表建議並非無懈可擊的，而且未將資料移動作業納入考量。 我們正著手將其新增為啟發學習法，但在此同時，您應該在套用建議之後，一律驗證您的工作負載。 若要深入了解複寫的資料表，請瀏覽下列[文件](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)。


## <a name="adaptive-gen2-cache-utilization"></a>調適型（Gen2）快取使用率
當您有很大的工作集時，您可能會遇到快取命中百分比和快取記憶體使用率偏低的情況。 在此案例中，您應該相應增加，以提高快取容量並重新執行您的工作負載。 如需詳細資訊，請流覽下列[檔](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache)。 

## <a name="tempdb-contention"></a>Tempdb 爭用

當 tempdb 爭用很高時，查詢效能可能會降低。  Tempdb 爭用可能會透過使用者定義的臨時表，或在大量的資料移動時發生。 針對此案例，您可以調整以進行更多 tempdb[配置，並設定資源類別和工作負載管理](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)，以提供更多記憶體給您的查詢。 
