---
title: Azure 串流分析中常見的查詢模式
description: 本文說明數個常見的查詢模式和設計，在 Azure 串流分析作業中很實用。
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 0eabe147563ee712e20e57aafc1029daf2a8610a
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641036"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Azure 串流分析中常見的查詢模式

Azure 串流分析的查詢會以類似 SQL 的查詢語言表達。 語言建構記載在[串流分析查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)指南中。 

查詢設計能傳達簡單的傳遞邏輯，將事件資料從某個輸入資料流移至輸出資料存放區，或執行多種模式比對和時態分析，以計算一段時間的彙總，，如[利用串流分析來建置 IoT 解決方案](stream-analytics-build-an-iot-solution-using-stream-analytics.md)指南中所述。 您可以聯結多個輸入的資料來合併串流事件，也可以查閱靜態參考資料，從而擴充事件值。 此外，您也可以將資料寫入至多個輸出。

本文章根據真實世界案例概述幾個常見查詢模式的解決方案。

## <a name="supported-data-formats"></a>支援的資料格式

Azure 串流分析可處理資料格式為 CSV、JSON 和 Avro 的事件。

JSON 和 Avro 可能包含巢狀物件 (記錄) 或陣列等複雜類型。 如需使用這些複雜資料類型的詳細資訊，請參閱[剖析 JSON 和 AVRO 資料](stream-analytics-parsing-json.md)一文。

## <a name="send-data-to-multiple-outputs"></a>將資料傳送至多個輸出

多個 **SELECT** 陳述式可用來將資料輸出到不同的輸出接收端。 例如，某個 **SELECT** 可以輸出閾值型警示，另一個則可以將事件輸出至 Blob 儲存體。

**輸入**：

| 請確定 | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**輸出 ArchiveOutput**：

| 請確定 | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**輸出 AlertOutput**：

| 請確定 | Time | Count |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000Z |3 |

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

**INTO** 子句會告訴串流分析要將資料寫入至哪個輸出。 第一個 **SELECT** 定義傳遞查詢，其會接收來自輸入的資料，並將該資料傳送至名為 **Archiveoutput** 的輸出。 第二個查詢會執行一些簡單的彙總和篩選，然後再將結果傳送至下游的警示系統輸出，稱為 **AlertOutput**。

請注意，**WITH** 子句可以用來定義多個子查詢區塊。 此選項具有對輸入來源開啟更少讀取器的優點。

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

如需詳細資訊，請參閱 [**WITH** 子句](/stream-analytics-query/with-azure-stream-analytics)。

## <a name="simple-pass-through-query"></a>簡單的傳遞查詢

簡單的傳遞查詢可以用來將輸入資料流資料複製到輸出。 例如，如果包含即時車輛資訊的資料流必須儲存在 SQL 資料庫中以進行信件分析，則簡單的傳遞查詢將會執行此作業。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**輸出**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**查詢**：

```SQL
SELECT
    *
INTO Output
FROM Input
```

**SELECT** * 查詢會預測傳入事件的所有欄位，並將其傳送至輸出。 同樣地，**SELECT** 也可以用來只預測輸入中的必要欄位。 在此範例中，如果車輛 *Make* 和 *Time* 是要儲存的唯一必要欄位，則可以在 **SELECT** 陳述式中指定這些欄位。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**輸出**：

| 請確定 | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:04.0000000Z |

**查詢**：

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>搭配 LIKE 和 NOT LIKE 的字串比對

**LIKE** 和 **NOT LIKE** 可以用來驗證欄位是否符合特定模式。 例如，您可以建立一個篩選條件，只傳回以字母 'A' 開頭並以數字 9 結尾的車牌。

**輸入**：

| 請確定 | License_plate | Time |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**輸出**：

| 請確定 | License_plate | Time |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**查詢**：

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

使用 **LIKE** 陳述式檢查 **License_plate** 欄位值。 其開頭應該為字母 A，接著是長度為零或更多字元的字串，並以數字 9 結尾。

