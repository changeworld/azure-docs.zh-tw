---
title: 內嵌歷程記錄遙測資料
description: 本文說明如何內嵌歷程記錄遙測資料。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d47fdb9461786d80d65ee2448cc983a7a8348ff2
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298762"
---
# <a name="ingest-historical-telemetry-data"></a>內嵌歷程記錄遙測資料

本文說明如何將歷史感應器資料內嵌到 Azure FarmBeats。

內嵌裝置和感應器等物聯網（IoT）資源中的歷程記錄資料，是 FarmBeats 的常見案例。 您會建立裝置和感應器的中繼資料，然後以標準格式將歷程記錄資料內嵌至 FarmBeats。

## <a name="before-you-begin"></a>開始之前

繼續進行本文之前，請確定您已從 IoT 裝置安裝 FarmBeats 和收集的歷程記錄資料。 您也需要啟用合作夥伴存取權，如下列步驟所述。

## <a name="enable-partner-access"></a>啟用合作夥伴存取

您必須啟用 Azure FarmBeats 實例的合作夥伴整合。 此步驟會建立可存取您的 Azure FarmBeats 實例作為裝置合作夥伴的用戶端，並提供後續步驟中所需的下列值：

- API 端點：這是 Datahub URL，例如 HTTPs://\<Datahub >. net
- 租用戶識別碼
- 用戶端識別碼
- 用戶端密碼
- EventHub 連接字串

請遵循下列步驟：

>[!NOTE]
> 您必須是系統管理員，才能執行下列步驟。

