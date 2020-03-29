---
title: 內嵌歷程記錄遙測資料
description: 本文介紹如何引入歷史遙測資料。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b0b9d62e8761cfb67d0642d8e5a97e7d1f05af12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064459"
---
# <a name="ingest-historical-telemetry-data"></a>內嵌歷程記錄遙測資料

本文介紹如何將歷史感應器資料引入 Azure FarmBeats。

從物聯網 （IoT） 資源（如設備和感應器）中引入歷史資料是 FarmBeats 中的常見方案。 為設備和感應器創建中繼資料，然後以標準格式將歷史資料引入 FarmBeats。

## <a name="before-you-begin"></a>開始之前

在繼續本文之前，請確保您已安裝 FarmBeats 並從 IoT 設備收集歷史資料。 您還需要啟用合作夥伴訪問，如以下步驟所述。

## <a name="enable-partner-access"></a>啟用合作夥伴存取權限

您需要啟用與 Azure FarmBeats 實例的合作夥伴集成。 此步驟將創建一個用戶端，該用戶端可作為設備合作夥伴訪問 Azure FarmBeats 實例，並為您提供後續步驟中所需的以下值：

- API 終結點：這是 Datahub URL，例如，HTTPs://\<資料中心>.azure網站.net
- 租用戶識別碼
- 用戶端識別碼
- 用戶端密碼
- 事件Hub連接字串

請遵循下列步驟：

> [!NOTE]
> 您必須是管理員才能執行以下步驟。