## <a name="calculation-over-past-events"></a>過去事件的計算

**LAG** 函式可以用來查看時間範圍內的過去事件，並將其與目前的事件進行比較。 例如，如果目前的汽車製造商與通過收費站的最後一輛汽車不同，就可以將其輸出。

**輸入**：

| 請確定 | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |

**輸出**：

| 請確定 | Time |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000Z |

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

使用 **LAG** 查看前一個事件的輸入資料流，同時擷取 *Make* 值，並將其與目前事件的 *Make* 進行比較，然後輸出事件。

如需詳細資訊，請參閱 [**LAG**](/stream-analytics-query/lag-azure-stream-analytics)。

## <a name="return-the-last-event-in-a-window"></a>傳回時間範圍內的最後一個事件

當系統即時使用事件時，沒有函式可判斷該事件是否會是該時間範圍內最後到達的事件。 為了達成此目的，輸入資料流程必須與另一個輸入資料流程聯結，其中事件的時間是該時間範圍內所有事件的最長時間。

**輸入**：

| License_plate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**輸出**：

| License_plate | 請確定 | Time |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

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

查詢的第一個步驟是在 10 分鐘的時間範圍內尋找時間戳記上限，這是該時間範圍最後一個事件的時間戳記。 第二個步驟會將第一個查詢的結果與原始串流聯結在一起，在每個時間範圍內尋找符合最後一個時間戳記的事件。 

**DATEDIFF** 是一種日期特有的函式，可比較並傳回兩個 DateTime 欄位之間的時間差。如需詳細資訊，請參閱 [date 函式](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics)。

如需聯結資料流的詳細資訊，請參閱 [**JOIN**](/stream-analytics-query/join-azure-stream-analytics)。

## <a name="data-aggregation-over-time"></a>一段時間的資料彙總

若要計算一段時間範圍的資訊，可以將資料彙總在一起。 在此範例中，會針對每個特定汽車所產生的過去10秒計算一次計數。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**輸出**：

| 請確定 | Count |
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

此彙總會依 *Make* 將汽車分組，並每隔 10 秒對其進行計數一次。 對於通過收費站的汽車，輸出具有其 *Make* 和 *Count*。

TumblingWindow 是用來將事件群組在一起的視窗化函式。 彙總可以套用至所有分組的事件。 如需詳細資訊，請參閱[視窗化函式](stream-analytics-window-functions.md)。

如需彙總的詳細資訊，請參閱[彙總函式](/stream-analytics-query/aggregate-functions-azure-stream-analytics)。

## <a name="periodically-output-values"></a>定期輸出值

萬一發生異常或遺失的事件，可以從更疏鬆的資料輸入產生定期輸出。 例如，每隔 5 秒產生事件，報告最近所見的資料點。

**輸入**：

| Time | 值 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**輸出 (前 10 個資料列)** ：

| Window_end | Last_event.Time | Last_event.Value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

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

此查詢會每隔 5 秒產生事件，並輸出先前所收到的最後一個事件。 **HOPPINGWINDOW** 持續時間會決定查詢要回到多久前，才能找到最新的事件。

如需詳細資訊，請參閱[跳動視窗](/stream-analytics-query/hopping-window-azure-stream-analytics)。

## <a name="correlate-events-in-a-stream"></a>使資料流中的事件相互關聯

藉由使用 **LAG** 函式查看過去事件，即可使相同資料流中的事件相互關聯。 例如，每次連續兩部來自相同 *Make* 的汽車在過去 90 秒內通過收費站時，就會產生輸出。

**輸入**：

| 請確定 | License_plate | Time |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000Z |

**輸出**：

| 請確定 | Time | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

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

**LAG** 函式可以查看前一個事件的輸入資料流，並擷取 *Make*，將其與目前事件的 *Make* 值進行比較。  一旦符合條件，就可以使用 **SELECT** 陳述式中的 **LAG** 來預測上一個事件中的資料。

