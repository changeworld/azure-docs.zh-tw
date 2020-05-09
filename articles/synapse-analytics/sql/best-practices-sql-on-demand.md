---
title: Azure Synapse 分析中的 SQL 隨選（預覽）最佳做法
description: 當您使用隨選 SQL （預覽）時，您應該知道的建議和最佳作法。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0015beadfea61fc31bf3f37232105b9cfd2ced71
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692156"
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

## <a name="push-wildcards-to-lower-levels-in-path"></a>將萬用字元推送至較低的路徑層級

您可以在路徑中使用萬用字元來[查詢多個檔案和資料夾](develop-storage-files-overview.md#query-multiple-files-or-folders)。 SQL 隨選列出儲存體帳戶中的檔案，從第一個 * 使用儲存體 API 開始，並排除不符合指定路徑的檔案。 如果有許多檔案符合指定路徑的最高至第一個萬用字元，減少檔案的初始清單可以改善效能。

## <a name="use-appropriate-data-types"></a>使用適當的資料類型

查詢中使用的資料類型會影響效能。 如果您這樣做，就能獲得更好的效能： 

- 使用最小的資料大小，以容納最大的可能值。
  - 如果字元值長度上限為30個字元，請使用長度為30的字元資料類型。
  - 如果所有字元資料行的值都是固定大小，請使用 char 或 Nchar。 否則，請使用 Varchar 或 Nvarchar。
  - 如果整數資料行的最大值為500，請使用 Smallint，因為它是可容納此值的最小資料類型。 您可以在[這裡](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)找到整數資料類型範圍。
- 可能的話，請使用 Varchar 和 char，而不是 Nvarchar 和 Nchar。
- 如果可能的話，請使用以整數為基礎的資料類型。 [排序]、[聯結] 和 [群組依據] 作業在整數上執行的速度比字元資料更快。
- 如果您使用的是架構推斷，請[檢查推斷的資料類型](#check-inferred-data-types)。

## <a name="check-inferred-data-types"></a>檢查推斷的資料類型

[架構推斷](query-parquet-files.md#automatic-schema-inference)可協助您快速撰寫查詢和流覽資料，而不需要知道檔案架構。 這種緩和的代價是，所推斷的資料型別會比實際的還要大。 當來源檔案中的資訊不足以確保使用適當的資料類型時，就會發生這種情況。 例如，Parquet 檔案不包含最大字元資料行長度的中繼資料，而 SQL 視需要將它推斷為 Varchar （8000）。 

您可以使用[sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15)來檢查查詢所產生的資料類型。

下列範例會示範如何優化推斷的資料類型。 程式是用來顯示推斷的資料類型。 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

以下為結果集。

|is_hidden|column_ordinal|NAME|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|Varchar （8000）|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

一旦我們知道查詢的推斷資料類型，就可以指定適當的資料類型：

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用 fileinfo 和 filepath 函式以特定的分割區為目標

資料通常會組織成分割區。 您可以指示 SQL 視需要查詢特定的資料夾和檔案。 此函式會減少查詢需要讀取和處理的檔案數目和資料量。 還有一個額外的好處，就是您可以獲得更好的效能。

如需詳細資訊，請參閱[檔案名](develop-storage-files-overview.md#filename-function)和[filepath](develop-storage-files-overview.md#filepath-function)函數和如何[查詢特定](query-specific-files.md)檔案的範例。

> [!TIP]
> 一律將 filepath 和 fileinfo 函式的結果轉換成適當的資料類型。 如果您使用字元資料類型，請務必使用適當的長度。

如果您儲存的資料未分割，請考慮將它分割，讓您可以使用這些函式來優化以這些檔案為目標的查詢。 從 SQL 隨選[查詢資料分割的 Spark 資料表](develop-storage-files-spark-tables.md)時，查詢只會自動鎖定所需的檔案。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 來增強查詢效能和聯結

[CETAS](develop-tables-cetas.md)是 SQL 隨選提供的其中一項最重要的功能。 CETAS 是一種平行作業，會建立外部資料表中繼資料，並將選取的查詢結果匯出至儲存體帳戶中的一組檔案。

您可以使用 CETAS，將經常使用的查詢部分（例如聯結的參考資料表）儲存到新的一組檔案。 接下來，您可以加入這個單一外部資料表，而不是在多個查詢中重複常見的聯結。

當 CETAS 產生 Parquet 檔案時，將會在第一次查詢以此外部資料表為目標時自動建立統計資料，進而提升效能。

## <a name="next-steps"></a>後續步驟

請參閱[疑難排解](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中的常見問題和解決方案。 如果您使用的是 SQL 集區，而不是隨選 SQL，請參閱[SQL 集區的最佳做法](best-practices-sql-pool.md)一文以取得特定指引。
