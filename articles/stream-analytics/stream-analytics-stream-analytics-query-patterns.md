---
title: Azure 串流分析中常見的查詢模式
description: 本文介紹了在 Azure 流分析作業中有用的幾種常見查詢模式和設計。
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982323"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Azure 串流分析中常見的查詢模式

Azure 串流分析的查詢會以類似 SQL 的查詢語言表達。 語言建構記載在[串流分析查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)指南中。 

查詢設計可以表示簡單的傳遞邏輯，將事件資料從一個輸入流移動到輸出資料存儲中，也可以進行豐富的模式匹配和時間分析，以便[使用流分析指南計算](stream-analytics-build-an-iot-solution-using-stream-analytics.md)不同時間視窗的聚合， 如生成 IoT 解決方案中的聚合。 可以從多個輸入聯接資料以合併流式處理事件，還可以針對靜態參考資料執行查找以豐富事件值。 您還可以將資料寫入多個輸出。

本文概述了基於實際方案的幾個常見查詢模式的解決方案。

## <a name="supported-data-formats"></a>支援的資料格式

Azure 串流分析可處理資料格式為 CSV、JSON 和 Avro 的事件。

JSON 和 Avro 可能包含巢狀物件 (記錄) 或陣列等複雜類型。 有關使用這些複雜資料類型的詳細資訊，請參閱[解析 JSON 和 AVRO 資料](stream-analytics-parsing-json.md)文章。

## <a name="simple-pass-through-query"></a>簡單的傳遞查詢

一個簡單的直通查詢可用於將輸入流資料複製到輸出中。 例如，如果需要將包含即時車輛資訊的資料流程保存在 SQL 資料庫中進行字母分析，則簡單的傳遞查詢將執行該作業。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |"1000" |
| 製造1 |2015-01-01T00:00:02.0000000Z |"2000" |

**輸出**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |"1000" |
| 製造1 |2015-01-01T00:00:02.0000000Z |"2000" |

**查詢**：

```SQL
SELECT
    *
INTO Output
FROM Input
```

**SELECT** = 查詢將建置事件的所有欄位專案，並將它們發送到輸出。 同樣 **，SELECT**也可用於僅從輸入中投影所需的欄位。 在此示例中，如果車輛*製造*時間和*時間*是唯一需要保存的欄位，則可以在**SELECT**語句中指定這些欄位。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |1000 |
| 製造1 |2015-01-01T00:00:02.0000000Z |2000 |
| 製造2 |2015-01-01T00:00:04.0000000Z |1500 |

**輸出**：

| 請確定 | Time |
| --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |
| 製造1 |2015-01-01T00:00:02.0000000Z |
| 製造2 |2015-01-01T00:00:04.0000000Z |

**查詢**：

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>隨時間而進行的資料聚合

要計算時間視窗中的資訊，可以將資料聚合在一起。 在此示例中，計算每個特定汽車製造的最後 10 分鐘的時間中的計數。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |1000 |
| 製造1 |2015-01-01T00:00:02.0000000Z |2000 |
| 製造2 |2015-01-01T00:00:04.0000000Z |1500 |

**輸出**：

| 請確定 | Count |
| --- | --- |
| 製造1 | 2 |
| 製造2 | 1 |

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

此聚合按 Make 對汽車進行分組 *，* 並每 10 秒對它們進行計數。 輸出有通過通行費的汽車*的製作*和*計數*。

翻翻視窗是一個視窗函數，用於將事件分組在一起。 聚合可以應用於所有分組事件。 有關詳細資訊，請參閱[視窗函數](stream-analytics-window-functions.md)。

有關聚合的詳細資訊，請參閱[彙總函式](/stream-analytics-query/aggregate-functions-azure-stream-analytics)。

## <a name="data-conversion"></a>資料轉換

可以使用**CAST**方法即時投射資料。 例如，汽車重量可以從**Nvarchar（max）** 類型轉換為**Bigint 類型**，並用於數值計算。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |"1000" |
| 製造1 |2015-01-01T00:00:02.0000000Z |"2000" |

**輸出**：

| 請確定 | Weight |
| --- | --- |
| 製造1 |3000 |

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

使用**CAST**語句指定其資料類型。 請參閱[資料類型（Azure 流分析）](/stream-analytics-query/data-types-azure-stream-analytics)上受支援的資料類型的清單。

有關[資料轉換函數](/stream-analytics-query/conversion-functions-azure-stream-analytics)的詳細資訊。

## <a name="string-matching-with-like-and-not-like"></a>字串匹配與喜歡和不喜歡

