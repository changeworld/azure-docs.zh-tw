---
title: 查詢加速 SQL 語言參考（預覽）
titleSuffix: Azure Storage
description: 瞭解如何使用查詢加速 sql 語法。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: 3408970bcf5e34ce9f0f0afe9e723b4877dcd694
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84193398"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>查詢加速 SQL 語言參考（預覽）

查詢加速支援類似 ANSI SQL 的語言，用來表示對 blob 內容的查詢。  查詢加速 SQL 用語是 ANSI SQL 的子集，具有一組有限的支援資料類型、運算子等，但它也會在 ANSI SQL 上展開，以支援以階層式半結構化資料格式（例如 JSON）進行查詢。 

> [!NOTE]
> 查詢加速功能處於公開預覽狀態，並可在加拿大中部和法國中部區域中取得。 若要查看限制，請參閱[已知問題](data-lake-storage-known-issues.md)一文。 若要註冊預覽，請參閱[此表單](https://aka.ms/adls/qa-preview-signup)。 

## <a name="select-syntax"></a>選取語法

查詢加速所支援的唯一 SQL 語句是 SELECT 語句。 這個範例會傳回運算式傳回 true 的每一個資料列。

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

若是 CSV 格式的資料，*資料表*必須是 `BlobStorage` 。  這表示查詢將會針對 REST 呼叫中指定的任何 blob 來執行。
針對 JSON 格式的資料，*資料表*是「資料表描述元」。   請參閱本文的[資料表描述](#table-descriptors)項一節。

在下列範例中，針對 WHERE*運算式*傳回 true 的每個資料列，這個語句會傳回從評估每個投影運算式所建立的新資料列。


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

下列範例會針對*運算式*傳回 true 的每個資料列，傳回匯總計算（例如，特定資料行的平均值）。 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

下列範例會傳回適當的位移，以分割 CSV 格式的 blob。  請參閱本文的[Sys. Split](#sys-split)一節。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>資料類型

|資料類型|描述|
|---------|-------------------------------------------|
|INT      |64 位元帶正負號的整數。                     |
|FLOAT    |64位（「雙精確度」）浮點數。|
|STRING   |可變長度的 Unicode 字串。            |
|timestamp|時間點。                           |
|BOOLEAN  |True 或 False。                             |

從 CSV 格式的資料讀取值時，所有的值都會讀取為字串。  您可以使用 CAST 運算式，將字串值轉換成其他類型。  視內容而定，值可能會隱含地轉換成其他類型。 如需詳細資訊，請參閱[資料類型優先順序（transact-sql）](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)。

## <a name="expressions"></a>運算式

### <a name="referencing-fields"></a>參考欄位

若為 JSON 格式的資料，或具有標頭資料列的 CSV 格式資料，則可能會依名稱參考欄位。  功能變數名稱可以加上引號或不帶引號。 加上引號的功能變數名稱會以雙引號字元（"）括住，其中可能包含空格，並且區分大小寫。  不具引號的功能變數名稱不區分大小寫，且不能包含任何特殊字元。

在 CSV 格式的資料中，欄位也可以由序數參考，並以底線（_）字元作為首碼。  例如，第一個欄位可能會被參考為 _1，或第十個欄位可能會被視為 _11。  依序數參考欄位適用于不包含標頭資料列的 CSV 格式資料，在此情況下，參考特定欄位的唯一方式是依序數。

### <a name="operators"></a>操作員

支援下列標準 SQL 運算子：

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

如果運算子左邊和右邊的資料類型不同，則會根據此處指定的規則來執行自動轉換：[資料類型優先順序（transact-sql）](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)。

查詢加速 SQL 語言僅支援該文章中所討論之資料類型的一小部分。  請參閱本文的[資料類型](#data-types)一節。

### <a name="casts"></a>轉換

根據這裡的規則，查詢加速 SQL 語言支援 CAST 運算子：[資料類型轉換（資料庫引擎）](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017)。  

查詢加速 SQL 語言僅支援該文章中討論的一小部分的資料類型。  請參閱本文的[資料類型](#data-types)一節。

### <a name="string-functions"></a>字串函數

查詢加速 SQL 語言支援下列標準 SQL 字串函數：

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

以下是一些範例：

|函式|範例|結果|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

[LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15)函數可協助您搜尋模式。 以下是一些使用[LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15)函數搜尋資料字串的範例 ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i `` 。

|查詢|範例|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>日期函式

支援下列標準 SQL 日期函數：

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

我們目前會轉換[標準 IS08601 的所有日期格式](https://www.w3.org/TR/NOTE-datetime)。 

#### <a name="date_add-function"></a>DATE_ADD 函式

查詢加速 SQL 語言支援函數的年、月、日、小時、分鐘、秒 ``DATE_ADD`` 。

範例：

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF 函式

查詢加速 SQL 語言支援函數的年、月、日、小時、分鐘、秒 ``DATE_DIFF`` 。

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>解壓縮函式

針對函式所支援的日期部分的摘錄 ``DATE_ADD`` ，查詢加速 SQL 語言支援 timezone_hour 和 timezone_minute 為日期部分。

範例：

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING 函式

範例：

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

下表描述您可以用來指定函數輸出格式的字串 ``TO_STRING`` 。

|格式字串    |輸出                               |
|-----------------|-------------------------------------|
|yy               |以2位數格式表示的年份–1999為 ' 99 '|
|y                |4位數格式的年份               |
|yyyy             |4位數格式的年份               |
|M                |年中的月份–1                    |
|MM               |零填補月份–01               |
|MMM              |Abbr. 年中的月份-JAN            |
|MMMM             |全月–可能                      |
|d                |月中的日（1-31）                  |
|dd               |月中的零填補日（01-31）     |
|a                |AM 或 PM                             |
|h                |一天中的小時（1-12）                   |
|hh               |零填補小時 od 日（01-12）     |
|H                |一天中的小時（0-23）                   |
|HH               |一天零填補的小時（00-23）      |
|m                |小時分鐘數（0-59）                |
|mm               |零填補分鐘（00-59）           |
|s                |秒數（0-59）             |
|ss               |零填補的秒數（00-59）          |
|S                |秒數的分數（0.1-0.9）        |
|SS               |秒數的分數（0.01-0.99）      |
|SSS              |秒數的分數（0.001-0.999）    |
|X                |位移（以小時為單位）                      |
|XX 或 XXXX       |以小時和分鐘為單位的位移（+ 0430）  |
|XXX 或 XXXXX     |以小時和分鐘為單位的位移（-07:00） |
|x                |位移（小時）（7）                  |
|xx 或 xxxx       |以小時和分鐘為單位的位移（+ 0530）    |
|Xxx 或 xxxxx     |以小時和分鐘為單位的位移（+ 05:30）   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP 函式

僅支援 IS08601 格式。

範例：

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> 您也可以使用 ``UTCNOW`` 函數來取得系統時間。


## <a name="aggregate-expressions"></a>匯總運算式

SELECT 語句可能包含一個或多個投射運算式或單一匯總運算式。  支援下列匯總運算式：

|運算式|描述|
|--|--|
|[COUNT （ \* ）](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |傳回符合述詞運算式的記錄數目。|
|[計數（運算式）](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |傳回運算式為非 null 的記錄數目。|
|[平均（運算式）](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |傳回 expression 的非 null 值的平均值。|
|[最小值（運算式）](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |傳回 expression 的最小非 null 值。|
|[最大值（運算式](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)）    |傳回 expression 的最大非 null 值。|
|[SUM （expression）](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |傳回運算式中所有非 null 值的總和。|

### <a name="missing"></a>遺漏

``IS MISSING``運算子是查詢加速 SQL 語言支援的唯一非標準。  針對 JSON 資料，如果特定輸入記錄中遺漏某個欄位，[運算式] 欄位 ``IS MISSING`` 將會評估為布林值 true。

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>資料表描述項

若是 CSV 資料，資料表名稱一律為 `BlobStorage` 。  例如：

```sql
SELECT * FROM BlobStorage
```

針對 JSON 資料，有其他選項可用：

```sql
SELECT * FROM BlobStorage[*].path
```

這可讓您查詢 JSON 資料的子集。

針對 JSON 查詢，您可以在 FROM 子句的一部分中提及路徑。 這些路徑可協助剖析 JSON 資料的子集。 這些路徑可以參考 JSON 陣列和物件值。

讓我們以更詳細的方式來瞭解這一點。

這是我們的範例資料：

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

您可能只對 `warehouses` 上述資料中的 JSON 物件感興趣。 `warehouses`物件是 JSON 陣列型別，因此您可以在 from 子句中提及。 您的範例查詢看起來會像這樣。

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

查詢會取得所有欄位，但只會選取緯度。

如果您只想要存取 `dimensions` JSON 物件值，您可以在查詢中使用參考該物件。 例如：

```sql
SELECT length FROM BlobStorage[*].dimensions
```

這也會限制您對物件成員的存取 `dimensions` 。 如果您想要存取 json 欄位的其他成員和 JSON 物件的內部值，則您可以使用如下列範例所示的查詢：

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage 和 BlobStorage [ \* ] 都會參考整個物件。 不過，如果您在 FROM 子句中有路徑，則必須使用 BlobStorage [ \* ]. path

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys. Split

這是 SELECT 語句的特殊形式，僅適用于 CSV 格式的資料。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

當您想要以批次方式下載並處理 CSV 資料記錄時，請使用此語句。 如此一來，您就可以平行處理記錄，而不需要一次下載所有記錄。 此語句不會從 CSV 檔案傳回記錄。 相反地，它會傳回批次大小的集合。 然後，您可以使用每個批次大小來取出一批資料記錄。 

使用*split_size*參數來指定您想要每個批次包含的位元組數目。 例如，如果您只想要一次處理 10 MB 的資料，您的語句看起來會像這樣： `SELECT sys.split(10485760)FROM BlobStorage` 因為 10 mb 等於10485760個位元組。 每個批次最多會包含符合這 10 MB 的記錄。 

在大部分情況下，每個批次的大小會稍微高於您指定的數目。 這是因為批次不能包含部分記錄。 如果批次中的最後一筆記錄在臨界值結束之前開始，批次將會變大，使其可以包含完整的記錄。 最後一個批次的大小可能會小於您指定的大小。

>[!NOTE]
> Split_size 至少必須為 10 MB （10485760）。

## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage 查詢加速（預覽）](data-lake-storage-query-acceleration.md)
- [使用 Azure Data Lake Storage 查詢加速來篩選資料（預覽）](data-lake-storage-query-acceleration-how-to.md)

