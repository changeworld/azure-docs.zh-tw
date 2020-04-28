---
title: 使用 Azure 串流分析處理即時 IoT 資料流程
description: IoT 感應器標記和具有串流分析的資料串流與即時資料處理
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 0755131f7d8071e37eadc1339ebc5e122725fa71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75426252"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>使用 Azure 串流分析處理即時 IoT 資料流程

在本文中，您將瞭解如何建立串流處理邏輯，以從物聯網（IoT）裝置收集資料。 您可以使用真實世界的物聯網（IoT）使用案例來示範如何快速且經濟地建立您的解決方案。

## <a name="prerequisites"></a>Prerequisites

* 建立免費的[Azure 訂](https://azure.microsoft.com/pricing/free-trial/)用帳戶。
* 從[GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)下載範例查詢和資料檔案。

## <a name="scenario"></a>狀況

Contoso 是一家工業自動化空間的公司，他們已完全自動化其製造程序。 這家製造廠的機械裝置具有感應器，可以發出即時資料串流。 在此案例中，生產現場經理想要取得感應器資料的即時深入資訊，來尋找模式並且對其採取動作。 您可以在感應器資料上使用串流分析查詢語言（SAQL），以從傳入的資料流程中尋找感興趣的模式。

在此範例中，資料是從德克薩斯儀器感應器標記裝置產生。 資料的承載格式是 JSON，如下所示：

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

在真實世界的案例中，您可以擁有數百個此類感應器，產生事件作為串流。 在理想情況下，閘道器裝置會執行程式碼，以將這些事件推送至 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)或 [Azure IoT 中樞](https://azure.microsoft.com/services/iot-hub/)。 串流分析作業會從事件中樞擷取這些事件，並針對串流執行即時分析查詢。 接著，您可以將結果傳送至其中一個[支援輸出](stream-analytics-define-outputs.md)。

為了方便使用，本入門指南會提供擷取自實際感應器標籤裝置的範例資料檔。 您可以對範例資料執行查詢，然後查看結果。 在後續教學課程中，您將了解如何將您的作業連接至輸入和輸出，並將它們部署到 Azure 服務。

## <a name="create-a-stream-analytics-job"></a>建立串流分析作業

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，從左側導覽功能表中選取 [ **+ 建立資源**]。 然後，從 [**分析**] 選取 [**串流分析作業**]。
   
    ![建立新的串流分析作業](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. 輸入唯一的工作名稱，並確認訂用帳戶為您作業適用的正確訂用帳戶。 建立新的資源群組，或從您的訂用帳戶中選取現有的一個。

1. 選取作業的 [位置]。 針對您的資源群組和所有資源使用相同的位置，以提高處理速度和降低成本。 進行設定之後，請選取 [**建立**]。
   
    ![建立新的串流分析作業詳細資料](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>建立 Azure 串流分析查詢
建立作業之後的下一個步驟是撰寫查詢。 您可以針對範例資料測試查詢，而不需要將輸入或輸出連接至您的作業。

從 GitHub 下載[helloworldasa-inputstream.json 檔名-InputStream。](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) 然後，在 Azure 入口網站中流覽至您的 Azure 串流分析作業。

從左側功能表中選取 [**作業拓撲**] 底下的 [**查詢**]。 然後選取 **[上傳範例輸入**]。 `HelloWorldASA-InputStream.json`上傳檔案，然後選取 **[確定]**。

![串流分析儀表板查詢圖格](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

請注意，資料的預覽會自動填入 [**輸入預覽**] 資料表中。

![範例輸入資料預覽](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>查詢：封存未經處理資料

形式最簡單的查詢是傳遞查詢，其會將所有輸入資料封存至其指定的輸出。 此查詢是在新的 Azure 串流分析作業中填入的預設查詢。

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

選取 [**測試查詢**]，並在 [**測試結果**] 資料表中查看結果。

![測試串流分析查詢的結果](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>查詢：根據條件篩選資料

讓我們嘗試根據條件來篩選結果。 我們想要僅針對來自 “sensorA” 的事件顯示結果。

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

在編輯器中貼上查詢，然後選取 [**測試查詢**] 來檢查結果。

![篩選資料串流](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>查詢：觸發商務工作流程的警示

讓我們的查詢變得更為詳細。 對於每一個類型的感應器，我們想要監視每 30 秒時段的平均溫度，並且只顯示平均溫度高於 100 度的結果。

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30 秒篩選查詢](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

您應該會看到僅包含245個數據列的結果，以及平均溫度大於100的感應器名稱。 此查詢會依據 **dspl** 分組事件串流，dspl 是感應器名稱，而**輪轉視窗**為 30 秒。 時態性查詢必須陳述您想要時間進行的方式。 藉由使用**TIMESTAMP by**子句，您就可以指定**OUTPUTTIME**資料行，將時間與所有時態性計算產生關聯。 如需詳細資訊，請參閱[時間管理](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics)和[視窗化函數](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics)。

### <a name="query-detect-absence-of-events"></a>查詢：測不存在的事件

我們要如何撰寫查詢來尋找輸入不足事件？ 讓我們找出上次感應器傳送資料的時間，然後在接下來的5秒內未傳送事件。

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![偵測不存在的事件](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

此處，我們對相同的資料串流使用 **LEFT OUTER** 聯結 (自我聯結)。 對於 **INNER** 聯結，只會在找到相符項目時傳回結果。  對於 **LEFT OUTER** 聯結，如果聯結左側的事件不相符，則會針對右側的所有資料行傳回具有 NULL 的資料列。 這項技術對於尋找不存在的事件很有用。 如需詳細資訊，請參閱[JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics)。

## <a name="conclusion"></a>結論

本文的目的是要示範如何撰寫不同的串流分析查詢語言查詢，並在瀏覽器中查看結果。 不過，這只是為了讓您開始著手。 串流分析支援各種輸入和輸出，並甚至可以使用 Azure Machine Learning 中的函式，使其成為分析資料串流的健全工具。 如需如何撰寫查詢的詳細資訊，請閱讀有關[常見查詢模式](stream-analytics-stream-analytics-query-patterns.md)的文章。

