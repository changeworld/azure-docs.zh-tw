---
title: 從氣象合作夥伴取得天氣資料
description: 本文說明如何取得合作夥伴的天氣資料。
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66138fb04b1053215a2c2ec07cec1b56e38fed0b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800642"
---
# <a name="get-weather-data-from-weather-partners"></a>從氣象合作夥伴取得天氣資料

Azure FarmBeats 可協助您使用以 docker 為基礎的連接器架構，將氣象資料提供者的天氣資料帶入其中。 使用此架構，氣象資料提供者會執行可與 FarmBeats 整合的 docker。 目前支援下列天氣資料提供者：

[DTN](https://www.dtn.com/dtn-content-integration/)

天氣資料可以用來產生可操作的深入解析，並在 FarmBeats 上建立 AI/ML 模型。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

若要取得天氣資料，請確定您已安裝 FarmBeats。 **1.2.11 或更高版本支援氣象整合**。 若要安裝 Azure FarmBeats，請參閱[安裝 FarmBeats](https://aka.ms/farmbeatsinstalldocumentation)。

## <a name="enable-weather-integration-with-farmbeats"></a>啟用 FarmBeats 的天氣整合

若要開始取得 FarmBeats 資料中樞上的氣象資料，請遵循下列步驟：

1. 前往您的 FarmBeats 資料中樞 swagger （https://farmbeatswebsite-api.azurewebsites.net/swagger)

2. 流覽至/Partner API，並使用下列輸入承載提出 POST 要求：

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   例如，若要從 DTN 取得天氣資料，請使用以下的承載。 您可以依據您的喜好設定來修改名稱和描述。

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > 如需合作夥伴物件的詳細資訊，請參閱[附錄](get-weather-data-from-weather-partner.md#appendix)

   上一個步驟會布建資源，讓 docker 在客戶的 FarmBeats 環境中執行。  

   布建上述資源需要大約10-15 分鐘的時間。

3. 檢查您在步驟2中建立之/Partner 物件的狀態。 若要這麼做，請在/Partner API 上提出 GET 要求，並檢查合作夥伴物件的**狀態**。 一旦 FarmBeats 成功布建合作夥伴，狀態就會設定為 [**作用中]。**

4. 流覽至/JobType API，並在相同的上提出 GET 要求。 檢查是否有在步驟2的夥伴新增程式中建立的天氣作業。 天氣作業中的**pipelineName**欄位將會是下列格式：「合作夥伴 name_partner type_job 名稱」。

5. 現在，您的 FarmBeats 實例有作用中的氣象資料合作夥伴，您可以執行作業來要求特定位置（緯度/經度）和日期範圍的氣象資料。 JobType 將會詳細說明執行天氣作業所需的參數。

   例如，針對 DTN，將會建立下列 JobType：
   
   - get_dtn_daily_observations （取得位置和時間週期的每日觀察）
   - get_dtn_daily_forecasts （取得位置和時間週期的每日預測）
   - get_dtn_hourly_observations （取得位置和時間週期的每小時觀察）
   - get_dtn_hourly_forecasts （取得位置和時間週期的每小時預測）

6. 請記下 JobType 的**識別碼**和參數。

7. 流覽至/Jobs API，並在/Jobs 上使用下列輸入承載提出 POST 要求：

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   例如，若要執行**get_dtn_daily_observations**，請使用下列承載：

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. 上一個步驟會執行合作夥伴 docker 中定義的天氣作業，並將氣象資料內嵌至 FarmBeats。 您可以在/Jobs 上提出 GET 要求，並在回應中尋找**currentState** ，以檢查作業的狀態。 完成後，currentState 會設定為 [**成功**]。

## <a name="query-ingested-weather-data"></a>查詢內嵌天氣資料

天氣作業完成後，您可以使用 FarmBeats Datahub REST Api 來查詢內嵌的氣象資料，以建立模型或可操作的深入解析。

### <a name="query-using-rest-api"></a>使用 REST API 查詢

若要使用 FarmBeats REST API 來查詢天氣資料，請遵循下列步驟：

1. 在您的 FarmBeats 資料中樞 swagger （中 https://yourdatahub.azurewebsites.net/swagger) ，流覽至/WEATHERDATALOCATION API 並提出 GET 要求。 回應將會針對在作業執行過程中指定的位置（緯度/經度）建立/WeatherDataLocation 物件。 請記下物件的**識別碼**和**weatherDataModelId** 。

2. 如步驟1所述，在**weatherDataModelId**的/WeatherDataModel API 上建立 GET/{id}。 「氣象資料模型」包含內嵌天氣資料的所有中繼資料和詳細資訊。 例如，**氣象資料模型**物件內的**天氣量值**，會有有關支援哪些天氣資訊，以及在哪些類型和單位中的詳細資料。 例如，

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   記下氣象資料模型的 GET/{id} 呼叫回應。

3. 流覽至**遙測**API，並使用下列輸入承載提出 POST 要求：

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. 包含可用於指定時間範圍之天氣資料的回應如下所示：

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

在上述範例中，回應有兩個時間戳記的資料，以及量值名稱（「溫度」）和兩個時間戳記中報告的天氣資料的值。 您必須參考相關聯的氣象資料模型（如上述步驟2所述），以解讀所報告值的類型和單位。

## <a name="appendix"></a>附錄

|        Partner   |  詳細資料   |
| ------- | -------             |
|     DockerDetails-imageName         |          Docker 映射名稱。 例如，docker.io/mydockerimage （hub.docker.com 中的影像）或 myazureacr.azurecr.io/mydockerimage （Azure Container Registry 中的影像）等等。 如果未提供登錄，則預設值為 hub.docker.com      |
|          DockerDetails-imageTag             |         Docker 映射的標記名稱。 預設值為「最新」     |
|  DockerDetails-認證      |  用來存取私人 docker 的認證。 這會由合作夥伴提供給客戶   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch 的 VM SKU。 如需所有可用的 Linux 虛擬機器，請參閱[這裡](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 有效值為：「Small」、「ExtraLarge」、「大型」、「A8」、「A9」、「中」、「A5」、「A6」、「A7」、「STANDARD_D1」、「STANDARD_D2」、「STANDARD_D3」、' STANDARD_D4 '、' STANDARD_D11 '、' STANDARD_D12 '、' STANDARD_D13 '、' STANDARD_D14 '、' A10 '、' A11 '、' STANDARD_D1_V2 '、' STANDARD_D2_V2 '、' STANDARD_D3_V2 '、' STANDARD_D4_V2 '、' STANDARD_D11_V2 '、' STANDARD_D12_V2 '、' STANDARD_D13_V2 '、' STANDARD_D14_V2 '、' STANDARD_G1 '、' STANDARD_G2 '、' STANDARD_G3 '、' STANDARD_G4 '、' STANDARD_G5 '、' STANDARD_D5_V2 '、' BASIC_A1 '、' BASIC_A2 '、' BASIC_A3 '、' BASIC_A4 '、' STANDARD_A1 '、' STANDARD_A2 '、' STANDARD_A3 '、' STANDARD_A4 '、' STANDARD_A5 '、' STANDARD_A6 '、' STANDARD_A7 '、' STANDARD_A8 '、' STANDARD_A9 '、' STANDARD_A10 '、' STANDARD_A11 '、' STANDARD_D15_V2 '、' STANDARD_F1 '、' STANDARD_F2 '、' STANDARD_F4 '、' STANDARD_F8 '、' STANDARD_F16 '、' STANDARD_NV6 '、' STANDARD_NV12 '、' STANDARD_NV24 '、' STANDARD_NC6 '、' STANDARD_NC12 '、' STANDARD_NC24 '、' STANDARD_NC24r '、「STANDARD_H8」、「STANDARD_H8m」、「STANDARD_H16」、「STANDARD_H16m」、「STANDARD_H16mr」、「STANDARD_H16r」、「STANDARD_A1_V2」、「STANDARD_A2_V2」、「STANDARD_A4_V2」、「STANDARD_A8_V2」、「STANDARD_A2m_V2」、「STANDARD_A4m_V2」、「STANDARD_A8m_V2」、「STANDARD_M64ms」、「STANDARD_M128s」、「STANDARD_D2_V3」。 **預設值為 "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  否。 batch 集區的專用電腦節點。 預設值為 1。 |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch-節點代理程式 SKU 識別碼。 目前僅支援 "batch. node.js 18.04" batch-節點代理程式。    |
| DockerDetails - partnerCredentials | 在 docker 中呼叫合作夥伴 API 的認證。 合作夥伴必須根據支援的驗證機制，將此資訊提供給他們的客戶（例如）。 使用者名稱/密碼或 API 金鑰。 |
| partnerType | 「天氣」（FarmBeats 中的其他合作夥伴類型為「感應器」和「影像」）  |
|  NAME   |   FarmBeats 系統中合作夥伴的所需名稱   |
|  description |  Description   |

## <a name="next-steps"></a>後續步驟

您現在已從 Azure FarmBeats 實例查詢感應器資料。 現在，請瞭解如何為您的伺服器陣列[產生對應](generate-maps-in-azure-farmbeats.md#generate-maps)。
