---
title: Azure Synapse 分析中的 SQL 隨選（預覽）最佳做法
description: 當您使用隨選 SQL （預覽）時，您應該知道的建議和最佳作法。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429066"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse 分析中的 SQL 隨選（預覽）最佳做法

在本文中，您會找到使用隨選 SQL （預覽）的最佳作法集合。 SQL 隨選是 Azure Synapse 分析中的額外資源。

## <a name="general-considerations"></a>一般考量

[隨選 SQL] 可讓您查詢 Azure 儲存體帳戶中的檔案。 它沒有本機儲存體或內嵌功能。 因此，查詢目標的所有檔案都是 SQL 隨選外部。 與從儲存體讀取檔案相關的所有內容，可能會影響查詢效能。

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>共置 Azure 儲存體帳戶和隨選 SQL

若要將延遲降至最低，請共置您的 Azure 儲存體帳戶和您的 SQL 隨選端點。 在工作區建立期間布建的儲存體帳戶和端點都位於相同的區域。

為了達到最佳效能，如果您使用 SQL 隨選存取其他儲存體帳戶，請確定它們位於相同的區域中。 如果它們不在相同的區域中，則遠端和端點區域之間的資料網路傳輸將會增加延遲。

## <a name="azure-storage-throttling"></a>Azure 儲存體節流

多個應用程式和服務可能會存取您的儲存體帳戶。 當應用程式、服務和 SQL 隨選工作負載所產生的結合 IOPS 或輸送量超過儲存體帳戶的限制時，就會發生儲存節流。 如此一來，您將會對查詢效能造成嚴重的負面影響。

一旦偵測到節流，SQL 隨選安裝就會有此案例的內建處理。 SQL 隨選會以較慢的步調對儲存體提出要求，直到節流解決為止。

> [!TIP]
> 若要執行最佳查詢，您不應該在查詢執行期間使用其他工作負載來壓力儲存體帳戶。

## <a name="prepare-files-for-querying"></a>準備檔案以進行查詢

可能的話，您可以準備檔案以獲得更佳的效能：

- 將 CSV 轉換成 Parquet-Parquet 是單欄式格式。 因為它已壓縮，所以其檔案大小會小於具有相同資料的 CSV 檔案。 SQL 隨選需要較少的時間和儲存體要求來讀取它。
- 如果查詢是以單一大型檔案為目標，您可以將其分割成多個較小的檔案，以獲得好處。
- 請嘗試將 CSV 檔案大小保留低於 10 GB。
- 最好是針對單一 OPENROWSET 路徑或外部資料表位置使用大小相同的檔案。
- 藉由將資料分割儲存至不同的資料夾或檔案名來分割您的資料-請核取 [[使用檔案名和 filepath 函數來鎖定特定](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)的分割區]。

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用 fileinfo 和 filepath 函式以特定的分割區為目標

資料通常會組織成分割區。 您可以指示 SQL 視需要查詢特定的資料夾和檔案。 此函式會減少查詢需要讀取和處理的檔案數目和資料量。 還有一個額外的好處，就是您可以獲得更好的效能。

如需詳細資訊，請參閱[檔案名](develop-storage-files-overview.md#filename-function)和[filepath](develop-storage-files-overview.md#filepath-function)函數和如何[查詢特定](query-specific-files.md)檔案的範例。

如果您儲存的資料未分割，請考慮將它分割，讓您可以使用這些函式來優化以這些檔案為目標的查詢。 從 SQL 隨選[查詢資料分割的 Spark 資料表](develop-storage-files-spark-tables.md)時，查詢只會自動鎖定所需的檔案。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 來增強查詢效能和聯結

[CETAS](develop-tables-cetas.md)是 SQL 隨選提供的其中一項最重要的功能。 CETAS 是一種平行作業，會建立外部資料表中繼資料，並將選取的查詢結果匯出至儲存體帳戶中的一組檔案。

您可以使用 CETAS，將經常使用的查詢部分（例如聯結的參考資料表）儲存到新的一組檔案。 接下來，您可以加入這個單一外部資料表，而不是在多個查詢中重複常見的聯結。

當 CETAS 產生 Parquet 檔案時，將會在第一次查詢以此外部資料表為目標時自動建立統計資料，進而提升效能。

## <a name="next-steps"></a>後續步驟

請參閱[疑難排解](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中的常見問題和解決方案。 如果您使用的是 SQL 集區，而不是隨選 SQL，請參閱[SQL 集區的最佳做法](best-practices-sql-pool.md)一文以取得特定指引。
