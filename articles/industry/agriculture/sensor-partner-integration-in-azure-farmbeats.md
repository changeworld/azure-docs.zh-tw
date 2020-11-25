---
title: 感應器合作夥伴整合
description: 本文描述感應器合作夥伴整合。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ef74c4b799c3a24636f88a8e704bf726104b034f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001587"
---
# <a name="sensor-partner-integration"></a>感應器合作夥伴整合

本文提供用以啟用感應器合作夥伴整合的 Azure FarmBeats **轉譯程式** 元件相關資訊。

透過此元件，合作夥伴可使用 FarmBeats Datahub API 來與 FarmBeats 整合，並將客戶裝置資料和遙測傳送至 FarmBeats Datahub。 一旦 FarmBeats 中有資料可用，該資料即會使用 FarmBeats Accelerator 來視覺化，並可用於資料融合及建立機器學習/人工智慧模型。

## <a name="before-you-start"></a>開始之前

若要開發轉譯程式元件，則需要下列可供存取 FarmBeats API 的認證。

- API 端點
- 租用戶識別碼
- 用戶端識別碼
- 用戶端密碼
- EventHub 連接字串

請參閱此章節以取得上述認證：[啟用裝置整合](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>轉譯程式開發

**以 REST API 為基礎的整合**

FarmBeats 的感應器資料整合功能會透過 REST API 公開。 這些功能包括中繼資料定義、裝置和感應器佈建，以及裝置和感應器管理。

**遙測擷取**

遙測資料會對應至 Azure 事件中樞上發佈的標準訊息以進行處理。 Azure 事件中樞是一項服務，可從連線的裝置和應用程式擷取即時資料 (遙測)。

**API 開發**

這些 API 包含 Swagger 技術文件。 如需 API 及其對應要求或回應的詳細資訊，請參閱 [Swagger](https://aka.ms/FarmBeatsSwagger)。

**驗證**

FarmBeats 使用 Microsoft Azure Active Directory 驗證。 Azure App Service 提供內建驗證和授權支援。

如需詳細資訊，請參閱 [Azure Active Directory](../../app-service/overview-authentication-authorization.md)。

FarmBeats Datahub 使用持有人驗證，其需要下列認證：
   - 用戶端識別碼
   - 用戶端密碼
   - 租用戶識別碼

呼叫者可使用這些認證來要求存取權杖。 此權杖必須在後續 API 要求的標頭區段中傳送，如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

下列範例  Python 程式碼提供存取權杖，其可用於對 FarmBeats 的後續 API 呼叫。

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```


**HTTP 要求標頭**

以下是對 FarmBeats Datahub 進行 API 呼叫時所需指定的最常見要求標頭。


**標頭** | **描述及範例**
--- | ---
Content-Type | 要求格式 (Content-Type: application/<format>)。 FarmBeats Datahub API 的格式為 JSON。 Content-Type: application/json
授權 | 指定進行 API 呼叫所需的存取權杖。 授權：Bearer <Access-Token>
Accept | 回應格式。 FarmBeats Datahub API 的格式為 JSON。 Accept: application/json

**API 要求**

若要提出 REST API 要求，您可結合 HTTP (GET、POST 或 PUT)方法；API 服務的 URL；用以查詢、提交資料、更新或刪除的資源其統一資源識別項 (URI)；以及一或多個 HTTP 要求標頭。 API 服務的 URL 是所提供的 API 端點。 以下是範例： HTTPs:// \<yourdatahub-website-name> . azurewebsites.net

您可選擇性地在 GET 呼叫上包含查詢參數來篩選回應中的資料、限制其大小及進行排序。

下列範例要求是為了取得裝置清單。

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
大多數 GET、POST 和 PUT 呼叫都需要 JSON 要求本文。

下列範例要求是為了建立裝置 (此範例包含具有要求本文的輸入 JSON)。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>資料格式

JSON 是一種與語言無關的通用資料格式，可提供任意資料結構的簡單文字表示。 如需詳細資訊，請參閱 [json.org](http://json.org)。

## <a name="metadata-specifications"></a>中繼資料規格

FarmBeats Datahub 具有下列 API，其可讓裝置合作夥伴建立和管理裝置或感應器中繼資料。

- /**DeviceModel**：DeviceModel 會對應至裝置的中繼資料，例如製造商和裝置類型 (閘道或節點)。
- /**Device**：Device 會對應至存在於伺服器陣列上的實體裝置。
- /**SensorModel**：SensorModel 會對應至感應器的中繼資料，例如製造商、感應器類型 (類比或數位)，以及感應器量值 (例如環境溫度和壓力)。
- /**Sensor**：Sensor 會對應至記錄值的實體感應器。 感應器通常會使用裝置識別碼連線到裝置。

  DeviceModel | 描述 |
  --- | ---
  Type (Node、Gateway)  | 裝置的類型 - 節點或閘道 |
  製造商  | 製造商的名稱 |
  ProductCode  | 裝置產品代碼，或是型號名稱或號碼。 例如，EnviroMonitor#6800。 |
  連接埠  | 連接埠名稱和類型 (數位或類比)。  |
  名稱  | 用於識別資源的名稱。 例如，型號名稱或產品名稱。 |
  描述  | 提供有意義的型號描述。 |
  屬性  | 製造商的其他屬性。 |
  **裝置** | **描述** |
  DeviceModelId  |相關裝置型號的識別碼。 |
  HardwareId   |裝置的唯一識別碼，例如 MAC 位址。  |
  ReportingInterval |報告間隔 (秒)。 |
  Location    |裝置緯度 (-90 到 +90)、經度 (-180 到 180) 和高度 (公尺)。 |
  ParentDeviceId | 此裝置所連線父裝置的識別碼。 例如，如果節點連線到閘道，則節點的 parentDeviceID 為閘道。 |
  名稱  | 用於識別資源的名稱。 裝置合作夥伴必須傳送與裝置合作夥伴端其裝置名稱一致的名稱。 如果裝置名稱是裝置合作夥伴端的使用者定義名稱，則應該將相同的使用者定義名稱傳播至 FarmBeats。  |
  描述  | 提供有意義的描述。  |
  屬性  |製造商的其他屬性。  |
  **SensorModel** | **描述** |
  Type (Analog、Digital)  |提及類比或數位感應器。|
  製造商  | 製造商的名稱。 |
  ProductCode  | 產品代碼，或型號名稱或號碼。 例如，RS-CO2-N01。  |
  SensorMeasures > Name  | 感應器量值的名稱。 僅支援小寫。 如需不同深度的度量，請指定深度。 例如，soil_moisture_15cm。 此名稱必須與遙測資料一致。 |
  SensorMeasures > DataType  | 遙測資料類型。 目前支援 Double。 |
  SensorMeasures > Type  | 感應器遙測資料的度量類型。 下列為系統定義的類型：AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、Pressure、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 若要新增更多，請參閱 /ExtendedType API。
  SensorMeasures > Unit | 感應器遙測資料的單位。 下列為系統定義的單位：NoUnit、Celsius、Fahrenheit、Kelvin、Rankine、Pascal、Mercury、PSI、MilliMeter、CentiMeter、Meter、Inch、Feet、Mile、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、Degree、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、Percentage、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、Liter、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond 和 InchesPerHour。 若要新增更多，請參閱 /ExtendedType API。
  SensorMeasures > AggregationType  | None、Average、Maximum、Minimum 或 StandardDeviation。
  SensorMeasures > Depth  | 感應器的深度 (公分)。 例如，地下 10 公分的濕度度量。
  SensorMeasures > Description  | 提供有意義的度量描述。
  名稱  | 用於識別資源的名稱。 例如，型號名稱或產品名稱。
  描述  | 提供有意義的型號描述。
  屬性  | 製造商的其他屬性。
  **Sensor**  | **描述** |
  HardwareId  | 製造商所設定感應器的唯一識別碼。
  SensorModelId  | 相關感應器型號的識別碼。
  Location  | 感應器緯度 (-90 到 +90)、經度 (-180 到 180) 和高度 (公尺)。
  Port > Name  |感應器所連線裝置上的連接埠名稱和類型。 這必須與裝置型號中所定義的名稱相同。
  deviceId  | 感應器所連線裝置的識別碼。
  名稱  | 用於識別資源的名稱。 例如，感應器名稱或產品名稱，以及型號號碼或產品代碼。
  描述  | 提供有意義的描述。
  屬性  | 製造商的其他屬性。

 如需每個物件及其屬性的資訊，請參閱 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

 > [!NOTE]
 > 這些 API 會傳回所建立每個執行個體的唯一識別碼。 轉譯程式必須保留此識別碼，以進行裝置管理和中繼資料同步。


**中繼資料同步**

轉譯程式應該傳送中繼資料的相關更新。 例如，更新案例包括裝置或感應器名稱的變更，以及裝置或感應器位置的變更。

轉譯程式應該能夠新增使用者在連結 FarmBeats 之後所安裝的新裝置或感應器。 同樣地，如果使用者已更新裝置或感應器，則也應該更新 FarmBeats 中對應的裝置或感應器。 需要更新裝置或感應器的一般案例包括裝置位置中的變更，或是在節點中新增感應器。


> [!NOTE]
> 裝置或感應器中繼資料不支援刪除。
>
> 若要更新中繼資料，則必須在裝置或感應器上呼叫 /Get/{id}、更新已變更的屬性，然後執行 /Put/{id}，讓使用者所設定的任何屬性都不會遺失。

### <a name="add-new-types-and-units"></a>新增類型和單位

FarmBeats 支援新增感應器量值類型和單位。 如需 /ExtendedType API 的詳細資訊，請參閱 [Swagger](https://aka.ms/FarmBeatsSwagger)。

## <a name="telemetry-specifications"></a>遙測規格

遙測資料會對應至 Azure 事件中樞上發佈的標準訊息以進行處理。 Azure 事件中樞是一項服務，可從連線的裝置和應用程式擷取即時資料 (遙測)。

## <a name="send-telemetry-data-to-farmbeats"></a>將遙測資料傳送至 FarmBeats

若要將遙測資料傳送至 FarmBeats，請建立用戶端，以將訊息傳送至 FarmBeats 中的事件中樞。 如需遙測資料的詳細資訊，請參閱[將遙測傳送至事件中樞](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) (機器翻譯)。

以下是範例 Python 程式碼，可將遙測當作用戶端傳送至指定的事件中樞。

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

標準訊息格式如下所示：

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
遙測 JSON 中的所有索引鍵名稱都應該是小寫。 範例包括 deviceid 和 sensordata。

例如，以下是遙測訊息：


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
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

> [!NOTE]
> 下列各節與其他變更相關 (例如 UI、錯誤管理等)，感應器可能會在開發轉譯程式元件時參考這些變更。


## <a name="link-a-farmbeats-account"></a>連結 FarmBeats 帳戶

客戶購買並部署裝置或感應器之後，即可在裝置合作夥伴的軟體即服務 (SaaS) 入口網站上存取裝置資料和遙測。 裝置合作夥伴可藉由提供方式來輸入下列認證，讓客戶將其帳戶連結至 Azure 上的 FarmBeats 執行個體：

   - 顯示名稱 (可供使用者定義此整合名稱的選擇性欄位)
   - API 端點
   - 租用戶識別碼
   - 用戶端識別碼
   - 用戶端密碼
   - EventHub 連接字串
   - 開始日期

   > [!NOTE]
   > 開始日期會啟用歷程記錄資料摘要，也就是從使用者所指定日期開始的資料。

## <a name="unlink-farmbeats"></a>取消 FarmBeats 的連結

裝置合作夥伴可讓客戶取消現有 FarmBeats 整合的連結。 取消 FarmBeats 的連結不應該會刪除在 FarmBeats Datahub 中所建立任何裝置或感應器中繼資料。 取消連結會執行下列動作：

   - 停止遙測流程。
   - 刪除並清除裝置合作夥伴上的整合認證。

## <a name="edit-farmbeats-integration"></a>編輯 FarmBeats 整合

裝置合作夥伴可讓客戶在用戶端密碼或連接字串變更時，編輯 FarmBeats 整合設定。 在此情況下，只能編輯下列欄位：

   - 顯示名稱 (如果適用)
   - 用戶端密碼 (應該以 "2x8***********" 格式或透過 [顯示]/[隱藏] 功能顯示，而不是純文字)
   - 連接字串 (應該以 "2x8***********" 格式或透過 [顯示]/[隱藏] 功能顯示，而不是純文字)

## <a name="view-the-last-telemetry-sent"></a>檢視上次傳送的遙測

裝置合作夥伴可讓客戶檢視上次傳送的遙測時間戳記 (位於 [Telemetry Sent] \(已傳送的遙測\) 下)。 這是已成功傳送至 FarmBeats 的最新遙測時間。

## <a name="troubleshooting-and-error-management"></a>疑難排解和錯誤管理

**疑難排解選項或支援**

如果客戶無法在指定的 FarmBeats 執行個體中接收裝置資料或遙測，則裝置合作夥伴應該提供支援和疑難排解機制。

**遙測資料保留**

遙測資料也應該保留一段預先定義的期間，以便在發生錯誤或資料遺失時，可用於偵錯或重新傳送遙測。

**錯誤管理或錯誤通知**

如果錯誤會影響裝置或感應器中繼資料，或是裝置合作夥伴系統中的資料整合或遙測資料流程，客戶應該會收到通知。 也應該設計和實行解決任何錯誤的機制。

**連線檢查清單**

裝置製造商或合作夥伴可使用下列檢查清單來確保客戶所提供的認證正確無誤：

   - 檢查是否可使用所提供的認證來接收存取權杖。
   - 檢查是否可使用所收到的存取權杖成功進行 API 呼叫。
   - 檢查是否建立 EventHub 用戶端連線。

## <a name="next-steps"></a>後續步驟

如需 REST API 的詳細資訊，請參閱 [REST API](rest-api-in-azure-farmbeats.md)。