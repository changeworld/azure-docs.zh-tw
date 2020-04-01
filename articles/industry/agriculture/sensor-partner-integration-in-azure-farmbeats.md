---
title: 感應器合作夥伴整合
description: 本文介紹了感測器合作夥伴集成。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3431576acbb01a0cc3a5f372460b28be05bf7ce7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437473"
---
# <a name="sensor-partner-integration"></a>感應器合作夥伴整合

本文提供有關 Azure FarmBeats**轉換器**元件的資訊,該元件支援感測器合作夥伴整合。

使用此元件,合作夥伴可以使用 FarmBeats Datahub API 與伺服器場 Beats 整合,並將客戶設備數據和遙測發送到 FarmBeats Datahub。 一旦數據在 FarmBeats 中可用,它就會使用 FarmBeats 加速器進行可視化,並可用於數據融合和建構機器學習/人工智慧模型。

## <a name="before-you-start"></a>開始之前

要開發轉換器元件,您需要以下憑據來啟用對 FarmBeats API 的訪問。

- API 端點
- 租用戶識別碼
- 用戶端識別碼
- 用戶端密碼
- 事件中心連接字串

有關獲取上述憑據,請參閱此部分:[啟用設備集成](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>翻譯開發

**以 REST API 為基礎的整合**

FarmBeats 的感應器資料整合功能透過 REST API 公開。 功能包括元數據定義、設備和感測器配置以及設備和感測器管理。

**遙測引入**

遙測資料對應到在 Azure 事件中心上發布的用於處理的規範消息。 Azure 事件中心是一種服務,它支援從連接的設備和應用程式引入即時數據(遙測)。

**API 開發**

API 包含斯瓦格技術文檔。 有關 API 及其相應請求或回應的詳細資訊,請參閱[Swagger](https://aka.ms/FarmBeatsSwagger)。

[驗證]****

伺服器場節拍使用 Microsoft Azure 活動目錄身份驗證。Azure 應用服務提供內建身份驗證和授權支援。

如需詳細資訊，請參閱 [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。

FarmBeats Datahub 使用無記名身份驗證,這需要以下認證:
   - 用戶端識別碼
   - 用戶端密碼
   - 租用戶識別碼

使用這些憑據,調用方可以請求訪問權杖。 權杖需要在後續 API 請求中(標頭部分)中發送,如下所示:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

以下範例 Python 代碼提供存取權杖,可用於對 FarmBeats 的後續 API 呼叫。

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

以下是對 FarmBeats Datahub 進行 API 呼叫時需要指定的最常見請求標頭。


**頭** | **描述及範例**
--- | ---
Content-Type | 請求格式(內容類型:應用程式/<format>)。 對於伺服器場節拍 Datahub API,格式為 JSON。 Content-Type: application/json
授權 | 指定進行 API 呼叫所需的存取權杖。 授權:承載<訪問權杖>
Accept | 回應格式。 對於伺服器場節拍 Datahub API,格式為 JSON。 接受應用程式/json

**API 要求**

要發出 REST API 請求,請將 HTTP(GET、POST 或 PUT)方法、API 服務的 URL、統一資源標識符 (URI) 與用於查詢、將資料提交到、更新或刪除以及一個或多個 HTTP 請求標頭的資源組合在一起。 API 服務的網址 是您提供的 API 終結點。 下面是一個範例:HTTPs://\<您的資料庫網站名稱>.azure 網站.net

或者,您可以在 GET 調用上包括查詢參數,以篩選 、限制的大小和在回應中對數據進行排序。

以下範例請求是獲取設備清單。

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
大多數 GET、POST 和 PUT 呼叫都需要 JSON 請求正文。

以下範例請求是創建設備。 (此示例具有帶有請求正文的輸入 JSON。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>資料格式

JSON 是一種與語言無關的通用數據格式,它提供了任意數據結構的簡單文本表示形式。 有關詳細資訊,請參閱[json.org](http://json.org)。

## <a name="metadata-specifications"></a>中繼資料規範

FarmBeats Datahub 具有以下 API,使設備合作夥伴能夠創建和管理設備或感測器元數據。

- /**設備模型**:設備模型對應於設備的元數據,例如製造商和設備類型,即閘道或節點。
- /**設備**:設備對應於伺服器場上存在的物理設備。
- /**感測器模型**:感測器模型對應於感測器的中繼資料,如製造商、類比或數位感測器的類型以及感測器測量值(如環境溫度和壓力)。
- /**感測器**:感測器對應於記錄值的物理感測器。 感應器通常連接到具有設備 ID 的設備。

  **裝置模型** |  |
  --- | ---
  類型(節點、閘道)  | 裝置型態 - 節點或閘道 |
  製造商  | 製造商名稱 |
  產品代碼  | 設備產品代碼或型號名稱或編號。 例如,環境監視器#6800。 |
  連接埠  | 埠名稱和類型,即數位或類比。  |
  名稱  | 名稱以標識資源。 例如,型號名稱或產品名稱。 |
  描述  | 提供模型的有意義的描述。 |
  屬性  | 製造商的其他屬性。 |
  **裝置** |  |
  裝置模型 Id  |關聯設備型號的 ID。 |
  硬體代碼   |設備的唯一 ID,如 MAC 位址。  |
  ReportingInterval |報告間隔(以秒為單位)。 |
  Location    |設備緯度(-90 至 +90)、經度(-180 至 180)和高程(以米為單位)。 |
  父裝置 Id | 此設備連接到的父設備的 ID。 例如,如果節點連接到閘道,則該節點具有父 DeviceID 作為閘道。 |
  名稱  | 名稱以標識資源。 設備合作夥伴需要發送與設備合作夥伴端的設備名稱一致的名稱。 如果設備名稱在設備合作夥伴端是使用者定義的,則同一使用者定義的名稱應傳播到 FarmBeats。  |
  描述  | 提供有意義的說明。  |
  屬性  |製造商的其他屬性。  |
  **感應器模型** |  |
  型態(模擬、數位)  |提及類比或數位感測器。|
  製造商  | 製造商名稱。 |
  產品代碼  | 產品代碼或型號名稱或編號。 例如,RS-CO2-N01。  |
  感應器測量>名稱  | 感測器測量值的名稱。 僅支援小寫。 對於不同深度的測量,請指定深度。 例如,soil_moisture_15cm。 此名稱必須與遙測數據一致。 |
  感應器測量>資料類型  | 遙測數據類型。 目前,支援雙精度值。 |
  感應器測量>型態  | 感測器遙測數據的測量類型。 以下是系統定義的類型:環境溫度、CO2、深度、電導性、葉濕性、長度、液位、硝酸鹽、O2、PH、磷酸鹽、點InTime、鉀、壓力、雨量計、相對濕度、鹽度、土壤水分、土壤溫度、太陽輻射、狀態、時間持續時間、紫外線輻射、UVIndex、體積、風向、風潤、風速、蒸發量、PAR。 要添加更多,請參閱 /擴展類型 API。
  感應器測量>單元 | 感測器遙測數據單位。 以下是系統定義的單位:無單位、攝氏度、華氏、開爾文、蘭金、帕斯卡、水星、PSI、米計、米、英寸、英尺、英里、千米、英里珀小時、英里珀小時、英里珀秒、KMPer秒、米佩爾小時、米佩爾秒、度、 瓦克珀廣場、千瓦PerSquareMeter、毫瓦佩爾廣場中心、米珠爾佩爾方中心米、米珠爾佩爾方中心米、米珠爾秒、度、 瓦克珀廣場、千瓦PerSquareMeter、毫瓦佩爾廣場中心、米珠爾佩爾方中心米、米珠爾佩爾方中心米、米珠爾佩爾方中心米、米佩爾秒、度、 瓦克珀廣場、千瓦PerSquareMeter、毫瓦佩爾廣場中心、米珠爾佩爾方中心米、米珠爾佩爾方中心米、米珠爾佩爾方中心米、米珠爾佩爾方中心米、米珠爾秒、度、 瓦克珀廣場、千瓦PerSquareMeter、毫瓦佩爾廣場中心、米珠爾佩爾方中心米、米珠爾佩爾方中心米、米珠爾佩爾方中心米、米佩爾秒、度、 瓦克珀廣場、千瓦PerSquareMeter、毫瓦佩爾廣場中心、米珠爾佩爾方中心 體積水含量、百分比、零件PerM、MicroMol、MicroMolePerLiter、西門子PerperPerPerPerPerPerm、MilliSiemensPerpercentiMeter、百分之尺、德米西門子PerMeter、KiloPascal、體積、升、毫升、秒、無ix時間、微摩爾、微摩爾、百萬元、微摩爾、米本、百萬元、百 萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、千米、百計、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百萬元、百分之尺、百萬元、百分之一、百萬元數、百分之尺、百萬元數、百萬元、百分之一、百萬元、百分之尺、百萬元數、百分之尺、百分之 要添加更多,請參閱 /擴展類型 API。
  偵測器測量>聚合類型  | 無、平均值、最大值、最小值或標準偏差。
  感應器測量>深度  | 感測器的深度(以釐米為單位)。 例如,測量地下10釐米的水分。
  感應器測量>說明  | 提供測量的有意義的描述。
  名稱  | 名稱以標識資源。 例如,型號名稱或產品名稱。
  描述  | 提供模型的有意義的描述。
  屬性  | 製造商的其他屬性。
  **感應器**  |  |
  硬體代碼  | 製造商設置的感測器的唯一 ID。
  感應器IdId  | 關聯感測器型號的 ID。
  Location  | 感測器緯度(-90 至 +90)、經度(-180 至 180)和高程(以米為單位)。
  連接埠>名稱  |感測器在設備上連接到的埠的名稱和類型。 這必須與設備模型中定義的名稱相同。
  deviceId  | 感測器連接到的設備 ID。
  名稱  | 名稱以標識資源。 例如,感測器名稱或產品名稱、型號或產品代碼。
  描述  | 提供有意義的說明。
  屬性  | 製造商的其他屬性。

 有關每個物件及其屬性的資訊,請參閱[斯瓦格](https://aka.ms/FarmBeatsDatahubSwagger)。

 > [!NOTE]
 > API 傳回所建立的每個實體的唯一 ID。 此 ID 需要由轉換器保留,以便進行設備管理和元數據同步。


**中繼同步**

翻譯人員應發送元數據的更新。 例如,更新方案是更改設備或感測器名稱以及更改設備或感測器位置。

轉換器應該能夠添加新設備或感測器,這些設備或感測器由使用者在附加後連結後安裝。 同樣,如果使用者更新了設備或感測器,則應在 FarmBeats 中更新相應的設備或感測器。 需要更新設備或感測器的典型方案是設備位置的更改或在節點中添加感測器。


> [!NOTE]
> 設備或感測器中繼資料不支援刪除。
>
> 要更新元資料,必須在設備或感測器上調用 /Get/{id},更新更改的屬性,然後執行 /Put/{id},以便用戶設置的任何屬性不會丟失。

### <a name="add-new-types-and-units"></a>新增類型和單位

FarmBeats 支援添加新的感測器測量類型和單位。 有關 /擴充型態 API 的詳細資訊,請參考[斯瓦格](https://aka.ms/FarmBeatsSwagger)。

## <a name="telemetry-specifications"></a>遙測規範

遙測資料對應到在 Azure 事件中心上發布的用於處理的規範消息。 Azure 事件中心是一種服務,它支援從連接的設備和應用程式引入即時數據(遙測)。

## <a name="send-telemetry-data-to-farmbeats"></a>傳送到伺服器中傳送遙測資料

要向 FarmBeats 發送遙測數據,請建立一個用戶端,該用戶端將消息發送到 FarmBeats 中的事件中心。 有關遙測數據的詳細資訊,請參閱[向事件中心發送遙測](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)數據。

下面是一個範例 Python 代碼,該代碼將遙測作為客戶端發送到指定的事件中心。

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

訊息格式如下:

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
遙測 JSON 中的所有鍵名稱都應小寫。 範例包括設備ID和感測器數據。

例如,下面是一條遙測消息:


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
> 以下各節與其他更改相關(例如。 UI、錯誤管理等)感測器合作夥伴在開發轉換器元件時可以參考的。


## <a name="link-a-farmbeats-account"></a>連結伺服器場節拍帳戶

客戶購買和部署設備或感測器後,他們可以在設備合作夥伴的軟體作為服務 (SaaS) 門戶上訪問設備數據和遙測數據。 裝置合作夥伴透過提供輸入以下認證的的方法,使客戶能夠將其帳戶連結到 Azure 上的伺服器場 Beats 實例:

   - 顯示名稱(使用者為此整合定義名稱的可選欄位)
   - API 端點
   - 租用戶識別碼
   - 用戶端識別碼
   - 用戶端密碼
   - 事件Hub連接字串
   - 開始日期

   > [!NOTE]
   > 開始日期啟用歷史數據饋送,即使用者指定日期的數據。

## <a name="unlink-farmbeats"></a>取消連結伺服器場節拍

設備合作夥伴可以使客戶能夠取消連結現有的伺服器場節拍集成。 取消連結伺服器場Beats 不應刪除在 FarmBeats Datahub 中創建的任何設備或感測器中數據。 取消連結執行以下操作:

   - 停止遙測流。
   - 刪除並擦除設備合作夥伴上的集成憑據。

## <a name="edit-farmbeats-integration"></a>編輯伺服器場節拍整合

如果用戶端金鑰或連接字串發生更改,設備合作夥伴可以幫助客戶編輯 FarmBeats 整合設定。 在這種情況下,只有以下欄位是可編輯的:

   - 顯示名稱(如適用)
   - 用戶端機密(應以"2x8+"格式或顯示/隱藏功能顯示,而不是明文顯示)
   - 連接字串(應以"2x8+"格式或顯示/隱藏功能顯示,而不是明文顯示)

## <a name="view-the-last-telemetry-sent"></a>檢視傳送的最後一個遙測資料

設備合作夥伴可以使客戶查看上次發送遙測的時間戳,該時間戳位於 **「遙測發送」** 下。 此時,最新的遙測數據已成功發送到 FarmBeats。

## <a name="troubleshooting-and-error-management"></a>容錯排除與錯誤管理

**排除選項或支援**

如果客戶無法在指定的 FarmBeats 實例中接收設備數據或遙測數據,則設備合作夥伴應提供支援和故障排除機制。

**遙測資料保留**

遙測數據也應保留預定義的時間段,以便在發生錯誤或數據丟失時在調試或重新發送遙測時非常有用。

**錯誤管理或錯誤通知**

如果錯誤影響設備或感測器元數據或設備合作夥伴系統中的數據集成或遙測數據流,客戶應收到通知。 還應設計和實施解決任何錯誤的機制。

**連線清單**

裝置製造商或合作夥伴可以使用以下檢查表確保客戶提供的認證準確無誤:

   - 檢查是否使用提供的憑據接收訪問權杖。
   - 檢查 API 呼叫是否成功與接收的訪問權杖一起。
   - 檢查事件中心客戶端連接是否已建立。

## <a name="next-steps"></a>後續步驟

有關 REST API 的詳細資訊,請參閱[REST API](rest-api-in-azure-farmbeats.md)。
