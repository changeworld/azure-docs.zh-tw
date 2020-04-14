---
title: 從天氣合作夥伴取得天氣資料
description: 本文介紹如何從合作夥伴獲取天氣數據。
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266158"
---
# <a name="get-weather-data-from-weather-partners"></a>從天氣合作夥伴取得天氣資料

Azure FarmBeats 可説明您使用基於 Docker 的連接器框架從天氣數據提供程式中帶來天氣數據。 使用此框架,天氣數據提供程式實現可與 FarmBeats 整合的 Docker。 目前支援以下天氣資料提供者:

[來自 Azure 開放資料集的 NOAA 資料](https://azure.microsoft.com/services/open-datasets/)

天氣數據可用於生成可操作的見解,並在 FarmBeats 上構建 AI/ML 模型。

## <a name="before-you-start"></a>開始之前

要獲取天氣數據,請確保已安裝 FarmBeats。 **1.2.11 版或更高版本支援天氣整合**。 要安裝 Azure 伺服器場節拍,請參閱[安裝伺服器場節拍](https://aka.ms/farmbeatsinstalldocumentation)。

## <a name="enable-weather-integration-with-farmbeats"></a>使用 FarmBeats 實現天氣整合

要開始獲取 FarmBeats 資料中心的天氣數據,請按照以下步驟操作:

1. 跳到伺服器場資料中心搖曳 (https://yourdatahub.azurewebsites.net/swagger)

2. 導覽/合作夥伴 API,並使用以下輸入負載發出 POST 請求:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
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

   例如,要通過 Azure 開放數據集從 NOAA 獲取天氣數據,請使用下面的有效負載。 您可以根據您的喜好修改名稱和說明。

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > 有關合作夥伴物件的詳細資訊,請參閱[附錄](get-weather-data-from-weather-partner.md#appendix)

   前面的步驟將預配資源,使 Docker 能夠在客戶的 FarmBeats 環境中運行。  

   提供上述資源大約需要 10-15 分鐘。

3. 檢查在步驟 2 中建立的 /Partner 物件的狀態。 為此,請在 /合作夥伴 API 上發出 GET 請求,並檢查合作夥伴物件**的狀態**。 一旦 FarmBets 成功為合作夥伴提供,狀態將設置為 **「活動**」。

4. 導航到 /作業類型 API,並在同一個上發出 GET 請求。 檢查作為步驟 2 中的合作夥伴添加過程的一部分創建的天氣作業。 天氣作業中的**管道名稱**欄位將採用以下格式:「合作夥伴-name_partner-type_job名稱」。

5. 現在,您的 FarmBeats 實例具有活動天氣數據合作夥伴,您可以執行作業以請求特定位置(緯度/經度)和日期範圍的天氣數據。 JobTypes 將詳細介紹運行天氣作業所需的參數。

   例如,對於 Azure 開放資料集中的 NOAA 資料,將建立作業類型以下的數據:

   - get_weather_data(取得 ISD/歷史天氣資料)
   - get_weather_forecast_data(取得 GFS/預測天氣資料)

6. 記下作業類型的**ID**和參數。

7. 導覽到 /jobs API,並在 /Jobs 上使用以下輸入負載發出 POST 請求:

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

   例如,要執行**get_weather_data**,請使用以下有效負載:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. 前面的步驟將運行合作夥伴 Docker 中定義的天氣作業,並將天氣數據引入到 FarmBeats 中。 您可以通過在 /Jobs 上發出 GET 要求來檢查作業的狀態,並在回應中尋找**目前狀態**。 完成後,當前狀態設置為 **"成功**"。

## <a name="query-ingested-weather-data"></a>查詢引入的天氣資料

天氣作業完成後,您可以查詢引入的天氣數據以生成模型或可操作的見解。 有兩種方法可以從 FarmBeats 訪問和查詢天氣數據:

- API 與
- 時間序列見解 (TSI)。

### <a name="query-using-rest-api"></a>使用 REST API 查詢

要使用 FarmBeats REST API 查詢天氣資料,請按照以下步驟操作:

1. 在伺服器場節拍資料中心搖曳器中(,https://yourdatahub.azurewebsites.net/swagger)導航到 /WeatherDataLocation API 並發出 GET 請求)。 回應將具有為作為作業運行的一部分指定的位置(緯度/經度)創建 /WeatherDataDatalocation 物件。 記下對象的**ID**和**天氣數據模型 Id。**

2. 在 /WeatherDataModel API 上為**天氣數據模型 Id**製作 GET/{id},如步驟 1 中所述。 "天氣數據模型"具有有關引入的天氣數據的所有元數據和詳細資訊。 例如,「**天氣數據模型」** 物件中的 **「天氣度量」** 包含有關支援哪些天氣資訊以及支援哪些類型和單位的詳細資訊。 例如，

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

   記下來自天氣資料模型 GET/{id} 調用的回應。

3. 導覽到**遙測**API,並發出具有以下輸入負載的 POST 請求:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. 包含可用於指定時間範圍的天氣數據的回應如下所示:

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

在前面的示例中,回應具有兩個時間戳的數據以及兩個時間戳中的度量值名稱("溫度")和報告的天氣數據的值。 您需要參考關聯的天氣數據模型(如上一步 2 所述),以解釋報告值的類型和單位。

### <a name="query-using-azure-time-series-insights-tsi"></a>使用 Azure 時間序列的見解 (TSI) 查詢

FarmBeats 使用[Azure 時間序列見解 (TSI)](https://azure.microsoft.com/services/time-series-insights/)在 IoT 比例範圍內引入、存儲、查詢和可視化數據,這些資料高度上下文化和針對時間序列進行了優化。

在 EventHub 上接收天氣數據,然後推送到 FarmBeats 資源組中的 TSI 環境。 然後可以直接從 TSI 查詢數據。 有關詳細資訊,請參閱[TSI 文件](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)。

按照以下步驟在 TSI 上可視化資料:

1. 跳到**Azure 門戶** > **FarmBeats DataHub 資源群組**>选择**時間序列與解**環境 (tsi-xxx) >**資料存取原則**。 使用讀取器或參與者訪問許可權添加使用者。

2. 跳到**時間序列的解**環境 (tsi-xxxx)**的概述**頁面,然後選擇**時序見解資源管理員網址**。 您現在可以可視化引入的天氣數據。

除了存儲、查詢和可視化天氣數據外,TSI 還支援整合到 Power BI 儀表板。 有關詳細資訊,請參閱[Power BI 中的時間序列見解可視化資料](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)。

## <a name="appendix"></a>附錄

|        Partner   |  詳細資料   |
| ------- | -------             |
|     Docker 詳細資訊 ─影像名稱         |          Docker 映像名稱。 例如,docker.io/azurefarmbeats/farmbeats-noaa(hub.docker.com中的圖像)或myazureacr.azurecr.io/mydockerimage(Azure 容器註冊表中的圖像)等。 如果未提供註冊表,則hub.docker.com      |
|          Docker 詳細資訊 - 影像標籤             |         標記 docker 映像的名稱。 默認值為"最新"     |
|  Docker 詳細資訊 - 認證      |  訪問專用 Docker 的認證。 這將由合作夥伴提供給客戶   |
|  Docker 詳細資訊 ─ azure BatchVM 詳細資訊 - 批次處理 VMSKU     |    Azure 處理 VM SKU。 有關所有可用的 Linux 虛擬機器,請參閱[此處](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 Valid values are: "Small', 'ExtraLarge', 'Large', 'A8', 'A9', 'Medium', 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'A11', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_G3', 'STANDARD_G4'"STANDARD_G5"，"STANDARD_D5_V2"，"BASIC_A1"， "BASIC_A2"，"BASIC_A3"，"BASIC_A4"，"STANDARD_A1"，"STANDARD_A2"，"STANDARD_A3"，"STANDARD_A4"，"STANDARD_A5"，"STANDARD_A6"，"STANDARD_A7"，"STANDARD_A8"，"STANDARD_A9"，"STANDARD_A10"，"STANDARD_A11"，"STANDARD_D15_V2"，"STANDARD_F1"，"STANDARD_F2"，"STANDARD_F4"，"STANDARD_F8 STANDARD_F16"，"STANDARD_F16"，"STANDARD_NV6"STANDARD_NV24，"STANDARD_NV12"，"STANDARD_NC24r STANDARD_NC24 STANDARD_NC6 STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12"，"STANDARD_NC12,"STANDARD_H8","STANDARD_H8m","STANDARD_H16","STANDARD_H16m","STANDARD_H16mr","STANDARD_H16r","STANDARD_A1_V2","STANDARD_A2_V2 STANDARD_A1_V2","STANDARD_A1_V2","STANDARD_A4_V2","STANDARD_A8_V2","STANDARD_A2m_V2","STANDARD_A4m_V2","STANDARD_A8m_V2","STANDARD_M64ms","STANDARD_M128s","STANDARD_D2_V3"。 **默認值為"standard_d2_v2"**  |
|    Docker 詳細資訊 ─ azureBatchVM 詳細資訊 - 專用電腦節點   |  否。 批次處理池的專用電腦節點。 預設值為 1。 |
|    Docker 詳細資訊 ─ azure BatchVM 詳細資訊 - 節點代理          |    Azure 批次處理節點代理 SKU ID。 目前僅支援「batch.node.ubuntu 18.04」批處理節點代理。    |
| Docker 詳細資訊 - 合作夥伴認證 | 用於在 docker 中調用合作夥伴 API 的認證。 合作夥伴需要根據支援的 auth 機制向客戶提供此資訊。例如。 使用者名/密碼或 API 金鑰。 |
| 合作夥伴類型 | "天氣"(FarmBeats 中的其他合作夥伴類型是"传感器"和"圖像")  |
|  NAME   |   FarmBeats 系統中合作夥伴的所需名稱   |
|  description |  描述   |

## <a name="next-steps"></a>後續步驟

現在,您從 Azure FarmBeats 實例中查詢了感測器數據。 現在,瞭解如何為伺服器場[產生地圖](generate-maps-in-azure-farmbeats.md#generate-maps)。