1. 下載[ZIP 檔案](https://aka.ms/farmbeatspartnerscriptv2)，並將其提取到您的本地磁碟機。 ZIP 檔案內將有一個檔。

2. 登錄到https://portal.azure.com/並轉到 Azure**活動目錄** > **應用註冊**。

3. 選擇作為伺服器場節拍部署的一部分創建**的應用註冊**。 它將具有與您的伺服器場節拍資料hub相同的名稱。

4. 選擇**公開 API** >**選擇添加用戶端應用程式**並輸入**04b07795-8ddb-461a-bbee-02f9e1bf7b46**並檢查**授權範圍**。 這將授予 Azure CLI（雲外殼）以執行以下步驟的存取權限：

5. 開啟 Cloud Shell。 此選項在 Azure 門戶右上角的工具列上可用。

    ![Azure 門戶工具列](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. 確保環境設置為**PowerShell**。 預設情況下，它設置為 Bash。

    ![PowerShell 工具列設置](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. 從雲殼實例中的步驟 1 上載檔。

    ![上傳工具列按鈕](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. 轉到上載檔的目錄。 預設情況下，檔會以使用者名上載到主目錄。

9. 執行下列指令碼。 該腳本要求使用租戶 ID，可以從**Azure 活動目錄** > **概述頁**獲取該 ID。

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1   

    ```

10. 按照螢幕上的說明捕獲**API 終結點**、**租戶 ID、****用戶端 ID、****用戶端金鑰**和**事件Hub連接字串**的值。

## <a name="create-device-or-sensor-metadata"></a>創建設備或感應器中繼資料

 現在，您已經擁有了所需的憑據，您可以定義設備和感應器。 為此，請通過調用 FarmBeats API 創建中繼資料。 請確保將 API 稱為您在上述部分中創建的用戶端應用。

 FarmBeats Datahub 具有以下 API，用於創建和管理設備或感應器中繼資料。

 > [!NOTE]
 > 作為合作夥伴，您只能讀取、創建和更新中繼資料;**刪除選項僅限於合作夥伴。**

- /**設備模型**：設備模型對應于設備的中繼資料，例如製造商和裝置類型，即閘道或節點。
- /**設備**：設備對應于伺服器場上存在的物理設備。
- /**感應器模型**：感應器模型對應于感應器的中繼資料，如製造商、類比或數位感應器的類型以及感應器測量（如環境溫度和壓力）。
- /**感應器**：感應器對應于記錄值的物理感應器。 感應器通常連接到具有裝置識別碼 的設備。  


|        DeviceModel   |  建議   |
| ------- | -------             |
|     類型（節點、閘道）        |          裝置類型 - 節點或閘道      |
|          製造商            |         製造商名稱    |
|  產品代碼                    |  設備產品代碼或型號名稱或編號。 例如，環境監視器#6800。  |
|            連接埠          |     埠名稱和類型，即數位或類比。
|     名稱                 |  名稱以標識資源。 例如，型號名稱或產品名稱。
      描述     | 提供模型的有意義的描述。
|    屬性          |    製造商的其他屬性。   |
|    **裝置**             |                      |
|   設備模型 Id     |     關聯設備型號的 ID。  |
|  硬體 Id          | 設備的唯一 ID，如 MAC 位址。
|  ReportingInterval        |   報告間隔（以秒為單位）。
|  Location            |  設備緯度（-90 至 +90）、經度（-180 至 180）和高程（以米為單位）。   
|父設備 Id       |    此設備連接到的父設備的 ID。 例如，連接到閘道的節點。 節點具有父設備 Id 作為閘道。  |
|    名稱            | 用於標識資源的名稱。 設備合作夥伴必須發送與合作夥伴端的設備名稱一致的名稱。 如果合作夥伴設備名稱是使用者定義的，則同一使用者定義的名稱應傳播到 FarmBeats。|
|     描述       |      提供有意義的說明。 |
|     屬性    |  製造商的其他屬性。
|     **感應器模型**        |          |
|       類型（類比、數位）          |      感應器的類型，無論是類比的還是數位的。       |
|          製造商            |       感應器的製造商。     |
|     產品代碼| 產品代碼或型號名稱或編號。 例如，RS-CO2-N01。 |
|       感應器測量>名稱       | 感應器測量值的名稱。 僅支援小寫。 對於不同深度的測量，請指定深度。 例如，soil_moisture_15cm。 此名稱必須與遙測資料一致。  |
|          感應器測量>資料類型       |遙測資料類型。 目前，支援雙精度值。|
|    感應器測量>類型    |感應器遙測資料的測量類型。 系統定義的類型是環境溫度、CO2、深度、電導性、葉濕性、長度、液位、硝酸鹽、O2、PH、磷酸鹽、點InTime、鉀、壓力、雨量計、相對濕度、鹽度、土壤水分、土壤溫度，太陽輻射，狀態，時間持續時間，紫外線輻射，紫外線指數，體積，風向，風潤，風速，蒸發，PAR。 要添加更多，請參閱 /擴展類型 API。|
|        感應器測量>單元              | 感應器遙測資料單位。 系統定義的單位為無單位、攝氏度、華氏、開爾文、蘭金、 帕斯卡、汞、PSI、米數、釐米、米、英寸、英尺、英里、千米、英里珀小時、英里珀秒、KMPERhour、KMperper秒、米佩爾小時、米佩爾秒、度、瓦尺、千瓦珀平米、毫瓦佩爾平米、米千瓦百萬計、體積水含量、百分比、零件百萬、微摩爾、微摩爾、百萬千瓦、微摩爾、微摩爾、百萬計、千里珀珀米、千瓦位、百萬計、千米、百萬計、千米、百萬計、千里尺、百萬計、千米、百萬計、千米、百萬計、千米、百萬計、千米、百萬計、十億位元、千里珀爾、千兆克、百萬元、千兆克、十億位元、千萬體、百萬計、千米計、千米、百萬計、千里尺、百萬元、千尺MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、體積成分、升、毫升、秒、UnixTimestamp、微摩爾PerMeterSquaredPer秒、英寸PerHour添加更多內容，請參閱/擴展類型 API。|
|    感應器測量>聚合類型    |  值可以是無、平均值、最大值、最小值或標準差。  |
|          名稱            | 名稱以標識資源。 例如，型號名稱或產品名稱。  |
|    描述        | 提供模型的有意義的描述。|
|   屬性       |  製造商的其他屬性。|
|    **感應器**      |          |
| 硬體 Id          |   製造商設置的感應器的唯一 ID。|
|  感應器模型Id     |    關聯感應器型號的 ID。|
| Location          |  感應器緯度（-90 至 +90）、經度（-180 至 180）和高程（以米為單位）。|
|   埠>名稱        |  感應器在設備上連接到的埠的名稱和類型。 這需要與設備模型中定義的名稱相同。|
|    裝置識別碼  |    感應器連接到的裝置識別碼。 |
| 名稱            |   名稱以標識資源。 例如，感應器名稱或產品名稱、型號或產品代碼。|
|    描述      | 提供有意義的說明。|
|    屬性        |製造商的其他屬性。|

有關物件的詳細資訊，請參閱[斯瓦格](https://aka.ms/FarmBeatsDatahubSwagger)。

### <a name="api-request-to-create-metadata"></a>創建中繼資料的 API 請求

要發出 API 請求，請將 HTTP （POST） 方法、API 服務的 URL 和 URI 合併到資源以查詢、將資料提交到請求、創建或刪除請求。 然後添加一個或多個 HTTP 要求標頭。 API 服務的 URL 是 API 終結點，即 Datahub URL（HTTPs://\<您的資料中心>.azure網站.net）。  

### <a name="authentication"></a>驗證

FarmBeats Datahub 使用無記名身份驗證，這需要上一節中生成的以下憑據：

- 用戶端識別碼
- 用戶端密碼
- 租用戶識別碼

使用這些憑據，調用方可以請求訪問權杖。 權杖必須在後續 API 請求中（標頭部分）中發送，如下所示：

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

以下示例 Python 代碼提供了訪問權杖，可用於對 FarmBeats 的後續 API 呼叫： 

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

以下是對 FarmBeats Datahub 進行 API 呼叫時必須指定的最常見請求標頭：

- **內容類型**： 應用程式 /json
- **授權**： 承載<訪問權杖>
- **接受**：應用程式/json

### <a name="input-payload-to-create-metadata"></a>輸入有效負載以創建中繼資料

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

感應器模型

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

以下示例請求創建設備。 此請求具有將 JSON 作為請求正文的有效負載。

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

下面是 Python 中的示例代碼。 此示例中使用的訪問權杖與身份驗證期間接收的訪問權杖相同。

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
> API 返回所創建的每個實例的唯一 ID。 您必須保留指示，才能發送相應的遙測消息。

### <a name="send-telemetry"></a>傳送遙測

現在，您已經創建了 FarmBeats 中的設備和感應器，您可以發送關聯的遙測消息。

### <a name="create-a-telemetry-client"></a>創建遙測用戶端

您必須將遙測發送到 Azure 事件中心進行處理。 Azure 事件中心是一種服務，它支援從連接的設備和應用程式引入即時資料（遙測）。 要向 FarmBeats 發送遙測資料，請創建一個用戶端，該用戶端將消息發送到 FarmBeats 中的事件中心。 有關發送遙測的詳細資訊，請參閱 Azure[事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)。

### <a name="send-a-telemetry-message-as-the-client"></a>將遙測消息作為用戶端發送

建立作為事件中心用戶端的連接後，可以將消息作為 JSON 發送到事件中心。

下面是將遙測作為用戶端發送到指定事件中心的示例 Python 代碼：

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

將歷史感應器資料格式轉換為 Azure FarmBeats 所理解的標準格式。 規範消息格式如下：

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

添加相應的設備和感應器後，在遙測消息中獲取裝置識別碼 和感應器 ID，如上一節所述。

下面是遙測消息的示例：


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

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>從感應器引入歷史/流資料後無法查看遙測資料

**症狀**：設備或感應器已部署，您已創建 FarmBeats 上的設備/感應器，並將遙測資料引入到 EventHub，但無法獲取或查看 FarmBeats 上的遙測資料。

**糾正措施**：

1. 確保已完成適當的合作夥伴註冊 - 您可以通過訪問資料庫搖曳器、導航到 /合作夥伴 API、執行獲取並檢查合作夥伴是否已註冊來檢查此情況。 如果沒有，請按照[此處的步驟](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)添加合作夥伴。

2. 確保已使用合作夥伴用戶端憑據創建了中繼資料（設備模型、設備、感應器模型、感應器）。

3. 確保使用了正確的遙測消息格式（如下文所述）：

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

有關基於 REST API 的集成詳細資訊的詳細資訊，請參閱[REST API](rest-api-in-azure-farmbeats.md)。
