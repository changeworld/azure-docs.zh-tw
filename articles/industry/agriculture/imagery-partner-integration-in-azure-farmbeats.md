---
title: 影像合作夥伴整合
description: 本文說明影像合作夥伴整合。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.custom: has-adal-ref
ms.openlocfilehash: 430907f43fb40f0ee24505bdc366a98a49f23b47
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613280"
---
# <a name="imagery-partner-integration"></a>影像合作夥伴整合

本文說明如何使用 Azure FarmBeats Translator 元件，將影像資料傳送至 FarmBeats。 農業影像資料可以從各種來源產生，例如 multispectral 攝影機、衛星和無人機。 農業影像合作夥伴可以與 FarmBeats 整合，為客戶提供其伺服器陣列的自訂產生對應。

資料一經提供，就可以透過 FarmBeats 加速器來視覺化，而且可能會用於農業企業或客戶系統整合師所建立的資料融合和機器學習/人工智慧（ML/AI）模型。

FarmBeats 提供下列功能：

- 使用/ExtendedType Api 定義自訂映射類型、來源和檔案格式。
- 透過/Scene 和/SceneFile Api，內嵌各種來源的影像資料。

下列資訊著重于在 FarmBeats 系統中取得任何形式的影像。

當您選取 [**無人機影像**] 區段時，快顯視窗會隨即開啟，以顯示無人機 orthomosaic 的高解析度影像。 您可以存取合作夥伴軟體，這有助於規劃無人機航班並取得原始資料。 您將繼續使用合作夥伴的軟體進行路徑規劃和 orthomosaic 影像裝訂。

無人機合作夥伴必須讓客戶將其客戶帳戶與其在 Azure 上的 FarmBeats 實例連結在一起。

您必須使用無人機合作夥伴軟體中的下列認證來連結 FarmBeats：

- API 端點
- 租用戶識別碼
- 用戶端識別碼
- 用戶端密碼

## <a name="api-development"></a>API 開發