如需詳細資訊，請參閱 [LAG](/stream-analytics-query/lag-azure-stream-analytics)。

## <a name="detect-the-duration-between-events"></a>偵測事件之間的持續時間

一旦收到 End 事件，就可以查看最後一個 Start 事件來計算事件的持續時間。 此查詢有助於判斷使用者在頁面或功能上花費的時間。

**輸入**：  

| User | 功能 | 事件 | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |結束 |2015-01-01T00:00:08.0000000Z |

**輸出**：  

| User | 功能 | Duration |
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

**LAST** 函式可以用來擷取特定條件內的最後一個事件。 在此範例中，條件是類型為 Start 的事件，藉由 **PARTITION BY** 使用者和功能來分割搜尋。 如此一來，在搜尋 Start 事件時，每個使用者和功能都會獨立處理。 **LIMIT DURATION** 將向後搜尋的時間限制為 End 和 Start 事件之間的 1 小時。

## <a name="count-unique-values"></a>計算唯一值

**COUNT** 和 **DISTINCT** 可用來計算某個時間範圍內在串流中所出現唯一欄位值的數目。 例如，在 2 秒鐘時間範圍內有多少部獨特 *Make* 的汽車通過收費站。

**輸入**：

| 請確定 | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**輸出：**

| Count_make | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**查詢：**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(DISTINCT Make)** 會傳回一個時間範圍內 **Make** 資料行的相異值計數。
如需詳細資訊，請參閱 [**COUNT** 彙總函式](/stream-analytics-query/count-azure-stream-analytics)。

## <a name="retrieve-the-first-event-in-a-window"></a>擷取時間範圍內的第一個事件

**IsFirst** 可用來取出時間範圍內的第一個事件。 例如，每隔 10 分鐘輸出第一部汽車資訊。

**輸入**：

| License_plate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**輸出**：

| License_plate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |

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

**IsFirst** 也可以分割資料，並計算每部特定汽車 *Make* 每隔 10 分鐘找到的第一個事件。

**輸出**：

| License_plate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

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

如需詳細資訊，請參閱 [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics)。

## <a name="remove-duplicate-events-in-a-window"></a>移除時間範圍內的重複事件

執行如計算指定時間範圍內事件平均值的作業時，應該要將重複的事件篩選出來。 在下列範例中，第二個事件是第一個事件的重複。

**輸入**：  

| deviceId | Time | 屬性 | 值 |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |溫度 |50 |
| 1 |2018-07-27T00:00:01.0000000Z |溫度 |50 |
| 2 |2018-07-27T00:00:01.0000000Z |溫度 |40 |
| 1 |2018-07-27T00:00:05.0000000Z |溫度 |60 |
| 2 |2018-07-27T00:00:05.0000000Z |溫度 |50 |
| 1 |2018-07-27T00:00:10.0000000Z |溫度 |100 |

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

**COUNT(DISTINCT Time)** 會傳回時間範圍內 Time 資料行中的相異值數目。 然後，可以使用第一個步驟的輸出，透過捨棄重複項目來計算每個裝置的平均值。

如需詳細資訊，請參閱 [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics)。

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>針對不同的案例/值指定邏輯 (CASE 陳述式)

**CASE** 陳述式可以根據特定準則，為不同的欄位提供不同的計算。 例如，將車道 'A' 指派給 *Make1* 的汽車，並將車道 'B' 指派給任何其他製造商。

**輸入**：

| 請確定 | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**輸出**：

| 請確定 |Dispatch_to_lane | Time |
| --- | --- | --- |
| Make1 |"A" |2015-01-01T00:00:01.0000000Z |
| Make2 |"B" |2015-01-01T00:00:02.0000000Z |

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

**CASE** 運算式會比較運算式和一組簡單運算式來決定其結果。 在此範例中，*Make1* 的車輛會分派給車道 'A'，而任何其他製造商的車輛則會指派給車道 'B'。

