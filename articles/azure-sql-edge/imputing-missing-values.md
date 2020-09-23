---
title: 填滿時間間距並輸入遺漏的值-Azure SQL Edge
description: 瞭解如何在 Azure SQL Edge 中填滿時間間距並輸入遺漏值
keywords: SQL Edge、時間序列
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 03896f4f7aa4e6efc78b498406e79a299318ed7a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934051"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>填滿時間間距並輸入遺漏值 

處理時間序列資料時，時間序列資料通常可能會遺漏屬性的值。 這也可能是因為資料的本質，或是資料收集中斷的緣故，資料集中有時間 *差距* 。

例如，當收集智慧型裝置的能源使用統計資料時，每次裝置無法運作時，使用量統計資料中會有間距。 同樣地，在機器遙測資料收集案例中，可能會將不同的感應器設定為以不同的頻率發出資料，而導致感應器遺失值。 例如，如果有兩個感應器、電壓和壓力，分別設定為 100 Hz 和 10-Hz 頻率，則電壓感應器會每隔1百分之一發出資料一次，而壓力感應器只會每十秒發出一次資料。

下表描述以一秒間隔收集的機器遙測資料集。 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

先前的資料集有兩個重要的特性。 

- 資料集不包含任何與數個時間戳記、、、和相關的資料點 `2020-09-07 06:14:47.000` `2020-09-07 06:14:48.000` `2020-09-07 06:14:50.000` `2020-09-07 06:14:53.000` `2020-09-07 06:14:55.000` 。 這些時間戳記是資料集中的 *間距* 。  
- 針對電壓和壓力讀數，有遺漏值（表示為 `null` ）。 

## <a name="gap-filling"></a>間距填滿 

間距填滿是一種技術，可協助建立連續、排序的時間戳記集，以簡化時間序列資料的分析。 在 Azure SQL Edge 中，在時間序列資料集中填滿間距最簡單的方式，就是定義包含所需時間分佈的臨時表，然後對 `LEFT OUTER JOIN` `RIGHT OUTER JOIN` 資料集資料表進行或操作。 

`MachineTelemetry`使用以上所示的資料做為範例，您可以使用下列查詢來產生連續、排序的時間戳記集以進行分析。 

> [!NOTE]
> 下列查詢會產生遺漏的資料列，以及屬性的時間戳記值和 `null` 值。 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
上述查詢會產生下列輸出，其中包含指定範圍內的所有 *一秒* 時間戳記。

結果集如下

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>輸入遺漏值

上述查詢會產生資料分析的遺漏時間戳記，不過，它不會取代任何遺漏的值 (以 null) 表示 `voltage` 和 `pressure` 讀數。 在 Azure SQL Edge 中，已將新的語法新增至 T-sql 和函式，這些函式會 `LAST_VALUE()` `FIRST_VALUE()` 根據資料集內的先前或下列值，提供插補遺漏值的機制。 

新的語法會將 `IGNORE NULLS` and `RESPECT NULLS` 子句加入至和函式 `LAST_VALUE()` `FIRST_VALUE()` 。 下列對 `MachineTelemetry` 資料集的查詢會使用 last_value 函式來計算遺漏值，其中遺漏值會取代為資料集中最後觀察到的值。

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
結果集如下

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

下列查詢會使用和函數來插補遺漏值 `LAST_VALUE()` `FIRST_VALUE` 。 若為，輸出資料行會以 `ImputedVoltage` 最後觀察到的值取代遺漏值，而針對輸出資料行，則 `ImputedPressure` 會以資料集內的下一個觀察值取代遺漏值。 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
結果集如下

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> 上述查詢會使用 `FIRST_VALUE()` 函數，以下一個觀察到的值取代遺漏值。 使用函數搭配子句可以達成相同的結果 `LAST_VALUE()` `ORDER BY <ordering_column> DESC` 。

## <a name="next-steps"></a>下一步 

- [FIRST_VALUE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/first-value-transact-sql?toc=/azure/azure-sql-edge/toc.json)
- [LAST_VALUE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/last-value-transact-sql?toc=/azure/azure-sql-edge/toc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [彙總函式 (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/aggregate-functions-transact-sql)
