---
title: Azure Data Lake Storage 查詢加速（預覽）
description: 查詢加速（預覽）是 Azure Data Lake Storage 的新功能，可讓應用程式和分析架構藉由僅抓取處理作業所需的資料，大幅優化資料處理。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 42eec4a0796a7f07c7e7d1c35571d9d4ddcf69d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82176697"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Azure Data Lake Storage 查詢加速（預覽）

查詢加速（預覽）是 Azure Data Lake Storage 的新功能，可讓應用程式和分析架構藉由僅抓取執行給定作業所需的資料，大幅優化資料處理。 這可減少取得所儲存資料的重要深入解析所需的時間和處理能力。

> [!NOTE]
> 查詢加速功能處於公開預覽狀態，並可在加拿大中部和法國中部區域中取得。 若要查看限制，請參閱[已知問題](data-lake-storage-known-issues.md)一文。 若要註冊預覽，請參閱[此表單](https://aka.ms/adls/qa-preview-signup)。  

## <a name="overview"></a>概觀

查詢加速接受篩選述*詞和資料*行*預測*，讓應用程式可以在從磁片讀取資料時篩選資料列和資料行。 只有符合述詞條件的資料會透過網路傳送至應用程式。 這可減少網路延遲和計算成本。  

您可以使用 SQL 來指定查詢加速要求中的資料列篩選述詞和資料行投影。 要求只會處理一個檔案。 因此，不支援 SQL 的先進關聯式功能，例如聯結和分組依據匯總。 查詢加速支援 CSV 和 JSON 格式的資料做為每個要求的輸入。

查詢加速功能不限於 Data Lake Storage （已啟用階層命名空間的儲存體帳戶）。 查詢加速與儲存體帳戶中**未**啟用階層命名空間的 blob 完全相容。 這表示當您在儲存體帳戶中處理已儲存為 blob 的資料時，可以達到相同的網路延遲和計算成本。

如需如何在用戶端應用程式中使用查詢加速的範例，請參閱[使用 Azure Data Lake Storage 查詢加速來篩選資料](data-lake-storage-query-acceleration-how-to.md)。

## <a name="data-flow"></a>資料流程

下圖說明一般應用程式如何使用查詢加速來處理資料。

> [!div class="mx-imgBorder"]
> ![查詢加速總覽](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. 用戶端應用程式藉由指定述詞和資料行投影來要求檔案資料。

2. 查詢加速會剖析指定的 SQL 查詢，並散發用來剖析和篩選資料的工作。

3. 處理器會從磁片讀取資料、使用適當的格式剖析資料，然後藉由套用指定的述詞和資料行投影來篩選資料。

4. 查詢加速會將回應分區結合成串流回到用戶端應用程式。

5. 用戶端應用程式會接收並剖析資料流程處理的回應。 應用程式不需要篩選任何額外的資料，而且可以直接套用所需的計算或轉換。

## <a name="better-performance-at-a-lower-cost"></a>較低的成本提供更佳的效能

查詢加速藉由減少應用程式所傳輸及處理的資料量來優化效能。

若要計算匯總值，應用程式通常會從檔案中取出**所有**資料，然後在本機處理和篩選資料。 分析工作負載的輸入/輸出模式會顯示應用程式通常只需要20% 的資料來執行任何指定的計算。 即使在套用資料[分割剪除](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning)之類的技術之後，此統計資料也是如此。 這表示該資料的80% 不必要地透過網路傳送、剖析及篩選應用程式。 此模式基本上是設計用來移除不必要的資料，會產生大量的計算成本。  

雖然 Azure 的特色是領先業界的網路，但在輸送量和延遲方面，不必要地在該網路上傳輸資料，對於應用程式效能仍是昂貴的。 藉由在儲存體要求期間篩選掉不必要的資料，查詢加速可免除這項成本。

此外，剖析和篩選不必要的資料所需的 CPU 負載，必須讓您的應用程式布建更多數目和較大的 Vm，才能執行此作業。 藉由將此計算負載轉移至查詢加速，應用程式可以實現大幅的成本節約。

## <a name="applications-that-can-benefit-from-query-acceleration"></a>可受益于查詢加速的應用程式

查詢加速是針對分散式分析架構和資料處理應用程式所設計。 

分散式分析架構（例如 Apache Spark 和 Apache Hive）在架構中包含了儲存體抽象層。 這些引擎也包括查詢優化工具，可以在決定使用者查詢的最佳查詢計劃時，納入基礎 i/o 服務功能的知識。 這些架構正開始整合查詢加速。 因此，這些架構的使用者會看到改良的查詢延遲和較低的擁有權總成本，而不需要對查詢進行任何變更。 

查詢加速也是針對資料處理應用程式所設計。 這些類型的應用程式通常會執行大規模的資料轉換，而不會直接導致分析深入解析，因此它們不一定會使用已建立的分散式分析架構。 這些應用程式通常與基礎儲存體服務具有更直接的關聯性，因此可以直接從查詢加速之類的功能獲益。 

如需應用程式如何整合查詢加速的範例，請參閱[使用 Azure Data Lake Storage 查詢加速來篩選資料](data-lake-storage-query-acceleration-how-to.md)。

## <a name="pricing"></a>定價

由於 Azure Data Lake Storage 服務內的計算負載增加，使用查詢加速的計價模式與一般的 Azure Data Lake Storage 交易模型不同。 查詢加速會收取掃描資料量的成本，以及傳回給呼叫者的資料量成本。 如需詳細資訊，請參閱[Azure Data Lake Storage Gen2 定價](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

雖然計費模型的變更，但查詢加速的計價模式是為了降低工作負載的擁有權總成本而設計，因為降低了成本更高的 VM 成本。

## <a name="next-steps"></a>後續步驟

- [查詢加速註冊表單](https://aka.ms/adls/qa-preview-signup)    
- [使用 Azure Data Lake Storage 查詢加速來篩選資料（預覽）](data-lake-storage-query-acceleration-how-to.md)
- [查詢加速 SQL 語言參考（預覽）](query-acceleration-sql-reference.md)