如需詳細資訊，請參閱 [case 運算式](/stream-analytics-query/case-azure-stream-analytics)。

## <a name="data-conversion"></a>資料轉換

您可以使用 **CAST** 方法來即時轉換資料。 例如，汽車權數可以從類型 **nvarchar(max)** 轉換成類型 **bigint**，並用於數值計算。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**輸出**：

| 請確定 | Weight |
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

使用 **CAST** 陳述式來指定其資料類型。 請參閱[資料類型 (Azure 串流分析)](/stream-analytics-query/data-types-azure-stream-analytics) 上的支援資料類型清單。

如需詳細資訊，請參閱[資料轉換函式](/stream-analytics-query/conversion-functions-azure-stream-analytics)。

## <a name="detect-the-duration-of-a-condition"></a>偵測某個條件的持續時間

對於跨越多個事件的條件，**LAG** 函式可以用來識別該條件的持續時間。 例如，假設有個錯誤導致所有車輛的重量不正確 (超過 20,000 磅)，而且必須計算該錯誤的持續時間。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000Z |25000 |
| Make1 |2015-01-01T00:00:03.0000000Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000Z |25000 |
| Make1 |2015-01-01T00:00:05.0000000Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000Z |25000 |
| Make1 |2015-01-01T00:00:07.0000000Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000Z |2000 |

**輸出**：

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

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
第一個 **SELECT** 陳述式會使目前的權數測量與先前的測量相互關聯，並將其與目前的測量一起預測。 第二個 **SELECT** 往回查看 *previous_weight* 小於 20000 的最後一個事件，其中目前權數小於 20000，而目前事件的 *previous_weight* 則大於 20000。

End_fault 是上一個事件發生錯誤的目前非錯誤事件，而 Start_fault 是在這之前的最後一個非錯誤事件。

## <a name="process-events-with-independent-time-substreams"></a>以獨立時間處理事件 (子資料流)

事件會因事件產生器之間的時鐘誤差、分割之間的時鐘誤差或網路延遲，而導致延遲發生或順序錯誤。
例如，*TollID* 2 的裝置時鐘比 *TollID* 1 晚 5 秒，而且 *TollID 3* 的裝置時鐘比 *TollID* 1 晚 10 秒。 計算可能會因每個收費而獨立發生，只將其自己的時鐘資料視為時間戳記。

**輸入**：

| LicensePlate | 請確定 | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000Z | 3 |

**輸出**：

| TollID | Count |
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

**TIMESTAMP BY OVER** 子句會使用子資料流個別查看每個裝置時間軸。 計算時會產生每個 *TollID* 的輸出事件，這表示事件的順序均與每個 *TollID* 有關，而不會重新排列順序，就像所有裝置都依據同一個時鐘一般。

如需詳細資訊，請參閱 [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)。

## <a name="session-windows"></a>工作階段時間範圍

工作階段時間範圍是一種時間範圍，可在事件發生時持續展開，並在一段特定時間之後未收到任何事件，或時間範圍到達其最大持續時間時，關閉計算。
此時間範圍在計算使用者互動資料時特別有用。 當使用者開始與系統互動，時間範圍就會開始，而在沒有觀察到更多事件時，也就是說，使用者已停止互動時，就會關閉時間範圍。
例如，使用者正在與記錄點擊次數的網頁進行互動，這時工作階段時間範圍可以用來找出使用者與網站互動的時間長度。

**輸入**：

| User_id | Time | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000Z | "www.example.com/e.html" |

**輸出**：

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

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

**SELECT** 會預測與使用者互動相關的資料，以及互動的持續時間。 如果 1 分鐘內沒有發生任何互動，則會依使用者和關閉的 **SessionWindow** 將資料分組，時間範圍大小上限為 60 分鐘。

如需 **SessionWindow** 的詳細資訊，請參閱[工作階段時間範圍](/stream-analytics-query/session-window-azure-stream-analytics)。

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>在 JavaScript 和 C# 中搭配使用者定義函式的語言擴充性

