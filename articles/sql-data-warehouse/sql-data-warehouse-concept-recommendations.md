---
title: SQL 分析建議
description: 瞭解 SQL 分析建議及其產生方式
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 5471236c09737eeef2d4cb7542c245d3087e726c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195952"
---
# <a name="sql-analytics-recommendations"></a>SQL 分析建議

本文說明透過 Azure Advisor 提供的 SQL 分析建議。  

SQL 分析會提供建議，以確保您的資料倉儲工作負載一致地針對效能進行優化。 建議與[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations)緊密整合，以直接在[Azure 入口網站](https://aka.ms/Azureadvisor)內提供最佳作法。 SQL 分析會針對您的作用中工作負載，以每日步調收集遙測和呈現建議。 以下概述支援的建議案例，以及如何套用建議的動作。

您今天可以[檢查您的建議](https://aka.ms/Azureadvisor)！ 此功能目前僅適用於 Gen2 資料倉儲。 

## <a name="data-skew"></a>資料扭曲

當執行工作負載時，資料扭曲可能會造成額外的資料移動或資源瓶頸。 下列文件說明如何識別資料扭曲，並透過選取最佳的散發索引鍵來防止發生資料扭曲。

- [識別並移除扭曲](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>沒有或過期的統計資料

具有較佳的統計資料可能會嚴重影響查詢效能，因為它會導致 SQL 查詢最佳化工具產生次佳的查詢計劃。 下列文件說明建立及更新統計資料的最佳做法：

- [建立及更新資料表統計資料](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

若要查看受這些建議影響的資料表清單，請執行下列 [T-SQL 指令碼](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)。 Advisor 會持續執行相同的 T-SQL 指令碼，以產生這些建議。

## <a name="replicate-tables"></a>複寫資料表

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
