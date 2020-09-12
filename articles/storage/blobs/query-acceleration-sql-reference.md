---
title: 查詢加速 SQL 語言參考
titleSuffix: Azure Storage
description: 瞭解如何使用查詢加速 sql 語法。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: affddf7367f58107106ae07a07b8baedac73e251
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659559"
---
# <a name="query-acceleration-sql-language-reference"></a>查詢加速 SQL 語言參考

查詢加速支援類似 ANSI SQL 的語言，可用於表示對 blob 內容的查詢。  查詢加速 SQL 方言是 ANSI SQL 的子集，其中包含一組有限的支援資料類型、運算子等，但也會在 ANSI SQL 上擴充，以支援以階層式半結構化資料格式（例如 JSON）進行的查詢。 

## <a name="select-syntax"></a>SELECT 語法

查詢加速所支援的唯一 SQL 語句是 SELECT 語句。 這個範例會傳回運算式傳回 true 的每個資料列。

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

若為 CSV 格式的資料， *資料表* 必須是 `BlobStorage` 。  這表示查詢將針對 REST 呼叫中指定的任何 blob 執行。
針對 JSON 格式的資料， *資料表* 是「資料表描述項」。   請參閱本文的「 [資料表描述](#table-descriptors) 項」一節。

在下列範例中，針對 WHERE *運算式* 傳回 true 的每個資料列，此語句會傳回評估每個投影運算式所產生的新資料列。


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

下列範例會傳回匯總計算 (例如：特定資料行的平均值) 在 *運算式* 傳回 true 的每個資料列上。 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

下列範例會傳回適當的位移來分割 CSV 格式的 blob。  請參閱本文的 [Sys. 分割](#sys-split) 一節。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>資料類型

|資料類型|描述|
|---------|-------------------------------------------|
|INT      |64 位元帶正負號的整數。                     |
|FLOAT    |64位 ( 「雙精確度」 ) 浮點數。|
|STRING   |可變長度的 Unicode 字串。            |
|timestamp|時間點。                           |
|BOOLEAN  |True 或 False。                             |

從 CSV 格式化的資料讀取值時，所有值都會讀取為字串。  您可以使用 CAST 運算式，將字串值轉換成其他類型。  根據內容，值可能會隱含地轉換成其他類型。 如需詳細資訊，請參閱 [ (transact-sql) 的資料類型優先順序 ](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql)。

## <a name="expressions"></a>運算式

### <a name="referencing-fields"></a>參考欄位

若為 JSON 格式的資料，或具有標頭資料列的 CSV 格式資料，欄位可以依名稱參考。  功能變數名稱可以用引號括住或不加引號。 加上引號的功能變數名稱會以雙引號字元括住 ( ") 、可以包含空格，而且會區分大小寫。  未加引號的功能變數名稱不區分大小寫，且不能包含任何特殊字元。

在 CSV 格式的資料中，也可以依序數參考欄位，前面加上底線 (_) 字元。  例如，第一個欄位可以參考為 _1，或第十個欄位可以參考為 _11。  依序數參考欄位適用于 CSV 格式的資料，其中不包含標頭資料列，在此情況下，參考特定欄位的唯一方式是依序數。

### <a name="operators"></a>操作員

以下是支援的標準 SQL 運算子：

|運算子|描述|
|--|--|
|[=](https://docs.microsoft.com/sql/t-sql/language-elements/equals-transact-sql)    |比較兩個運算式是否相等 (比較運算子)。|
|[!=](https://docs.microsoft.com/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |測試一個運算式是否等於另一個運算式 (比較運算子)。|
|[<>](https://docs.microsoft.com/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |比較兩個不等於 (比較運算子) 的運算式。|
|[<](https://docs.microsoft.com/sql/t-sql/language-elements/less-than-transact-sql)    |比較兩個運算式是否小於 (比較運算子) 。|
|[<=](https://docs.microsoft.com/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |比較兩個運算式是否小於或等於 (比較運算子) 。|
|[>](https://docs.microsoft.com/sql/t-sql/language-elements/greater-than-transact-sql)    |比較兩個運算式是否大於 (比較運算子) 。 |
|[>=](https://docs.microsoft.com/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |比較大於或等於 (比較運算子) 的兩個運算式。|
|[+](https://docs.microsoft.com/sql/t-sql/language-elements/add-transact-sql)    |兩個數字相加。 這個加法算術運算子也可以在日期中加入數字 (表示天數)。|
|[-](https://docs.microsoft.com/sql/t-sql/language-elements/subtract-transact-sql)    |執行兩個數字的減法運算 (算術減法運算子)。 |
|[/](https://docs.microsoft.com/sql/t-sql/language-elements/divide-transact-sql)    |將一個數字除以另一個數字 (算術除法運算子)。|
|[*](https://docs.microsoft.com/sql/t-sql/language-elements/multiply-transact-sql)    |乘以兩個運算式 (算術乘法運算子)。|
|[%](https://docs.microsoft.com/sql/t-sql/language-elements/modulo-transact-sql)    |傳回某個數值除以另一個數值的餘數。|
|[AND](https://docs.microsoft.com/sql/t-sql/language-elements/bitwise-and-transact-sql)    |在兩個整數值之間，執行位元邏輯 AND 運算。|
|[OR](https://docs.microsoft.com/sql/t-sql/language-elements/bitwise-or-transact-sql)    |在兩個指定整數值之間執行位邏輯 OR 運算，以轉譯成 Transact-sql 語句內的二進位運算式。|
|[NOT](https://docs.microsoft.com/sql/t-sql/language-elements/not-transact-sql)    |執行布林輸入的否定運算。|
|[CAST](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)    |將一種資料類型的運算式轉換成另一種。|
|[BETWEEN](https://docs.microsoft.com/sql/t-sql/language-elements/between-transact-sql)    |指定要測試的範圍。|
|[IN](https://docs.microsoft.com/sql/t-sql/language-elements/in-transact-sql)    |判斷指定的值是否符合子查詢或清單中的任何值。|
|[NULLIF](https://docs.microsoft.com/sql/t-sql/language-elements/nullif-transact-sql)    |如果兩個指定的運算式相等，便傳回 Null 值。|
|[COALESCE](https://docs.microsoft.com/sql/t-sql/language-elements/coalesce-transact-sql)    |依序評估引數，並傳回一開始未評估為 Null 之第一個運算式的目前值。|

如果運算子左邊和右邊的資料類型不同，則會根據此處指定的規則來執行自動轉換： [資料類型優先順序 (transact-sql) ](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql)。

查詢加速 SQL 語言僅支援在該文章中討論的一小部分資料類型。  請參閱本文的 [資料類型](#data-types) 一節。

### <a name="casts"></a>轉換

根據此處的規則，查詢加速 SQL 語言支援轉換運算子： [資料類型轉換 (資料庫引擎) ](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine)。  

查詢加速 SQL 語言僅支援在該文章中討論的一小部分資料類型。  請參閱本文的 [資料類型](#data-types) 一節。

### <a name="string-functions"></a>字串函數

查詢加速 SQL 語言支援下列標準 SQL 字串函數：

|函式|描述|
|--|--|
|CHAR_LENGTH    | 如果字串運算式是字元資料類型，則傳回字串運算式的長度（以字元為單位）。否則，會傳回字串運算式的長度（以位元組為單位）， (不小於位數除以 8) 的最小整數。  (此函數與 CHARACTER_LENGTH 函數相同。 ) |
|CHARACTER_LENGTH    |如果字串運算式是字元資料類型，則傳回字串運算式的長度（以字元為單位）。否則，會傳回字串運算式的長度（以位元組為單位）， (不小於位數除以 8) 的最小整數。  (此函式與 CHAR_LENGTH 函數相同|
|[LOWER](https://docs.microsoft.com/sql/t-sql/functions/lower-transact-sql)    |將大寫字元資料轉換成小寫之後，傳回字元運算式。|
|[UPPER](https://docs.microsoft.com/sql/t-sql/functions/upper-transact-sql)    |傳回小寫字元資料轉換成大寫的字元運算式。|
|[SUBSTRING](https://docs.microsoft.com/sql/t-sql/functions/substring-transact-sql)    |傳回 SQL Server 中的字元、二進位、文字或影像運算式的一部分。|
|[TRIM](https://docs.microsoft.com/sql/t-sql/functions/trim-transact-sql)    |從字串的開頭和結尾移除空白字元 char (32) 或其他指定的字元。|
|LEADING    |描述|
|TRAILING    |描述|

以下是一些範例：

|函式|範例|結果|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>日期函式

以下是支援的標準 SQL 日期函數：

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

我們目前會轉換 [標準 IS08601 的所有日期格式](https://www.w3.org/TR/NOTE-datetime)。 

#### <a name="date_add-function"></a>DATE_ADD 函式

查詢加速 SQL 語言支援函數的年、月、日、小時、分鐘、秒 ``DATE_ADD`` 。

範例：

' ' sql DATE_ADD (datepart，quantity，timestamp) DATE_ADD ( ' minute '，1，CAST ( ' 2017-01-02T03：04： 05.006 Z ' 作為時間戳記) 
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>解壓縮函式

針對函式所支援的日期部分以外的解壓縮 ``DATE_ADD`` ，查詢加速 SQL 語言支援 timezone_hour，並 timezone_minute 為日期部分。

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
|yy               |以2位數格式表示的年份-1999 為 ' 99 '|
|y                |以4位數格式表示的年份               |
|yyyy             |以4位數格式表示的年份               |
|M                |年中的月-1                    |
|MM               |零填補的月份–01               |
|MMM              |Abbr. 年中的月-一月            |
|MMMM             |全月-5 月                      |
|d                |每月日 (1-31)                   |
|dd               |每月零填補的日期 (01-31)      |
|a                |AM 或 PM                             |
|h                |每小時 (1-12)                    |
|hh               |零填補時數的 od 日 (01-12)      |
|H                |每小時 (0-23)                    |
|HH               |一天零填補的小時 (00-23)       |
|m                | (0-59) 的每小時分鐘數                |
|mm               |以零填補的分鐘 (00-59)            |
|s                | (0-59) 的第二分鐘             |
|ss               |以零填補的秒數 (00-59)           |
|S                |秒數的小數 (0.1-0.9)         |
|SS               |秒數的小數 (0.01-0.99)       |
|Sss              |秒數的小數 (0.001-0.999)     |
|X                |位移（小時）                      |
|XX 或 XXXX       |以小時和分鐘為單位的位移 (+ 0430)   |
|XXX 或 XXXXX     |以小時和分鐘為單位的位移 (-07:00)  |
|x                |以小時為單位的位移 (7)                   |
|xx 或 xxxx       |以小時和分鐘為單位的位移 (+ 0530)     |
|Xxx 或 xxxxx     |以小時和分鐘為單位的位移 (+ 05:30)    |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP 函式

只支援 IS08601 格式。

範例：

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> 您也可以使用 ``UTCNOW`` 函數來取得系統時間。


## <a name="aggregate-expressions"></a>匯總運算式

SELECT 語句可包含一或多個投射運算式或單一匯總運算式。  以下是支援的匯總運算式：

|運算式|描述|
|--|--|
|[計數 (\*) ](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |傳回符合述詞運算式的記錄數目。|
|[計算 (運算式) ](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |傳回運算式為非 null 的記錄數目。|
|[平均 (運算式) ](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql)    |傳回運算式之非 null 值的平均值。|
|[最小 (運算式) ](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql)    |傳回運算式的最小非 null 值。|
|[MAX (運算式](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql)    |傳回運算式的最大非 null 值。|
|[SUM (運算式) ](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql)    |傳回運算式的所有非 null 值的總和。|

### <a name="missing"></a>失蹤

``IS MISSING``運算子是查詢加速 SQL 語言所支援的唯一非標準。  針對 JSON 資料，如果特定輸入記錄中遺漏某個欄位，則 [運算式] 欄位 ``IS MISSING`` 將會評估為布林值 true。

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>資料表描述項

若為 CSV 資料，資料表名稱一律為 `BlobStorage` 。  例如：

```sql
SELECT * FROM BlobStorage
```

針對 JSON 資料，有其他選項可供使用：

```sql
SELECT * FROM BlobStorage[*].path
```

這可讓您查詢 JSON 資料的子集。

針對 JSON 查詢，您可以在 FROM 子句的一部分提及路徑。 這些路徑將有助於剖析 JSON 資料的子集。 這些路徑可以參考 JSON 陣列和物件值。

讓我們以更詳細的範例來瞭解這一點。

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

您可能只對 `warehouses` 上述資料的 JSON 物件有興趣。 `warehouses`物件是 JSON 陣列類型，因此您可以在 FROM 子句中提及。 您的範例查詢看起來會像這樣。

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

此查詢會取得所有欄位，但只會選取緯度。

如果您只想要存取 `dimensions` JSON 物件值，您可以在查詢中使用參考該物件。 例如：

```sql
SELECT length FROM BlobStorage[*].dimensions
```

這也會限制您存取物件的成員 `dimensions` 。 如果您想要存取 JSON 欄位的其他成員和 JSON 物件的內部值，您可以使用如下列範例所示的查詢：

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage 和 BlobStorage [ \* ] 兩者都會參考整個物件。 但是，如果您在 FROM 子句中有一個路徑，則您必須使用 BlobStorage [ \* ]. path

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys. 分割

這是 SELECT 語句的特殊形式，僅適用于 CSV 格式的資料。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

如果您想要下載然後以批次方式處理 CSV 資料記錄，請使用此語句。 如此一來，您就可以平行處理記錄，而不必一次下載所有記錄。 此語句不會傳回 CSV 檔案中的記錄。 相反地，它會傳回批次大小的集合。 然後，您可以使用每個批次大小來取得一批資料記錄。 

使用 *split_size* 參數指定您想要每個批次包含的位元組數目。 例如，如果您想要一次只處理 10 MB 的資料，則語句看起來會像這樣： `SELECT sys.split(10485760)FROM BlobStorage` 因為 10 mb 等於10485760個位元組。 每個批次最多可包含大小超過 10 MB 的記錄。 

在大部分的情況下，每個批次的大小會稍微高於您指定的數目。 這是因為批次不能包含部分記錄。 如果批次中的最後一筆記錄在臨界值結束之前開始，批次將會比較大，使其可以包含完整的記錄。 最後一個批次的大小可能會比您指定的大小還小。

>[!NOTE]
> Split_size 必須至少為 10 MB (10485760) 。

## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage 查詢加速](data-lake-storage-query-acceleration.md)
- [使用 Azure Data Lake Storage 查詢加速來篩選資料](data-lake-storage-query-acceleration-how-to.md)

