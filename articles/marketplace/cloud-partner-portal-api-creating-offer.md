---
title: 建立或修改供應專案-Azure Marketplace
description: API 來建立新的或更新現有的供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87420221"
---
# <a name="create-or-modify-an-offer"></a>建立或修改供應項目

> [!NOTE]
> Cloud Partner 入口網站 Api 已與整合，並且將繼續在合作夥伴中心中運作。 轉換導入了少量的變更。 請參閱 [CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md) 中所列的變更，以確保您的程式碼在轉換至合作夥伴中心之後仍繼續運作。 CPP Api 應該僅用於已整合的現有產品，然後轉換為合作夥伴中心;新產品應使用合作夥伴中心提交 Api。

此呼叫會更新發行者命名空間內的特定供應項目，或建立新的供應項目。

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 參數

|  **名稱**         |  **描述**                      |  **Data type**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  發行者識別碼，例如 `contoso` |   String |
| offerId           |  供應項目識別碼                     |   String        |
| api-version       |  API 的最新版本            |   Date           |
|  |  |  |

## <a name="header"></a>標頭

|  **名稱**        |  **ReplTest1**               |
|  ---------       |  ----------              | 
| Content-Type     | `application/json`       |
| 授權    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>本文範例

下列範例會建立 offerID 為 `contosovirtualmachine` 的供應項目。

### <a name="request"></a>要求

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>回應

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> 若要修改此供應項目，請在上述要求中加入 **If-Match** 標頭 (設定為 *)。 使用與上述相同的要求主體，但根據需要修改值。 

### <a name="response-status-codes"></a>回應狀態碼

| **程式碼**  |  **描述**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. 已成功處理要求，並已成功修改供應項目。           |
|  201      | `Created`. 已成功處理要求，並已成功建立供應項目。   |
|  400      | `Bad/Malformed request`. 錯誤回應本文可能會提供更多資訊。            |
|  403      | `Forbidden`. 用戶端沒有所要求命名空間的存取權。                     |
|  404      | `Not found`. 用戶端所參考的實體不存在。                           |
|  412      | 伺服器不符合其中一個申請者在要求中指定的先決條件。 用戶端應該檢查與要求一起傳送的 ETAG。 |
|  |  |

## <a name="uploading-artifacts"></a>正在上傳成品

成品 (例如影像和標誌) 應該透過將它們上傳至 Web 上可存取的位置來共用，然後將每個成品包含在 PUT 要求中作為 URI，如上述範例所示。 系統會偵測到這些檔案不存在於 Azure Marketplace 儲存體中，並將這些檔案下載到儲存體中。  因此，您會發現未來的 GET 要求會傳回這些檔案的 Azure Marketplace 服務 URL。

## <a name="categories-and-industries"></a>類別和產業

建立新的供應專案時，您必須在 marketplace 中指定供應專案的類別。 （選擇性）針對某些供應專案類型，您也可以指定產業。 根據供應專案類型，使用下表中的特定索引鍵值，提供適用于供應專案的類別/產業。

### <a name="azure-marketplace-categories"></a>Azure Marketplace 分類

這些類別及其各自的金鑰適用于 Azure 應用程式、虛擬機器、核心虛擬機器、容器、容器應用程式、IoT Edge 模組，以及 SaaS 供應專案類型。 粗體斜體的專案 (像是 ***分析***) 之類的分類和標準文字專案 (例如資料深入解析) 是它們底下的子類別。 使用確切的索引鍵值，而不變更間距或大小寫。

