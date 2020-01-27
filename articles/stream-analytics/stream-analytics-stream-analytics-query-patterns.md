---
title: Azure 串流分析中常見的查詢模式
description: 本文說明數個在 Azure 串流分析作業中有用的常見查詢模式和設計。
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982323"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Azure 串流分析中常見的查詢模式

Azure 串流分析的查詢會以類似 SQL 的查詢語言表達。 語言建構記載在[串流分析查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)指南中。 

查詢設計可以表達簡單的傳遞邏輯，將事件資料從某個輸入資料流程移至輸出資料存放區，也可以執行豐富的模式比對和時態性分析，以計算各種時間視窗的匯總，如同[使用串流分析指南建立 IoT 解決方案](stream-analytics-build-an-iot-solution-using-stream-analytics.md)中所述。 您可以聯結多個輸入的資料來結合串流事件，也可以對靜態參考資料進行查閱，以擴充事件值。 您也可以將資料寫入多個輸出。

本文概述以真實世界案例為基礎的數個常見查詢模式的解決方案。

## <a name="supported-data-formats"></a>支援的資料格式

Azure 串流分析可處理資料格式為 CSV、JSON 和 Avro 的事件。

JSON 和 Avro 可能包含巢狀物件 (記錄) 或陣列等複雜類型。 如需使用這些複雜資料類型的詳細資訊，請參閱[剖析 JSON 和 AVRO 資料](stream-analytics-parsing-json.md)一文。

## <a name="simple-pass-through-query"></a>簡單傳遞查詢

簡單的傳遞查詢可以用來將輸入資料流程資料複製到輸出中。 例如，如果包含即時車輛資訊的資料流程必須儲存在 SQL database 中以進行信件分析，簡單的傳遞查詢就會執行此作業。

**輸入**：

| 製作 | 時間 | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |"1000" |
| Make1 |2015-01-01T00：00： 02.0000000 Z |"2000" |

**輸出**：

| 製作 | 時間 | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |"1000" |
| Make1 |2015-01-01T00：00： 02.0000000 Z |"2000" |

**查詢**：

```SQL
SELECT
    *
INTO Output
FROM Input
```

**SELECT** * 查詢會投射傳入事件的所有欄位，並將其傳送至輸出。 同樣地， **SELECT**也可以用來從輸入中只投影必要欄位。 在此範例中，如果車輛*Make*和*Time*是唯一需要儲存的欄位，則可以在**SELECT**語句中指定這些欄位。

**輸入**：

| 製作 | 時間 | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |1000 |
| Make1 |2015-01-01T00：00： 02.0000000 Z |2000 |
| Make2 |2015-01-01T00：00： 04.0000000 Z |1500 |

**輸出**：

| 製作 | 時間 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make1 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 04.0000000 Z |

**查詢**：

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>一段時間的資料匯總

若要計算某個時間範圍內的資訊，可以將資料匯總在一起。 在此範例中，會針對每個特定汽車的時間，計算過去10分鐘內的計數。

**輸入**：

| 製作 | 時間 | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |1000 |
| Make1 |2015-01-01T00：00： 02.0000000 Z |2000 |
| Make2 |2015-01-01T00：00： 04.0000000 Z |1500 |

**輸出**：

| 製作 | 計數 |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**查詢**：

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

此匯總會*將*cars 分組，並每隔10秒計算一次。 輸出的*品牌* *和汽車數已*通過收費。

TumblingWindow 是用來將事件群組在一起的視窗化函數。 匯總可以套用至所有群組的事件。 如需詳細資訊，請參閱[視窗化函數](stream-analytics-window-functions.md)。

如需匯總的詳細資訊，請參閱[彙總函式](/stream-analytics-query/aggregate-functions-azure-stream-analytics)。

## <a name="data-conversion"></a>資料轉換

您可以使用**cast**方法，即時轉換資料。 例如，汽車權數可以從類型**Nvarchar （max）** 轉換為**Bigint**類型，並用於數值計算。

**輸入**：

| 製作 | 時間 | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |"1000" |
| Make1 |2015-01-01T00：00： 02.0000000 Z |"2000" |

**輸出**：

| 製作 | Weight |
| --- | --- |
| Make1 |3000 |

**查詢**：

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

