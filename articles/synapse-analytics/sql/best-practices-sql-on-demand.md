---
title: 無伺服器 SQL 集區 (預覽) 的最佳作法
description: 使用無伺服器 SQL 集區 (預覽版) 的建議和最佳作法。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6fd0ba19739b75e72541ac84d6b1696ab2819dee
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317436"
---
# <a name="best-practices-for-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics 的無伺服器 SQL 集區 (預覽) 的最佳作法

在本文中，您將找到使用無伺服器 SQL 集區 (預覽版) 的最佳作法集合。 無伺服器 SQL 集區是 Azure Synapse Analytics 中的資源。

## <a name="general-considerations"></a>一般考量

無伺服器 SQL 集區可讓您查詢 Azure 儲存體帳戶中的檔案。 其沒有本機儲存或內嵌功能。 因此查詢目標的所有檔案都是無伺服器 SQL 集區的外部。 從儲存體讀取檔案的任何相關動作，都可能會影響查詢效能。

## <a name="colocate-your-azure-storage-account-and-serverless-sql-pool"></a>共置您的 Azure 儲存體帳戶和無伺服器 SQL 集區

若要將延遲降至最低，請共置您的 Azure 儲存體帳戶和無伺服器的 SQL 集區端點。 在工作區建立期間佈建的儲存體帳戶和端點都會位於相同區域。

為了達到最佳效能，如果您存取無伺服器 SQL 集區的其他儲存體帳戶，請確定它們位於相同的區域中。 如果不在相同區域，則遠端區域與端點區域之間的資料網路傳輸延遲將會增加。

## <a name="azure-storage-throttling"></a>Azure 儲存體節流

存取您儲存體帳戶的應用程式和服務可能會有很多個。 當應用程式、服務和無伺服器的 SQL 集區工作負載所產生的組合 IOPS 或輸送量超過儲存體帳戶的限制時，就會發生儲存體節流。 然後，這將會對查詢效能造成嚴重的負面影響。

偵測到節流時，無伺服器 SQL 集區具有內建處理來解決此問題。 無伺服器 SQL 集區會以較慢的步調提出要求，直到節流解決為止。

> [!TIP]
> 為了最佳化查詢執行，請勿在查詢執行期間以其他工作負載來壓迫儲存體帳戶。

## <a name="prepare-files-for-querying"></a>準備檔案以進行查詢

可能的話，您可以準備檔案以獲得更好的效能：

