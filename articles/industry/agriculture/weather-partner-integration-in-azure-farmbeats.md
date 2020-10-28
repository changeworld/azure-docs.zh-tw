---
title: 氣象合作夥伴整合
description: 本文說明氣象資料提供者如何與 FarmBeats 整合
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: dd5d05ff6ed2368308f90f61ea0a6f107e43acd7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740772"
---
# <a name="weather-partner-integration"></a>氣象合作夥伴整合

本文提供 Azure FarmBeats **Connector** docker 元件的相關資訊，這些資訊可供氣象資料提供者開發以利用其 Api 與 FarmBeats 整合，並將天氣資料傳送至 FarmBeats。 資料在 FarmBeats 中可供使用時，可用於資料融合，以及用來建立機器學習/人工智慧模型。

 > [!NOTE]
 > 基於本檔的目的，我們將使用從 Azure 開放資料集使用 NOAA 建立的參考，而且可從取得 [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) 。
 > 對應的 docker 映射可在 [https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

氣象合作夥伴將需要提供 docker 映射/程式 (與下面所述的規格) ，並將 docker 映射裝載在客戶可存取的容器登錄中。 氣象合作夥伴將需要提供下列資訊給其客戶：

- Docker 映射 URL
- Docker 映射標記
- 存取 docker 映射的金鑰/認證
- 客戶專屬的 API 金鑰/認證，可存取來自氣象夥伴系統的資料
- VM SKU 詳細資料 (合作夥伴可提供此資訊，以免其 docker 具有特定的 VM 需求，否則客戶可以選擇 Azure 中支援的 VM Sku) 

客戶會使用上述的 docker 資訊，在其 FarmBeats 實例中註冊氣象夥伴。 若要深入瞭解客戶如何使用 docker 在 FarmBeats 中內嵌氣象資料，請參閱[取得天氣資料](./get-weather-data-from-weather-partner.md)的指南

## <a name="connector-docker-development"></a>連接器 docker 開發

**以 REST API 為基礎的整合**

FarmBeats Api 包含 Swagger 技術檔。 如需所有 Api 及其對應要求或回應的詳細資訊，請參閱 [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)。 

如果您已安裝 FarmBeats，您可以存取 FarmBeats swagger，網址為： `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

請注意，"-api" 會附加至您的 FarmBeats 網站名稱。
API 端點將會是： `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub lib

FarmBeats 會提供可供氣象夥伴使用的 lib。 Lib 目前可作為 [參考執行的](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)一部分。 未來，將會以適用于多種語言的 SDK 形式提供。

### <a name="authentication"></a>驗證

**使用 FarmBeats Api 進行驗證**

FarmBeats 會使用持有人驗證，而且可以藉由在要求的標頭區段中提供存取權杖來存取 Api，如下所示：

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

您可以從客戶的 FarmBeats 實例上執行的 Azure Function 要求存取權杖。 Azure 函式 URL 將會以引數的形式提供給 docker 程式，而您可以在 URL 上提出 GET 要求來取得存取權杖。 來自 URL 的回應將會包含存取權杖。 Datahub lib 提供 helper 函式，可讓合作夥伴取得存取權杖。 更多詳細資料在[這裡](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)。

存取權杖僅適用于幾個小時，而且必須在到期時重新要求。

**使用合作夥伴端 Api 進行驗證**

為了讓客戶能夠在 docker 執行期間使用合作夥伴端 Api 進行驗證，客戶必須在合作夥伴註冊期間提供認證，如下所示：

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API 服務會將此 dict 序列化，並將其儲存在 [KeyVault](../../key-vault/general/basic-concepts.md)中。

[Azure Data Factory](../../data-factory/introduction.md) 是用來協調天氣作業並啟動資源，以執行 docker 程式碼。 它也提供一種機制，可將資料安全地推送至 docker 作業執行所在的 VM。 現在安全地儲存在 KeyVault 中的 API 認證會以安全字串的形式從 KeyVault 讀取，並在 docker 容器的工作目錄中提供作為擴充屬性，作為檔案 (activity.jsworking_dir 路徑上的 activity.js，) docker 程式碼可以在執行時間讀取此檔案中的認證，以代表客戶存取合作夥伴端 Api。 認證將可在檔案中使用，如下所示：

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
請注意，"partnerCredentials" 會以客戶在合作夥伴註冊期間提供的確切方式提供

FarmBeats lib 提供 helper 函式，可讓合作夥伴從活動內容讀取認證。 更多詳細資料在[這裡](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)。

檔案的存留期只會在 docker 程式碼執行期間執行，並將在 docker 執行結束之後刪除。

如需 ADF 管線和活動運作方式的詳細資訊，請參閱 [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](../../data-factory/copy-activity-schema-and-type-mapping.md) 。

**HTTP 要求標頭**

以下是當您對 FarmBeats 進行 API 呼叫時，最常需要指定的要求標頭。

**標頭** | **描述及範例**
--- | ---
Content-Type | 要求格式 (Content-Type: application/<format>)。 FarmBeats Datahub API 的格式為 JSON。 Content-Type: application/json
授權 | 指定進行 API 呼叫所需的存取權杖。 授權：Bearer <Access-Token>
Accept | 回應格式。 FarmBeats Datahub API 的格式為 JSON。 Accept: application/json

## <a name="data-format"></a>資料格式

JSON 是一種與語言無關的通用資料格式，可提供任意資料結構的簡單文字表示。 如需詳細資訊，請參閱 [json.org](http://json.org)。

## <a name="docker-specifications"></a>Docker 規格

Docker 程式必須有兩個元件： **啟動** 程式和 **工作** 。 可以有一個以上的作業。

### <a name="bootstrap"></a>拔靴複製法

當客戶在 FarmBeats 上起始 docker 註冊時，應該會執行此元件。 將傳遞給此程式的引數 (arg1、arg2) ：

- FarmBeats API 端點： API 要求的 FarmBeats API 端點：這是對 FarmBeats 部署進行 API 呼叫的端點。
- Azure 函式 URL：這是您自己的個人端點，可提供您 FarmBeats Api 的存取權杖。 只要呼叫此 url 上的 GET，就會在其回應中提取存取權杖。

啟動程式的責任是建立必要的中繼資料，讓使用者可以執行您的作業來取得天氣資料。 請參閱 [這裡](https://github.com/azurefarmbeats/noaa_docker)的參考實行。 您可以根據自己的需求來更新檔案上的 bootstrap_manifest.js，而參考啟動程式會為您建立必要的中繼資料。

下列中繼資料會在此程式中建立。 

 > [!NOTE]
 > **請注意** ，如果您更新了 [參考執行](https://github.com/azurefarmbeats/noaa_docker)中所述的檔案 bootstrap_manifest.js，則不需要建立下列中繼資料，因為啟動程式會根據您的資訊清單檔建立相同的中繼資料。

- /**WeatherDataModel** ： WeatherDataModel 是代表氣象資料的模型，並且對應至來源所提供的不同資料集。 例如，DailyForecastSimpleModel 可能會一天提供平均溫度、濕度和降雨資訊，而 DailyForecastAdvancedModel 可能會在每小時的資料細微性提供更多資訊。 您可以建立任意數目的 WeatherDataModels。
- /**JobType** ： FarmBeats 具有可擴充的作業管理系統。 作為氣象資料提供者，您將會有不同的資料集/Api (例如 GetDailyForecasts) -您可以在 FarmBeats 中啟用它們作為 JobType。 一旦建立 JobType，客戶就可以觸發該類型的作業，以取得其地點/伺服器陣列的氣象資料 (請參閱 [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)) 中的 JobType 和作業 api。

### <a name="jobs"></a>工作

每次 FarmBeats 使用者執行您在啟動程式過程中建立的/JobType 作業時，就會叫用此元件。 作業的 docker run 命令會定義為您所建立之 **/JobType** 的一部分。
- 作業的責任是從來源提取資料，並將其推送至 FarmBeats。 取得資料所需的參數，應該在啟動程式過程中定義為/JobType 的一部分。
- 在作業過程中，程式將必須根據在啟動程式過程中建立的/WeatherDataModel 建立 **/WeatherDataLocation** 。 **/WeatherDataLocation** 會對應至 (lat/long) 的位置，由使用者提供作為工作的參數。

### <a name="details-of-the-objects"></a>物件的詳細資料

  WeatherDataModel | 描述 |
  --- | ---
  名稱  | 氣象資料模型的名稱 |
  描述  | 提供有意義的型號描述。 |
  屬性  | 由資料提供者所定義的其他屬性。 |
  weatherMeasures > 名稱  | 天氣量值的名稱。 例如 humidity_max |
  weatherMeasures > 資料類型  | Double 或 Enum。 如果列舉，則需要 measureEnumDefinition |
  weatherMeasures > measureEnumDefinition  | 只有當 DataType 是 Enum 時才需要。 例如 {"NoRain"：0，"雪"：1，"Drizzle"：2，"Rain"： 3} |
  weatherMeasures > 類型  | 天氣遙測資料的類型。 例如 "RelativeHumidity"。 以下是系統定義的類型： AmbientTemperature、NoUnit、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、壓力、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、WindDirection、WindRun、WindSpeed、Evapotranspiration、、、、、、 若要新增更多，請參閱/ExtendedType API，或在下方的 [ [新增類型和單位] 區段](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) 中
  weatherMeasures > 單位 | 氣象遙測資料的單位。 下列為系統定義的單位：NoUnit、Celsius、Fahrenheit、Kelvin、Rankine、Pascal、Mercury、PSI、MilliMeter、CentiMeter、Meter、Inch、Feet、Mile、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、Degree、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、Percentage、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、Liter、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond 和 InchesPerHour。 若要新增更多，請參閱/ExtendedType API 或下方的「 [新增類型和單位」一節](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) 。
  weatherMeasures > AggregationType  | 無、平均、最大值、最小值、List.standarddeviation、總和、總計
  weatherMeasures > 深度  | 感應器的深度 (公分)。 例如，地下 10 公分的濕度度量。
  weatherMeasures > 描述  | 提供有意義的度量描述。 |
  **JobType** | **說明** |
  名稱  | 作業的名稱，例如 Get_Daily_Forecast;客戶將執行以取得天氣資料的作業|
  pipelineDetails > 參數 > 名稱  | 參數的名稱 |
  pipelineDetails > > 類型的參數 | 字串、Int、Float、Bool、Array 的任一個 |
  pipelineDetails > > isRequired 的參數 | 布林如果需要參數，則為 true，否則為 false。預設值為 true |
  pipelineDetails > > defaultValue 的參數 | 參數的預設值 |
  pipelineDetails > 參數 > 描述 | 參數說明 |
  屬性  | 製造商的其他屬性。
  屬性 > **programRunCommand** | docker run 命令-當客戶執行氣象工作時，將會執行此命令。 |
  **WeatherDataLocation** | **說明** |
  weatherDataModelId  | 在啟動程式期間建立的對應 WeatherDataModel 識別碼|
  location  | 代表緯度、經度和提高許可權 |
  名稱 | 物件的名稱 |
  描述 | 描述 |
  farmId | **選擇性** 用戶端在作業參數中提供的伺服器陣列識別碼 |
  屬性  | 製造商的其他屬性。

 如需每個物件及其屬性的資訊，請參閱 [Swagger](https://aka.ms/FarmBeatsSwagger)。

 > [!NOTE]
 > 這些 API 會傳回所建立每個執行個體的唯一識別碼。 轉譯程式必須保留此識別碼，以進行裝置管理和中繼資料同步。

**中繼資料同步**

連接器 docker 應該能夠傳送中繼資料的更新。 更新案例的範例包括：在氣象提供者的資料集中新增氣象參數、新增功能 (例如。 新增30天預測) 

> [!NOTE]
> 中繼資料（例如）不支援刪除。 氣象資料模型。
>
> 若要更新中繼資料，您必須在氣象資料模型上呼叫/Get/{ID}、更新已變更的屬性，然後進行/Put/{ID}，讓使用者設定的任何屬性都不會遺失。

## <a name="weather-data-telemetry-specifications"></a>天氣資料 (遙測) 規格

天氣資料會對應到已推送至 Azure 事件中樞進行處理的標準訊息。 Azure 事件中樞是一項服務，可從連線的裝置和應用程式擷取即時資料 (遙測)。 若要將天氣資料傳送至 FarmBeats，您必須建立用戶端，以將訊息傳送至 FarmBeats 中的事件中樞。 若要深入瞭解如何傳送遙測，請參閱將 [遙測傳送至事件中樞](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

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
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

例如，以下是遙測訊息：

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>疑難排解和錯誤管理

**錯誤記錄**

由於夥伴作業將在現有的工作架構中執行，因此這些錯誤的記錄方式與 (FarmBeats 中其他預先存在的作業（例如 GetFarmData、SensorPlacement ) 等）的錯誤相同。 ADF 管線中執行的 ADF 活動會記錄 STDERR 和 STDOUT。 這兩個檔案都可在 FarmBeats 資源群組內的 "datahublogs-xxx" 儲存體帳戶中使用。

Datahub lib 提供 helper 函式，以在整體 Datahub 記錄檔中啟用記錄功能。 更多詳細資料在[這裡](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)。

**疑難排解選項或支援**

如果客戶無法在指定的 FarmBeats 實例中收到氣象資料，氣象夥伴應提供支援和疑難排解的機制。

## <a name="add-extendedtype"></a>新增 ExtendedType

FarmBeats 支援新增感應器量值類型和單位。 請注意，您可以在[此處](https://github.com/azurefarmbeats/noaa_docker)的參考實行中更新檔案 bootstrap_manifest.js，藉以新增單位/類型

若要加入新的 WeatherMeasure 類型（例如 "PrecipitationDepth"），請遵循下列步驟。

1. 使用查詢篩選-key = WeatherMeasureType 在/ExtendedType 上提出 GET 要求
2. 請記下傳回之物件的識別碼。
3. 在傳回的物件中，將新的類型加入至清單，並在/ExtendedType{ID} 上提出 PUT 要求，並提供下列新清單。 輸入承載應與上面收到的回應相同，並將新的單位附加至值清單的結尾。

如需 /ExtendedType API 的詳細資訊，請參閱 [Swagger](https://aka.ms/FarmBeatsSwagger)。

## <a name="next-steps"></a>後續步驟

現在您已有與 FarmBeats 整合的連接器 docker。 接下來，您可以瞭解如何使用 docker 將天氣資料帶入 FarmBeats。 請參閱 [取得天氣資料](get-weather-data-from-weather-partner.md)。