| 類別 | SaaS 金鑰 | Azure App 金鑰 | 虛擬機器、容器、容器應用程式、IoT Edge 模組、核心虛擬機器金鑰 |
| --- | --- | --- | --- |
| ***分析*** | ***分析*** | ***分析-azure-應用程式*** | ***分析-amp*** |
| 資料見解 | 資料-深入解析 | 資料-深入解析 | 資料-深入解析 |
| 資料分析 | 資料分析 | 資料分析 | 資料分析 |
| 巨量資料 | 大型資料 | bigData | 大型資料 |
| 預測性分析 | 預測性分析 | 預測性分析 | 預測性分析 |
| 即時/串流分析 | 即時串流分析 | 即時串流分析 | 即時串流分析 |
| 其他 | 其他 | 其他-分析 | 其他 |
| ***AI + Machine Learning*** | ***ArtificialIntelligence*** | ***ai 和機器學習*** | ***ai 和機器學習*** |
| Bot 服務 | bot-服務 | bot-服務 | bot-服務 |
| 認知服務 | 認知服務 | 認知服務 | 認知服務 |
| ML 服務 | ml-服務 | ml-服務 | ml-服務 |
| 自動化 ML | 自動化-ml | 自動化-ml | 自動化-ml |
| 商務/機器人程式自動化 | 商務機器人-進程-自動化 | 商務機器人-進程-自動化 | 商務機器人-進程-自動化 |
| 資料標示 | 資料標籤 | 資料標籤 | 資料標籤 |
| 資料準備 | 資料準備 | 資料準備 | 資料準備 |
| 知識挖掘 | 知識-挖掘 | 知識-挖掘 | 知識-挖掘 |
| ML 作業 | ml-作業 | ml-作業 | ml-作業 |
| 其他 | 其他-AI 和機器學習 | 其他 | 其他 |
| ***區塊鏈*** | ***區塊鏈*** | ***區塊鏈*** | ***區塊鏈*** |
| 應用程式加速器 | 應用程式加速器 | 應用程式加速器 | 應用程式加速器 |
| 單一節點總帳 | 單一節點-總帳 | 單一節點-總帳 | 單一節點-總帳 |
| 多節點總帳 | 多節點-總帳 | 多節點-總帳 | 多節點-總帳 |
| 工具 | tools | tools | tools |
| 其他 | 其他 | 其他 | 其他 |
| ***計算*** | ***計算-saas*** | ***計算-azure-應用程式*** | ***計算*** |
| 應用程式基礎結構 | appInfra | appInfrastructure | 應用程式基礎結構 |
| 作業系統 | clientOS | clientOS | 作業系統 |
| 快取 | 快取 | 快取 | 快取 |
| 其他 | 其他-計算 | 其他-計算 | 其他 |
| ***容器*** | ***容器*** | ***容器*** | ***容器*** |
| 容器應用程式 | 容器-應用程式 | 容器-應用程式 | 容器-應用程式 |
| 容器映像 | 容器-映射 | 容器-映射 | 容器-映射 |
| 使用容器開始 | 開始使用-容器 | 開始使用-容器 | 開始使用-容器 |
| 其他 | 其他 | 其他 | 其他 |
| ***資料庫*** | ***資料庫-saas*** | ***database*** | ***資料庫*** |
| NoSQL 資料庫 | nosql 資料庫 | nosql 資料庫 | nosql 資料庫 |
| 關聯式資料庫 | 關聯式資料庫 | 關聯式資料庫 | 關聯式資料庫 |
| 總帳/區塊鏈資料庫 | 總帳-區塊鏈-資料庫 | 總帳-區塊鏈-資料庫 | 總帳-區塊鏈-資料庫 |
| 資料 Lake | 資料-lake | 資料-lake | 資料-lake |
| 資料倉儲 (data warehouse) | 資料倉儲 | 資料倉儲 | 資料倉儲 |
| 其他 | 其他-資料庫 | 其他-資料庫 | 其他 |
| ***開發人員工具*** | ***開發人員-工具-saas*** | ***開發人員-工具-azure-應用程式*** | ***開發人員工具*** |
| 工具 | 工具-開發人員-工具 | 工具-開發人員-工具 | 工具-開發人員-工具 |
| 指令碼 | 指令碼 | 指令碼 | 指令碼 |
| 開發人員服務 | devService | devService | 開發人員-服務 |
| 其他 | 其他開發人員工具 | 其他開發人員工具 | 其他 |
| ***DevOps*** | ***devops*** | ***devops*** | ***devops*** |
| 其他 | 其他 | 其他 | 其他 |
| ***身分識別*** | ***身分識別*** | ***身分識別*** | ***身分識別*** |
| 存取管理 | 存取-管理 | 存取-管理 | 存取-管理 |
| 其他 | 其他 | 其他 | 其他 |
| ***整合*** | ***集成*** | ***集成*** | ***集成*** |
| Messaging (傳訊) | 傳訊 | 傳訊 | 傳訊 |
| 其他 | 其他 | 其他 | 其他 |
| ***物聯網*** | ***IoT*** | ***物聯網-azure-應用程式*** | ***物聯網*** |
| IoT 核心服務 | N/A | iot-核心-服務 | iot-核心-服務 |
| IoT Edge 模組 | N/A | iot edge 模組 | iot edge 模組 |
| IoT 解決方案 | iot-解決方案 | iot-解決方案 | iot-解決方案 |
| 資料分析 & 視覺效果 | 資料分析與視覺效果 | 資料分析與視覺效果 | 資料分析與視覺效果 |
| IoT 連線能力 | iot 連線能力 | iot 連線能力 | iot 連線能力 |
| 其他 | 其他-物聯網 | 其他-物聯網 | 其他 |
| ***IT & 管理工具*** | ***ITandAdministration*** | ***it 與管理工具-azure-應用程式*** | ***it 與管理工具*** |
| 管理解決方案 | 管理-解決方案 | 管理-解決方案 | 管理-解決方案 |
| 商務應用程式 | businessApplication | businessApplication | 商務應用程式 |
| 其他 | 其他-管理工具 | 其他-管理工具 | 其他 |
| ***監視 & 診斷*** | ***監視和診斷*** | ***監視和診斷*** | ***監視和診斷*** |
| 其他 | 其他 | 其他 | 其他 |
| ***媒體*** | ***media*** | ***media*** | ***media*** |
| 媒體服務 | media-services | media-services | media-services |
| 內容保護 | content-protection | content-protection | content-protection |
| 即時 & 隨選串流 | 即時和隨選串流 | 即時和隨選串流 | 即時和隨選串流 |
| 其他 | 其他 | 其他 | 其他 |
| ***移轉*** | ***遷移*** | ***遷移*** | ***遷移*** |
| 資料移轉 | 資料移轉 | 資料移轉 | 資料移轉 |
| 其他 | 其他 | 其他 | 其他 |
| ***混合現實*** | ***mixed-reality*** | ***mixed-reality*** | ***mixed-reality*** |
| 其他 | 其他 | 其他 | 其他 |
| ***網路功能*** | ***網路*** | ***網路*** | ***網路*** |
| 裝置管理員 | 裝置管理員 | 裝置管理員 | 裝置管理員 |
| 連線能力 | 連線能力 | 連線能力 | 連線能力 |
| 防火牆 | 防火牆 | 防火牆 | 防火牆 |
| 負載平衡器 | 負載平衡器 | 負載平衡器 | 負載平衡器 |
| 其他 | 其他 | 其他 | 其他 |
| ***安全性*** | ***安全*** | ***安全*** | ***安全*** |
| 身分識別與存取管理 | 身分識別和存取管理 | 身分識別和存取管理 | 身分識別和存取管理 |
| 威脅防護 | 威脅防護 | 威脅防護 | 威脅防護 |
| 資訊保護 | 資訊保護 | 資訊保護 | 資訊保護 |
| 其他 | 其他 | 其他 | 其他 |
| ***Storage*** | ***儲存體-saas*** | ***儲存體-azure-應用程式*** | ***儲存體*** |
| 備份 & 復原 | 備份 (backup) | 備份 (backup) | 備份與修復 |
| 企業混合式儲存體 | 企業混合式儲存體 | 企業混合式儲存體 | 企業混合式儲存體 |
| 檔案共用 | 檔案共用 | 檔案共用 | 檔案共用 |
| 資料生命週期管理 | 資料生命週期-管理 | 資料生命週期-管理 | 資料生命週期-管理 |
| 其他 | 其他-儲存體 | 其他-儲存體 | 其他 |
| ***網路*** | ***Web*** | ***Web*** | ***Web*** |
| Cms 的 blog & | blog 和-cms | blog 和-cms | blog 和-cms |
| 入門 Web Apps | 入門-web-應用程式 | 入門-web-應用程式 | 入門-web-應用程式 |
| 電子商務 | 電子商務 | 電子商務 | 電子商務 |
| Web Apps 架構 | web 應用程式-架構 | web 應用程式-架構 | web 應用程式-架構 |
| Web Apps | web 應用程式 | web 應用程式 | web 應用程式 |
| 其他 | 其他 | 其他 | 其他 |
||||