這些 Api 包含 Swagger 技術檔。 如需 Api 和對應要求或回應的相關資訊，請參閱[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

## <a name="authentication"></a>驗證

FarmBeats 會使用 Microsoft Azure [Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) （Azure AD）。 Azure App Service 提供內建的驗證和授權支援。 

如需 Azure AD 的詳細資訊，請參閱 [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。   

FarmBeats Datahub 會使用持有人驗證，其需要下列認證：

- 用戶端識別碼
- 用戶端密碼
- 租用戶識別碼

呼叫者可以使用先前的認證，要求在標頭區段中，于後續的 API 要求中傳送存取權杖，如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

下列 Python 程式碼範例會抓取存取權杖。 接著，您可以使用權杖來進行後續的 API 呼叫以 FarmBeats。

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

## <a name="http-request-headers"></a>HTTP 要求標頭

以下是您對 FarmBeats Datahub 進行 API 呼叫時，需要指定的最常見要求標頭。

**頁首** | **描述及範例**
--- | ---
Content-Type  | 要求格式（Content-type： application/<format>）。 針對 FarmBeats Datahub Api，格式為 JSON。 Content-Type: application/json
授權 | 指定進行 API 呼叫所需的存取權杖。 授權：持有人 <存取權杖>
Accept  | 回應格式。 針對 FarmBeats Datahub Api，格式為 JSON。 Accept： application/json


## <a name="api-requests"></a>API 要求

若要提出 REST API 要求，您可以結合：

- HTTP 方法（GET、POST 和 PUT）。
- API 服務的 URL。
- 資源 URI （用來查詢、提交資料、更新或刪除）。
- 一或多個 HTTP 要求標頭。

（選擇性）您可以在 GET 呼叫上包含查詢參數來篩選、限制的大小，以及排序回應中的資料。

下列範例要求是取得裝置的清單：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

大部分的 GET、POST 和 PUT 呼叫都需要 JSON 要求主體。

下列範例要求是建立裝置。 此範例的輸入 JSON 具有要求主體。


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"accept: application/json" -H
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>資料格式

JSON 是一種與語言無關的通用資料格式，可提供任意資料結構的簡單文字標記法。 如需詳細資訊，請參閱[JSON 組織](https://JSON.org)。

## <a name="ingest-imagery-into-farmbeats"></a>將影像內嵌至 FarmBeats

當合作夥伴具有認證可連線至 FarmBeats Datahub 之後，合作夥伴會在 Translator 元件中採取下列步驟。

1.  根據要上傳的影像類型，為下欄欄位建立新的擴充類型：

    - **場景來源**：例如，drone_partner_name
    - **場景類型**：例如，無人機
    - **場景檔案類型**：例如，chlorophyll 索引
    - **場景檔案內容類型**：例如，影像/tiff

2.  呼叫/Farms API，以從 Azure FarmBeats 系統內取得伺服器陣列清單。
3.  提供客戶從伺服器陣列清單中選擇單一伺服器陣列的能力。

    夥伴系統必須顯示合作夥伴軟體內的伺服器陣列，才能執行路徑規劃和無人機航班和影像收集。

4.  呼叫/Scene API 並提供必要的詳細資料，以建立具有唯一場景識別碼的新場景。
5.  接收 blob SAS URL，以在 FarmBeats 系統中所選伺服器陣列的內容中，將所需的影像上傳至 FarmBeats Datahub。

以下是 API 呼叫的詳細流程。

### <a name="step-1-extendedtype"></a>步驟1： ExtendedType

簽入/ExtendedType API，以查看 FarmBeats 上是否有可用的類型和檔案來源。 若要這麼做，請在/ExtendedType API 上呼叫 GET。

以下是系統定義的值：

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

此步驟只需執行一次。 此新場景類型的範圍僅限於已安裝 Azure FarmBeats 的訂用帳戶。

例如，若要新增 SceneSource： "SlantRange"，您可以在/ExtendedType API 的識別碼上加上索引鍵 "SceneSource" 輸入裝載。

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

綠色的欄位是系統定義之場景來源值的新新增。

### <a name="step-2-get-farm-details"></a>步驟2：取得伺服器陣列詳細資料

幕後（tiff 或 .csv 檔案）位於伺服器陣列的內容中。 您需要在/Farm API 上執行 GET，以取得伺服器陣列的詳細資料。 此 API 會傳回 FarmBeats 中可用的伺服器陣列清單。 您可以選取要內嵌資料的伺服器陣列。

取得/Farm 回應：

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>步驟3：建立場景識別碼（POST 呼叫）

使用指定的資訊建立新的場景（tiff 或 .csv 檔案），以提供與場景相關聯的日期、序列和伺服器陣列識別碼。 與場景相關聯的中繼資料可以在 [屬性] 下定義，其中包括量值的持續時間和類型。

建立新場景時，會建立與伺服器陣列相關聯的新場景識別碼。 建立場景識別碼之後，使用者可以使用相同的來建立新的檔案（tiff 或 .csv），並儲存檔案的內容。

/Scene API 上 POST 呼叫的範例輸入承載：

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API 回應：

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**建立場景檔案**

在步驟3中傳回的場景識別碼是場景檔案的輸入。 場景檔案會傳回 SAS URL 權杖，其有效期為24小時。

如果使用者需要以程式設計方式上傳影像串流，則可以使用場景檔案識別碼、位置和 URL，將 blob 儲存體 SDK 用於定義方法。

/SceneFile API 上 POST 呼叫的範例輸入承載：

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API 回應：

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

/SceneFile API 的 POST 呼叫會傳回 SAS 上傳 URL，其可用來上傳 .csv 或 tiff 檔案，方法是使用 Azure Blob 儲存體用戶端或程式庫。


## <a name="next-steps"></a>後續步驟

如需以 REST API 為基礎的整合詳細資訊，請參閱[REST API](rest-api-in-azure-farmbeats.md)。