**喜歡**和**不喜歡**可用於驗證欄位是否與特定模式匹配。 例如，可以創建篩選器以僅返回以字母"A"開頭，以數位 9 結尾的牌照。

**輸入**：

| 請確定 | License_plate | Time |
| --- | --- | --- |
| 製造1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| 製造2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| 製造3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**輸出**：

| 請確定 | License_plate | Time |
| --- | --- | --- |
| 製造2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| 製造3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**查詢**：

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

使用**LIKE**語句檢查**License_plate**欄位值。 它應該以字母"A"開頭，然後具有零個或多個字元的任何字串，以數位 9 結尾。

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>針對不同的案例/值指定邏輯 (CASE 陳述式)

**CASE**語句可以根據特定條件為不同的欄位提供不同的計算。 例如，將車道"A"分配給*Make1*和車道"B"到任何其他車輛。

**輸入**：

| 請確定 | Time |
| --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |
| 製造2 |2015-01-01T00:00:02.0000000Z |
| 製造2 |2015-01-01T00:00:03.0000000Z |

**輸出**：

| 請確定 |Dispatch_to_lane | Time |
| --- | --- | --- |
| 製造1 |"A" |2015-01-01T00:00:01.0000000Z |
| 製造2 |"B" |2015-01-01T00:00:02.0000000Z |

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

**CASE**運算式將運算式與一組簡單運算式進行比較以確定其結果。 在此示例中 *，Make1*的車輛被發送到車道"A"，而任何其他車輛的車輛將被分配車道"B"。

有關詳細資訊，請參閱[大小寫運算式](/stream-analytics-query/case-azure-stream-analytics)。

## <a name="send-data-to-multiple-outputs"></a>將資料傳送至多個輸出

多個**SELECT**語句可用於將資料輸出到不同的輸出接收器。 例如，一個 SELECT 可以輸出基於閾值的警報，而另一個**SELECT**可以將事件輸出到 Blob 存儲。

**輸入**：

| 請確定 | Time |
| --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |
| 製造1 |2015-01-01T00:00:02.0000000Z |
| 製造2 |2015-01-01T00:00:01.0000000Z |
| 製造2 |2015-01-01T00:00:02.0000000Z |
| 製造2 |2015-01-01T00:00:03.0000000Z |

**輸出存檔輸出**：

| 請確定 | Time |
| --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |
| 製造1 |2015-01-01T00:00:02.0000000Z |
| 製造2 |2015-01-01T00:00:01.0000000Z |
| 製造2 |2015-01-01T00:00:02.0000000Z |
| 製造2 |2015-01-01T00:00:03.0000000Z |

**輸出警報輸出**：

| 請確定 | Time | Count |
| --- | --- | --- |
| 製造2 |2015-01-01T00:00:10.0000000Z |3 |

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

**INTO**子句告訴流分析要將資料寫入哪些輸出。 第一個**SELECT**定義一個傳遞查詢，該查詢從輸入接收資料並將其發送到名為 **"存檔輸出**"的輸出。 第二個查詢在將結果發送到稱為**Alert輸出**的下游警報系統輸出之前，執行一些簡單的聚合和篩選。

請注意 **，WITH**子句可用於定義多個子查詢塊。 此選項的好處是打開較少的讀取器到輸入源。

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

有關詳細資訊，請參閱[**WITH**子句](/stream-analytics-query/with-azure-stream-analytics)。

## <a name="count-unique-values"></a>計算唯一值

**COUNT**和 **"指定"** 可用於計算在時間視窗內出現在流中的唯一欄位值數。 可以創建查詢，以計算在 2*Makes*秒的視窗中通過收費站的汽車數量。

**輸入**：

| 請確定 | Time |
| --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |
| 製造1 |2015-01-01T00:00:02.0000000Z |
| 製造2 |2015-01-01T00:00:01.0000000Z |
| 製造2 |2015-01-01T00:00:02.0000000Z |
| 製造2 |2015-01-01T00:00:03.0000000Z |

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

**COUNT（"分項製作"）** 返回時間視窗中"**製造"** 列中不同值的計數。
有關詳細資訊，請參閱[**COUNT**彙總函式](/stream-analytics-query/count-azure-stream-analytics)。

## <a name="calculation-over-past-events"></a>過去事件的計算

**LAG**函數可用於在時間視窗內查看過去的事件，並將這些事件與當前事件進行比較。 例如，如果當前汽車與通過通行費的最後一輛車不同，則可以輸出當前汽車。

**輸入**：

| 請確定 | Time |
| --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |
| 製造2 |2015-01-01T00:00:02.0000000Z |

