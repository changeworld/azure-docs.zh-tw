---
title: '使用 Azure Synapse 連結中的無伺服器 SQL 集區來查詢 Azure Cosmos DB 資料 (預覽) '
description: 在本文中，您將瞭解如何在 Azure Synapse Link 中使用 SQL 隨選查詢 Azure Cosmos DB (preview) 。
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 2b1af6fa5b0ccb95476c4ae169481e4aaa15f4f9
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737833"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>使用 Azure Synapse 連結中的無伺服器 SQL 集區來查詢 Azure Cosmos DB 資料 (預覽) 

Synapse 無伺服器 SQL 集區可讓您以近乎即時的方式，在已啟用 [Azure Synapse 連結](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 的 Azure Cosmos DB 容器中分析資料，而不會影響交易式工作負載的效能。 它提供了一個熟悉的 T-sql 語法，可從 [分析存放區](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 查詢資料，並透過 t-sql 介面整合各種 BI 和臨機操作查詢工具的連接。

若要查詢 Azure Cosmos DB，可透過[OPENROWSET](develop-openrowset.md)函式（包括大部分的[SQL 函數和運算子](overview-features.md)）支援完整的[選取](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15)介面區。 您也可以儲存查詢的結果，此查詢會使用 [create external table as select](develop-tables-cetas.md#cetas-in-sql-on-demand)，從 Azure Cosmos DB 中讀取資料，以及 Azure Blob 儲存體中的資料和 Azure Data Lake Storage。 您目前無法使用 [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand)將無伺服器 SQL 集區查詢結果儲存至 Azure Cosmos DB。

在本文中，您將瞭解如何使用無伺服器 SQL 集區來撰寫查詢，該查詢將會從已啟用 Synapse 連結的 Azure Cosmos DB 容器中查詢資料。 然後，您可以深入瞭解如何在 Azure Cosmos DB 容器上建立無伺服器的 SQL 集區，並在 [本](./tutorial-data-analyst.md) 教學課程中將它們連接到 Power BI 模型。 

> [!IMPORTANT]
> 本教學課程使用具有 [Azure Cosmos DB 妥善定義架構](../../cosmos-db/analytical-store-introduction.md#schema-representation)的容器。 無伺服器 SQL 集區針對 [Azure Cosmos DB 完整精確度架構](#full-fidelity-schema) 所提供的查詢體驗，是根據預覽意見反應而變更的暫時行為。 請勿依賴沒有子句的結果集架構 `OPENROWSET` `WITH` ，從具有完整精確度架構的容器讀取資料，因為查詢體驗可能會變更，並配合妥善定義的架構。 請在 [Azure Synapse Analytics 意見反應論壇](https://feedback.azure.com/forums/307516-azure-synapse-analytics) 或連絡人 [synapse 連結產品團隊](mailto:cosmosdbsynapselink@microsoft.com) 張貼您的意見反應，以提供意見反應。

## <a name="overview"></a>概觀

若要支援查詢及分析 Azure Cosmos DB 分析存放區中的資料，無伺服器 SQL 集區會使用下列 `OPENROWSET` 語法：

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Azure Cosmos DB 連接字串會指定 Azure Cosmos DB 帳戶名稱、資料庫名稱、資料庫帳戶主要金鑰，以及要運作的選擇性區功能變數名稱稱 `OPENROWSET` 。 

> [!IMPORTANT]
> 請確定您在之後使用別名 `OPENROWSET` 。 如果您未在函式之後指定別名，就會有 [已知的問題](#known-issues) 會造成 Synapse 無伺服器 SQL 端點的連接問題 `OPENROWSET` 。

連接字串的格式如下：
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

在語法中，指定了不含引號的 Azure Cosmos DB 容器名稱 `OPENROWSET` 。 如果容器名稱具有任何特殊字元 (例如，虛線 '-' ) ，則應該將名稱包裝在 `[]` 語法中 (方括弧) 內 `OPENROWSET` 。

> [!NOTE]
> 無伺服器 SQL 集區不支援查詢 Azure Cosmos DB 交易式存放區。

## <a name="sample-data-set"></a>範例資料集

本文中的範例是以「 [疾病防護」和「控制」 (ECDC) covid-19-19 個案例](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) 和 [covid-19-19 個開放式研究資料集 (纜線) 、doi： 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)的資料為基礎。 

您可以查看這些頁面上的授權和資料結構，以及下載 [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) 和 [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) 資料集的範例資料。

若要遵循本文展示如何使用無伺服器 SQL 集區來查詢 Cosmos DB 資料，請確定您已建立下列資源：
* 已[啟用 Synapse 連結](../../cosmos-db/configure-synapse-link.md)的 Azure Cosmos DB 資料庫帳戶
* 名為的 Azure Cosmos DB 資料庫 `covid`
* 有兩個名為 `EcdcCases` 和且已 `Cord19` 載入上述範例資料集的 Azure Cosmos DB 容器。

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>使用自動架構推斷探索 Azure Cosmos DB 資料

若要在 Azure Cosmos DB 中探索資料，最簡單的方式就是利用自動架構推斷功能。 藉由省略 `WITH` 語句中的子句 `OPENROWSET` ，您可以指示無伺服器 SQL 集區自動偵測 (推斷) Azure Cosmos DB 容器分析存放區的架構。

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
在上述範例中，我們會指示無伺服器 SQL 集區，以 `covid` 在 `MyCosmosDbAccount` 上述範例中使用 Azure Cosmos DB 金鑰 (虛設的 Azure Cosmos DB 帳戶連接到資料庫) 。 接著，我們會 `EcdcCases` 在區域中存取容器的分析存放區 `West US 2` 。 因為沒有特定屬性的投射，所以函式 `OPENROWSET` 會傳回 Azure Cosmos DB 專案中的所有屬性。 

假設 Cosmos DB 容器中的專案具有 `date_rep` 、 `cases` 和 `geo_id` 屬性，下表顯示此查詢的結果：

| date_rep | 案例 | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

如果您需要流覽相同 Azure Cosmos DB 資料庫中其他容器的資料，您可以使用相同的連接字串和參考所需的容器做為第三個參數：

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>明確指定架構

雖然中的自動架構推斷功能 `OPENROWSET` 提供簡單、容易使用的 querience，但您的商務案例可能會要求您明確地從 Azure Cosmos DB 資料的唯讀相關屬性指定架構。

`OPENROWSET` 可讓您明確指定想要從容器中的資料讀取哪些屬性，以及指定其資料類型。 假設我們已從 [ECDC covid-19 資料集中](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) 匯入一些資料，並將下列結構匯入 Azure Cosmos DB：

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Azure Cosmos DB 中的這些一般 JSON 檔可以表示為 Synapse SQL 中的一組資料列和資料行。 `OPENROWSET` 函數可讓您指定要讀取的屬性子集，以及子句中的精確資料行類型 `WITH` ：

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

此查詢的結果可能如下所示：

| date_rep | 案例 | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

如需有關應用於 Azure Cosmos DB 值之 SQL 類型的詳細資訊，請參閱本文結尾的 [sql 型別對應規則](#azure-cosmos-db-to-sql-type-mappings) 。

## <a name="querying-nested-objects-and-arrays"></a>查詢嵌套的物件和陣列

Azure Cosmos DB 可讓您將更複雜的資料模型組合成嵌套物件或陣列，以表示更複雜的資料模型。 Azure Cosmos DB 的 [Synapse] 連結的 [自動同步功能] 會管理現成的分析存放區中的架構標記法，包括處理可從無伺服器 SQL 集區進行豐富查詢的嵌套資料類型。

例如， [電源線-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) 資料集具有遵循下列結構的 JSON 檔：

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

當函式讀取時，Azure Cosmos DB 中的嵌套物件和陣列會以 JSON 字串的形式呈現在查詢結果中 `OPENROWSET` 。 將這些複雜類型的值讀取為 SQL 資料行的其中一個選項是使用 SQL JSON 函數：

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

此查詢的結果可能如下所示：

| title | authors | first_autor_name |
| --- | --- | --- |
| Epidemi 的補充資訊 .。。 |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | 朱利安 |  

另一個選項是使用子句時，您也可以在物件中指定嵌套值的路徑 `WITH` ：

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

深入瞭解如何在[無伺服器 SQL 集](query-parquet-nested-types.md)區中分析 Synapse 連結和嵌套結構中的[複雜資料類型](../how-to-analyze-complex-schema.md)。

> [!IMPORTANT]
> 如果您在文字中看到非預期的字元（例如，而不是）， `MÃƒÂ©lade` `Mélade` 則您的資料庫定序不會設定為 [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) 定序。 
> 使用像這樣的 SQL 語句，[將資料庫的定序變更](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)為某些 UTF8 定序 `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` 。

## <a name="flattening-nested-arrays"></a>簡維的嵌套陣列

Azure Cosmos DB 的資料可能會有像是來自 [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) 資料集之作者陣列的嵌套 subarrays：

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

在某些情況下，您可能需要「聯結」最上層專案的屬性， (中繼資料) ，以及陣列 (作者) 的所有元素。 無伺服器 SQL 集區可讓您在嵌套的陣列上套用函式來壓平合併的結構 `OPENJSON` ：

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

此查詢的結果可能如下所示：

| title | authors | first | last | 聯繫 |
| --- | --- | --- | --- | --- |
| Epidemi 的補充資訊 .。。 |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | 朱利安 | Mélade | `   {"laboratory":"Centre de Recher…` |
Epidemi 的補充資訊 .。。 | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | 尼古拉斯 | 億# |`{"laboratory":"","institution":"U…` | 
| Epidemi 的補充資訊 .。。 |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Epidemi 的補充資訊 .。。 |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | 奧利維爾 | 弗洛雷斯 |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> 如果您在文字中看到非預期的字元（例如，而不是）， `MÃƒÂ©lade` `Mélade` 則您的資料庫定序不會設定為 [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) 定序。 
> 使用像這樣的 SQL 語句，[將資料庫的定序變更](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)為某些 UTF8 定序 `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` 。

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB 至 SQL 類型對應

請務必先注意，雖然 Azure Cosmos DB 的交易式存放區與架構無關，但架構化分析存放區以優化分析查詢效能。 利用 Synapse 連結的自動同步功能，Azure Cosmos DB 管理現成的分析存放區中的架構標記法，包括處理嵌套的資料類型。 由於無伺服器 SQL 集區會查詢分析存放區，因此請務必瞭解如何將 Azure Cosmos DB 輸入資料類型對應到 SQL 資料類型。

SQL (Core) API 的 Azure Cosmos DB 帳戶支援數位、字串、布林值、null、嵌套物件或陣列的 JSON 屬性類型。 如果您在中使用子句，則需要選擇符合這些 JSON 類型的 SQL 類型 `WITH` `OPENROWSET` 。 請參閱下列 SQL 資料行類型，以用於 Azure Cosmos DB 中的不同屬性類型。

| Azure Cosmos DB 屬性類型 | SQL 資料行類型 |
| --- | --- |
| 布林值 | bit |
| 整數 | BIGINT |
| Decimal | FLOAT |
| String | Varchar (UTF8 資料庫定序)  |
| 日期時間 (ISO 格式化字串)  | Varchar (30)  |
| 日期時間 (unix 時間戳記)  | BIGINT |
| Null | `any SQL type` 
| 嵌套物件或陣列 | Varchar (max)  (UTF8 資料庫定序) ，序列化為 JSON 文字 |

## <a name="full-fidelity-schema"></a>完整精確度架構

Azure Cosmos DB 完整的精確度架構會記錄容器中每個屬性的值和其最符合的類型。
`OPENROWSET` 在具有完整精確度之容器的函式中，會在每個資料格中提供型別和實際值。 讓我們假設下列查詢會從具有完整精確度架構的容器中讀取專案：

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) as rows
```

此查詢的結果會傳回格式化為 JSON 文字的類型和值： 

| date_rep | 案例 | geo_id |
| --- | --- | --- |
| {"date"： "2020-08-13"} | {"int32"： "254"} | {"string"： "RS"} |
| {"date"： "2020-08-12"} | {"int32"： "235"}| {"string"： "RS"} |
| {"date"： "2020-08-11"} | {"int32"： "316"} | {"string"： "RS"} |
| {"date"： "2020-08-10"} | {"int32"： "281"} | {"string"： "RS"} |
| {"date"： "2020-08-09"} | {"int32"： " | {"string"： "RS"} |
| {"string"： "2020/08/08"} | {"int32"： "312"} | {"string"： "RS"} |
| {"date"： "2020-08-07"} | {"float64"： "339.0"} | {"string"： "RS"} |

您可以針對每個值，查看 Cosmos DB 容器專案中識別的類型。 屬性的大部分值 `date_rep` `date` 都包含值，但其中某些值會不正確地儲存為 Cosmos DB 中的字串。 完整的精確度架構會傳回正確類型的 `date` 值，以及格式不正確的 `string` 值。
案例數目是儲存為值的資訊 `int32` ，但有一個以十進位數輸入的值。 此值具有 `float64` 類型。 如果有一些值超過最大 `int32` 數目，則會儲存為 `int64` 類型。 `geo_id`此範例中的所有值都會儲存為 `string` 類型。

> [!IMPORTANT]
> `OPENROWSET` 沒有子句的函式會 `WITH` 以預期的型別和不正確輸入類型的值來公開這兩個值。 此 funcion 是針對資料探索而設計，而不是用於報告。 請勿剖析從此函數傳回的 JSON 值來建立報表，並使用明確 [的 WITH 子句](#querying-items-with-full-fidelity-schema) 來建立您的報表。
> 您應清除 Azure Cosmos DB 容器中具有不正確類型的值，以在完整精確度分析存放區中套用 corection。 

若要查詢 Mongo DB API 種類的 Azure Cosmos DB 帳戶，您可以深入瞭解分析存放區中的完整精確度架構標記法，以及 [此處](../../cosmos-db/analytical-store-introduction.md#analytical-schema)所使用的擴充屬性名稱。

### <a name="querying-items-with-full-fidelity-schema"></a>使用完全失真的架構來查詢專案

在查詢完全精確度的架構時，您必須在子句中明確指定 SQL 型別和預期的 Cosmos DB 屬性型別 `WITH` 。 請勿 `OPENROWSET` `WITH` 在報表中使用不含子句，因為結果集的格式可能會根據意見反應變更為預覽狀態。

在下列範例中，我們會假設 `string` 屬性的正確類型 `geo_id` 和 `int32` 屬性的正確類型 `cases` ：

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

`geo_id` `cases` 具有其他類型的和值會以值的形式傳回 `NULL` 。 此查詢只會參考 `cases` 運算式 () 中具有指定之類型的 `cases.int32` 。

如果您有與其他類型 (的值 `cases.int64` ， `cases.float64` 則無法在 Cosmos DB 容器中清除) ，您必須在子句中明確參考 `WITH` 它們併合並結果。 下列查詢會匯總 `int32` 、 `int64` 和 `float64` 儲存在資料行中的 `cases` ：

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

在此範例中，案例數目會儲存為 `int32` 、 `int64` 或 `float64` 值，而且所有值都必須先解壓縮，才能計算每個國家/地區的案例數目。 

## <a name="known-issues"></a>已知問題

- 在 **MUST** 函式 `OPENROWSET` (（例如) ）之後，必須指定別名 `OPENROWSET (...) AS function_alias` 。 省略別名可能會導致連線問題，而且 Synapse 無伺服器 SQL 端點可能暫時無法使用。 此問題將在2020年11月解決。
- 無伺服器 SQL 集區針對 [Azure Cosmos DB 完整精確度架構](#full-fidelity-schema) 提供的查詢體驗，是根據預覽意見反應而變更的暫時行為。 請勿依賴沒有子句的架構在 `OPENROWSET` `WITH` 公開預覽期間提供，因為查詢體驗可能會根據客戶的意見反應，與妥善定義的架構對齊。 Contact [Synapse link 產品小組](mailto:cosmosdbsynapselink@microsoft.com) 以提供意見反應。

下表列出可能的錯誤和疑難排解動作：

| Error | 根本原因 |
| --- | --- |
| 語法錯誤：<br/> -' Openrowset ' 附近的語法不正確<br/> - `...` 不是可辨識的大量 OPENROWSET 提供者選項。<br/> -接近的語法不正確 `...` | 可能的根本原因<br/> -不使用 ' CosmosDB ' 做為第一個參數，<br/> -使用字串常值，而不是第三個參數中的識別碼，<br/> -未指定第三個參數 (容器名稱)  |
| CosmosDB 連接字串中發生錯誤 | -未指定帳戶、資料庫、金鑰 <br/> -無法辨識的連接字串中有一些選項。<br/> -分號 `;` 放置於連接字串的結尾 |
| 解析 CosmosDB 路徑失敗，錯誤為「帳戶名稱不正確」或「資料庫名稱不正確」 | 找不到指定的帳號名稱、資料庫名稱或容器，或尚未啟用指定集合的分析儲存體|
| 解析 CosmosDB 路徑失敗，錯誤為「密碼值不正確」或「秘密為 null 或空白」 | 帳戶金鑰無效或遺失。 |
| `column name`類型的 `type name` 資料行與外部資料類型不相容`type name` | 在子句中指定的資料行類型 `WITH` 與 Cosmos DB 容器中的類型不相符。 請嘗試變更資料行類型，如「 [SQL 類型](#azure-cosmos-db-to-sql-type-mappings) 對應」或「使用類型 Azure Cosmos DB 一節中所述 `VARCHAR` 。 |
| 資料行包含 `NULL` 所有資料格中的值。 | 可能是錯誤的資料行名稱或路徑運算式在 `WITH` 子句中。 在資料行類型) in 子句之後，資料行名稱 (或路徑運算式 `WITH` 必須符合 Cosmos DB 集合中的某些屬性名稱。 比較會區分 **大小寫**  (例如， `productCode` 和 `ProductCode` 是不同的屬性) 。 |

您可以在 [Azure Synapse 意見反應頁面](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862)上報告建議和問題。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [使用 Power BI 和無伺服器 Synapse SQL 集區搭配 Azure Synapse 連結](../../cosmos-db/synapse-link-power-bi.md)
- [如何在 SQL 中依需求建立和使用視圖](create-use-views.md) 
- [在 Azure Cosmos DB 上建立 SQL 隨選視圖的教學課程，並透過 DirectQuery 將它們連接至 Power BI 模型](./tutorial-data-analyst.md)
