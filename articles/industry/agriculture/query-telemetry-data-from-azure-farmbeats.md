---
title: 查詢內嵌的遙測資料
description: 本文說明如何查詢內嵌遙測資料。
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: b9067e2f78c8098d4a21263ac89caf03da631274
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677477"
---
# <a name="query-ingested-telemetry-data"></a>查詢內嵌的遙測資料

本文說明如何從 Azure FarmBeats 查詢內嵌感應器資料。

從物聯網 (IoT) 資源（例如裝置和感應器）擷取資料是 FarmBeats 中的常見案例。 您可以建立裝置和感應器的中繼資料，然後將歷程記錄資料內嵌至以標準格式 FarmBeats 的資料。 一旦 FarmBeats Datahub 有可用的感應器資料，我們就可以查詢相同的來產生可採取動作的見解或組建模型。

## <a name="before-you-begin"></a>開始之前

繼續進行本文之前，請確定您已將 FarmBeats 和內嵌感應器遙測資料從 IoT 裝置安裝到 FarmBeats。

若要內嵌感應器遙測資料，請造訪內嵌歷程 [記錄遙測資料](ingest-historical-telemetry-data-in-azure-farmbeats.md)

繼續進行之前，您也必須確定您熟悉 FarmBeats REST Api，因為您將使用 Api 來查詢內嵌遙測。 如需有關 FarmBeats Api 的詳細資訊，請參閱 [FARMBEATS REST api](rest-api-in-azure-farmbeats.md)。 **確定您能夠對您的 FarmBeats Datahub 端點提出 API 要求** 。

## <a name="query-ingested-sensor-telemetry-data"></a>查詢內嵌感應器遙測資料

從 FarmBeats 存取和查詢遙測資料的方式有兩種：

- API 和
- 時間序列深入解析 (TSI) 。

### <a name="query-using-rest-api"></a>使用 REST API 查詢

遵循下列步驟，使用 FarmBeats REST Api 查詢內嵌感應器遙測資料：

1. 找出您感興趣的感應器。 若要這樣做，您可以在/Sensor API 上提出 GET 要求。

> [!NOTE]
> 感興趣的感應器物件的 **識別碼** 和 **sensorModelId** 。

2. 針對 **sensorModelId** 在/SensorModel API 上建立 GET/{id}，如步驟1所述。 「感應器模型」具有來自感應器之內嵌遙測的所有中繼資料和詳細資料。 例如，感應器 **模型** 物件內的 **感應器量值** 會詳細說明感應器傳送的量值，以及哪些類型和單位。 例如，套用至物件的

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
記下 GET/{id} 呼叫中的感應器型號回應。

3. 使用下列輸入承載對/Telemetry API 進行 POST 呼叫

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
在上述的回應範例中，查詢的感應器遙測會提供兩個時間戳記的資料，以及量值名稱 ( "moist_soil_last" ) 以及在兩個時間戳記中報告的遙測值。 您將需要參考相關聯的感應器模型 (如步驟 2) 所述，以解讀報告值的類型和單位。

### <a name="query-using-azure-time-series-insights-tsi"></a>使用 Azure 時間序列深入解析 (TSI 進行查詢) 

FarmBeats 利用 [Azure 時間序列深入解析 (TSI) ](https://azure.microsoft.com/services/time-series-insights/) 來內嵌、儲存、查詢和視覺化 IoT 規模的資料--高度內容相關並針對時間序列優化的資料。

遙測資料會在 EventHub 上收到，然後處理並推送至 FarmBeats 資源群組內的 TSI 環境。 然後可以從 TSI 直接查詢資料。 如需詳細資訊，請參閱 [TSI 檔](../../time-series-insights/time-series-insights-explorer.md)

遵循下列步驟來視覺化 TSI 上的資料：

1. 移至 **Azure 入口網站**  >  **FarmBeats DataHub 資源群組** > 選取 **時間序列深入** 解析環境 (tsi-xxxx) > **資料存取原則** 。 新增具有讀者或參與者存取權的使用者。
2. 移至 **時間序列深入** 解析環境 (tsi-xxxx) 的 **總覽** 頁面，然後選取 **時間序列深入解析總管 URL** 。 您現在可以將內嵌遙測視覺化。

除了儲存、查詢和視覺化遙測之外，TSI 也可整合至 Power BI 的儀表板。 如需詳細資訊，請參閱 [這裡]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>後續步驟

您現在已查詢 Azure FarmBeats 實例中的感應器資料。 現在，瞭解如何為您的伺服器陣列 [產生對應](generate-maps-in-azure-farmbeats.md#generate-maps) 。