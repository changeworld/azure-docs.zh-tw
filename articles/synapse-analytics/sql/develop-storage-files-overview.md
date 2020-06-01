---
title: 在 Synapse SQL 中使用 SQL 隨選 (預覽) 查詢儲存體檔案
description: 說明如何在 Synapse SQL 中使用 SQL 隨選 (預覽) 資源來查詢儲存體檔案。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 204fd1b1a0a2984886684bbabf33dc7e73c1b45c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653531"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>在 Synapse SQL 中使用 SQL 隨選 (預覽) 資源來查詢儲存體檔案

SQL 隨選 (預覽) 可讓您查詢資料湖中的資料。 其提供可搭載半結構化和非結構化資料查詢的 T-SQL 查詢介面區。

在查詢方面，可支援下列 T-SQL 層面：

- 完整的 [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 介面區，包括大部分的 SQL 函式及運算子等等。
- CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)) 會建立[外部資料表](develop-tables-external-tables.md)，然後將 Transact-SQL SELECT 陳述式的結果以平行方式匯出至 Azure 儲存體。

如需有關目前支援及不支援的功能詳細資訊，請參閱 [SQL 隨選概觀](on-demand-workspace-overview.md)一文。

當 Azure AD 使用者執行查詢時，預設是使用 Azure AD 傳遞驗證通訊協定來存取儲存體帳戶。 因此，系統會在儲存層級上模擬使用者並檢查權限。 您可以[控制儲存體存取](develop-storage-files-storage-access-control.md)，以符合您的需求。

## <a name="extensions"></a>延伸模組

為了在 Azure 儲存體檔案中進行資料就地查詢時有流暢的體驗，SQL 隨選使用了 [OPENROWSET](develop-openrowset.md) 函式與其他功能：

- [查詢多個檔案或資料夾](#query-multiple-files-or-folders)
- [PARQUET 檔案格式](#parquet-file-format)
- [使用分隔文字的其他選項 (欄位結束字元、資料列結束字元、逸出字元)](#additional-options-for-working-with-delimited-text)
- [讀取選擇的資料行子集](#read-a-chosen-subset-of-columns)
- [結構描述推斷](#schema-inference)
- [filename 函式](#filename-function)
- [filepath 函式](#filepath-function)
- [使用複雜類型和巢狀或重複的資料結構](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>查詢多個檔案或資料夾

若要對資料夾或資料夾集合中的一組檔案執行 T-SQL 查詢，並同時將其視為單一實體或資料列集，請提供單一資料夾的路徑，或代表一組檔案或資料夾的模式 (使用萬用字元)。

適用的規則如下：

- 模式可能會出現在目錄路徑或檔案名稱中。
- 相同目錄步驟或檔案名稱中可出現多個模式。
- 如果有多個萬用字元，則會在產生的檔案集中包含所有相符路徑內的檔案。

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

如需使用範例，請參閱[查詢資料夾和多個檔案](query-folders-multiple-csv-files.md)。

### <a name="parquet-file-format"></a>PARQUET 檔案格式

若要查詢 Parquet 來源資料，請使用 FORMAT = 'PARQUET'

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

如需使用範例，請參閱[查詢 Parquet 檔案](query-parquet-files.md)一文。

### <a name="additional-options-for-working-with-delimited-text"></a>使用分隔文字的其他選項

引進這些額外的參數是為了使用 CSV (分隔的文字) 檔案：

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' 會指定檔案中用來將本身和所有分隔符號值逸出的字元。 如果逸出字元後面接著本身或任何分隔符號值以外的值，讀取值時就會捨棄逸出字元。
無論 FIELDQUOTE 已啟用或未啟用，都會套用 ESCAPE_CHAR 參數。 其不會用來逸出引號字元。 為配合 Excel CSV 的行為，引號字元會以雙引號來逸出。
- FIELDTERMINATOR ='field_terminator' 會指定要使用的欄位結束字元。 預設的欄位結束字元為逗號 (" **,** ")
- ROWTERMINATOR ='row_terminator' 會指定要使用的資料列結束字元。 預設的資料列結束字元是新行字元： **\r\n**。

### <a name="read-a-chosen-subset-of-columns"></a>讀取選擇的資料行子集

若要指定您想要讀取的資料行，您可以在 OPENROWSET 陳述式內提供選擇性的 WITH 子句。

- 如果有 CSV 資料檔案，應提供資料行名稱及其資料類型，才能讀取所有資料行。 如果您想要讀取一小組資料行，請使用序數從原始資料檔案中依序挑選資料行。 資料行將會以加上順序的指派來繫結。
- 如果有 Parquet 資料檔案，請提供與原始資料檔案中資料行名稱相符的資料行名稱。 資料行將會依名稱來繫結。

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

如需範例，請參閱[在不指定所有資料行的情況下讀取 CSV 檔案](query-single-csv-file.md#returning-subset-of-columns)。

### <a name="schema-inference"></a>結構描述推斷

您可以藉由省略 OPENROWSET 陳述式中的 WITH 子句，來指示服務從基礎檔案中自動偵測 (推斷) 結構描述。

> [!NOTE]
> 這目前僅適用於 PARQUET 檔案格式。

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

請確定使用[適當的推斷資料類型](best-practices-sql-on-demand.md#check-inferred-data-types)來達到最佳效能。 

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

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>使用複雜類型和巢狀或重複的資料結構

為了在處理以巢狀或重複資料類型 (例如 [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) 檔案) 儲存的資料時擁有流暢的體驗，SQL 隨選已新增下列延伸模組。

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

若要從巢狀資料行 (例如 Struct) 存取巢狀元素，請使用「點標記法」將欄位名稱串連到路徑中。 在 OPENROWSET 函式的 WITH 子句中，提供路徑作為 column_name。

語法片段範例如下：

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

根據預設，OPENROWSET 函式會以 WITH 子句中提供的資料行名稱來比對來源欄位名稱和路徑。 您可以使用 WITH 子句來存取包含在相同來源 Parquet 檔案中，但位於不同巢狀層級的元素。

**傳回值**

- 針對不在巢狀型別群組中的所有 Parquet 類型，函式會從指定的專案和指定的路徑傳回純量值 (例如 int、decimal 和 Varchar)。
- 如果路徑指向屬於巢狀型別的元素，則函式會從指定路徑的最上層元素開始傳回 JSON 片段。 JSON 片段的類型為 varchar(8000)。
- 如果在指定的 column_name 中找不到屬性，則函數會傳回錯誤。
- 如果在指定的 column_path 中找不到屬性，則視[路徑模式](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)而定，函式會在 strict 模式中傳回錯誤，而在 lax 模式中則會傳回 null。

如需查詢範例，請參閱[查詢 Parquet 巢狀型別](query-parquet-nested-types.md#access-elements-from-nested-columns)一文中的＜從巢狀資料行存取元素＞一節。

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

## <a name="next-steps"></a>後續步驟

如需如何查詢不同檔案類型以及建立和使用檢視的詳細資訊，請參閱下列文章：

- [查詢單一 CSV 檔案](query-single-csv-file.md)
- [查詢 Parquet 檔案](query-parquet-files.md)
- [查詢 JSON 檔案](query-json-files.md)
- [查詢 Parquet 巢狀型別](query-parquet-nested-types.md)
- [查詢資料夾和多個 CSV 檔案](query-folders-multiple-csv-files.md)
- [在查詢中使用檔案中繼資料](query-specific-files.md)
- [建立和使用檢視](create-use-views.md)