使用**CAST**語句來指定其資料類型。 請參閱[資料類型（Azure 串流分析）](/stream-analytics-query/data-types-azure-stream-analytics)上支援的資料類型清單。

如需[資料轉換函數](/stream-analytics-query/conversion-functions-azure-stream-analytics)的詳細資訊。

## <a name="string-matching-with-like-and-not-like"></a>搭配 LIKE 和 NOT LIKE 的字串比對

**Like**和**NOT like**可以用來驗證欄位是否符合特定模式。 例如，您可以建立一個篩選準則，只傳回以字母 ' A ' 開頭並以數位9結尾的授權盤子。

**輸入**：

| 製作 | License_plate | 時間 |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |AAA-999 |2015-01-01T00：00： 02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00：00： 03.0000000 Z |

**輸出**：

| 製作 | License_plate | 時間 |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00：00： 02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00：00： 03.0000000 Z |

**查詢**：

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

使用**LIKE**語句來檢查**License_plate**域值。 其開頭必須是字母 ' A '，然後有任何零或多個字元的字串，結尾為數字9。

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>針對不同的案例/值指定邏輯 (CASE 陳述式)

**CASE**語句可以根據特定準則，為不同的欄位提供不同的計算。 例如，將通道 ' A ' 指派給*Make1*的汽車，並將通道 ' B ' 指派給任何其他品牌。

**輸入**：

| 製作 | 時間 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 03.0000000 Z |

**輸出**：

| 製作 |Dispatch_to_lane | 時間 |
| --- | --- | --- |
| Make1 |為 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |位元組 |2015-01-01T00：00： 02.0000000 Z |

**解決方案**：

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

**CASE**運算式會比較運算式與一組簡單運算式，以判斷其結果。 在此範例中，會將*Make1*的車輛分派給通道 ' A '，而任何其他品牌的車輛將會指派給通道 ' B '。

如需詳細資訊，請參閱[case 運算式](/stream-analytics-query/case-azure-stream-analytics)。

## <a name="send-data-to-multiple-outputs"></a>將資料傳送至多個輸出

您可以使用多個**SELECT**語句，將資料輸出到不同的輸出接收。 例如，一個**SELECT**可以輸出以閾值為基礎的警示，而另一個則可以將事件輸出至 blob 儲存體。

**輸入**：

| 製作 | 時間 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make1 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 03.0000000 Z |

**輸出 archiveoutput 輸出**：

| 製作 | 時間 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make1 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 03.0000000 Z |

**輸出 AlertOutput**：

| 製作 | 時間 | 計數 |
| --- | --- | --- |
| Make2 |2015-01-01T00：00： 10.0000000 Z |3 |

**查詢**：

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

**INTO**子句會告訴串流分析要將資料寫入哪個輸出。 第一個**SELECT**會定義傳遞查詢，以接收來自輸入的資料，並將其傳送至名為**archiveoutput 輸出**的輸出。 第二個查詢會先執行一些簡單的匯總和篩選，然後再將結果傳送至名為**AlertOutput**的下游警示系統輸出。

請注意， **WITH**子句可以用來定義多個子查詢區塊。 此選項的優點是對輸入來源開啟較少的讀取器。

**查詢**：

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

如需詳細資訊，請參閱[ **WITH**子句](/stream-analytics-query/with-azure-stream-analytics)。

## <a name="count-unique-values"></a>計算唯一值

[**計數**] 和 [**相異**] 可用來計算在某個時間範圍內，出現在資料流程中之唯一域值的數目。 您可以建立一個查詢，以計算在 2*秒的時間*範圍內通過收費亭的一種唯一的汽車。

**輸入**：

| 製作 | 時間 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make1 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |2015-01-01T00：00： 03.0000000 Z |

**輸出：**

| Count_make | 時間 |
| --- | --- |
| 2 |2015-01-01T00：00： 02.000 Z |
| 1 |2015-01-01T00：00： 04.000 Z |

**查詢：**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**Count （DISTINCT Make）** 會傳回一個時間範圍內 [ **Make** ] 資料行中相異值的計數。
如需詳細資訊，請參閱[ **COUNT** aggregate function](/stream-analytics-query/count-azure-stream-analytics)。

## <a name="calculation-over-past-events"></a>過去事件的計算

