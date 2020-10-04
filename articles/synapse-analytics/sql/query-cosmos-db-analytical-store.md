---
title: '使用 Azure Synapse 連結中的 SQL 無伺服器查詢 Azure Cosmos DB 資料 (預覽) '
description: 在本文中，您將瞭解如何在 Azure Synapse Link 中使用 SQL 隨選查詢 Azure Cosmos DB (preview) 。
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 9d36cee1ebf9f58991db6fd40fd6aa9422546b61
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710748"
---
# <a name="query-azure-cosmos-db-data-using-sql-serverless-in-azure-synapse-link-preview"></a>使用 Azure Synapse 連結中的 SQL 無伺服器查詢 Azure Cosmos DB 資料 (預覽) 

Synapse SQL 無伺服器 (先前的 SQL 隨選) 可讓您以近乎即時的方式，在已啟用 [Azure Synapse 連結](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 的 Azure Cosmos DB 容器中分析資料，而不會影響交易式工作負載的效能。 它提供了一個熟悉的 T-sql 語法，可從 [分析存放區](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 查詢資料，並透過 t-sql 介面整合各種 BI 和臨機操作查詢工具的連接。

> [!NOTE]
> 支援使用 SQL 無伺服器查詢 Azure Cosmos DB 分析存放區目前處於閘道預覽狀態。 

若要查詢 Azure Cosmos DB，可透過[OPENROWSET](develop-openrowset.md)函式（包括大部分的[SQL 函數和運算子](overview-features.md)）支援完整的[選取](/sql/t-sql/queries/select-transact-sql.md?view=sql-server-ver15&preserve-view=true)介面區。 您也可以儲存查詢的結果，此查詢會使用 [create external table as select](develop-tables-cetas.md#cetas-in-sql-on-demand)，從 Azure Cosmos DB 中讀取資料，以及 Azure Blob 儲存體中的資料和 Azure Data Lake Storage。 您目前無法使用 [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand)將 SQL 無伺服器查詢結果儲存至 Azure Cosmos DB。

在本文中，您將瞭解如何使用 SQL 無伺服器來撰寫查詢，該查詢將會從已啟用 Synapse 連結的 Azure Cosmos DB 容器中查詢資料。 然後，您可以在 [本](./tutorial-data-analyst.md) 教學課程中深入瞭解如何透過 Azure Cosmos DB 容器建立 SQL 無伺服器，並將其連接到 Power BI 模型。 

## <a name="overview"></a>概觀

為了支援查詢及分析 Azure Cosmos DB 分析存放區中的資料，SQL 無伺服器會使用下列 `OPENROWSET` 語法：

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ]
```

Azure Cosmos DB 連接字串會指定 Azure Cosmos DB 帳戶名稱、資料庫名稱、資料庫帳戶主要金鑰，以及要運作的選擇性區功能變數名稱稱 `OPENROWSET` 。 連接字串的格式如下：
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

在語法中，指定了不含引號的 Azure Cosmos DB 容器名稱 `OPENROWSET` 。 如果容器名稱具有任何特殊字元 (例如，虛線 '-' ) ，則應該將名稱包裝在 `[]` 語法中 (方括弧) 內 `OPENROWSET` 。

> [!NOTE]
> SQL 無伺服器不支援查詢 Azure Cosmos DB 交易式存放區。

## <a name="sample-data-set"></a>範例資料集

本文中的範例是以「 [疾病防護」和「控制」 (ECDC) covid-19-19 個案例](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) 和 [covid-19-19 個開放式研究資料集 (纜線) 、doi： 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)的資料為基礎。 

您可以查看這些頁面上的授權和資料結構，以及下載 [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) 和 [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) 資料集的範例資料。

若要遵循本文展示如何使用 SQL 無伺服器查詢 Cosmos DB 資料，請確定您已建立下列資源：
* 已[啟用 Synapse 連結](../../cosmos-db/configure-synapse-link.md)的 Azure Cosmos DB 資料庫帳戶
* 名為的 Azure Cosmos DB 資料庫 `covid`
* 有兩個名為 `EcdcCases` 和且已 `Cord19` 載入上述範例資料集的 Azure Cosmos DB 容器。

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>使用自動架構推斷探索 Azure Cosmos DB 資料

若要在 Azure Cosmos DB 中探索資料，最簡單的方式就是利用自動架構推斷功能。 藉由省略 `WITH` 語句中的子句 `OPENROWSET` ，您可以指示 SQL 無伺服器自動偵測 (推斷) Azure Cosmos DB 容器分析存放區的架構。

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
在上述範例中，我們會指示 SQL 無伺服器連接到 `covid` `MyCosmosDbAccount` 使用 Azure Cosmos DB (金鑰驗證的 Azure Cosmos DB 帳戶中的資料庫) 。 接著，我們會 `EcdcCases` 在區域中存取容器的分析存放區 `West US 2` 。 因為沒有特定屬性的投射，所以函式 `OPENROWSET` 會傳回 Azure Cosmos DB 專案中的所有屬性。

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

Azure Cosmos DB 可讓您將更複雜的資料模型組合成嵌套物件或陣列，以表示更複雜的資料模型。 Azure Cosmos DB 的 [Synapse] 連結的 [自動同步功能] 會管理現成的分析存放區中的架構標記法，包括處理可從 SQL 無伺服器進行豐富查詢的嵌套資料類型。

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

深入瞭解如何在[SQL 無伺服器](query-parquet-nested-types.md)中分析 Synapse 連結和嵌套結構中的[複雜資料類型](../how-to-analyze-complex-schema.md)。

> [!IMPORTANT]
> 如果您在文字中看到非預期的字元（例如，而不是）， `MÃƒÂ©lade` `Mélade` 則您的資料庫定序不會設定為 [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) 定序。 
> 使用像這樣的 SQL 語句，[將資料庫的定序變更](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)為某些 UTF8 定序 `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` 。


## <a name="flattening-nested-arrays"></a>簡維的嵌套陣列

Azure Cosmos DB 的資料可能會有嵌套的子陣列，例如來自 [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) 資料集的作者陣列：

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

在某些情況下，您可能需要「聯結」最上層專案的屬性， (中繼資料) ，以及陣列 (作者) 的所有元素。 SQL 無伺服器可讓您在 `OPENJSON` 嵌套的陣列上套用函式來壓平合併的結構：

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

請務必先注意，雖然 Azure Cosmos DB 的交易式存放區與架構無關，但架構化分析存放區以優化分析查詢效能。 利用 Synapse 連結的自動同步功能，Azure Cosmos DB 管理現成的分析存放區中的架構標記法，包括處理嵌套的資料類型。 由於 SQL 無伺服器會查詢分析存放區，因此請務必瞭解如何將 Azure Cosmos DB 輸入資料類型對應到 SQL 資料類型。

SQL (Core) API 的 Azure Cosmos DB 帳戶支援數位、字串、布林值、null、嵌套物件或陣列的 JSON 屬性類型。 如果您在中使用子句，則需要選擇符合這些 JSON 類型的 SQL 類型 `WITH` `OPENROWSET` 。 請參閱下列 SQL 資料行類型，以用於 Azure Cosmos DB 中的不同屬性類型。

| Azure Cosmos DB 屬性類型 | SQL 資料行類型 |
| --- | --- |
| 布林值 | bit |
| 整數 | BIGINT |
| Decimal | FLOAT |
| 字串 | Varchar (UTF8 資料庫定序)  |
| 日期時間 (ISO 格式化字串)  | Varchar (30)  |
| 日期時間 (unix 時間戳記)  | BIGINT |
| Null | `any SQL type` 
| 嵌套物件或陣列 | Varchar (max)  (UTF8 資料庫定序) ，序列化為 JSON 文字 |

若要查詢 Mongo DB API 種類的 Azure Cosmos DB 帳戶，您可以深入瞭解分析存放區中的完整精確度架構標記法，以及 [此處](../../cosmos-db/analytical-store-introduction.md#analytical-schema)所使用的擴充屬性名稱。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [如何在 SQL 中依需求建立和使用視圖](create-use-views.md) 
- [在 Azure Cosmos DB 上建立 SQL 隨選視圖的教學課程，並透過 DirectQuery 將它們連接至 Power BI 模型](./tutorial-data-analyst.md)
