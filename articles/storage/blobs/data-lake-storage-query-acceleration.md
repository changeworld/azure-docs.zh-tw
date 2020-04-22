---
title: Azure 資料湖儲存查詢加速(預覽)
description: 查詢加速(預覽)是 Azure 數據湖儲存的新功能,它使應用程式和分析框架能夠通過僅檢索處理操作所需的數據來顯著優化數據處理。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 191a3280075403c8c5b57c5ffca1c7707d1ddb11
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771816"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Azure 資料湖儲存查詢加速(預覽)

查詢加速(預覽)是 Azure 數據湖儲存的新功能,它使應用程式和分析框架能夠通過僅檢索執行給定操作所需的數據來顯著優化數據處理。 這減少了獲得對存儲數據的關鍵洞察所需的時間和處理能力。

> [!NOTE]
> 查詢加速功能處於公共預覽版中,並且在加拿大中部和法國中部區域可用。 要查看限制,請參閱[已知問題](data-lake-storage-known-issues.md)一文。 要在預覽版中註冊,請參閱[此表單](https://aka.ms/adls/qa-preview-signup)。  

## <a name="overview"></a>概觀

查詢加速度接受篩選*謂詞*和*列投影*,使應用程式能夠在從磁碟讀取數據時篩選行和列。 只有滿足謂詞條件的數據通過網路傳輸到應用程式。 這降低了網路延遲和計算成本。  

可以使用 SQL 在查詢加速請求中指定行篩選器謂詞和列投影。 請求只處理一個檔。 因此,不支援 SQL 的高級關係功能,如按聚合聯接和分組。 查詢加速支援 CSV 和 JSON 格式化的數據作為輸入每個請求。

查詢加速功能不僅限於數據湖存儲(在其中啟用了分層命名空間的存儲帳戶)。 查詢加速與存儲帳戶中的 Blob 完全相容,這些 blob**未**啟用分層命名空間。 這意味著,當您處理已存儲為存儲帳戶中的 blob 的數據時,可以同樣降低網路延遲和計算成本。

有關如何在用戶端應用程式中使用查詢加速的範例,請參閱[使用 Azure 資料湖儲存查詢加速篩選資料](data-lake-storage-query-acceleration-how-to.md)。

## <a name="data-flow"></a>設計師中

下圖說明了典型應用程式如何使用查詢加速來處理數據。

> [!div class="mx-imgBorder"]
> ![查詢加速概述](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. 用戶端應用程式通過指定謂詞和列投影來請求文件數據。

2. 查詢加速分析指定的 SQL 查詢,並分發用於分析和篩選數據的工作。

3. 處理器從磁碟讀取數據,使用適當的格式分析數據,然後通過應用指定的謂詞和列投影篩選數據。

4. 查詢加速將回應分片合併到用戶端應用程式。

5. 用戶端應用程式接收並分析流式回應。 應用程式不需要篩選任何其他數據,可以直接應用所需的計算或轉換。

## <a name="better-performance-at-a-lower-cost"></a>以更低的成本提高性能

查詢加速通過減少應用程式傳輸和處理的數據量來優化性能。

若要計算聚合值,應用程式通常從檔中檢索**所有數據**,然後在本地處理和篩選數據。 對分析工作負載的輸入/輸出模式的分析表明,應用程式通常只需要 20% 的數據,他們讀取來執行任何給定的計算。 即使應用[了分區修剪](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning)等技術,此統計資訊也是如此。 這意味著 80% 的數據通過網路不必要地傳輸、解析和篩選。 此模式(本質上是用於刪除不需要的數據)會產生巨大的計算成本。  

儘管 Azure 具有業界領先的網路,但在吞吐量和延遲方面,在網路中不必要地傳輸數據對於應用程式性能來說仍然成本高昂。 通過在存儲請求期間篩選出不需要的數據,查詢加速消除了此成本。

此外,分析和篩選不需要的數據所需的 CPU 負載需要應用程式預配更多數量和更大的 VM 才能完成工作。 通過將計算負載轉移到查詢加速,應用程式可以顯著節省成本。

## <a name="applications-that-can-benefit-from-query-acceleration"></a>受益於查詢加速的應用程式

查詢加速專為分散式分析框架和數據處理應用程序而設計。 

分散式分析框架(如 Apache Spark 和 Apache Hive)在框架中包括儲存抽象層。 這些引擎還包括查詢優化器,這些優化器可以在確定用戶查詢的最佳查詢計劃時整合基礎I/O服務功能的知識。 這些框架開始集成查詢加速。 因此,這些框架的使用者將看到查詢延遲的改善和較低的總擁有成本,而無需對查詢進行任何更改。 

查詢加速也專為數據處理應用程式而設計。 這些類型的應用程式通常執行大規模資料轉換,這些轉換可能不會直接導致分析見解,因此它們並不總是使用已建立的分散式分析框架。 這些應用程式通常與基礎存儲服務有更直接的關係,因此可以直接受益於查詢加速等功能。 

有關應用程式如何整合查詢加速的範例,請參考 Azure[資料的儲存查詢加速篩選資料](data-lake-storage-query-acceleration-how-to.md)。

## <a name="pricing"></a>定價

由於 Azure 數據湖儲存服務中的計算負載增加,使用查詢加速的定價模型不同於正常的 Azure 數據湖存儲事務模型。 查詢加速對掃描的數據量以及返回給調用方的數據量的成本收費。

儘管計費模型進行了更改,但查詢加速的定價模型旨在降低工作負載的總擁有成本,因為 VM 成本的成本更低。

## <a name="next-steps"></a>後續步驟

- [查詢加速註冊表單](https://aka.ms/adls/qa-preview-signup)    
- [使用 Azure 資料湖儲存查詢加速篩選資料](data-lake-storage-query-acceleration-how-to.md)
- [查詢加速 SQL 語言參考(預覽)](query-acceleration-sql-reference.md)
- 查詢加速 REST API 參考



