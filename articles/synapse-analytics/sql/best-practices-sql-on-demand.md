---
title: Azure Synapse Analytics 中的 SQL 隨選最佳做法 (預覽)
description: 使用 SQL 隨選 (預覽) 時，您應該知道的建議和最佳做法。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 86678365d1510199247e8a1aaa48ec844d07de32
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592928"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的 SQL 隨選最佳做法 (預覽)

您將在本文找到使用 SQL 隨選 (預覽) 時的最佳做法集合。 SQL 隨選是 Azure Synapse Analytics 中的額外資源。

## <a name="general-considerations"></a>一般考量

SQL 隨選可讓您查詢 Azure 儲存體帳戶中的檔案。 其沒有本機儲存或內嵌功能。 因此，查詢的所有目標檔案都在 SQL 隨選外部。 從儲存體讀取檔案的任何相關動作，都可能會影響查詢效能。

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>共置 Azure 儲存體帳戶和 SQL 隨選

若要將延遲降至最低，請共置您的 Azure 儲存體帳戶和您的 SQL 隨選端點。 在工作區建立期間佈建的儲存體帳戶和端點都會位於相同區域。

為了達到最佳效能，如果您使用 SQL 隨選存取其他儲存體帳戶，請確定這些帳戶位於相同區域。 如果不在相同區域，則遠端和端點區域之間的資料網路傳輸延遲將會增加。

## <a name="azure-storage-throttling"></a>Azure 儲存體節流

存取您儲存體帳戶的應用程式和服務可能會有很多個。 當應用程式、服務和 SQL 隨選工作負載所產生的彙總 IOPS 或輸送量超過儲存體帳戶的限制時，就會發生儲存體節流。 然後，這將會對查詢效能造成嚴重的負面影響。

偵測到節流後，SQL 隨選會有此案例的內建處理方式。 在節流解除之前，SQL 隨選會以較慢的步調對儲存體提出要求。

> [!TIP]
> 為了最佳化查詢執行，您不應在查詢執行期間以其他工作負載來壓迫儲存體帳戶。

## <a name="prepare-files-for-querying"></a>準備檔案以進行查詢

可能的話，您可以準備檔案以獲得更好的效能：

- 將 CSV 和 JSON 轉換成 Parquet - Parquet 是單欄式格式。 由於已經過壓縮，所以其檔案大小會比具有相同資料的 CSV 或 JSON 檔案小。 SQL 隨選讀取該檔案所需的時間和儲存體要求會比較少。
- 如果查詢是以單一大型檔案為目標，則好處是可將其分割成多個較小的檔案。
- 請盡可能將 CSV 檔案大小維持在小於 10 GB。
- 單一 OPENROWSET 路徑或外部資料表 LOCATION 的檔案大小應相同。
- 若要藉由將資料分割儲存至不同資料夾或不同檔案名稱，來分割您的資料，請參閱[使用 filename 和 filepath 函式將特定資料分割設為目標](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)。

## <a name="push-wildcards-to-lower-levels-in-path"></a>將萬用字元推送至較低的路徑層級

