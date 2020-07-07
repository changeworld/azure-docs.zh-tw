---
title: 查詢內嵌的遙測資料
description: 本文說明如何查詢內嵌遙測資料。
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80349809"
---
# <a name="query-ingested-telemetry-data"></a>查詢內嵌的遙測資料

本文說明如何從 Azure FarmBeats 查詢內嵌感應器資料。

從物聯網（IoT）資源（例如裝置和感應器）內嵌資料是 FarmBeats 的常見案例。 您會建立裝置和感應器的中繼資料，然後以標準格式將歷程記錄資料內嵌至 FarmBeats。 在 FarmBeats Datahub 上提供感應器資料之後，我們可以查詢相同的，以產生可操作的深入解析或建立模型。

## <a name="before-you-begin"></a>開始之前

繼續進行本文之前，請確定您已將 FarmBeats 和內嵌感應器遙測資料從 IoT 裝置安裝到 FarmBeats。

若要內嵌感應器遙測資料，請造訪內嵌歷程[記錄遙測資料](ingest-historical-telemetry-data-in-azure-farmbeats.md)

在繼續之前，您也必須確定您熟悉 FarmBeats REST Api，因為您會使用 Api 來查詢內嵌遙測。 如需 FarmBeats Api 的詳細資訊，請參閱[FARMBEATS REST api](rest-api-in-azure-farmbeats.md)。 **確定您能夠對 FarmBeats Datahub 端點提出 API 要求**。

## <a name="query-ingested-sensor-telemetry-data"></a>查詢內嵌感應器遙測資料

有兩種方式可存取和查詢來自 FarmBeats 的遙測資料：

- API 和
- 時間序列深入解析（TSI）。

### <a name="query-using-rest-api"></a>使用 REST API 查詢

遵循下列步驟，使用 FarmBeats REST Api 來查詢內嵌感應器遙測資料：

1. 識別您感興趣的感應器。 若要這麼做，您可以在/Sensor API 上提出 GET 要求。

> [!NOTE]
> 感興趣的感應器物件的**識別碼**和**sensorModelId** 。

2. 如步驟1所述，在**sensorModelId**的/SensorModel API 上建立 GET/{id}。 「感應器型號」包含來自感應器之內嵌遙測的所有中繼資料和詳細資訊。 例如，感應器**模型**物件內的**感應器量值**，有關于哪些量值是感應器傳送的，以及在哪些類型和單位中的詳細資料。 例如，

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
記下感應器型號的 GET/{id} 呼叫回應。

3. 使用下列輸入承載在/Telemetry API 上進行 POST 呼叫

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
4. 來自/Telemetry API 的回應看起來會像這樣：

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
在上述範例回應中，查詢的感應器遙測會在兩個時間戳記中提供兩個時間戳記的資料，以及量值名稱（"moist_soil_last"）和報告遙測的值。 您必須參考相關聯的感應器模型（如步驟2中所述），以解讀所報告值的類型和單位。

### <a name="query-using-azure-time-series-insights-tsi"></a>使用 Azure 時間序列深入解析（TSI）進行查詢

FarmBeats 利用[Azure 時間序列深入解析（TSI）](https://azure.microsoft.com/services/time-series-insights/)來內嵌、儲存、查詢和視覺化 IoT 層級的資料--高度內容相關且針對時間序列優化的資料。

系統會在 EventHub 上接收遙測資料，然後處理並推送至 FarmBeats 資源群組內的 TSI 環境。 然後可以從 TSI 直接查詢資料。 如需詳細資訊，請參閱[TSI 檔](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

依照下列步驟將 TSI 上的資料視覺化：

1. 移至**Azure 入口網站**  >  **FarmBeats DataHub 資源群組**> 選取 [**時間序列深入解析**環境（tsi）] > [**資料存取原則**]。 新增具有讀取者或參與者存取權的使用者。
2. 移至**時間序列深入解析**環境的 [**總覽**] 頁面（tsi-xxxx），然後選取 [**時間序列深入解析總管 URL**]。 您現在將能夠以視覺化方式呈現內嵌遙測。

除了儲存、查詢和視覺效果的遙測之外，TSI 也能夠整合到 Power BI 的儀表板。 如需詳細資訊，請參閱[這裡]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>後續步驟

您現在已從 Azure FarmBeats 實例查詢感應器資料。 現在，請瞭解如何為您的伺服器陣列[產生對應](generate-maps-in-azure-farmbeats.md#generate-maps)。