**LAG**函數可以用來查看時間範圍內的過去事件，並將它們與目前的事件進行比較。 例如，如果目前的汽車製造商與經過收費的最後一輛車不同，就可以輸出。

**輸入**：

| 製作 | 時間 |
| --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |
| Make2 |2015-01-01T00：00： 02.0000000 Z |

**輸出**：

| 製作 | 時間 |
| --- | --- |
| Make2 |2015-01-01T00：00： 02.0000000 Z |

**查詢**：

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

使用**LAG**查看輸入資料流程的一個事件，並抓取*make*值，並將它與目前事件的*make*值進行比較，然後輸出事件。

如需詳細資訊，請參閱[**LAG**](/stream-analytics-query/lag-azure-stream-analytics)。

## <a name="retrieve-the-first-event-in-a-window"></a>取出視窗中的第一個事件

**IsFirst**可以用來在時間範圍內取出第一個事件。 例如，每隔10分鐘就會輸出第一輛車資訊。

**輸入**：

| License_plate | 製作 | 時間 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00：00： 05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00：02： 17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00：05： 01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00：06： 00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00：09： 31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00：12： 02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00：13： 45.0000000 Z |

**輸出**：

| License_plate | 製作 | 時間 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00：00： 05.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00：12： 02.0000000 Z |

**查詢**：

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst**也可以分割資料，並將第一個事件計算為每隔10分鐘一*次找到的*每個特定車輛。

**輸出**：

| License_plate | 製作 | 時間 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00：00： 05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00：02： 17.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00：06： 00.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00：12： 02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00：13： 45.0000000 Z |

**查詢**：

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

如需詳細資訊，請參閱[**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics)。

## <a name="return-the-last-event-in-a-window"></a>傳回視窗中的最後一個事件

當系統即時使用事件時，沒有可判斷事件是否會在該時間範圍內到達最後一個的函式。 為了達到此目的，輸入資料流程必須與另一個專案聯結，其中事件的時間是該視窗上所有事件的最長時間。

**輸入**：

| License_plate | 製作 | 時間 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00：00： 05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00：02： 17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00：05： 01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00：06： 00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00：09： 31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00：12： 02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00：13： 45.0000000 Z |

**輸出**：

| License_plate | 製作 | 時間 |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00：09： 31.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00：13： 45.0000000 Z |

**查詢**：

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

查詢的第一個步驟是在10分鐘的視窗中尋找時間戳記上限，這是該視窗最後一個事件的時間戳記。 第二個步驟會聯結第一個查詢的結果與原始資料流程，以尋找符合每個視窗中最後一個時間戳記的事件。 

**DATEDIFF**是一種日期特有的函式，可比較並傳回兩個日期時間欄位之間的時間差。如需詳細資訊，請參閱[日期函數](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics)。

如需聯結資料流程的詳細資訊，請參閱[**聯結**](/stream-analytics-query/join-azure-stream-analytics)。


## <a name="correlate-events-in-a-stream"></a>將資料流程中的事件相互關聯

藉由使用**LAG**函數查看過去的事件，即可完成相同資料流程中的事件相互關聯。 例如，每次來自相同的兩個連續*車輛經過收費*，最後90秒就會產生輸出。

**輸入**：

| 製作 | License_plate | 時間 |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00：00： 01.0000000 Z |
| Make1 |AAA-999 |2015-01-01T00：00： 02.0000000 Z |
| Make2 |DEF-987 |2015-01-01T00：00： 03.0000000 Z |
| Make1 |GHI-345 |2015-01-01T00：00： 04.0000000 Z |

**輸出**：

| 製作 | 時間 | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00：00： 02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00：00： 01.0000000 Z |

**查詢**：

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**LAG**函數可以查看輸入資料流程中的一個事件，並抓取*make*值，並將其與目前事件的*make*值進行比較。  一旦符合條件，就可以使用**SELECT**語句中的**LAG**來投射上一個事件的資料。

如需詳細資訊，請參閱[LAG](/stream-analytics-query/lag-azure-stream-analytics)。

## <a name="detect-the-duration-between-events"></a>偵測事件之間的持續時間

一旦收到結束事件，就可以查看最後一個開始事件來計算事件的持續時間。 此查詢有助於判斷使用者在頁面或功能上花費的時間。

**輸入**：  