### <a name="microsoft-appsource-categories"></a>Microsoft AppSource 分類

這些類別及其各自的金鑰適用于 SaaS、PowerBI 應用程式、Dynamics 365 business central、Dynamics 365 for customer engagement 和 Dynamics 365 for operation 供應專案類型。 以粗體斜體 (的專案，像是 ***分析***) 之類的類別目錄和標準文字專案 (例如高階分析) 是它們底下的子類別。 使用確切的索引鍵值，而不變更間距或大小寫。

| 類別 | SaaS 金鑰 | Dynamics 365 business central、Dynamics 365 for customer engagement、Dynamics 365 for operation 金鑰 | PowerBI 應用程式金鑰 |
| --- | --- | --- | --- |
| ***分析*** | ***分析*** | ***分析*** | ***分析*** |
| 進階分析 | advanced analytics | advanced analytics | advanced analytics |
| & 報表的視覺效果 | 視覺效果-報告 | 視覺效果-報告 | 視覺效果-報告 |
| 其他 | 其他 | 其他-分析 | 其他-分析 |
| ***AI + Machine Learning*** | ***ArtificialIntelligence*** | ***ai-plus-機器學習服務-dynamics*** | ***ai-plus-機器學習-appsource*** |
| 商務用 AI | 商務用 ai | 商務用 ai | 商務用 ai |
| Bot 應用程式 | bot-應用程式 | bot-應用程式 | bot-應用程式 |
| 其他 | 其他-ai 和機器學習 | 其他-ai 和機器學習 | 其他-ai 和機器學習 |
| ***共同作業*** | ***共同作業*** | ***共同作業*** | ***協作*** |
| 聯絡 & 人員 | 連絡人-人員 | 連絡人-人員 | 連絡人與人員 |
| 會議管理 | 會議管理 | 會議管理 | 會議管理 |
| 網站設計 & 管理 | 網站設計-管理 | 網站設計-管理 | 網站設計與管理 |
| 工作 & 專案管理 | 工作-專案管理 | 工作-專案管理 | 工作和專案管理 |
| 語音 & 視訊會議 | 語音-視頻-會議 | 語音-視頻-會議 | 語音和影片-會議 |
| 其他 | 其他-協同作業 | 其他-協同作業 | 其他 |
| ***法規遵循 & 法律*** | ***合 規*** | ***合 規*** | ***合規性和法律*** |
| 稅金 & 審核 | 稅金-audit | 稅金-audit | 稅務和審核 |
| Legal | Legal | Legal | legal |
| 資料，治理 & 隱私權 | 資料管理-隱私權 | 資料管理-隱私權 | 資料治理和隱私權 |
| 健全狀況 & 安全性 | 健全狀況-安全 | 健全狀況-安全 | 健全狀況和安全性 |
| 其他 | 其他合規性-合法 | 其他合規性-合法 | 其他 |
| ***Customer Service*** | ***CustomerService*** | ***CustomerService*** | ***客戶-服務*** |
| 連絡人中心 | 連絡人-中心 | 連絡人-中心 | 連絡人-中心 |
| 臉部至臉部服務 | 臉部對臉部-服務 | 臉部對臉部-服務 | 臉部對臉部-服務 |
| 返回 Office & 員工服務 | 返回辦公室-員工-服務 | 返回辦公室-員工-服務 | 返回辦公室和員工服務 |
| 知識 & 案例管理 | 知識-案例管理 | 知識-案例管理 | 知識和案例管理 |
| 社交媒體 & Omnichannel contact Engagement | 社交媒體-omnichannel contact-參與 | 社交媒體-omnichannel contact-參與 | 社交媒體與 omnichannel contact-參與 |
| 其他 | 其他-客戶-服務 | 其他-客戶-服務 | 其他 |
| ***財務*** | ***財務*** | ***財務*** | ***金融*** |
| 會計 | 會計 | 會計 | 會計 |
| 資產管理 | 資產管理 | 資產管理 | 資產管理 |
| 分析，匯總 & 報告 | 分析-匯總-報告 | 分析-匯總-報告 | 分析-合併和報告 |
| 點數 & 集合 | 點數-集合 | 點數-集合 | 點數和集合 |
| 合規性 & 風險管理 | 合規性-風險管理 | 合規性-風險管理 | 合規性和風險管理 |
| 其他 | 其他-財務 | 其他-財務 | 其他 |
| ***Human Resources*** | ***HumanResources*** | ***HumanResources*** | ***人力資源*** |
| 人才收購 | 人才收購 | 人才收購 | 人才收購 |
| 人才管理 | 人才管理 | 人才管理 | 人才管理 |
| HR 作業 | hr-作業 | hr-作業 | hr-作業 |
| 員工規劃 & 分析 | 員工-規劃-分析 | 員工-規劃-分析 | 員工-規劃與分析 |
| 其他 | 其他人-資源 | 其他人-資源 | 其他 |
| ***物聯網*** | ***IoT*** | ***物聯網-dynamics*** | ***物聯網-appsource*** |
| 資產管理 & 作業 | 資產管理-作業 | 資產管理-作業 | 資產管理和作業 |
| 連接的產品 | 連接-產品 | 連接-產品 | 連接-產品 |
| 智慧供應鏈 | 智慧供應鏈 | 智慧供應鏈 | 智慧供應鏈 |
| 預測性維護 | 預測性-維護 | 預測性-維護 | 預測性-維護 |
| 遠端監視 | 遠端監視 | 遠端監視 | 遠端監視 |
| 安全性 & 安全性 | 安全-安全性 | 安全-安全性 | 安全性和安全性 |
| 智慧型基礎結構 & 資源 | 智慧型基礎結構-資源 | 智慧型基礎結構-資源 | 智慧型基礎結構和資源 |
| 車輛 & 行動性 | 車輛-行動性 | 車輛-行動性 | 車輛和行動性 |
| 其他 | 其他-物聯網 | 其他-物聯網 | 其他 |
| ***IT & 管理工具*** | ***ITandAdministration*** | ***ITandAdministration*** | ***it 與管理工具*** |
| 管理解決方案 | 管理-解決方案 | 管理-解決方案 | 管理-解決方案 |
| 商務應用程式 | businessApplication | businessApplication | 商務應用程式 |
| 其他 | 其他-管理工具 | 其他-管理工具 | 其他 |
| ***行銷*** | ***行銷*** | ***行銷*** | ***行銷*** |
| 廣告 | 廣告 | 廣告 | 廣告 |
| 分析 | 分析-行銷 | 分析-行銷 | 分析-行銷 |
| 行銷活動管理 & 自動化 | 活動-管理-自動化 | 活動-管理-自動化 | 行銷活動-管理與自動化 |
| 電子郵件行銷 | 電子郵件-行銷 | 電子郵件-行銷 | 電子郵件-行銷 |
| L2-事件 & 資源管理 | 事件-資源管理 | 事件-資源管理 | 事件與資源管理 |
| 研究 & 分析 | 研究分析 | 研究分析 | 研究與分析 |
| 社群媒體 | 社交媒體 | 社交媒體 | 社交媒體 |
| 其他 | 其他-行銷 | 其他-行銷 | 其他 |
| ***& 供應鏈的作業*** | ***OperationsSupplyChain*** | ***OperationsSupplyChain*** | ***營運和供應鏈*** |
| 資產 & 生產管理 | 資產-生產-管理 | 資產-生產-管理 | 資產和生產管理 |
| 需求預測 | 需求預測 | 需求預測 | 需求預測 |
| 資訊管理 & 連線能力 | 資訊管理-連線能力 | 資訊管理-連線能力 | 資訊管理與連線能力 |
| 規劃、購買 & 報告 | 規劃-購買-報告 | 規劃-購買-報告 | 規劃-購買和報告 |
| 品質 & 服務管理 | 品質-服務管理 | 品質-服務管理 | 品質和服務管理 |
| 銷售 & 訂單管理 | 銷售-訂單-管理 | 銷售-訂單-管理 | 銷售與訂單管理 |
| 運輸 & 倉儲管理 | 運輸-倉儲-管理 | 運輸-倉儲-管理 | 運輸和倉儲管理 |
| 其他 | 其他-操作供應鏈 | 其他-操作供應鏈 | 其他 |
| ***生產力*** | ***生產力*** | ***生產力*** | ***生產力*** |
| & 管理的內容建立 | 內容-建立-管理 | 內容-建立-管理 | 內容建立與管理 |
| Language & 翻譯 | 語言翻譯 | 語言翻譯 | 語言和翻譯 |
| 文件管理 | 檔管理 | 檔管理 | 檔管理 |
| 電子郵件管理 | 電子郵件-管理 | 電子郵件-管理 | 電子郵件-管理 |
| 搜尋 & 參考 | 搜尋參考 | 搜尋參考 | 搜尋和參考 |
| 其他 | 其他-生產力 | 其他-生產力 | 其他 |
| 遊戲化 | 遊戲化 | 遊戲化 | 遊戲化 |
| ***Sales*** | ***Sales*** | ***Sales*** | ***Sales*** |
| 電話 | 電話 | 電話 | 電話 |
| 設定、價格、報價 (CPQ)  | 設定-價格-報價 | 設定-價格-報價 | 設定-價格-報價 |
| 合約管理 | 合約-管理 | 合約-管理 | 合約-管理 |
| CRM | crm | crm | crm |
| 電子商務 | 電子商務 | 電子商務 | 電子商務 |
| 商務資料擴充 | 商務資料-擴充 | 商務資料-擴充 | 商務資料-擴充 |
| 銷售支援 | 銷售-啟用 | 銷售-啟用 | 銷售-啟用 |
| 其他 | 其他-銷售 | 其他-銷售 | 其他-銷售 |
| ***地理位置*** | ***地理位置*** | ***地理位置*** | ***地理位置*** |
| 地圖 | maps | maps | maps |
| 新聞 & 天氣 | 新聞和氣象 | 新聞和氣象 | 新聞和氣象 |
| 其他 | 其他-地理位置 | 其他-地理位置 | 其他-地理位置 |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource 產業

