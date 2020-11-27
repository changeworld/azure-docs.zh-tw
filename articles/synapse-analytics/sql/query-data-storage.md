---
title: 使用無伺服器 SQL 集區查詢資料儲存體 (預覽)
description: 本文說明如何使用 Azure Synapse Analytics 中的無伺服器 SQL 集區 (預覽) 資源來查詢 Azure 儲存體。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 81a892b10996686cf58d45867e3d889505b5d3d9
ms.sourcegitcommit: 5e2f5efba1957ba40bd951c3dcad42f4a00734ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2020
ms.locfileid: "96299576"
---
# <a name="query-storage-files-with-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>具有無伺服器 SQL 集區 (預覽) 的查詢儲存體檔案不支援此語法

無伺服器 SQL 集區 (預覽) 可讓您查詢資料湖中的資料。 其提供可搭載半結構化和非結構化資料查詢的 T-SQL 查詢介面區。 在查詢方面，可支援下列 T-SQL 層面：

- 完整的 [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 介面區，包括大部分的 [SQL 函式及運算子](overview-features.md)。
- CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)) 會建立[外部資料表](develop-tables-external-tables.md)，然後將 Transact-SQL SELECT 陳述式的結果以平行方式匯出至 Azure 儲存體。

如需有關目前支援及不支援的功能詳細資訊，請參閱[無伺服器 SQL 集區概觀](on-demand-workspace-overview.md)一文，或參閱下列文章：
- [開發儲存體存取](develop-storage-files-overview.md)，您可以在其中了解如何使用[外部資料表](develop-tables-external-tables.md)及 [OPENROWSET](develop-openrowset.md) 函式從儲存體讀取資料。
- [控制儲存體存取](develop-storage-files-storage-access-control.md)，您可以在其中了解如何讓 Synapse SQL 使用 SAS 驗證或工作區的受控識別來存取儲存體。

## <a name="overview"></a>概觀

為了在 Azure 儲存體檔案中進行資料就地查詢時有流暢的體驗，無伺服器 SQL 集區使用了 [OPENROWSET](develop-openrowset.md) 函式與其他功能：

