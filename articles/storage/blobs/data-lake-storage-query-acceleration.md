---
title: Azure Data Lake Storage 查詢加速
description: 查詢加速可讓應用程式和分析架構藉由只抓取處理作業所需的資料，大幅優化資料處理。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 09/09/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: ae92828f08ae4abf9cc28f18872cca27ce747be4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657643"
---
# <a name="azure-data-lake-storage-query-acceleration"></a>Azure Data Lake Storage 查詢加速

查詢加速可讓應用程式和分析架構藉由只抓取執行指定作業所需的資料，來大幅優化資料處理。 這樣可減少對儲存資料進行重要深入解析所需的時間和處理能力。

## <a name="overview"></a>概觀

查詢加速接受篩選述 *詞和資料* 行 *投射* ，可讓應用程式在從磁片讀取資料時，篩選資料列和資料行。 只有符合述詞條件的資料會透過網路傳送至應用程式。 這可減少網路延遲和計算成本。  

您可以使用 SQL 來指定查詢加速要求中的資料列篩選述詞和資料行預測。 要求只會處理一個檔案。 因此，不支援 SQL 的 advanced 關聯式功能，例如聯結和依匯總分組。 查詢加速支援 CSV 和 JSON 格式的資料，做為每個要求的輸入。

查詢加速功能不限於 Data Lake Storage () 上啟用階層命名空間的儲存體帳戶。 查詢加速 **與未在** 其上啟用階層命名空間的儲存體帳戶中的 blob 完全相容。 這表示當您處理已儲存為儲存體帳戶中 blob 的資料時，可以達到相同的網路延遲和計算成本降低。

如需如何在用戶端應用程式中使用查詢加速的範例，請參閱 [使用 Azure Data Lake Storage 查詢加速來篩選資料](data-lake-storage-query-acceleration-how-to.md)。

## <a name="data-flow"></a>資料流程

下圖說明一般應用程式如何使用查詢加速來處理資料。

> [!div class="mx-imgBorder"]
> ![查詢加速總覽](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. 用戶端應用程式藉由指定述詞和資料行投影來要求檔案資料。

2. 查詢加速會剖析指定的 SQL 查詢，並散發工作來剖析和篩選資料。

3. 處理器會從磁片讀取資料、使用適當的格式來剖析資料，然後套用指定的述詞和資料行投影來篩選資料。

4. 查詢加速會將回應分區合併回用戶端應用程式。

5. 用戶端應用程式會接收並剖析資料流程回應。 應用程式不需要篩選任何額外的資料，也可以直接套用所需的計算或轉換。

## <a name="better-performance-at-a-lower-cost"></a>以較低的成本提供更佳的效能

查詢加速藉由減少應用程式所傳輸及處理的資料量來優化效能。

若要計算匯總值，應用程式通常會從檔案中取出 **所有** 資料，然後在本機處理和篩選資料。 分析工作負載的輸入/輸出模式分析顯示，應用程式通常只需要其所讀取的20% 資料來執行任何指定的計算。 即使在套用資料 [分割剪除](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning)之類的技術之後，此統計資料也是如此。 這表示，80% 的資料會不必要地透過網路傳輸、經過剖析，並依應用程式進行篩選。 此模式基本上是設計來移除不必要的資料，會產生大量的計算成本。  

雖然 Azure 具有領先業界的網路，但在輸送量和延遲方面，不必要地跨該網路傳輸資料仍是應用程式效能的代價。 藉由在儲存體要求期間篩選出不必要的資料，查詢加速會消除這項成本。

此外，剖析及篩選不必要資料所需的 CPU 負載，需要您的應用程式布建更多數量及更大的 Vm，才能執行其工作。 藉由將此計算負載轉移至查詢加速，應用程式可以大幅節省成本。

## <a name="applications-that-can-benefit-from-query-acceleration"></a>可從查詢加速獲益的應用程式

查詢加速是針對分散式分析架構和資料處理應用程式所設計。 

分散式分析架構（例如 Apache Spark 和 Apache Hive）包含架構內的儲存體抽象層。 這些引擎也包含查詢優化工具，可在決定使用者查詢的最佳查詢計劃時，納入基礎 i/o 服務功能的知識。 這些架構開始整合查詢加速。 如此一來，這些架構的使用者就會看到改善的查詢延遲，而且擁有較低的擁有權總成本，而不需要對查詢進行任何變更。 

查詢加速也是針對資料處理應用程式所設計。 這些類型的應用程式通常會執行大規模的資料轉換，而不會直接導致分析見解，因此它們不一定會使用已建立的分散式分析架構。 這些應用程式通常與基礎儲存體服務有更直接的關聯性，因此可以直接受益于像是查詢加速的功能。 

如需應用程式如何整合查詢加速的範例，請參閱 [使用 Azure Data Lake Storage 查詢加速來篩選資料](data-lake-storage-query-acceleration-how-to.md)。

## <a name="pricing"></a>定價

由於 Azure Data Lake Storage 服務內的計算負載增加，使用查詢加速的計價模式與一般 Azure Data Lake Storage 交易模型不同。 查詢加速會收取掃描的資料量，以及傳回給呼叫端的資料量成本。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 定價](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

儘管計費模型的變更，查詢加速的定價模式的設計是為了降低工作負載的擁有權總成本，並減少成本較高的 VM 成本。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Lake Storage 查詢加速來篩選資料](data-lake-storage-query-acceleration-how-to.md)
- [查詢加速 SQL 語言參考](query-acceleration-sql-reference.md)