**輸出**：

| 請確定 | Time |
| --- | --- |
| 製造2 |2015-01-01T00:00:02.0000000Z |

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

使用**LAG**可回探一個事件的輸入流，檢索 *"製造*"值並將其與當前事件的 *"製造*"值進行比較並輸出事件。

有關詳細資訊，請參閱[**LAG**](/stream-analytics-query/lag-azure-stream-analytics)。

## <a name="retrieve-the-first-event-in-a-window"></a>檢索視窗中的第一個事件

**IsFirst**可用於檢索時間視窗中的第一個事件。 例如，每隔 10 分鐘提供一次汽車資訊。

**輸入**：

| License_plate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |製造1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |製造3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |製造1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |製造2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |製造2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |製造1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |製造4 |2015-07-27T00:13:45.0000000Z |

**輸出**：

| License_plate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |製造1 |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |製造1 |2015-07-27T00:12:02.0000000Z |

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

**IsFirst**還可以將資料分區，並將第一個事件計算到每個特定汽車 *，每*10 分鐘間隔找到。

**輸出**：

| License_plate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |製造1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |製造3 |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |製造2 |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |製造1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |製造4 |2015-07-27T00:13:45.0000000Z |

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

有關詳細資訊，請參閱[**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics)。

## <a name="return-the-last-event-in-a-window"></a>返回視窗中的最後一個事件

由於事件被系統即時使用，因此沒有函數可以確定事件是否將是該時間視窗到達的最後一個事件。 為此，需要將輸入流與另一個聯接，其中事件的時間是該視窗所有事件的最大時間。

**輸入**：

| License_plate | 請確定 | Time |
| --- | --- | --- |
| DXE 5291 |製造1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |製造3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |製造1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |製造2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |製造2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |製造1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |製造4 |2015-07-27T00:13:45.0000000Z |

**輸出**：

| License_plate | 請確定 | Time |
| --- | --- | --- |
| VFE 1616 |製造2 |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |製造4 |2015-07-27T00:13:45.0000000Z |

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

查詢上的第一步在 10 分鐘視窗中查找最大時間戳記，即該視窗的最後一個事件的時間戳記。 第二步將第一個查詢的結果與原始流聯接，以查找與每個視窗中最後一個時間戳記匹配的事件。 

**DATEDIFF**是一個特定于日期的函數，用於比較和返回兩個 DateTime 欄位之間的時差，有關詳細資訊，請參閱[日期函數](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics)。

有關聯接流的詳細資訊，請參閱[**JOIN**](/stream-analytics-query/join-azure-stream-analytics)。


## <a name="correlate-events-in-a-stream"></a>關聯流中的事件

可以通過使用**LAG**函數查看過去的事件來關聯同一流中的事件。 例如，每次來自同一*Make*的連續兩輛車在過去 90 秒內通過通行費時，都可以生成輸出。

**輸入**：

| 請確定 | License_plate | Time |
| --- | --- | --- |
| 製造1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| 製造1 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| 製造2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| 製造1 |GHI-345 |2015-01-01T00:00:04.0000000Z |

**輸出**：

| 請確定 | Time | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| 製造1 |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

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

**LAG**函數可以回查看輸入流一個事件並檢索 *"Make"* 值，並將其與當前事件的 *"Make"* 值進行比較。  滿足條件後，可以使用**SELECT**語句中的**LAG**來預測上一個事件的資料。

有關詳細資訊，請參閱[LAG](/stream-analytics-query/lag-azure-stream-analytics)。

## <a name="detect-the-duration-between-events"></a>偵測事件之間的持續時間

可以通過在收到結束事件後查看最後一個"開始"事件來計算事件的持續時間。 此查詢可用於確定使用者在頁面或功能上花費的時間。

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

**LAST**函數可用於檢索特定條件下的最後一個事件。 在此示例中，條件是"開始"類型的事件，按**分區 BY**使用者和功能對搜索進行分區。 這樣，在搜索"開始"事件時，將獨立處理每個使用者和功能。 **限制持續時間**將搜索時間限制為"結束"和"開始"事件之間的 1 小時。

## <a name="detect-the-duration-of-a-condition"></a>偵測某個條件的持續時間

對於跨越多個事件的條件 **，LAG**函數可用於標識該條件的持續時間。 例如，假設有個錯誤導致所有車輛的重量不正確 (超過 20,000 磅)，而且必須計算該錯誤的持續時間。

**輸入**：

