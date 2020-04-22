---
title: 查詢加速 SQL 語言參考(預覽)
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
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772115"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>查詢加速 SQL 語言參考(預覽)

查詢加速支援類似 ANSI SQL 的語言,用於對 blob 內容表示查詢。  查詢加速 SQL 方言是 ANSI SQL 的子集,具有有限的受支援數據類型、運算符等,但它也擴展了 ANSI SQL 以支援對分層半結構化數據格式(如 JSON)的查詢。 

> [!NOTE]
> 查詢加速功能處於公共預覽版中,並且在加拿大中部和法國中部區域可用。 要查看限制,請參閱[已知問題](data-lake-storage-known-issues.md)一文。 要在預覽版中註冊,請參閱[此表單](https://aka.ms/adls/qa-preview-signup)。 

## <a name="select-syntax"></a>選擇語法

查詢加速度支援的唯一 SQL 語句是 SELECT 語句。 此示例返回表達式返回 true 的每一行。

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

對 CSV 格式*table*的資料,`BlobStorage`表必須為 。  這意味著查詢將針對 REST 調用中指定的任何 Blob 運行。
對於 JSON 格式的數據,*表*是「表描述符」。   請參閱本文的[表描述符](#table-descriptors)部分。

在下面的示例中,對於 WHERE*表達式*返回 true 的每個行,此語句將返回一個新行,該行是從計算每個投影表達式時產生的。


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

下面的範例返回*表示式*返回 true 的每個行上的聚合計算(例如:特定列的平均值)。 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

下面的範例返回用於拆分 CSV 格式的 blob 的適當偏移量。  請參閱本文的[Sys.拆分](#sys-split)部分。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>資料類型

|資料類型|描述|
|---------|-------------------------------------------|
|INT      |64 位元帶正負號的整數。                     |
|FLOAT    |64 位("雙精度")浮點。|
|STRING   |可變長度 Unicode 字串。            |
|timestamp|時間點。                           |
|BOOLEAN  |True 或 False。                             |

從 CSV 格式的資料讀取值時,所有值都讀為字串。  可以使用 CAST 運算式將字串值轉換為其他類型的字串值。  根據上下文,可以隱式地轉換為其他類型的值。 有關詳細資訊,請參閱[數據類型優先順序(轉用 SQL)。](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)

## <a name="expressions"></a>運算式

### <a name="referencing-fields"></a>參考欄位

對於 JSON 格式的數據,或者具有標頭行的 CSV 格式數據,欄位可以按名稱引用。  欄位名稱可以引用或取消引用。 引用的欄位名稱以雙引號字元 ())括起來,可能包含空格,並且區分大小寫。  未引用的欄位名稱不區分大小寫,可能不包含任何特殊字元。

在 CSV 格式的資料中,字段也可以由具有下劃線 (*) 字元的預綴引用。  例如,第一個字段可以引用為 |1,或者第 11 個字段可以引用為 |11。  通過處理形式引用欄位對於不包含標頭行的 CSV 格式數據很有用,在這種情況下,引用特定欄位的唯一方法是按單位。

### <a name="operators"></a>操作員

支援以下標準 SQL 運算子:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

如果運算符的左側和右側的數據類型不同,則將根據此處指定的規則執行自動轉換:[數據類型優先順序(處理-SQL)。](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)

查詢加速 SQL 語言僅支援本文中討論的數據類型的一小部分。  請參閱本文的[「資料類型」](#data-types)部分。

### <a name="casts"></a>轉換

查詢加速 SQL 語言支援 CAST 運算子,根據此處的規則:[資料類型轉換(資料庫引擎)。](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017)  

查詢加速 SQL 語言僅支援本文中討論的數據類型的一小部分。  請參閱本文的[「資料類型」](#data-types)部分。

### <a name="string-functions"></a>字串函數

查詢加速 SQL 語言支援以下標準 SQL 字串函數:

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

下面是一些範例:

|函式|範例|結果|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

[LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15)函數可説明您搜索模式。 下面是一些使用[LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15)函數來搜索數據字``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``串 的範例。

|查詢|範例|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>日期函式

支援以下標準 SQL 日期函數:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

目前,我們轉換標準[IS08601 的所有日期格式](https://www.w3.org/TR/NOTE-datetime)。 

#### <a name="date_add-function"></a>DATE_ADD功能

查詢加速 SQL 語言支援函``DATE_ADD``數的 年、月、日、小時、分鐘、秒。

範例：

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF功能

查詢加速 SQL 語言支援函``DATE_DIFF``數的 年、月、日、小時、分鐘、秒。

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>擷取功能

對於``DATE_ADD``函數支援的日期部件以外的 EXTRACT,查詢加速 SQL 語言支援timezone_hour和timezone_minute作為日期部分。

範例：

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING功能

範例：

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

此表描述可用於指定函數的輸出格式的``TO_STRING``字串。

|格式字串    |輸出                               |
|-----------------|-------------------------------------|
|yy               |2 位元格式的年份 – 1999 年為"99"|
|y                |4 位元格式的年份               |
|yyyy             |4 位元格式的年份               |
|M                |一年月 = 1                    |
|MM               |零填滿月 = 01               |
|MMM              |Abbr. 一年月 - JAN            |
|MMMM             |整個月數 = 5 月                      |
|d                |月日 (1-31)                  |
|dd               |零填充月日 (01-31)     |
|a                |上午或下午                             |
|h                |一天中小時 (1-12)                   |
|hh               |零填滿小時 od 天 (01-12)     |
|H                |一天中小時 (0-23)                   |
|HH               |一天零加時 (00-23)      |
|m                |小時分鐘 (0-59)                |
|mm               |零填充分鐘 (00-59)           |
|s                |第二分鐘 (0-59)             |
|ss               |零填充秒數 (00-59)          |
|S                |秒數分數 (0.1-0.9)        |
|SS               |秒數分數 (0.01-0.99)      |
|Sss              |秒數分數 (0.001-0.999)    |
|X                |以小時表示偏移                      |
|XX 或 XXXX       |偏移(以小時和分鐘表示 )(+0430)  |
|XXX 或 XXXXX     |偏移(小時和分鐘)(-07:00) |
|x                |偏移(小時數)(7)                  |
|xx 或 xxx       |偏移(小時和分鐘)(+0530)    |
|Xxx 或 xxxxxx     |偏移(小時和分鐘)(+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP功能

僅支援 IS08601 格式。

範例：

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> 您還可以使用 函``UTCNOW``數 獲取系統時間。


## <a name="aggregate-expressions"></a>彙總運算式

SELECT 語句可以包含一個或多個投影表達式或單個聚合運算式。  支援以下集合式:

|運算是|描述|
|--|--|
|[計數(\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |返回與謂詞表達式匹配的記錄數。|
|[COUNT(運算式)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |返回表達式為非空的記錄數。|
|[平均(運算式)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |返回表達式的非空值的平均值。|
|[MIN(運算式)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |返回表達式的最小非空值。|
|[最大(運算式](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15))    |返回表達式的最大非空值。|
|[SUM(運算式)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |返回表達式的所有非空值的總和。|

### <a name="missing"></a>失蹤

運算子``IS MISSING``是查詢加速 SQL 語言支援的唯一非標準。  對於 JSON 數據,如果特定輸入記錄中缺少一個字段,則表達式``IS MISSING``欄位 將計算為布林值 true。

<a id="table-descriptors" />

## <a name="table-descriptors"></a>表描述子

對於 CSV 資料,表`BlobStorage`格名稱始終為 。  例如：

```sql
SELECT * FROM BlobStorage
```

對於 JSON 資料,還有其他選項可用:

```sql
SELECT * FROM BlobStorage[*].path
```

這允許查詢 JSON 數據的子集。

對於 JSON 查詢,您可以在 FROM 子句的一部分中提及路徑。 這些路徑將有助於分析 JSON 數據的子集。 這些路徑可以引用 JSON 數位和物件值。

讓我們舉一個例子來更詳細地理解這一點。

這是我們的範例資料:

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

您可能只對上述數據的`warehouses`JSON 物件感興趣。 該`warehouses`物件是 JSON 數位類型,因此您可以在 FROM 子句中提及這一點。 您的示例查詢可以如下所示。

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

查詢獲取所有欄位,但僅選擇緯度。

如果只想訪問`dimensions`JSON 物件值,可以使用查詢中引用該物件。 例如：

```sql
SELECT length FROM BlobStorage[*].dimensions
```

這還會限制您對`dimensions`對象成員的訪問。 如果要訪問 JSON 欄位的其他成員和 JSON 物件的內部值,則可以使用查詢,如以下範例所示:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> Blob 儲存及\*Blob 儲存* 都引用整個物件。 但是,如果在 FROM 子句中有一個路徑,則需要使用 BlobStorage]\*[路徑]

<a id="sys-split" />

## <a name="syssplit"></a>系統分割

這是 SELECT 語句的一種特殊形式,僅適用於 CSV 格式的數據。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

如果要下載,然後分批處理 CSV 數據記錄,請使用此語句。 這樣,您可以並行處理記錄,而無需一次下載所有記錄。 此語句不會從 CSV 檔返回記錄。 相反,它返回批處理大小的集合。 然後,可以使用每個批處理大小檢索一批數據記錄。 

使用*split_size*參數指定您希望每個批次處理包含的位元組數。 例如,如果一次只想處理 10 MB 的數據,則語句如下`SELECT sys.split(10485760)FROM BlobStorage`所示: 因為 10 MB 等於 10,485,760 字節。 每個批處理將包含盡可能多的記錄,以適應這些 10 MB。 

在大多數情況下,每個批次的大小將略高於您指定的數位。 這是因為批處理不能包含部分記錄。 如果批處理中的最後一個記錄在閾值結束之前開始,則批處理將更大,以便它可以包含完整的記錄。 最後一個批次處理的大小可能小於您指定的大小。

>[!NOTE]
> split_size必須至少為 10 MB (10485760)。

## <a name="see-also"></a>另請參閱

- [Azure 資料湖儲存查詢加速(預覽)](data-lake-storage-query-acceleration.md)
- [使用 Azure 資料湖儲存查詢加速篩選資料(預覽)](data-lake-storage-query-acceleration-how-to.md)