- [查詢多個檔案或資料夾](#query-multiple-files-or-folders)
- [PARQUET 檔案格式](#query-parquet-files)
- [查詢 CSV 和分隔文字 (欄位結束字元、資料列結束字元、逸出字元)](#query-csv-files)
- [讀取選擇的資料行子集](#read-a-chosen-subset-of-columns)
- [結構描述推斷](#schema-inference)
- [filename 函式](#filename-function)
- [filepath 函式](#filepath-function)
- [使用複雜類型和巢狀或重複的資料結構](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>查詢 PARQUET 檔案

若要查詢 Parquet 來源資料，請使用 FORMAT = 'PARQUET'

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

如需使用範例，請參閱[查詢 Parquet 檔案](query-parquet-files.md)一文。

## <a name="query-csv-files"></a>查詢 CSV 檔案

若要查詢 CSV 來源資料，請使用 FORMAT = 'CSV'。 當您查詢 CSV 檔案時，您可以在 `OPENROWSET` 函式中指定 CSV 檔案的結構描述：

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

還有一些其他選項可用來將剖析規則調整為自訂 CSV 格式：
- ESCAPE_CHAR = 'char' 會指定檔案中用來將本身和所有分隔符號值逸出的字元。 如果逸出字元後面接著本身或任何分隔符號值以外的值，讀取值時就會捨棄逸出字元。
無論 FIELDQUOTE 已啟用或未啟用，都會套用 ESCAPE_CHAR 參數。 其不會用來逸出引號字元。 引號字元必須以另一個引號字元來逸出。 只有在以引號字元封住值時，引號字元才能在資料行值中出現。
- FIELDTERMINATOR ='field_terminator' 會指定要使用的欄位結束字元。 預設的欄位結束字元為逗號 (" **,** ")
- ROWTERMINATOR ='row_terminator' 會指定要使用的資料列結束字元。 預設的資料列結束字元是新行字元： **\r\n**。

## <a name="file-schema"></a>檔案結構描述

Synapse SQL 中的 SQL 語言可讓您將檔案的結構描述定義為 `OPENROWSET` 函式的一部分，以及讀取全部或部分的資料行，或嘗試使用結構描述推斷，自動從檔案中判斷資料行類型。

### <a name="read-a-chosen-subset-of-columns"></a>讀取選擇的資料行子集

若要指定您想要讀取的資料行，您可以在 `OPENROWSET` 陳述式內提供選擇性的 WITH 子句。

- 如果有 CSV 資料檔案，應提供資料行名稱及其資料類型，才能讀取所有資料行。 如果您想要讀取一小組資料行，請使用序數從原始資料檔案中依序挑選資料行。 資料行將會以加上順序的指派來繫結。
- 如果有 Parquet 資料檔案，請提供與原始資料檔案中資料行名稱相符的資料行名稱。 資料行將會依名稱來繫結。

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 varchar(max)
) as rows
```

針對每個資料行，您都必須在 `WITH` 子句中指定資料行名稱和類型。
如需範例，請參閱[在不指定所有資料行的情況下讀取 CSV 檔案](query-single-csv-file.md#return-a-subset-of-columns)。

## <a name="schema-inference"></a>結構描述推斷

您可以藉由省略 `OPENROWSET` 陳述式中的 WITH 子句，來指示服務從基礎檔案中自動偵測 (推斷) 結構描述。

> [!NOTE]
> 這目前僅適用於 PARQUET 檔案格式。

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

請確定使用[適當的推斷資料類型](best-practices-sql-on-demand.md#check-inferred-data-types)來達到最佳效能。 

## <a name="query-multiple-files-or-folders"></a>查詢多個檔案或資料夾

若要對資料夾或資料夾集合中的一組檔案執行 T-SQL 查詢，並同時將其視為單一實體或資料列集，請提供單一資料夾的路徑，或代表一組檔案或資料夾的模式 (使用萬用字元)。

適用的規則如下：

- 模式可能會出現在目錄路徑或檔案名稱中。
- 相同目錄步驟或檔案名稱中可出現多個模式。
- 如果有多個萬用字元，則會在產生的檔案集中包含所有相符路徑內的檔案。

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

如需使用範例，請參閱[查詢資料夾和多個檔案](query-folders-multiple-csv-files.md)。

## <a name="file-metadata-functions"></a>檔案中繼資料函式

### <a name="filename-function"></a>Filename 函式

此函式會傳回資料列來源的檔案名稱。 

若要查詢特定檔案，請閱讀[查詢特定檔案](query-specific-files.md#filename)一文中的＜Filename＞一節。

傳回資料類型為 nvarchar(1024)。 為了達到最佳效能，請一律將 filename 函式的結果轉換成適當的資料類型。 如果您使用字元資料類型，請務必確認長度適當。

### <a name="filepath-function"></a>Filepath 函式

此函式會傳回完整路徑或部分路徑的：

- 若在沒有參數的情況下進行呼叫，會傳回資料列來源的完整檔案路徑。
- 若以參數進行呼叫，則會傳回路徑的一部分，該部分會與參數中所指定位置上的萬用字元相符。 例如，參數值 1 會傳回符合第一個萬用字元的路徑部分。

如需詳細資訊，請參閱[查詢特定檔案](query-specific-files.md#filepath)一文的 Filepath 區段。

傳回資料類型為 nvarchar(1024)。 為了達到最佳效能，請一律將 filepath 函式的結果轉換成適當的資料類型。 如果您使用字元資料類型，請務必確認長度適當。

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>使用複雜類型和巢狀或重複的資料結構

為了在使用以巢狀或重複資料類型 (例如 [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) 檔案) 儲存的資料時擁有流暢的體驗，無伺服器 SQL 集區已新增下列延伸模組。

#### <a name="project-nested-or-repeated-data"></a>投射巢狀或重復資料

若要投射資料，請在包含資料行 (巢狀資料類型) 的 Parquet 檔案上執行 SELECT 陳述式。 在輸出時，巢狀值會序列化為 JSON，並以 varchar(8000) SQL 資料類型的形式傳回。

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

如需詳細資訊，請參閱[查詢 Parquet 巢狀型別](query-parquet-nested-types.md#project-nested-or-repeated-data)一文中的＜投射巢狀或重復資料＞一節。

#### <a name="access-elements-from-nested-columns"></a>從巢狀資料行存取元素

若要從巢狀資料行 (例如 Struct) 存取巢狀元素，請使用「點標記法」將欄位名稱串連到路徑中。 在 `OPENROWSET` 函式的 WITH 子句中，提供路徑作為 column_name。

語法片段範例如下：

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

根據預設，`OPENROWSET` 函式會以 WITH 子句中提供的資料行名稱來比對來源欄位名稱和路徑。 您可以使用 WITH 子句來存取包含在相同來源 Parquet 檔案中，但位於不同巢狀層級的元素。

**傳回值**

- 針對不在巢狀型別群組中的所有 Parquet 類型，函式會從指定的專案和指定的路徑傳回純量值 (例如 int、decimal 和 varchar)。
- 如果路徑指向屬於巢狀型別的元素，則函式會從指定路徑的最上層元素開始傳回 JSON 片段。 JSON 片段的類型為 varchar(8000)。
- 如果在指定的 column_name 中找不到屬性，則函數會傳回錯誤。
- 如果在指定的 column_path 中找不到屬性，則視[路徑模式](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)而定，函式會在 strict 模式中傳回錯誤，而在 lax 模式中則會傳回 null。

如需查詢範例，請參閱[查詢 Parquet 巢狀型別](query-parquet-nested-types.md#read-properties-from-nested-object-columns)一文中的＜從巢狀資料行存取元素＞一節。

#### <a name="access-elements-from-repeated-columns"></a>從重複的資料行存取元素

若要從重複的資料行存取元素 (例如陣列或對應的元素)，請針對您需要投影和提供的每個純量元素，使用 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 函式：

- 使用巢狀或重復資料行作為第一個參數
- 第二個參數則使用 [JSON 路徑](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)，該路徑會指定要存取的元素或屬性

若要從重複的資料行存取非純量元素，請針對您需要投影和提供的每個非純量元素，使用 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 函式：

- 使用巢狀或重復資料行作為第一個參數
- 第二個參數則使用 [JSON 路徑](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)，該路徑會指定要存取的元素或屬性

請參閱下面的語法片段：

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

您可以在[查詢 Parquet 巢狀型別](query-parquet-nested-types.md#access-elements-from-repeated-columns)一文中，找到用來從重復資料行中存取元素的查詢範例。

## <a name="query-samples"></a>查詢範例

您可以使用範例查詢來深入了解如何查詢各種類型的資料。

### <a name="tools"></a>工具

要發出查詢所需的工具：
    - Azure Synapse Studio (預覽)
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>示範設定

您的第一個步驟是 **建立資料庫**，您將在其中執行查詢。 然後，您將在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)，將物件初始化。 

此安裝指令碼會建立資料來源、資料庫範圍認證，以及用來讀取這些範例中資料的外部檔案格式。

> [!NOTE]
> 資料庫僅用於檢視中繼資料而非實際資料。  請記下您使用的資料庫名稱以供稍後使用。

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>所提供的示範資料

示範資料包含下列資料集：

- NYC 計程車 - 黃色計程車車程記錄 - 公用 NYC 資料集 (CSV 和 Parquet 格式) 的一部分
- 以 CSV 格式填入的資料集
- 具有巢狀資料行的範例 Parquet 檔案
- JSON 格式的書籍

| 資料夾路徑                                                  | 描述                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | CSV 格式資料的父資料夾                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | 有不同 CSV 格式人口資料檔案的資料夾。 |
| /csv/taxi/                                                   | 有 CSV 格式 NYC 公用資料檔案的資料夾              |
| /parquet/                                                    | Parquet 格式資料的父資料夾                     |
| /parquet/taxi                                                | 使用 Hive/Hadoop 資料分割配置，依年份和月份來分割的 Parquet 格式 NYC 公用資料檔案。 |
| /parquet/nested/                                             | 具有巢狀資料行的範例 Parquet 檔案                     |
| /json/                                                       | JSON 格式資料的父資料夾                        |
| /json/books/                                                 | 具有書籍資料的 JSON 檔案                                   |


## <a name="next-steps"></a>後續步驟

如需如何查詢不同檔案類型以及建立和使用檢視的詳細資訊，請參閱下列文章：

- [查詢 CSV 檔案](query-single-csv-file.md)
- [查詢 Parquet 檔案](query-parquet-files.md)
- [查詢 JSON 檔案](query-json-files.md)
- [查詢巢狀值](query-parquet-nested-types.md)
- [查詢資料夾和多個 CSV 檔案](query-folders-multiple-csv-files.md)
- [在查詢中使用檔案中繼資料](query-specific-files.md)
- [建立和使用檢視](create-use-views.md)