| User | 功能 | 活動 | 時間 |
| --- | --- | --- | --- |
| user@location.com |RightMenu |開始 |2015-01-01T00：00： 01.0000000 Z |
| user@location.com |RightMenu |結束 |2015-01-01T00：00： 08.0000000 Z |

**輸出**：  

| User | 功能 | 課程時間 |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**查詢**：

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

**最後**一個函式可以用來抓取特定條件中的最後一個事件。 在此範例中，條件是「啟動」類型的事件，並依使用者和功能分割依**分割**區的搜尋。 如此一來，在搜尋 Start 事件時，每個使用者和功能都會獨立處理。 [**限制持續時間**] 會限制在結束和啟動事件之間的時間，向後搜尋1小時。

## <a name="detect-the-duration-of-a-condition"></a>偵測某個條件的持續時間

對於跨越多個事件的條件， **LAG**函數可以用來識別該條件的持續時間。 例如，假設有個錯誤導致所有車輛的重量不正確 (超過 20,000 磅)，而且必須計算該錯誤的持續時間。

**輸入**：

| 製作 | 時間 | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00：00： 01.0000000 Z |2000 |
| Make2 |2015-01-01T00：00： 02.0000000 Z |25000 |
| Make1 |2015-01-01T00：00： 03.0000000 Z |26000 |
| Make2 |2015-01-01T00：00： 04.0000000 Z |25000 |
| Make1 |2015-01-01T00：00： 05.0000000 Z |26000 |
| Make2 |2015-01-01T00：00： 06.0000000 Z |25000 |
| Make1 |2015-01-01T00：00： 07.0000000 Z |26000 |
| Make2 |2015-01-01T00：00： 08.0000000 Z |2000 |

**輸出**：

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00：00： 02.000 Z |2015-01-01T00：00： 07.000 Z |

**查詢**：

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
第一個**SELECT**語句會將目前的權數量詞與先前的測量相互關聯，並將它與目前的測量一併投射在一起。 第二個**SELECT**會查看*previous_weight*小於20000的最後一個事件，其中目前的權數小於20000，而目前事件的*previous_weight*大於20000。

End_fault 是上一個事件發生錯誤的目前非錯誤事件，而 Start_fault 是之前的最後一個非錯誤事件。

## <a name="periodically-output-values"></a>定期輸出值

萬一發生異常或遺失的事件，可以從更多的資料輸入產生週期性間隔輸出。 例如，每隔 5 秒產生事件，報告最近所見的資料點。

**輸入**：

| 時間 | 值 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**輸出 (前 10 個資料列)** ：

| Window_end | Last_event。階段 | Last_event。Value |
| --- | --- | --- |
| 2014-01-01T14：01： 00.000 Z |2014-01-01T14：01： 00.000 Z |1 |
| 2014-01-01T14：01： 05.000 Z |2014-01-01T14：01： 05.000 Z |2 |
| 2014-01-01T14：01： 10.000 Z |2014-01-01T14：01： 10.000 Z |3 |
| 2014-01-01T14：01： 15.000 Z |2014-01-01T14：01： 15.000 Z |4 |
| 2014-01-01T14：01： 20.000 ' 值 Z |2014-01-01T14：01： 15.000 Z |4 |
| 2014-01-01T14：01： 25.000 Z |2014-01-01T14：01： 15.000 Z |4 |
| 2014-01-01T14：01： 30.000 Z |2014-01-01T14：01： 30.000 Z |5 |
| 2014-01-01T14：01： 35.000 Z |2014-01-01T14：01： 35.000 Z |6 |
| 2014-01-01T14：01： 40.000 Z |2014-01-01T14：01： 35.000 Z |6 |
| 2014-01-01T14：01： 45.000 Z |2014-01-01T14：01： 35.000 Z |6 |

**查詢**：

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

此查詢會每隔 5 秒產生事件，並輸出先前所收到的最後一個事件。 **HOPPINGWINDOW**持續期間會決定查詢要回到多久才能找到最新的事件。

如需詳細資訊，請參閱跳動[視窗](/stream-analytics-query/hopping-window-azure-stream-analytics)。

## <a name="process-events-with-independent-time-substreams"></a>以獨立時間處理事件（子串流）