- 將 CSV 和 JSON 轉換成 Parquet。 Parquet 是單欄式格式。 由於已經過壓縮，所以其檔案大小會比包含相同資料的 CSV 或 JSON 檔案小。 無伺服器的 SQL 集區需要較少的時間，而且需要較少的儲存體要求來讀取它。
- 如果查詢是以單一大型檔案為目標，則好處是可將其分割成多個較小的檔案。
- 請盡可能將 CSV 檔案大小維持在小於 10 GB。
- 單一 OPENROWSET 路徑或外部資料表 LOCATION 的檔案大小應相同。
- 藉由將分割區儲存至不同的資料夾或檔案名稱來分割您的資料。 請參閱[使用 filename 和 filepath 函式以特定的分割區為目標](#use-filename-and-filepath-functions-to-target-specific-partitions)。

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>將萬用字元推送至較低的路徑層級

您可以在路徑中使用萬用字元，以[查詢多個檔案和資料夾](query-data-storage.md#query-multiple-files-or-folders)。 無伺服器 SQL 集區會列出儲存體帳戶中的檔案，從第一個 * 使用儲存體 API 開始。 其會排除不符合指定路徑的檔案。 如果最多符合指定路徑中第一個萬用字元的檔案有很多個，則減少檔案的初始清單可改善效能。

## <a name="use-appropriate-data-types"></a>使用適當的資料類型

您在查詢中使用的資料類型會影響效能。 如果您遵循下列指導方針，可以獲得最佳效能： 

- 使用最小的資料大小，以容納最大的可能值。
  - 如果字元值的長度上限為 30 個字元，請使用長度為 30 的字元資料類型。
  - 如果所有字元資料行的值都是固定大小，請使用 **char** 或 **nchar** 。 否則的話，請使用 **varchar** 或 **nvarchar** 。
  - 如果整數資料行的最大值為 500，請使用 **smallint** ，因為其為可容納此值的最小資料類型。 您可以在[本文](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)中找到整數資料類型範圍。
- 可能的話，請使用 **varchar** 和 **char** ，而不是 **nvarchar** 和 **nchar** 。
- 請盡可能使用以整數為基礎的資料類型。 排序、聯結和群組依據作業在整數上完成的速度比在字元資料上更快。
- 如果您使用結構描述推斷，[請檢查推斷的資料類型](#check-inferred-data-types)。

## <a name="check-inferred-data-types"></a>檢查推斷的資料類型

[結構描述推斷](query-parquet-files.md#automatic-schema-inference)可協助您快速撰寫查詢和探索資料，且不需要知道檔案結構描述。 這項便利的代價是，推斷的資料類型可能會大於實際的資料類型。 當來源檔案中的資訊不足以確保能使用適當的資料類型時，就會發生這種情況。 例如，Parquet 檔案不包含有關字元資料行長度上限的中繼資料。 因此無伺服器 SQL 集區會將它推斷為 Varchar (8000) 。

您可以使用 [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) 來檢查查詢的結果資料類型。

下列範例會示範如何最佳化推斷的資料類型。 我們使用此程序來顯示推斷的資料類型： 
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

結果集如下：

|is_hidden|column_ordinal|NAME|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

您知道查詢的推斷資料類型之後，就可以指定適當的資料類型：

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>使用 filename 和 filepath 函式以特定的分割區為目標

資料通常會組織成分割區。 您可以指示無伺服器 SQL 集區來查詢特定的資料夾和檔案。 這麼做會減少查詢需要讀取和處理的檔案數目和資料量。 還有一個額外的好處，就是您可以獲得更好的效能。

如需詳細資訊，請閱讀 [filename](query-data-storage.md#filename-function) 和 [filepath](query-data-storage.md#filepath-function) 函式，並參閱[查詢特定檔案](query-specific-files.md)的範例。

> [!TIP]
> 請一律將 filepath 和 filename 函式的結果轉換成適當的資料類型。 如果您使用字元資料類型，請務必使用適當的長度。

> [!NOTE]
> 用於分割區刪除的 filepath 和 filename 函式目前不支援外部資料表，但針對 Azure Synapse Analytics 而自動為每個 Apache Spark 中所建立資料表建立的外部資料表除外。

如果您儲存的資料未分割，請考慮加以分割。 如此一來，您就可以使用這些函式來最佳化以這些檔案為目標的查詢。 當您從無伺服器的 SQL 集區 [查詢 Azure Synapse 資料表的資料分割 Apache Spark](develop-storage-files-spark-tables.md) 時，查詢只會自動將所需的檔案設為目標。

## <a name="use-parser_version-20-to-query-csv-files"></a>使用 PARSER_VERSION 2.0 查詢 CSV 檔案

查詢 CSV 檔案時，您可以使用效能最佳化剖析器。 如需詳細資料，請參閱 [PARSER_VERSION](develop-openrowset.md)。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 來增強查詢效能和聯結

[CETAS](develop-tables-cetas.md) 是無伺服器 SQL 集區中最重要的其中一項功能。 CETAS 是平行作業，可建立外部資料表中繼資料，並將 SELECT 查詢結果匯出至儲存體帳戶中的一組檔案。

您可以使用 CETAS，將經常使用的查詢部分 (例如聯結的參考資料表) 儲存到新的一組檔案。 然後您可以聯結至這個單一外部資料表，而不是在多個查詢中重複常見的聯結。

CETAS 產生 Parquet 檔案後，當第一個查詢以此外部資料表為目標時，系統就會自動建立統計資料，進而提升效能。

## <a name="azure-ad-pass-through-performance"></a>Azure AD 傳遞效能

無伺服器 SQL 集區可讓您使用 Azure Active Directory (Azure AD) 傳遞或 SAS 認證，來存取儲存體中的檔案。 使用 Azure AD 傳遞的效能可能比較使用 SAS 還慢。

如果您需要更好的效能，請嘗試使用 SAS 認證來存取儲存體，直到已 Azure AD 傳遞效能改善為止。

## <a name="next-steps"></a>後續步驟

請檢閱[疑難排解](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中的解決方案和常見問題。 如果您要使用專用的 SQL 集區，而不是無伺服器的 SQL 集區，請參閱專用 SQL 集區的 [最佳作法](best-practices-sql-pool.md) 以取得特定指引。