您可以使用以 JavaScript 或 C# 語言撰寫的自訂函式，來擴充 Azure 串流分析查詢語言。 使用者定義函式 (UDF) 是自訂/複雜的計算，無法使用 **SQL** 語言輕鬆地表示。 這些 UDF 可以定義一次，並在查詢內多次使用。 例如，UDF 可以用來將十六進位 *nvarchar(max)* 值轉換成 *bigint* 值。

**輸入**：

| Device_id | HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
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

使用者定義函式會根據每個取用的事件，計算 HexValue 中的 *bigint* 值。

若需詳細資訊，請參閱 [JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) 和 [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf)。

## <a name="advanced-pattern-matching-with-match_recognize"></a>搭配 MATCH_RECOGNIZE 的進階模式比對

**MATCH_RECOGNIZE** 是一種進階模式比對機制，可以用來比對事件序列與定義完善的規則運算式模式。
例如，如果有兩個連續的警告訊息，管理員需要收到通知，則在 ATM 作業期間，會即時監視 ATM 是否故障。

**輸入**：

| ATM_id | Operation_id | Return_Code | Time |
| --- | --- | --- | --- |
| 1 | 「輸入 Pin 碼」 | "Success" | 2017-01-26T00:10:00.0000000Z |
| 2 | 「打開出錢槽」 | "Success" | 2017-01-26T00:10:07.0000000Z |
| 2 | 「關閉出錢槽」 | "Success" | 2017-01-26T00:10:11.0000000Z |
| 1 | 「輸入提款金額」 | "Success" | 2017-01-26T00:10:08.0000000Z |
| 1 | 「打開出錢槽」 | 「警告」 | 2017-01-26T00:10:14.0000000Z |
| 1 | 「列印存款餘額」 | 「警告」 | 2017-01-26T00:10:19.0000000Z |

**輸出**：

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | 「打開出錢槽」 | 2017-01-26T00:10:14.0000000Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
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
**PATTERN** 定義要用於比對的規則運算式，在此情況下，任何數目的成功作業後面至少連續兩次失敗。
成功和失敗是使用 Return_Code 值所定義，一旦符合條件，就會利用 *ATM_id*、第一個警告作業和第一個警告時間來推算 **MEASURES**。

如需詳細資訊，請參閱 [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)。

## <a name="geofencing-and-geospatial-queries"></a>地理柵欄和地理空間查詢

Azure 串流分析提供內建的地理空間函式，可用來實作車隊管理、共駕、連線汽車和資產追蹤等案例。
地理空間資料可以採用 GeoJSON 或 WKT 格式內嵌成事件資料流或參考資料的一部分。
例如，專精於製造列印護照機器的公司，會將其機器租用給政府和領事館。 這些機器的位置會受到嚴格控制，以避免放錯位置和可能用於偽造護照。 每部機器都配有 GPS 追蹤器，這項資訊會轉送回 Azure 串流分析作業。
製造商想要追蹤這些機器的位置，並在其中一部機器離開授權區域時收到警示，如此一來，他們就可以從遠端停用、警示當局和取回設備。

**輸入**：

| Equipment_id | Equipment_current_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000Z |
| 1 | "POINT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000Z |
| 1 | "POINT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000Z |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

**參考資料輸入**：

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**輸出**：

| Equipment_id | Equipment_alert_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

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

此查詢可讓製造商自動監視機器位置，當機器離開允許的地理柵欄時，就會收到警示。 內建的地理空間功能可讓使用者使用查詢內的 GPS 資料，而不需要協力廠商程式庫。

如需詳細資訊，請參閱[搭配 Azure 串流分析的地理柵欄和地理空間彙總案例](geospatial-scenarios.md)一文。

## <a name="get-help"></a>取得說明

如需進一步的協助，請嘗試 [Azure 串流分析的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
