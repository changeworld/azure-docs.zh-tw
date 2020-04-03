---
title: 突觸 SQL 建議
description: 使用 Synapse SQL 建議與產生方式
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c4bbd98cc28b242be5310fab76521a80de8fcb7c
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584115"
---
# <a name="synapse-sql-recommendations"></a>突觸 SQL 建議

本文介紹了通過 Azure Advisor 提供的 Synapse SQL 建議。  

Synapse SQL 提供建議,以確保數據倉庫工作負載始終針對性能進行優化。 建議與 Azure [Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations)緊密整合,直接在[Azure 門戶](https://aka.ms/Azureadvisor)中為您提供最佳做法。 Synapse SQL 以每日節奏收集活動工作負載的遙測和曲面建議。 下面概述了支持的建議方案以及如何應用建議的操作。

您可以[檢視您的建議](https://aka.ms/Azureadvisor)。 此功能目前僅適用於 Gen2 資料倉儲。 

## <a name="data-skew"></a>資料位斜

當執行工作負載時，資料扭曲可能會造成額外的資料移動或資源瓶頸。 下列文件說明如何識別資料扭曲，並透過選取最佳的散發索引鍵來防止發生資料扭曲。

- [識別並移除扭曲](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>無或過時的統計資訊

具有不理想的統計資訊會嚴重影響查詢性能,因為它可能導致 SQL 查詢優化器生成不理想的查詢計劃。 下列文件說明建立及更新統計資料的最佳做法：

- [建立及更新資料表統計資料](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

要檢視這些建議影響表的清單,執行以下[T-SQL 文稿](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)。 Advisor 會持續執行相同的 T-SQL 指令碼，以產生這些建議。

## <a name="replicate-tables"></a>複製表

針對複寫的資料表建議，Advisor 會根據下列實體特性偵測資料表候選項目：

- 複寫的資料表大小
- Number of columns
- 資料表散發類型
- 資料分割數目

Advisor 會持續運用工作負載型啟發學習法 (例如資料表存取頻率、平均傳回的資料列數，以及與資料倉儲大小和活動相關的閾值) 來確保產生高品質的建議。 

以下說明針對每個複寫的資料表建議，您在 Azure 入口網站中可能找到的工作負載型啟發學習法：

- 掃描平均 - 過去七天內，針對每個資料表存取，從資料表傳回的資料列平均百分比
- 經常讀取、無更新 - 指出資料表在過去七天有顯示存取活動但沒有更新
- 讀取/更新比例 - 過去七天內，資料表的存取頻率相對於其更新時間的比例
- 活動 - 根據存取活動評估使用狀況。 這會將資料表存取活動與過去七天內整個資料倉儲的平均資料表存取活動做比較。 

目前 Advisor 一次最多只會顯示四個複寫的資料表候選項目，並以叢集資料行存放區索引排列最高活動優先順序。

> [!IMPORTANT]
> 複寫的資料表建議並非無懈可擊的，而且未將資料移動作業納入考量。 我們正著手將其新增為啟發學習法，但在此同時，您應該在套用建議之後，一律驗證您的工作負載。 如果您發現造成您工作負載效能變差的複寫資料表建議，請與 sqldwadvisor@service.microsoft.com 連絡。 若要深入了解複寫的資料表，請瀏覽下列[文件](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table)。