1. 下載[zip](https://aka.ms/farmbeatspartnerscriptv2)檔案，並將它解壓縮到您的本機磁片磁碟機。 Zip 檔案中將會有一個檔案。
2. 登入 https://portal.azure.com/ 並移至**Azure Active Directory** > **應用程式註冊**。

3. 選取在 FarmBeats 部署過程中建立的**應用程式註冊**。 它的名稱會與您的 FarmBeats Datahub 相同。

4. 選取 [**公開 API** ] > 選取 [**新增用戶端應用程式**]，然後輸入**04b07795-8ddb-461a-bbee-02f9e1bf7b46**並檢查**授權範圍**。 這會授與 Azure CLI （Cloud Shell）的存取權，以執行下列步驟：

5. 開啟 Cloud Shell。 此選項可在 Azure 入口網站右上角的工具列上取得。

    ![Azure 入口網站工具列](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. 請確定環境已設定為**PowerShell**。 根據預設，它會設定為 Bash。

    ![PowerShell 工具列設定](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. 上傳 Cloud Shell 實例中步驟1的檔案。

    ![上傳工具列按鈕](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. 移至上傳檔案的目錄。 根據預設，檔案會上傳至使用者名稱底下的主目錄。

9. 執行下列指令碼。 腳本會要求提供租使用者識別碼，您可以從**Azure Active Directory** > 的 **[總覽] 頁面**取得。

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1   

    ```

10. 依照畫面上的指示來捕獲**API 端點**、租使用者**識別碼**、**用戶端識別碼**、**用戶端密碼**和**EventHub 連接字串**的值。
## <a name="create-device-or-sensor-metadata"></a>建立裝置或感應器中繼資料

 現在您已有必要的認證，您可以定義裝置和感應器。 若要這麼做，請呼叫 FarmBeats Api 來建立中繼資料。 請務必呼叫 Api，做為您在上一節中建立的用戶端應用程式。

 FarmBeats Datahub 具有下列 Api，可讓您建立及管理裝置或感應器中繼資料。

 > [!NOTE]
 > 身為合作夥伴，您只能存取讀取、建立和更新中繼資料; **[刪除] 選項僅限於合作夥伴。**

- /**devicemodel 傳遞**： devicemodel 傳遞對應至裝置的中繼資料，例如製造商和裝置類型，也就是閘道或節點。
- /**裝置**：裝置對應至存在於伺服器陣列上的實體裝置。
- /**SensorModel**： SensorModel 對應于感應器的中繼資料，例如製造商、感應器類型，也就是類比或數位，以及感應器測量，例如環境溫度和壓力。
- /**感應器**：感應器會對應到記錄值的實體感應器。 感應器通常會連接到裝置識別碼為的裝置。  


|        DeviceModel   |  建議   |
| ------- | -------             |
|     類型（節點、閘道）        |          裝置節點或閘道的類型      |
|          製造商            |         製造商的名稱    |
|  ProductCode                    |  裝置產品代碼或型號名稱或數位。 例如，EnviroMonitor # 6800。  |
|            連接埠          |     埠名稱和類型，也就是數位或類比。
|     名稱                 |  用來識別資源的名稱。 例如，模型名稱或產品名稱。
      描述     | 提供模型的有意義描述。
|    屬性          |    製造商提供的其他屬性。   |
|    **裝置**             |                      |
|   DeviceModelId     |     相關聯裝置型號的識別碼。  |
|  HardwareId          | 裝置的唯一識別碼，例如 MAC 位址。
|  ReportingInterval        |   報告間隔（以秒為單位）。
|  Location            |  裝置緯度（-90 到 + 90）、經度（-180 到180）和提高許可權（以計量計）。   
|ParentDeviceId       |    此裝置所連接之父裝置的識別碼。 例如，連接到閘道的節點。 節點已 parentDeviceId 為閘道。  |
|    名稱            | 用來識別資源的名稱。 裝置合作夥伴必須傳送與合作夥伴端上的裝置名稱一致的名稱。 如果合作夥伴裝置名稱是 [使用者定義]，則相同的使用者定義名稱應傳播至 FarmBeats。|
|     描述       |      提供有意義的描述。 |
|     屬性    |  製造商提供的其他屬性。
|     **SensorModel**        |          |
|       類型（類比、數位）          |      感應器的類型，不論是類比或數位。       |
|          製造商            |       感應器的製造商。     |
|     ProductCode| 產品代碼或型號名稱或數位。 例如，RS-CO2-N01。 |
|       SensorMeasures > 名稱       | 感應器量值的名稱。 僅支援小寫。 如需不同深度的測量，請指定深度。 例如，soil_moisture_15cm。 此名稱必須與遙測資料一致。  |
|          SensorMeasures > DataType       |遙測資料類型。 目前支援 double。|
|    SensorMeasures > 類型    |感應器遙測資料的測量類型。 系統定義的類型為 AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、壓力、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 若要新增更多，請參閱/ExtendedType API。|
|        SensorMeasures > 單位              | 感應器遙測資料的單位。 系統定義的單位為 NoUnit、攝氏、華氏、開氏、Rankine、Pascal、水星、PSI、毫米、釐米、計量、英寸、墊腳、英里、公里、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、學位、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、百分比、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、升、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond、InchesPerHour 若要新增更多，請參閱/ExtendedType API。|
|    SensorMeasures > AggregationType    |  值可以是 none、average、maximum、下限或 StandardDeviation。  |
|          名稱            | 用來識別資源的名稱。 例如，模型名稱或產品名稱。  |
|    描述        | 提供模型的有意義描述。  |
|   屬性       |  製造商提供的其他屬性。  |
|    **感應器**      |          |
| HardwareId          |   製造商所設定之感應器的唯一識別碼。 |
|  SensorModelId     |    相關聯感應器型號的識別碼。   |
| Location          |  感應器緯度（-90 到 + 90）、經度（-180 到180）和提高許可權（以計量計）。|
|   埠 > 名稱        |  裝置上感應器連線的埠名稱和類型。 這必須與裝置模型中所定義的名稱相同。 |
|    裝置識別碼  |    感應器所連接之裝置的識別碼。     |
| 名稱            |   用來識別資源的名稱。 例如，感應器名稱或產品名稱，以及型號或產品代碼。|
|    描述      | 提供有意義的描述。 |
|    屬性        |製造商提供的其他屬性。 |

如需物件的詳細資訊，請參閱[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

### <a name="api-request-to-create-metadata"></a>建立中繼資料的 API 要求

若要提出 API 要求，請將 HTTP （POST）方法、API 服務的 URL，以及用來查詢、提交資料至、建立或刪除要求的資源的 URI 結合在一起。 然後，您可以新增一或多個 HTTP 要求標頭。 API 服務的 URL 是 API 端點，也就是 Datahub URL （HTTPs://\<yourdatahub >. azurewebsites. net）。  

### <a name="authentication"></a>驗證

FarmBeats Datahub 會使用持有人驗證，其需要在上一節中產生的下列認證：

- 用戶端識別碼
- 用戶端密碼
- 租用戶識別碼

呼叫者可以使用這些認證來要求存取權杖。 在後續的 API 要求中，必須在標頭區段中傳送權杖，如下所示：

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

下列 Python 程式碼範例會提供存取權杖，可用於後續對 FarmBeats 的 API 呼叫： 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP 要求標頭**

以下是您對 FarmBeats Datahub 進行 API 呼叫時，必須指定的最常見要求標頭：

- **內容類型**： application/json
- **授權**：持有人 < 存取權杖 >
- **Accept**： application/json

### <a name="input-payload-to-create-metadata"></a>用來建立中繼資料的輸入承載

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

裝置

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
感應器

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
下列範例要求會建立裝置。 此要求具有輸入 JSON 做為要求主體的承載。

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

以下是 Python 中的範例程式碼。 此範例中使用的存取權杖，與驗證期間所收到的相同。

```python
import requests
import json

# Got access token - Calling the Device Model API
headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```


> [!NOTE]
> Api 會針對每個建立的實例傳回唯一的識別碼。 您必須保留識別碼，才能傳送對應的遙測訊息。

### <a name="send-telemetry"></a>傳送遙測

現在您已在 FarmBeats 中建立裝置和感應器，可以傳送相關聯的遙測訊息。

### <a name="create-a-telemetry-client"></a>建立遙測用戶端

您必須將遙測傳送至 Azure 事件中樞以進行處理。 Azure 事件中樞是一種服務，可讓您從連線的裝置和應用程式內嵌即時資料（遙測）。 若要將遙測資料傳送至 FarmBeats，請建立用戶端，將訊息傳送至 FarmBeats 中的事件中樞。 如需有關傳送遙測的詳細資訊，請參閱[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)。

### <a name="send-a-telemetry-message-as-the-client"></a>以用戶端的身分傳送遙測訊息

建立事件中樞用戶端的連線之後，您可以將訊息傳送至事件中樞做為 JSON。

以下的範例 Python 程式碼會將遙測當做用戶端傳送至指定的事件中樞：

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

將歷程感應器資料格式轉換為 Azure FarmBeats 瞭解的標準格式。 標準訊息格式如下所示：

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<values>"
        }
      ]
    }
 ]
}
```

新增對應的裝置和感應器之後，請取得遙測訊息中的裝置識別碼和感應器識別碼，如上一節中所述。

以下是遙測訊息的範例：


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```

## <a name="troubleshooting"></a>疑難排解

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>從感應器內嵌歷程/串流資料之後，無法查看遙測資料

**徵兆**：已部署裝置或感應器，而且您已在 FarmBeats 上建立裝置/感應器，並將遙測內嵌至 EventHub，但無法取得或查看 FarmBeats 上的遙測資料。

矯正**措施：**

1. 請確定您已完成適當的合作夥伴註冊-您可以前往 datahub swagger，流覽至/Partner API、執行 Get 並檢查是否已註冊夥伴，以檢查此項。 如果沒有，請遵循[這裡的步驟](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)來新增合作夥伴。

2. 請確定您已使用合作夥伴用戶端認證建立中繼資料（Devicemodel 傳遞、裝置、SensorModel、感應器）。

3. 請確定您已使用正確的遙測訊息格式（如下所示）：

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```


## <a name="next-steps"></a>後續步驟

如需以 REST API 為基礎的整合詳細資料的詳細資訊，請參閱[REST API](rest-api-in-azure-farmbeats.md)。