這些產業和其各自的金鑰適用于 SaaS、PowerBI 應用程式、Dynamics 365 business central、Dynamics 365 for customer engagement 和 Dynamics 365 for operation 供應專案類型。 粗體斜體的專案 (例如 ***汽車***) 是類別目錄和標準文字專案 (例如 AutomotiveL2) 是其下的子類別。 使用確切的索引鍵值，而不變更間距或大小寫。

| 產業 | SaaS、Dynamics 365 business central、Dynamics 365 for customer engagement、Dynamics 365 for operation keys | PowerBI 應用程式金鑰 |
| --- | --- | --- |
| ***汽車*** | ***汽車*** | ***汽車*** |
| 汽車 | AutomotiveL2 | AutomotiveL2 |
| ***農業*** | ***農業*** | ***農業*** |
| 其他-Unsegmented | 農業 \_ OtherUnsegmented | 其他-unsegmented |
| ***Distribution*** | ***Distribution*** | ***分佈*** |
| 批發 | 批發 | 批發 |
| 包裹 & 套件寄送 | ParcelAndPackageShipping | 包裹和包裹運送 |
| ***Education*** | ***Education*** | ***教育*** |
| 高等教育 | HigherEducation | 高等教育 |
| 主要 & 次要教育/K-12 | PrimaryAndSecondaryEducationK12 | 主要與次要教育 |
| & 博物館的程式庫 | LibrariesAndMuseums | 程式庫與博物館 |
| ***金融服務*** | ***FinancialServices*** | ***金融服務*** |
| 銀行 & 資本市場 | BankingAndCapitalMarkets | 銀行與資本市場 |
| Insurance | Insurance | 保險 |
| ***政府*** | ***政府*** | ***政府*** |
| 防禦 & 情報 | DefenseAndIntelligence | 防禦與情報 |
| 公共安全 & 司法 | PublicSafetyAndJustice | 公用-安全與司法 |
| 文職政府 | CivilianGovernment | 文職-政府 |
| ***醫療保健*** | ***HealthCareandLifeSciences*** | ***醫療*** |
| 健全狀況醫療給付 | HealthPayor | 健全狀況-醫療給付 |
| 健康狀態提供者 | HealthProvider | 健全狀況-提供者 |
| Pharmaceuticals | Pharmaceuticals | 製藥 |
| ***製造 & 資源*** | ***製造業*** | ***製造和資源*** |
| 化學 & Agrochemical | ChemicalAndAgrochemical | 化學和-agrochemical |
| 離散製造 | DiscreteManufacturing | 離散-製造業 |
| 能源 | 能源 | energy |
| ***零售 & 消費性商品*** | ***RetailandConsumerGoods*** | ***零售與消費性產品*** |
| 消費品 | ConsumerGoods | 消費性商品 |
| 零售商 | 零售商 | 零售商 |
| ***媒體 & 通訊*** | ***MediaAndCommunications*** | ***媒體和通訊*** |
| 媒體 & 娛樂 | MediaandEntertainment | 媒體和娛樂 |
| 電信業 | 電信業 | 電信 |
| ***專業服務*** | ***ProfessionalServices*** | ***專業服務*** |
| Legal | Legal | legal |
| 合作夥伴專業服務 | PartnerProfessionalServices | 合作夥伴-專業-服務 |
| ***架構 & 結構*** | ***ArchitectureAndConstruction*** | ***架構和結構*** |
| 其他-Unsegmented | ArchitectureAndConstruction \_ OtherUnsegmented | 其他-unsegmented |
| ***旅遊 & 旅遊*** | ***HospitalityandTravel*** | ***旅遊和旅遊*** |
|    飯店 & 的休閒 | HotelsAndLeisure | 飯店和休閒 |
| 旅遊和運輸 | TravelAndTransportation | 旅遊與運輸 |
| 餐廳 & 食物服務 | RestaurantsAndFoodServices | 餐廳和食物-服務 |
| ***其他公共事業產業*** | ***OtherPublicSectorIndustries*** | ***其他公共磁區-產業*** |
| 林業 & 釣魚 | ForestryAndFishing | 林業和釣魚 |
| 非營利組織 | 非營利組織 | 非營利組織 |
| ***房地產*** | ***房地產*** | ***房地產*** |
| 其他-Unsegmented | RealEstate \_ OtherUnsegmented | 其他-unsegmented |
|||