| 請確定 | Time | Weight |
| --- | --- | --- |
| 製造1 |2015-01-01T00:00:01.0000000Z |2000 |
| 製造2 |2015-01-01T00:00:02.0000000Z |25000 |
| 製造1 |2015-01-01T00:00:03.0000000Z |26000 |
| 製造2 |2015-01-01T00:00:04.0000000Z |25000 |
| 製造1 |2015-01-01T00:00:05.0000000Z |26000 |
| 製造2 |2015-01-01T00:00:06.0000000Z |25000 |
| 製造1 |2015-01-01T00:00:07.0000000Z |26000 |
| 製造2 |2015-01-01T00:00:08.0000000Z |2000 |

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
第一個**SELECT**語句將當前重量測量與前一個測量值相關聯，並將其與當前測量一起投影。 第二個**SELECT**回顧*previous_weight*小於 20000 的最後一個事件，其中當前權重小於 20000，當前事件*previous_weight*大於 20000。

End_fault是當前非故障事件，其中上一個事件有故障，Start_fault是之前最後一個非故障事件。

## <a name="periodically-output-values"></a>定期輸出值

在發生不規則或缺失事件的情況下，可以從更稀疏的資料輸入生成常規間隔輸出。 例如，每隔 5 秒產生事件，報告最近所見的資料點。

**輸入**：

| Time | 值 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**輸出 (前 10 個資料列)**：

| Window_end | Last_event時間 | Last_event價值 |
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

此查詢會每隔 5 秒產生事件，並輸出先前所收到的最後一個事件。 **"跳動視窗**"持續時間確定查詢查找最新事件的回波時間。

有關詳細資訊，請參閱[跳槽視窗](/stream-analytics-query/hopping-window-azure-stream-analytics)。

## <a name="process-events-with-independent-time-substreams"></a>使用獨立時間處理事件（子流）

事件會因事件產生器之間的時鐘誤差、分割之間的時鐘誤差或網路延遲，而導致延遲發生或順序錯誤。
例如 *，TollID* 2 的設備時鐘比*TollID* 1 落後 5 秒 *，TollID* 3 的設備時鐘比*TollID* 1 落後 10 秒。 計算可以針對每個收費站獨立進行，只考慮其自己的時鐘資料作為時間戳記。

**輸入**：

| LicensePlate | 請確定 | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |製造1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |製造2 |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |製造1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |製造3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |製造2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |製造1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |製造3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |製造4 |2015-07-27T00:00:07.0000000Z | 3 |

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

**"時間點"BY**子句使用子流獨立查看每個設備時間表。 每個*TollID*的輸出事件在計算時生成，這意味著事件相對於每個*TollID*進行排序，而不是像所有設備都在同一時鐘上一樣重新排序。

有關詳細資訊，請參閱[時間時間。](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)

## <a name="remove-duplicate-events-in-a-window"></a>移除時間範圍內的重複事件

執行如計算指定時間範圍內事件平均值的作業時，應該要將重複的事件篩選出來。 在下面的示例中，第二個事件是第一個事件的重複項。

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

**COUNT(DISTINCT Time)** 會傳回時間範圍內 Time 資料行中的相異值數目。 然後，第一步的輸出可用於通過丟棄重複項來計算每個設備的平均值。

有關詳細資訊，請參閱[COUNT（時間）](/stream-analytics-query/count-azure-stream-analytics)。

## <a name="session-windows"></a>會話視窗

會話視窗是一個視窗，它隨著事件發生而不斷擴展，如果在特定時間之後未收到任何事件或視窗達到其最大持續時間，則關閉以進行計算。
此視窗在計算使用者交互資料時特別有用。 當使用者開始與系統交互時，視窗開始，並在未觀察到更多事件時關閉，這意味著使用者已停止交互。
例如，使用者正在與記錄點擊次數的網頁進行交互，可以使用會話視窗來瞭解使用者與網站交互的時間。

**輸入**：

| User_id | Time | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00：00：00.000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00：00：20.000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00：00：55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00：01：10.000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00：01：15.0000000Z | "www.example.com/e.html" |

**輸出**：

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00：00：00.000000Z | 2017-01-26T00：01：10.000000Z | 70 |
| 1 | 2017-01-26T00：00：55.0000000Z | 2017-01-26T00：01：15.0000000Z | 20 |

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

**SELECT**將預測與使用者交互相關的資料以及交互的持續時間。 按使用者分組資料，並在 1 分鐘內未發生交互時關閉的**SessionWindow，** 最大視窗大小為 60 分鐘。

有關**會話視窗**的詳細資訊，請參閱[會話視窗](/stream-analytics-query/session-window-azure-stream-analytics)。

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>JavaScript 和 C 中使用者定義的函數的語言可擴充性#

