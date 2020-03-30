---
title: 查詢引入遙測資料
description: 本文介紹如何查詢引入的遙測資料。
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349809"
---
# <a name="query-ingested-telemetry-data"></a>查詢引入遙測資料

本文介紹如何查詢 Azure FarmBeats 中引入的感應器資料。

從物聯網 （IoT） 資源（如設備和感應器）中引入資料是 FarmBeats 中常見的方案。 為設備和感應器創建中繼資料，然後以標準格式將歷史資料引入 FarmBeats。 一旦在 FarmBeats Datahub 上提供了感應器資料，我們可以查詢相同的資料來生成可操作的見解或生成模型。

## <a name="before-you-begin"></a>開始之前

在繼續本文之前，請確保已安裝 FarmBeats 並將感應器遙測資料從 IoT 設備引入 FarmBeats。

要引入感應器遙測資料，請訪問[引入歷史遙測資料](ingest-historical-telemetry-data-in-azure-farmbeats.md)

在繼續操作之前，還需要確保熟悉 FarmBeats REST API，因為您將使用 API 查詢引入的遙測資料。 有關伺服器場節拍 API 的詳細資訊，請參閱[伺服器場節拍 REST API](rest-api-in-azure-farmbeats.md)。 **確保您能夠向伺服器場資料庫終結點發出 API 請求**。

## <a name="query-ingested-sensor-telemetry-data"></a>查詢引入感應器遙測資料

有兩種方法可以從 FarmBeats 訪問和查詢遙測資料：

- API 和
- 時間序列見解 （TSI）。

### <a name="query-using-rest-api"></a>使用 REST API 查詢

按照以下步驟使用 FarmBeats REST API 查詢引入的感應器遙測資料：

1. 確定您感興趣的感應器。 您可以通過對 /感應器 API 發出 GET 請求來執行此操作。

> [!NOTE]
> 相關感應器物件的**ID**和**感應器模型 Id。**

2. 在 /感應器模型 API 上為**感應器 ModelId**製作 GET/{id}，如步驟 1 所述。 "感應器模型"具有感應器引入遙測的所有中繼資料和詳細資訊。 例如，"**感應器模型"** 物件中的**感應器測量**具有有關感應器發送的度量值以及類型和單位的詳細資訊。 例如，

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
記下來自感應器型號 GET/{id} 調用的回應。

3. 使用以下輸入負載對 /遙測 API 執行 POST 調用

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. 來自 /遙測 API 的回應如下所示：

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
在上述示例回應中，查詢的感應器遙測資料提供了兩個時間戳記的資料以及兩個時間戳記中的度量值名稱（"moist_soil_last"）和報告遙測的值。 您需要參考關聯的感應器模型（如步驟 2 中所述），以解釋報告值的類型和單位。

### <a name="query-using-azure-time-series-insights-tsi"></a>使用 Azure 時間序列見解 （TSI） 查詢

FarmBeats 利用[Azure 時間序列見解 （TSI）](https://azure.microsoft.com/services/time-series-insights/)在 IoT 比例範圍內引入、存儲、查詢和視覺化資料，這些資料高度上下文化和針對時間序列進行了優化。

遙測資料在 EventHub 上接收，然後處理並推送到 FarmBeats 資源組中的 TSI 環境。 然後可以直接從 TSI 查詢資料。 有關詳細資訊，請參閱[TSI 文檔](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

按照以下步驟在 TSI 上視覺化資料：

1. 轉到**Azure 門戶** > **伺服器場資料Hub 資源組**>選擇**時間序列見解**環境 （tsi-xxx） >**資料訪問策略**。 使用讀取器或參與者存取權限添加使用者。
2. 轉到**時間序列見解**環境 （tsi-xxxx）**的概述**頁面，然後選擇**時序見解資源管理器 URL**。 現在，您將能夠視覺化引入的遙測資料。

除了存儲、查詢和視覺化遙測之外，TSI 還支援集成到 Power BI 儀表板。 有關詳細資訊，請參閱[此處]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>後續步驟

現在，您從 Azure FarmBeats 實例中查詢了感應器資料。 現在，瞭解如何為伺服器場[生成地圖](generate-maps-in-azure-farmbeats.md#generate-maps)。