您可以在路徑中使用萬用字元，以[查詢多個檔案和資料夾](develop-storage-files-overview.md#query-multiple-files-or-folders)。 SQL 隨選會使用儲存體 API，從第一個 * 開始列出儲存體帳戶中的檔案，並排除不符合指定路徑的檔案。 如果最多符合指定路徑中第一個萬用字元的檔案有很多個，則減少檔案的初始清單可以改善效能。

## <a name="use-appropriate-data-types"></a>使用適當的資料類型

查詢中使用的資料類型會影響效能。 若要獲得更好的效能，您可以： 

- 使用最小的資料大小，以容納最大的可能值。
  - 如果字元值的長度上限為 30 個字元，請使用長度為 30 的字元資料類型。
  - 如果所有字元資料行的值都是固定大小，請使用 Char 或 Nchar。 否則的話，請使用 varchar 或 Nvarchar。
  - 如果整數資料行的最大值為 500，請使用 Smallint，因為其是可容納此值的最小資料類型。 您可以在[這裡](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)找到整數資料類型範圍。
- 可能的話，請使用 varchar 和 char，而不是 Nvarchar 和 Nchar。
- 請盡可能使用以整數為基礎的資料類型。 排序、聯結和群組依據作業在整數上執行的速度比在字元資料上快。
- 如果您使用結構描述推斷，[請檢查推斷的資料類型](#check-inferred-data-types)。

## <a name="check-inferred-data-types"></a>檢查推斷的資料類型

[結構描述推斷](query-parquet-files.md#automatic-schema-inference)可協助您快速撰寫查詢和探索資料，而且不需要知道檔案結構描述。 此簡便方法的代價是，推斷的資料類型會比實際大小還大。 當來源檔案中的資訊不足以確保能使用適當的資料類型時，就會發生這種情況。 例如，Parquet 檔案不包含有關字元資料行長度上限的中繼資料，而 SQL 隨選將其推斷為 varchar(8000)。 

您可以使用 [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15) 來檢查查詢的結果資料類型。

下列範例會示範如何最佳化推斷的資料類型。 我們使用程序來顯示推斷的資料類型。 
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
|0|1|vendor_id|varchar(8000)|8000|
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

資料通常會組織成分割區。 您可以指示 SQL 隨選查詢特定的資料夾和檔案。 此函式會減少查詢需要讀取和處理的檔案數目和資料量。 還有一個額外的好處，就是您可以獲得更好的效能。

如需詳細資訊，請參閱 [filename](develop-storage-files-overview.md#filename-function) 和 [filepath](develop-storage-files-overview.md#filepath-function) 函式和範例，以了解如何[查詢特定檔案](query-specific-files.md)。

> [!TIP]
> 請一律將 filepath 和 fileinfo 函式的結果轉換成適當的資料類型。 如果您使用字元資料類型，請務必確認長度適當。

> [!NOTE]
> 用於分割區刪除的 filepath 和 fileinfo 函式目前不支援外部資料表，但針對 Azure Synapse Analytics 而自動為每個 Apache Spark 中所建立資料表建立的外部資料表除外。

如果您儲存的資料未分割，請考慮將其分割，以便使用這些函式來最佳化以這些檔案為目標的查詢。 從 SQL 隨選[查詢分割的 Spark 資料表](develop-storage-files-spark-tables.md)時，查詢會自動鎖定在所需的檔案。

## <a name="use-parser_version-20-for-querying-csv-files"></a>使用 PARSER_VERSION 2.0 來查詢 CSV 檔案

查詢 CSV 檔案時，您可以使用效能最佳化剖析器。 如需詳細資料，請參閱 [PARSER_VERSION](develop-openrowset.md)。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 來增強查詢效能和聯結

[CETAS](develop-tables-cetas.md) 是 SQL 隨選上其中一項最重要的功能。 CETAS 是平行作業，可建立外部資料表中繼資料，並將 SELECT 查詢結果匯出至儲存體帳戶中的一組檔案。

您可以使用 CETAS，將經常使用的查詢部分 (例如聯結的參考資料表) 儲存到新的一組檔案。 接下來，您可以聯結至這個單一外部資料表，而不是在多個查詢中重複常見的聯結。

CETAS 產生 Parquet 檔案後，當第一個查詢以此外部資料表為目標時，系統就會自動建立統計資料，進而提升效能。

## <a name="aad-pass-through-performance"></a>AAD 傳遞效能

SQL 隨選可讓您使用 AAD 傳遞或 SAS 認證來存取儲存體中的檔案。 相較於 SAS，使用 AAD 傳遞的效能可能比較慢。 

如果您需要更好的效能，請嘗試使用 SAS 認證來存取儲存體，直到已 AAD 傳遞效能改善為止。

## <a name="next-steps"></a>後續步驟

請參閱[疑難排解](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中的常見問題和解決方案。 如果您使用的是 SQL 集區，而不是 SQL 隨選，請參閱 [SQL 集區的最佳做法](best-practices-sql-pool.md)一文來取得特定指引。
