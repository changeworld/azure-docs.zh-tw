---
title: 從天氣合作夥伴取得天氣資料
description: 本文說明如何取得合作夥伴的氣象資料。
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460881"
---
# <a name="get-weather-data-from-weather-partners"></a>從天氣合作夥伴取得天氣資料

Azure FarmBeats 可協助您使用以 Docker 為基礎的連接器架構，將天氣資料提供者的氣象資料導入。 使用此架構時，氣象資料提供者會執行可與 FarmBeats 整合的 Docker。 目前支援下列氣象資料提供者。

  ![FarmBeats 合作夥伴](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

天氣資料可用來產生可採取行動的見解，並在 FarmBeats 中建立 AI 或 ML 模型。

## <a name="before-you-start"></a>開始之前

若要取得天氣資料，請確定您已 [安裝 FarmBeats](./install-azure-farmbeats.md)。 1.2.11 和更高版本支援氣象整合。 

## <a name="enable-weather-integration-with-farmbeats"></a>啟用與 FarmBeats 的氣象整合

若要開始在您的 FarmBeats Datahub 上取得天氣資料：

1. 移至您的 FarmBeats Datahub Swagger `https://farmbeatswebsite-api.azurewebsites.net/swagger` 。

2. 移至/Partner API，然後提出 POST 要求。 使用下列輸入承載：

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

   例如，若要從 DTN 取得天氣資料，請使用下列承載。 您可以根據您的喜好設定來修改名稱和描述。

   > [!NOTE]
   > 下列步驟需要 API 金鑰。 若要取得 DTN 訂用帳戶的金鑰，請聯絡 DTN。

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
   > 如需有關 partner 物件的詳細資訊，請參閱本文中的 [附錄](get-weather-data-from-weather-partner.md#appendix) 。

   上一個步驟將布建資源，讓 Docker 在客戶的 FarmBeats 環境中執行。  

   布建資源大約需要10到15分鐘的時間。

3. 檢查您在上一個步驟中建立之/Partner 物件的狀態。 若要檢查狀態，請在/Partner API 上提出 GET 要求，並檢查夥伴物件的狀態。 FarmBeats 成功布建夥伴之後，狀態會設定為 [**作用中]。**

4. 在/JobType API 上，提出 GET 要求。 檢查您稍早在合作夥伴新增程式中建立的氣象工作。 在氣象工作中，[ **pipelineName** ] 欄位的格式如下： [ **夥伴-name_partner-type_job 名稱**]。

      現在您的 FarmBeats 實例有使用中的氣象資料合作夥伴。 您可以執行工作來要求特定位置的氣象資料 (緯度和經度) 和日期範圍。 工作類型將會詳細說明執行氣象工作所需的參數。

      例如，針對 DTN，將會建立下列工作類型：
   
      - **get_dtn_daily_observations**：取得位置和時間週期的每日觀察。
      - **get_dtn_daily_forecasts**：取得地點和時間週期的每日預測。
      - **get_dtn_hourly_observations**：取得位置和時間週期的每小時觀察。
      - **get_dtn_hourly_forecasts**：取得地點和時間週期的每小時預測。

6. 記下作業類型的識別碼和參數。

7. 移至/Jobs API，並在/Jobs. 上提出 POST 要求 使用下列輸入承載：

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

   例如，若要執行 **get_dtn_daily_observations**，請使用下列承載：

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

8. 上述步驟會執行合作夥伴 Docker 中所定義的氣象作業，並將天氣資料內嵌至 FarmBeats。 您可以在/Jobs. 上提出 GET 要求以檢查作業的狀態 在回應中尋找 **currentState**。 當您完成時， **currentState** 會設為 [ **成功**]。

## <a name="query-ingested-weather-data"></a>查詢內嵌氣象資料

在天氣工作完成後，您可以使用 FarmBeats Datahub REST Api 來查詢內嵌氣象資料，以建立模型或可操作的見解。

若要使用 FarmBeats REST API 來查詢氣象資料：

1. 在您的 FarmBeats Datahub [Swagger](https://yourdatahub.azurewebsites.net/swagger)中，移至/WeatherDataLocation API 並提出 GET 要求。 回應包括針對位置所建立的/WeatherDataLocation 物件 (緯度和經度) 執行指定的作業。 記下物件的 **識別碼** 和 **weatherDataModelId** 。

2. 如同您先前所做的，對 **weatherDataModelId** 的/WeatherDataModel API 提出 GET/{id} 要求。 氣象資料模型會顯示所有中繼資料，以及有關內嵌氣象資料的詳細資料。 例如，在氣象資料模型物件中，氣象措施會詳細說明支援哪些天氣資訊，以及哪些類型和單位。 例如：

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

   記下來自氣象資料模型 GET/{id} 呼叫的回應。

3. 移至遙測 API 並提出 POST 要求。 使用下列輸入承載：

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    回應會顯示指定時間範圍內可用的氣象資料：

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

在上述範例中，回應會顯示兩個時間戳記的資料。 它也會顯示在兩個時間戳記中回報之天氣資料的量值名稱 (溫度) 和值。 請參閱相關聯的氣象資料模型，以解讀報告值的類型和單位。

## <a name="troubleshoot-job-failures"></a>針對作業失敗進行疑難排解

若要疑難排解作業失敗，請 [檢查作業記錄](troubleshoot-azure-farmbeats.md#weather-data-job-failures)。


## <a name="appendix"></a>附錄

|        Partner   |  詳細資料   |
| ------- | -------             |
|     DockerDetails-imageName         |          Docker 映射名稱。 例如，docker.io/mydockerimage hub.docker.com 中的 (映射) 或 myazureacr.azurecr.io/mydockerimage 中 Azure Container Registry) 中的 (影像。 如果未提供登錄，預設值會是 hub.docker.com。      |
|          DockerDetails-imageTag             |         Docker 映射的標記名稱。 預設值為「最新」。     |
|  DockerDetails-認證      |  存取私用 Docker 的認證。 夥伴會提供認證。   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU。 如需詳細資訊，請參閱 [所有可用的 Linux 虛擬機器](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 <BR> <BR> 有效的值為 ' Small '、' ExtraLarge '、' 大型 '、' A8 '、' A9 '、' Medium '、' A5 '、' A6 '、' A7 '、' STANDARD_D1 '、' STANDARD_D2 '、' STANDARD_D3 '、' STANDARD_D4 '、' STANDARD_D11 '、' STANDARD_D12 '、' STANDARD_D13 '、' STANDARD_D14 '、' A10 '、' A11 '、' STANDARD_D1_V2 '、' STANDARD_D2_V2 '、' STANDARD_D3_V2 '、' STANDARD_D4_V2 '、' STANDARD_D11_V2 '、' STANDARD_D12_V2 '、' STANDARD_D13_V2 '、' STANDARD_D14_V2 '、' STANDARD_G1 '、' STANDARD_G2 '、' STANDARD_G3 '、' STANDARD_G4 '，「STANDARD_G5」、「STANDARD_D5_V2」、「BASIC_A1」、「BASIC_A2」、「BASIC_A3」、「BASIC_A4」、「STANDARD_A1」、「STANDARD_A2」、「STANDARD_A3」、「STANDARD_A4」、「STANDARD_A5」、「STANDARD_A6 '、' STANDARD_A7 '、' STANDARD_A8 '、' STANDARD_A9 '、' STANDARD_A10 '、' STANDARD_A11 '、' STANDARD_D15_V2 '、' STANDARD_F1 '、' STANDARD_F2 '、' STANDARD_F4 '、' STANDARD_F8 '、' STANDARD_F16 '、' STANDARD_NV6 '、' STANDARD_NV12 '、' STANDARD_NV24 '、' STANDARD_NC6 '、' STANDARD_NC12 '、' STANDARD_NC24 '、' STANDARD_NC24r '、「STANDARD_H8」、「STANDARD_H8m」、「STANDARD_H16」、「STANDARD_H16m」、「STANDARD_H16mr」、「STANDARD_H16r」、「STANDARD_A1_V2」、「STANDARD_A2_V2」、「STANDARD_A4_V2」、「STANDARD_A8_V2」、「STANDARD_A2m_V2」、「STANDARD_A4m_V2」、「STANDARD_A8m_V2」、「STANDARD_M64ms」、「STANDARD_M128s」和「STANDARD_D2_V3」。 *預設值為 ' STANDARD_D2_V2 '。*  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  每個 batch 集區的專用電腦節點數目。 預設值為 1。 |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch-節點代理程式 SKU 識別碼。 目前僅支援 "batch. ubuntu 18.04" 批次節點代理程式。    |
| DockerDetails - partnerCredentials | 在 Docker 中呼叫夥伴 API 的認證。 合作夥伴會根據支援的授權機制來提供此資訊;例如，使用者名稱和密碼，或 API 金鑰。 |
| partnerType | 「天氣」。 FarmBeats 中的其他夥伴類型為「感應器」和「影像」。  |
|  NAME   |   FarmBeats 系統中所需的夥伴名稱。   |
|  description |  描述   |

## <a name="next-steps"></a>後續步驟

您已從 Azure FarmBeats 實例查詢感應器資料，接下來請瞭解如何為您的伺服器陣列 [產生對應](generate-maps-in-azure-farmbeats.md#generate-maps) 。