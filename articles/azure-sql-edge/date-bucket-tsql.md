---
title: Date_Bucket (Transact-sql) -Azure SQL Edge
description: 瞭解如何在 Azure SQL Edge 中使用 Date_Bucket
keywords: Date_Bucket, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 896caae2dfd79c4678ffb34c531fb56835e9bd66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886836"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

`origin` `1900-01-01 00:00:00.000` 如果未指定原始參數，此函式會傳回對應到每個 datetime 值區開頭的 datetime 值，從參數定義的時間戳記，或預設的原始值。 

如需所有 Transact-SQL 日期和時間資料類型與函式的概觀，請參閱[日期和時間資料類型與函式 (Transact-SQL)](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/)。

[Transact-SQL 語法慣例](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

## <a name="syntax"></a>語法

```sql
DATE_BUCKET (datePart, number, date, origin)
```

## <a name="arguments"></a>引數

*datePart*

與 ‘number’ 參數搭配使用的 *date* 部分。 例如 年、月、分、秒等等。

> [!NOTE]
> `DATE_BUCKET` 不會接受 *datePart* 引數的使用者定義變數對等項目。
  
|*datePart*|縮寫|  
|---|---|
|**day**|**dd**, **d**|  
|**week**|**wk**, **ww**|  
|**hour**|**hh**|  
|**minute**|**mi**, **n**|  
|**second**|**ss**, **s**|  
|**millisecond**|**ms**|  

*number*

決定值區寬度並與 *datePart* 引數結合的整數。 這代表從原始時間起算的 dataPart 值區寬度。 **`This argument cannot be a negative integer value`**. 

*date*

可解析成下列其中一個值的運算式：

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **time**

針對 *date*，`DATE_BUCKET` 會接受資料行運算式、運算式或使用者定義的變數，前提是其必須解析為上述任何資料類型。

**來源** 

可解析成下列其中一個值的選擇性運算式：

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **time**

的資料類型 `Origin` 應該符合參數的資料類型 `Date` 。 

`DATE_BUCKET` 如果未指定函式的原始值，則使用預設的原始日期值 `1900-01-01 00:00:00.000` ，亦即 12:00 AM，于1月 1 1900 日星期一。

## <a name="return-type"></a>傳回類型

這個方法的傳回值資料類型為動態。 傳回型別取決於提供給 `date` 的引數而定。 如果提供有效的輸入資料類型給 `date`，`DATE_BUCKET` 便會傳回相同的資料類型。 如果為 `date` 參數指定了字串常值，`DATE_BUCKET` 就會引發錯誤。

## <a name="return-values"></a>傳回值

### <a name="understanding-the-output-from-date_bucket"></a>了解 `DATE_BUCKET` 的輸出

`Date_Bucket` 會傳回與 datePart 和 number 參數對應的最新日期或時間值。 例如在下列運算式中，`Date_Bucket` 會傳回 `2020-04-13 00:00:00.0000000` 的輸出值，因為輸出的計算依據是從預設原始時間 `1900-01-01 00:00:00.000` 起算為期一週的值區。 值 `2020-04-13 00:00:00.0000000` 是在原始值 `1900-01-01 00:00:00.000` 後的 6276 週。 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

針對下列所有運算式，系統將會傳回相同的輸出值 `2020-04-13 00:00:00.0000000`。 這是因為 `2020-04-13 00:00:00.0000000` 是在原始日期後的 6276 週，而 6276 可由 2、3、4 和 6 整除。

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

下列運算式的輸出是 `2020-04-06 00:00:00.0000000` 預設源時間的6275周 `1900-01-01 00:00:00.000` 。

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

下列運算式的輸出是 `2020-06-09 00:00:00.0000000` 從指定的來源時間起的75周 `2019-01-01 00:00:00` 。

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(wk, 5, @date, @origin)
```

## <a name="datepart-argument"></a>datepart 引數

**dayofyear**、**day** 和 **weekday** 都會傳回相同的值。 每個 *datepart* 及其縮寫都會傳回相同的值。
  
## <a name="number-argument"></a>number 引數

*number* 引數不得超過正數 **int** 值的範圍。 在下列陳述式中，*number* 引數超過 **int** 的範圍 (超過 1)。 下列陳述式會傳回下列錯誤訊息："`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

如果將負數的 number 值傳遞給 `Date_Bucket` 函式，則會傳回下列錯誤。 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>date 引數  

`DATE_BUCKET` 會傳回與 `date` 引數的資料類型對應的基底值。 在下列範例中，系統會傳回具有 datetime2 資料類型的輸出值。 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="origin-argument"></a>來源引數  

和引數的資料型別 `origin` `date` 必須相同。 如果使用不同的資料類型，將會產生錯誤。

## <a name="remarks"></a>備註

在下列子句中使用 `DATE_BUCKET`：

+ GROUP BY
+ HAVING
+ 排序依據
+ SELECT \<list>
+ WHERE

## <a name="examples"></a>範例

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>A. 使用值區寬度 1 從原始時間計算 Date_Bucket

這些陳述式各自會從原始時間以值區寬度 1 遞增 *date_bucket*：

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

以下為結果集。

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. 使用運算式當做 number 和 date 參數的引數

這些範例會使用不同的運算式類型，作為 *number* 和 *date* 參數的引數。 這些範例是使用 'AdventureWorksDW2017' 資料庫所建置的。
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>指定使用者自訂變數成為 number 和 date  

此範例會將使用者定義變數指定為 *number* 和 *date* 的引數：
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

以下為結果集。

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>指定資料行成為 date

在下列範例中，我們會計算依每週日期值區分組的 OrderQuantity 總和和 UnitPrice 總和。
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

以下為結果集。
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>指定純量系統函數成為 date

這個範例會針對 *date* 指定 `SYSDATETIME`。 傳回的精確值取決於陳述式執行的日期和時間：
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

以下為結果集。

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>指定純量子查詢和純量函數成為 number 和 date

此範例會使用純量子查詢 `MAX(OrderDate)`，作為 *number* 和 *date* 的引數。 `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` 會作為 number 參數的人工引數，以示範如何從值清單中選取 *number* 引數。
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>指定數值運算式和純量系統函數成為 number 和 date

此範例使用數值運算式 ((10/2))，和純量系統函數 (SYSDATETIME)，做為 number 和 date 的引數。
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>指定彙總視窗函數成為 number

此範例會使用彙總視窗函式，作為 *number* 的引數。
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 
### <a name="c-using-a-non-default-origin-value"></a>C. 使用非預設的原始值

這個範例會使用非預設的 orgin 值來產生日期值區。 

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(hh, 2, @date, @origin)
```

## <a name="see-also"></a>另請參閱

[CAST 和 CONVERT &#40;Transact-SQL&#41;](/sql/t-sql/functions/cast-and-convert-transact-sql/)