事件會因事件產生器之間的時鐘誤差、分割之間的時鐘誤差或網路延遲，而導致延遲發生或順序錯誤。
例如， *TollID* 2 的裝置時鐘是*TollID* 1 後的五秒，而*TollID* 3 的裝置時鐘是*TollID* 1 之後的10秒。 計算可能會因每個收費而獨立發生，只考慮自己的時鐘資料做為時間戳記。

**輸入**：

| LicensePlate | 製作 | 時間 | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00：00： 01.0000000 Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00：00： 05.0000000 Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00：00： 01.0000000 Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00：00： 04.0000000 Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00：00： 10.0000000 Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00：00： 03.0000000 Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00：00： 11.0000000 Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00：00： 07.0000000 Z | 3 |

**輸出**：

| TollID | 計數 |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**查詢**：

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**時間戳記 OVER by**子句會使用子串流個別查看每個裝置時間軸。 每個*TollID*的輸出事件都會在計算時產生，這表示事件與每個*TollID*相關，而不是因為所有裝置都是在相同的時鐘重新排列。

如需詳細資訊，請參閱[TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)。

## <a name="remove-duplicate-events-in-a-window"></a>移除時間範圍內的重複事件

執行如計算指定時間範圍內事件平均值的作業時，應該要將重複的事件篩選出來。 在下列範例中，第二個事件是第一個的重複專案。

**輸入**：  

| deviceId | 時間 | 屬性 | 值 |
| --- | --- | --- | --- |
| 1 |2018-07-27T00：00： 01.0000000 Z |溫度 |50 |
| 1 |2018-07-27T00：00： 01.0000000 Z |溫度 |50 |
| 2 |2018-07-27T00：00： 01.0000000 Z |溫度 |40 |
| 1 |2018-07-27T00：00： 05.0000000 Z |溫度 |60 |
| 2 |2018-07-27T00：00： 05.0000000 Z |溫度 |50 |
| 1 |2018-07-27T00：00： 10.0000000 Z |溫度 |100 |

**輸出**：  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**查詢**：

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

[**計數（相異時間）** ] 會傳回時間範圍內 [時間] 資料行中相異值的數目。 第一個步驟的輸出可以藉由捨棄重複專案，用來計算每個裝置的平均值。

如需詳細資訊，請參閱[計數（相異時間）](/stream-analytics-query/count-azure-stream-analytics)。

## <a name="session-windows"></a>會話視窗

會話視窗是一種視窗，會在一段特定時間之後未收到任何事件，或視窗到達其最大持續時間時，持續展開事件並關閉計算。
此視窗在計算使用者互動資料時特別有用。 當使用者開始與系統互動，並在沒有觀察到更多事件時關閉（也就是說，使用者已停止互動）時，就會啟動視窗。
例如，使用者正在與網頁進行互動，其中記錄了點擊次數，而會話視窗可用來找出使用者與網站互動的時間長度。

**輸入**：

| User_id | 時間 | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00：00： 00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00：00： 20.0000000 Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00：00： 55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00：01： 10.0000000 Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00：01： 15.0000000 Z | "www.example.com/e.html" |

**輸出**：

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00：00： 00.0000000 Z | 2017-01-26T00：01： 10.0000000 Z | 70 |
| 1 | 2017-01-26T00：00： 55.0000000 Z | 2017-01-26T00：01： 15.0000000 Z | 20 |

**查詢**：

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

[**選取**] 會投射與使用者互動相關的資料，以及互動的持續時間。 依使用者分組資料，並在1分鐘內不會發生互動時關閉的**SessionWindow** ，最大的視窗大小為60分鐘。

如需**SessionWindow**的詳細資訊，請參閱[會話視窗](/stream-analytics-query/session-window-azure-stream-analytics)。

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>在 JavaScript 和中使用使用者定義函式的語言擴充性C#

您可以使用以 JavaScript 或C#語言撰寫的自訂函式來擴充 Azure 串流分析查詢語言。 使用者定義函數（UDF）是自訂/複雜的計算，無法使用**SQL**語言輕鬆表示。 這些 Udf 可以定義一次，並在查詢中多次使用。 例如，UDF 可以用來將十六進位*Nvarchar （max）* 值轉換為*Bigint*值。

**輸入**：

| Device_id | HexValue |
| --- | --- |
| 1 | 數值 |
| 2 | 11B |
| 3 | "121" |

**輸出**：

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

