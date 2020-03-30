---
title: 影像合作夥伴整合
description: 本文介紹影像合作夥伴集成。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131859"
---
# <a name="imagery-partner-integration"></a>影像合作夥伴整合

本文介紹如何使用 Azure FarmBeats 轉換器元件將影像資料發送到 FarmBeats。 農業影像資料可以從各種來源生成，如多光譜攝像機、衛星和無人機。 農業影像合作夥伴可以與 FarmBeats 集成，為客戶提供為其農場自訂生成的地圖。

資料一旦可用，可以通過 FarmBeats 加速器進行視覺化，並可能用於農業企業或客戶系統集成商的資料融合和機器學習/人工智慧 （ML/AI） 模型構建。

FarmBeats 提供以下功能：

- 使用 /擴展類型 API 定義自訂圖像類型、源和檔案格式。
- 通過 /Scene 和 /SceneFile API 從各種來源引入影像資料。

以下資訊側重于將任何形式的影像引入 FarmBeats 系統。

選擇**無人機影像**部分時，將打開一個快顯視窗，顯示無人機正射馬賽克的高解析度圖像。 您可以訪問合作夥伴軟體，該軟體有助於規劃無人機飛行並獲得原始資料。 您將繼續使用合作夥伴的軟體進行路徑規劃和正交鑲嵌圖像拼接。

無人機合作夥伴需要使客戶能夠將其客戶帳戶與其在 Azure 上的伺服器場Beats實例連結。

您必須在無人機合作夥伴軟體中使用以下憑據來連結 FarmBeats：

- API 端點
- 租用戶識別碼
- 用戶端識別碼
- 用戶端密碼

## <a name="api-development"></a>API 開發

API 包含斯瓦格技術文檔。 有關 API 和相應請求或回應的資訊，請參閱[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

## <a name="authentication"></a>驗證

伺服器場節拍使用 Microsoft Azure[活動目錄](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)（Azure AD）。 Azure 應用服務提供內置身份驗證和授權支援。 

如需 Azure AD 的詳細資訊，請參閱 [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。   

FarmBeats Datahub 使用無記名身份驗證，這需要以下憑據：

- 用戶端識別碼
- 用戶端密碼
- 租用戶識別碼

使用以前的憑據，調用方可以請求訪問權杖，該權杖需要在後續 API 請求中（標頭部分）中發送，如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

以下 Python 代碼示例檢索訪問權杖。 然後，您可以將權杖用於後續對 FarmBeats 的 API 呼叫。

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
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

## <a name="http-request-headers"></a>HTTP 要求標頭

以下是對 FarmBeats Datahub 進行 API 呼叫時需要指定的最常見請求標頭。

**頭** | **描述及範例**
--- | ---
Content-Type  | 請求格式（內容類型：應用程式/<format>）。 對於伺服器場節拍 Datahub API，格式為 JSON。 Content-Type: application/json
授權 | 指定進行 API 呼叫所需的訪問權杖。 授權：承載<訪問權杖>
Accept  | 回應格式。 對於伺服器場節拍 Datahub API，格式為 JSON。 接受：應用程式/json


## <a name="api-requests"></a>API 請求

要發出 REST API 請求，請組合：

- HTTP 方法（獲取、POST 和 PUT）。
- API 服務的 URL。
- 資源 URI（用於查詢、提交資料、更新或刪除）。
- 一個或多個 HTTP 要求標頭。

或者，您可以在 GET 調用上包括查詢參數，以篩選 、限制 的大小和在回應中對資料進行排序。

以下示例請求是獲取設備清單：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

大多數 GET、POST 和 PUT 呼叫都需要 JSON 請求正文。

以下示例請求是創建設備。 此示例具有帶有請求正文的輸入 JSON。


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>資料格式

JSON 是一種與語言無關的通用資料格式，它提供了任意資料結構的簡單文本表示形式。 有關詳細資訊，請參閱[JSON 組織](https://JSON.org)。

## <a name="ingest-imagery-into-farmbeats"></a>將圖像引入伺服器場節拍

合作夥伴擁有連接到 FarmBeats Datahub 的憑據後，合作夥伴將執行"翻譯"元件中的以下步驟。

1.  根據要上載的圖像類型，為以下欄位創建新的擴展類型：

    - **場景源**：例如，drone_partner_name
    - **場景類型**：例如，無人機
    - **場景檔案類型**：例如，葉綠素索引
    - **場景檔內容類型**：例如，圖像/圖片

2.  調用 /Farms API 從 Azure FarmBeats 系統獲取伺服器場清單。
3.  為客戶提供從伺服器場清單中選擇單個伺服器場的能力。

    合作夥伴系統必須在合作夥伴軟體中顯示伺服器場，以執行路徑規劃和無人機飛行和圖像收集。

4.  調用 /Scene API 並提供所需的詳細資訊，以創建具有唯一場景 ID 的新場景。
5.  接收 blob SAS URL，以在伺服器場Beats系統中將所需的圖像上載到伺服器場Beats Datahub（所選伺服器場的上下文中）。

下面是 API 呼叫的詳細流。

### <a name="step-1-extendedtype"></a>第 1 步：擴展類型

簽入 /擴展類型 API 以查看類型和檔源在 FarmBeats 上是否可用。 為此，請調用 /擴展類型 API 上的 GET。

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

此步驟是一次性設置。 此新場景類型的範圍僅限於安裝 Azure FarmBeats 的訂閱。

例如，要添加 SceneSource："SlantRange"，您需要在 /擴展類型 API 的 ID 上使用鍵"SceneSource"輸入負載執行 PUT。

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

綠色欄位是系統定義的場景源值的新添加。

### <a name="step-2-get-farm-details"></a>第 2 步：獲取伺服器場詳細資訊

場景（.tiff 或 .csv 檔）位於伺服器場的上下文中。 您需要通過在 /Farm API 上執行 GET 來獲取伺服器場詳細資訊。 API 返回伺服器場節拍中可用的伺服器場清單。 您可以選擇要為其引入資料的伺服器場。

GET /農場回應：

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

### <a name="step-3-create-a-scene-id-post-call"></a>第 3 步：創建場景 ID（POST 調用）

使用給定的資訊創建新的場景（.tiff 或 .csv 檔），該資訊提供與場景關聯的日期、序列和伺服器場 ID。 與場景關聯的中繼資料可以在屬性下定義，其中包括度量值的持續時間和類型。

創建新場景將創建一個與伺服器場關聯的新場景 ID。 創建場景 ID 後，使用者可以使用相同的來創建新檔 （.tiff 或 .csv） 並存儲檔的內容。

在 /Scene API 上的 POST 調用的示例輸入負載：

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

**創建場景檔**

步驟 3 中返回的場景 ID 是場景檔的輸入。 場景檔返回一個 SAS URL 權杖，該權杖的有效期為 24 小時。

如果使用者需要上傳圖像流的程式設計方法，則 Blob 存儲 SDK 可用於使用場景檔 ID、位置和 URL 定義方法。

在 /SceneFile API 上的 POST 調用的示例輸入負載：

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

對 /SceneFile API 的 POST 調用返回 SAS 上載 URL，該 URL 可用於使用 Azure Blob 存儲用戶端或庫上載 .csv 或 .tiff 檔。


## <a name="next-steps"></a>後續步驟

有關基於 REST API 的集成詳細資訊的詳細資訊，請參閱[REST API](rest-api-in-azure-farmbeats.md)。
