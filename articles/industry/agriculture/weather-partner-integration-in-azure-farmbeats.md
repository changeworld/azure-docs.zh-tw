---
title: 氣象合作夥伴整合
description: 瞭解氣象資料提供者如何與 FarmBeats 整合。
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147074"
---
# <a name="weather-partner-integration-with-farmbeats"></a>氣象合作夥伴與 FarmBeats 整合

本文提供 Azure FarmBeats Connector Docker 元件的相關資訊。 作為氣象資料提供者，您可以使用連接器 Docker 與 FarmBeats 整合。 使用其 Api 將天氣資料傳送至 FarmBeats。 在 FarmBeats 中，資料可用於資料融合，以及用來建立機器學習模型或人工智慧模型。

 > [!NOTE]
 > 在本文中，我們會使用從國家海洋暨和大氣系統管理的 Azure 開放資料集和氣象資料所建立的 [參考實行](https://github.com/azurefarmbeats/noaa_docker) (NOAA) 。 我們也會使用對應的 [Docker 映射](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)。

您必須提供 [適合的 docker 映射或程式](#docker-specifications) ，並將 docker 映射裝載在客戶可以存取的容器登錄中。 為您的客戶提供下列資訊：

- Docker 映射 URL
- Docker 映射標記
- 用來存取 Docker 映射的金鑰或認證
- 客戶專屬的 API 金鑰或認證，以存取您系統中的資料
- 如果您的 Docker 映射有特定的 VM 需求，VM SKU 詳細資料 (提供這些詳細資料。 否則，客戶可以選擇 Azure 中支援的 VM Sku。 ) 

客戶會使用此 Docker 資訊，在其 FarmBeats 實例中註冊氣象夥伴。 如需客戶如何使用 Docker 在 FarmBeats 中內嵌氣象資料的詳細資訊，請參閱 [從天氣合作夥伴取得資料](./get-weather-data-from-weather-partner.md)。

## <a name="connector-docker-development"></a>連接器 Docker 開發

**以 REST API 為基礎的整合**

FarmBeats Api 包含 Swagger 技術檔。 如需 Api 及其對應要求或回應的詳細資訊，請參閱 [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)。 

如果您已經安裝 FarmBeats，請存取您的 FarmBeats Swagger，網址為 `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

請注意， *api* 會附加至您的 FarmBeats 網站名稱。 API 端點為 `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub lib

FarmBeats 會提供可供您使用的 lib。 Lib 目前可作為 [參考執行的一部分](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)。 稍後，將會以適用于多種語言的 SDK 形式提供。

### <a name="authentication"></a>驗證

**使用 FarmBeats Api 進行驗證**

FarmBeats 會使用持有人驗證。 您可以藉由在要求的標頭區段中提供存取權杖來存取 Api。 以下為範例：

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

您可以從客戶 FarmBeats 實例上執行的 Azure Functions 應用程式要求存取權杖。 Azure Functions URL 會以引數的形式提供給 Docker 程式。 您可以在 URL 上提出要求，以取得存取權杖 `GET` 。 來自 URL 的回應包含存取權杖。 

使用 Datahub lib 中的 helper 函式來取得存取權杖。 如需詳細資訊，請參閱 [NOAA Docker 映射的 GitHub 頁面](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)。

存取權杖的有效時間只有幾個小時。 當它過期時，您必須再次要求它。

**使用合作夥伴端 Api 進行驗證**

若要在 Docker 作業執行時使用合作夥伴端 Api 進行驗證，客戶必須在合作夥伴註冊期間提供認證。 以下為範例：

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API 服務會將此 dict 序列化，並將其儲存在 [金鑰保存庫](../../key-vault/general/basic-concepts.md)中。

[Azure Data Factory](../../data-factory/introduction.md) 用來協調天氣工作。 它會加速資源以執行 Docker 程式碼。 Data Factory 也提供一種機制，可將資料安全地推送至 Docker 作業執行所在的 VM。 然後，會將 API 認證安全地儲存在金鑰保存庫中。 

認證會從金鑰保存庫讀取為安全字串。 它們在 Docker 容器的工作目錄中是以擴充屬性的形式提供。 它們的檔案路徑 */mnt/working_dir/activity.js* 。 

Docker 程式碼可以在執行時間讀取 *activity.js* 的認證，以存取客戶的合作夥伴端 api。 在 JSON 檔案中，認證看起來像此程式碼範例：

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
`partnerCredentials`認證的提供方式與客戶在合作夥伴註冊期間提供的方式相同。

FarmBeats lib 提供 helper 函數。 使用這些函式可從活動屬性讀取認證。 如需詳細資訊，請參閱 [NOAA Docker 映射的 GitHub 頁面](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)。

只有當 Docker 程式碼正在執行時，才會使用此檔案。 程式碼完成後，就會刪除該檔案。

如需 Data Factory 管線和活動運作方式的詳細資訊，請參閱 [架構和資料類型對應](../../data-factory/copy-activity-schema-and-type-mapping.md)。

**HTTP 要求標頭**

下表顯示當您對 FarmBeats 進行 API 呼叫時，您需要指定的最常見要求標頭。

標頭 | 描述及範例
--- | ---
Content-Type | 要求格式。 範例： `Content-Type: application/<format>` <br/>FarmBeats Datahub API 的格式為 JSON。 範例： ` Content-Type: application/json`
授權 | 進行 API 呼叫所需的存取權杖。 範例： `Authorization: Bearer <Access-Token>`
Accept | 回應格式。 FarmBeats Datahub API 的格式為 JSON。 範例： `Accept: application/json`

## <a name="data-format"></a>資料格式

JSON 是一種與語言無關的通用資料格式，可提供任意資料結構的簡單文字表示。 如需詳細資訊，請參閱 [JSON.org](https://json.org)。

## <a name="docker-specifications"></a>Docker 規格

Docker 程式需要兩個元件：啟動程式和工作。 此程式可以有一個以上的作業。

### <a name="bootstrap"></a>拔靴複製法

當客戶在 FarmBeats 上啟動 Docker 註冊時，應執行啟動載入器元件。 下列引數 (`arg1` 和 `arg2`) 會傳遞給程式：

- **FARMBEATS api 端點** ： api 要求的 FarmBeats api 端點。 此端點會對 FarmBeats 部署進行 API 呼叫。
- **AZURE FUNCTIONS URL** ：您自己的端點。 此 URL 提供 FarmBeats Api 的存取權杖。 您可以呼叫 `GET` 此 URL 來提取存取權杖。

啟動程式會建立使用者執行工作所需的中繼資料，以取得氣象資料。 如需詳細資訊，請參閱 [參考實行](https://github.com/azurefarmbeats/noaa_docker)。 

如果您自訂檔案 *上的bootstrap_manifest.js* ，則參考啟動程式會為您建立必要的中繼資料。 啟動程式程式會建立下列中繼資料： 

 > [!NOTE]
 > 如果您在 [參考實行](https://github.com/azurefarmbeats/noaa_docker)所描述的檔案 *上更新bootstrap_manifest.js* ，則不需要建立下列中繼資料。 啟動程式會使用您的資訊清單檔來建立必要的中繼資料。

- /**WeatherDataModel** ： WeatherDataModel 中繼資料代表氣象資料。 它會對應至來源所提供的資料集。 例如，DailyForecastSimpleModel 可能會提供每日一次的平均溫度、濕度和降雨資訊。 相反地，DailyForecastAdvancedModel 可能會在每小時的資料細微性提供更多詳細資訊。 您可以建立任意數目的氣象資料模型。
- /**JobType** ： FarmBeats 具有可擴充的作業管理系統。 作為氣象資料提供者，您將會有不同的資料集和 Api (例如 GetDailyForecasts) 。 您可以使用 JobType 在 FarmBeats 中啟用這些資料集和 Api。 建立工作類型之後，客戶可以觸發該類型的作業，以取得其位置或其相關伺服器陣列的天氣資料。 如需詳細資訊，請參閱 [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)中的 JobType 和作業 api。

### <a name="jobs"></a>工作

每次 FarmBeats 使用者執行您在啟動程式過程中建立的/JobType 作業時，就會叫用工作元件。 作業的 Docker run 命令會定義為您所建立之/JobType 的一部分。

作業會從來源中提取資料，並將其推送至 FarmBeats。 在啟動程式期間，取得資料所需的參數應定義為/JobType. 的一部分。

在作業過程中，程式必須根據在啟動程式期間建立的/WeatherDataModel 建立/WeatherDataLocation。 /WeatherDataLocation 會對應至 (緯度和經度座標的位置，) 使用者提供做為工作的參數。

### <a name="object-details"></a>物件詳細資料

WeatherDataModel | 描述 |
--- | ---
名稱  | 氣象資料模型的名稱。 |
描述  | 模型的有意義描述。 |
屬性  | 由資料提供者所定義的其他屬性。 |
weatherMeasures > 名稱  | 天氣量值的名稱。 例如，humidity_max。 |
weatherMeasures > 資料類型  | Double 或 Enum。 如果列舉，則需要 measureEnumDefinition。 |
weatherMeasures > measureEnumDefinition  | 只有當 DataType 是 Enum 時才需要。 例如， `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
weatherMeasures > 類型  | 天氣遙測資料的類型。 例如，RelativeHumidity。 系統定義的類型為 AmbientTemperature、NoUnit、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、壓力、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、WindDirection、WindRun、WindSpeed、Evapotranspiration、和 PAR。 若要加入其他類型，請參閱本文中的 [新增 ExtendedType](#add-extendedtype) 一節。
weatherMeasures > 單位 | 氣象遙測資料的單位。 系統定義的單位為 NoUnit、攝氏、華氏、開氏、Rankine、Pascal、水銀、PSI、毫米、釐米、計量、英寸、英尺、英里、平方公里、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、度數、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、PartsPerMillion、MicroMole、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、MilliLiter、UnixTimestamp、MicroMolePerMeterSquaredPerSecond、InchesPerHour、和。 若要新增更多單位，請參閱本文中的 [新增 ExtendedType](#add-extendedtype) 一節。
weatherMeasures > AggregationType  | 匯總的型別。 可能的值為無、平均、最大值、最小值、List.standarddeviation、總和和總計。
weatherMeasures > 深度  | 感應器的深度 (公分)。 例如，地下 10 公分的濕度度量。
weatherMeasures > 描述  | 測量的有意義描述。 

JobType | 描述 |
--- | ---
名稱  | 作業的名稱。 例如，Get_Daily_Forecast。 客戶將執行此作業來取得天氣資料。|
pipelineDetails > 參數 > 名稱  | 參數的名稱。 |
pipelineDetails > > 類型的參數 | 參數類型。 可能的值包括 String、Int、Float、Bool 和 Array。 |
pipelineDetails > > isRequired 的參數 | 參數的布林值。 如果需要參數，則此值為 true。 否則，此值為 false。 預設值是 true。 |
pipelineDetails > > defaultValue 的參數 | 參數的預設值。 |
pipelineDetails > 參數 > 描述 | 參數的描述。 |
屬性  | 製造商的其他屬性。
屬性 > programRunCommand | Docker run 命令。 當客戶執行氣象工作時，會執行此命令。 |

WeatherDataLocation | 描述 |
--- | ---
weatherDataModelId  | 在啟動程式期間建立的對應 WeatherDataModel 識別碼。|
location  | 緯度、經度和提高許可權。 |
名稱 | 物件的名稱。 |
描述 | 氣象資料位置的描述。 |
farmId |  (伺服器陣列的選擇性) 識別碼。 客戶可提供此識別碼作為作業參數的一部分。 |
屬性  | 製造商的其他屬性。

如需物件和其屬性的詳細資訊，請參閱 [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger)。

> [!NOTE]
> Api 會為每個建立的實例傳回唯一的識別碼。 裝置管理和元資料同步的翻譯工具必須保留此識別碼。

**中繼資料同步**

連接器 Docker 元件應該能夠傳送中繼資料的更新。 例如，它應該會在氣象提供者將新的參數加入至資料集，或新增新功能（例如新的30天預測）時傳送更新。

> [!NOTE]
> 氣象資料模型中的中繼資料不支援刪除。
>
> 若要更新中繼資料，您必須 `/Get/{ID}` 在氣象資料模型上呼叫。 更新已變更的屬性，然後進行 `/Put/{ID}` 以保留使用者所設定的任何屬性。

## <a name="weather-data-telemetry-specifications"></a>天氣資料 (遙測) 規格

天氣資料會對應到已推送至 Azure 事件中樞進行處理的標準訊息。 Azure 事件中樞是一項服務，可從連線的裝置和應用程式擷取即時資料 (遙測)。 

若要將天氣資料傳送至 FarmBeats，請建立用戶端，以將訊息傳送至 FarmBeats 中的事件中樞。 如需詳細資訊，請參閱 [將遙測傳送至事件中樞](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。

下列 Python 程式碼範例會以用戶端的形式將遙測傳送至指定的事件中樞。

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

以下是標準訊息格式：

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

以下是遙測訊息的範例：

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

### <a name="error-logging"></a>錯誤記錄

夥伴作業會在現有的工作架構中執行。 因此，錯誤的記錄方式與其他預先存在之 FarmBeats 作業的錯誤相同 (例如 GetFarmData 和 SensorPlacement) 。 在 Data Factory 管線內執行的 Data Factory 活動都會記錄 `STDERR` 和 `STDOUT` 。 這兩個檔案都可在 `datahublogs-xxx` FarmBeats 資源群組內的儲存體帳戶中使用。

Datahub lib 提供 helper 函式，以在整體 Datahub 記錄檔中啟用記錄功能。 如需詳細資訊，請參閱 [NOAA Docker 映射的 GitHub 頁面](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)。

### <a name="troubleshooting-and-support"></a>疑難排解和支援

如果客戶無法在 FarmBeats 實例中接收氣象資料，請提供支援和針對問題進行疑難排解的機制。

## <a name="add-extendedtype"></a>新增 ExtendedType

FarmBeats 支援新增感應器量值類型和單位。 您可以藉由更新 [參考執行](https://github.com/azurefarmbeats/noaa_docker)中的檔案 *bootstrap_manifest.js* ，來新增單位或類型。

請遵循下列步驟來新增 WeatherMeasure 類型，例如 PrecipitationDepth。

1. `GET`使用查詢對/ExtendedType 提出要求 `filter - key = WeatherMeasureType` 。
2. 請記下傳回之物件的識別碼。
3. 在傳回的物件中，將新的類型加入至清單。 `PUT`在/ExtendedType{ID} 上使用下列新清單提出要求。 輸入承載應與您稍早收到的回應相同。 新的單位應該附加至值清單的結尾。

如需有關/ExtendedType API 的詳細資訊，請參閱 [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger)。

## <a name="next-steps"></a>下一步

現在您已有與 FarmBeats 整合的連接器 Docker 元件。 接下來，瞭解如何在 FarmBeats 中使用您的 Docker 映射來 [取得氣象資料](get-weather-data-from-weather-partner.md) 。 