使用者定義函式會針對每個取用的事件，計算來自 HexValue 的*Bigint*值。

如需詳細資訊， [請](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions)參閱 JavaScript [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf)和。

## <a name="advanced-pattern-matching-with-match_recognize"></a>搭配 MATCH_RECOGNIZE 的先進模式比對

**MATCH_RECOGNIZE**是一種先進的模式比對機制，可以用來比對事件序列與定義完善的正則運算式模式。
例如，如果有兩個連續的警告訊息，系統管理員需要收到通知，則在 ATM 的作業期間，會即時監視 ATM 的失敗。

**輸入**：

| ATM_id | Operation_id | Return_Code | 時間 |
| --- | --- | --- | --- |
| 1 | 「輸入 Pin」 | "Success" | 2017-01-26T00：10： 00.0000000 Z |
| 2 | 「開啟 Money 位置」 | "Success" | 2017-01-26T00：10： 07.0000000 Z |
| 2 | 「結束 Money 位置」 | "Success" | 2017-01-26T00：10： 11.0000000 Z |
| 1 | 「輸入提款數量」 | "Success" | 2017-01-26T00：10： 08.0000000 Z |
| 1 | 「開啟 Money 位置」 | Warning | 2017-01-26T00：10： 14.0000000 Z |
| 1 | 「列印銀行餘額」 | Warning | 2017-01-26T00：10： 19.0000000 Z |

**輸出**：

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | 「開啟 Money 位置」 | 2017-01-26T00：10： 14.0000000 Z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

此查詢符合至少兩個連續失敗事件，並在符合條件時產生警示。
**PATTERN**會定義要用於比對的正則運算式，在此案例中為任何數目的成功作業，後面至少連續兩次失敗。
成功和失敗是使用 Return_Code 值所定義，一旦符合條件，就會使用*ATM_id*、第一個警告作業和第一個警告時間來預測**量值**。

如需詳細資訊，請參閱[MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)。

## <a name="geofencing-and-geospatial-queries"></a>地理柵欄和地理空間查詢
Azure 串流分析提供內建的地理空間函式，可用來實行車隊管理、分享、連線 cars 和資產追蹤等案例。
地理空間資料可以用 GeoJSON 或 WKT 格式內嵌成事件資料流程或參考資料的一部分。
例如，在製造機器中專門用來列印護照的公司，會將他們的機器租用給政府和 consulates。 這些機器的位置會受到嚴格控制，以避免 misplacing 及可能用於偽造護照。 每部機器都配合 GPS 追蹤器，這項資訊會轉送回 Azure 串流分析作業。
製造人員想要追蹤這些機器的位置，並在其中一個人離開授權的區域時收到警示，如此一來，他們就可以從遠端停用、警示授權單位和取出設備。

**輸入**：

| Equipment_id | Equipment_current_location | 時間 |
| --- | --- | --- |
| 1 | "POINT （-122.13288797982818 47.64082002051315）" | 2017-01-26T00：10： 00.0000000 Z |
| 1 | "POINT （-122.13307252987875 47.64081350934929）" | 2017-01-26T00：11： 00.0000000 Z |
| 1 | "POINT （-122.13308862313283 47.6406508603241）" | 2017-01-26T00：12： 00.0000000 Z |
| 1 | "POINT （-122.13341048821462 47.64043760861279）" | 2017-01-26T00：13： 00.0000000 Z |

**參考資料輸入**：

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "多邊形（（-122.13326028450979 47.6409833866794，-122.13261655434621 47.6409833866794，-122.13261655434621 47.64061471602751，-122.13326028450979 47.64061471602751，-122.13326028450979 47.6409833866794））" |

**輸出**：

| Equipment_id | Equipment_alert_location | 時間 |
| --- | --- | --- |
| 1 | "POINT （-122.13341048821462 47.64043760861279）" | 2017-01-26T00：13： 00.0000000 Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

此查詢可讓製造商自動監視機器位置，當機器離開允許的地理柵欄時，就會收到警示。 內建地理空間功能可讓使用者在查詢中使用 GPS 資料，而不需要協力廠商程式庫。

如需詳細資訊，請參閱[使用 Azure 串流分析的地理柵欄和地理空間匯總案例](geospatial-scenarios.md)一文。

## <a name="get-help"></a>取得說明

如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
