---
title: Synapse SQL 建議
description: 深入了解 Synapse SQL 建議與其產生方式
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e4564005e3b9cc9673cc20596d4114d102174b9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482848"
---
# <a name="synapse-sql-recommendations"></a>Synapse SQL 建議

此文章說明透過 Azure Advisor 所提供的 Synapse SQL 建議。  

Synapse SQL 提供的建議可確保為了效能持續最佳化資料倉儲工作負載。 建議與 [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 緊密整合，直接在 [Azure 入口網站](https://aka.ms/Azureadvisor)內為您提供最佳做法。 Synapse SQL 每天都會收集遙測資料，並針對您的作用中工作負載提出建議。 下面說明支援的建議情節以及如何套用建議的動作。

您可以[立即查看建議](https://aka.ms/Azureadvisor)！ 

## <a name="data-skew"></a>資料扭曲

當執行工作負載時，資料扭曲可能會造成額外的資料移動或資源瓶頸。 下列文件說明如何識別資料扭曲，並透過選取最佳的散發索引鍵來防止發生資料扭曲。

- [識別並移除扭曲](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>沒有統計資料或已過期

若只有次佳的統計資料，會造成 SQL 查詢最佳化工具產生次佳的查詢計劃，而嚴重影響查詢效能。 下列文件說明建立及更新統計資料的最佳做法：

- [建立及更新資料表統計資料](sql-data-warehouse-tables-statistics.md)

若要查看受這些建議影響的資料表清單，請執行下列 [T-SQL 指令碼](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)。 Advisor 會持續執行相同的 T-SQL 指令碼，以產生這些建議。

## <a name="replicate-tables"></a>複寫資料表

針對複寫的資料表建議，Advisor 會根據下列實體特性偵測資料表候選項目：

- 複寫的資料表大小
- Number of columns
- 資料表散發類型
- 資料分割數目

Advisor 會持續運用工作負載型啟發學習法 (例如資料表存取頻率、平均傳回的資料列數，以及與資料倉儲大小和活動相關的閾值) 來確保產生高品質的建議。

下一節說明針對每個複寫的資料表建議，您在 Azure 入口網站中可能找到的工作負載型啟發學習法：

- 掃描平均 - 過去七天內，針對每個資料表存取，從資料表傳回的資料列平均百分比
- 經常讀取、無更新 - 指出資料表在過去七天有顯示存取活動但沒有更新
- 讀取/更新比例 - 過去七天內，資料表的存取頻率相對於其更新時間的比例
- 活動 - 根據存取活動評估使用狀況。 此活動會將資料表存取活動與過去七天內整個資料倉儲的平均資料表存取活動做比較。

目前 Advisor 一次最多只會顯示四個複寫的資料表候選項目，並以叢集資料行存放區索引排列最高活動優先順序。

> [!IMPORTANT]
> 複寫的資料表建議並非無懈可擊的，而且未將資料移動作業納入考量。 我們正著手將其新增為啟發學習法，但在此同時，您應該在套用建議之後，一律驗證您的工作負載。 若要深入了解複寫的資料表，請瀏覽下列[文件](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)。


## <a name="adaptive-gen2-cache-utilization"></a>自適型 (Gen2) 快取使用率
當您有很大的工作集時，您可能會遇到快取命中百分比較低和快取使用率較高的情況。 在此情節中，您應該擴大以增加快取容量並重新執行您的工作負載。 如需詳細資訊，請瀏覽下列[文件](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache) \(部分機器翻譯\)。 

## <a name="tempdb-contention"></a>Tempdb 爭用

當 tempdb 爭用很高時，查詢效能可能會降低。  Tempdb 爭用可能會透過使用者定義的暫存資料表，或在大量的資料移動時發生。 在此情節中，您可以針對更多 tempdb 配置進行調整，並[設定資源類別和工作負載管理](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management) \(部分機器翻譯\)，為您的查詢提供更多記憶體。 

## <a name="data-loading-misconfiguration"></a>資料載入設定錯誤

您應該一律從與 SQL 集區相同的區域中的儲存體帳戶載入資料，以將延遲降至最低。 使用[COPY 語句來進行高輸送量資料的](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)內嵌，並將您的暫存檔案分割到您的儲存體帳戶中，以達到最大的輸送量。 如果您無法使用 COPY 語句，您可以使用 SqlBulkCopy API 或具有高批次大小的 bcp，以獲得更好的輸送量。 如需其他資料載入指引，請流覽下列[檔](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)。 