Azure 流分析查詢語言可以通過以 JavaScript 或 C# 語言編寫的自訂函數進行擴展。 使用者定義的函數 （UDF） 是自訂/複雜計算，無法使用**SQL**語言輕鬆表示。 這些 UF 可以定義一次，並在查詢中多次使用。 例如，UDF 可用於將十六進位*Nvarchar（max）* 值轉換為*大值*。

**輸入**：

| Device_id | 六角值 |
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

使用者定義函數將從十六進位值計算所消耗的每個事件上的*大*值。

有關詳細資訊，請參閱[JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions)和[C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf)。

## <a name="advanced-pattern-matching-with-match_recognize"></a>高級模式與MATCH_RECOGNIZE匹配

**MATCH_RECOGNIZE**是一種高級模式匹配機制，可用於將事件序列與定義良好的正則運算式模式匹配。
例如，如果需要通知管理員兩條連續警告訊息，則在 ATM 操作期間即時監視 ATM 故障。

**輸入**：

| ATM_id | Operation_id | Return_Code | Time |
| --- | --- | --- | --- |
| 1 | "輸入銷" | "Success" | 2017-01-26T00：10：0000000Z |
| 2 | "打開資金槽" | "Success" | 2017-01-26T00：10：07.0000000Z |
| 2 | "關閉資金槽" | "Success" | 2017-01-26T00：10：11.000000Z |
| 1 | "輸入提取數量" | "Success" | 2017-01-26T00：10：08.0000000Z |
| 1 | "打開資金槽" | "警告" | 2017-01-26T00：10：14.0000000Z |
| 1 | "列印銀行餘額" | "警告" | 2017-01-26T00：10：19.0000000Z |

**輸出**：

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "打開資金槽" | 2017-01-26T00：10：14.0000000Z |

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

此查詢至少匹配兩個連續的失敗事件，並在滿足條件時生成警報。
**PATTERN**定義要在匹配中使用的正則運算式，在這種情況下，任意數量的成功操作，然後是至少兩個連續失敗。
成功和失敗使用Return_Code值定義，一旦滿足條件，則使用*ATM_id、* 第一個警告操作和第一個警告時間預測 **"措施**"。

有關詳細資訊，請參閱[MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)。

## <a name="geofencing-and-geospatial-queries"></a>地理圍欄和地理空間查詢
Azure 流分析提供內置地理空間功能，可用於實現車隊管理、行駛共用、互聯汽車和資產跟蹤等方案。
地理空間資料可以採用 GeoJSON 或 WKT 格式作為事件流或參考資料的一部分。
例如，一家專門生產用於列印護照的機器的公司，將其機器租賃給政府和領事館。 這些機器的位置受到嚴格控制，以避免錯放和可能用於偽造護照。 每台機器都裝有 GPS 跟蹤器，該資訊將中繼回 Azure 流分析作業。
製造商希望跟蹤這些機器的位置，並在其中一台機器離開授權區域時收到警報，這樣他們可以遠端禁用、向當局發出警報並取回設備。

**輸入**：

| Equipment_id | Equipment_current_location | Time |
| --- | --- | --- |
| 1 | "點（-122.13288797982818 47.64082002051315）" | 2017-01-26T00：10：0000000Z |
| 1 | "點（-122.13307252987875 47.64081350934929）" | 2017-01-26T00：11：0000000Z |
| 1 | "點（-122.13308862313283 47.6406508603241） | 2017-01-26T00：12：0000000Z |
| 1 | "點（-122.13341048821462 47.64043760861279）" | 2017-01-26T00：13：0000000Z |

**參考資料輸入**：

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON（（-122.13326028450979 47.6409833866794，-122.1326165544621 47.6409833866794，-122.132616554462 1 47.64061471602751，-122.13326028450979 47.64061471602751，-122.13326028450979 47.64093383866794））" |

**輸出**：

| Equipment_id | Equipment_alert_location | Time |
| --- | --- | --- |
| 1 | "點（-122.13341048821462 47.64043760861279）" | 2017-01-26T00：13：0000000Z |

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

該查詢使製造商能夠自動監視機器位置，當機器離開允許的地理圍欄時，會收到警報。 內置地理空間功能允許使用者在沒有協力廠商庫的情況下在查詢中使用 GPS 資料。

有關詳細資訊，請參閱[Azure 流分析一文的地理圍欄和地理空間聚合方案](geospatial-scenarios.md)。

## <a name="get-help"></a>取得說明

有關進一步説明，請嘗試我們的[Azure 流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [使用 Azure 流分析開始](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
