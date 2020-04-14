---
title: 檢索產品/服務狀態 |Azure 應用商店
description: API 可擷取供應項目的目前狀態。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 9cf6ca27101a08ff58f32dcd31413256762490a2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255904"
---
# <a name="retrieve-offer-status"></a>擷取供應項目狀態

> [!NOTE]
> 雲合作夥伴門戶 API 與合作夥伴中心集成,在您的產品/服務遷移到合作夥伴中心後將繼續工作。 集成引入了小更改。 查看[雲合作夥伴門戶 API 參考](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)中列出的更改,以確保代碼在遷移到合作夥伴中心後繼續工作。

擷取供應項目的目前狀態。

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 參數

|  **名稱**       |   **說明**                            |  **資料類型** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | 發行者識別碼，例如 `Contoso`  |     String     |
|  offerId        | 可唯一識別供應項目的 GUID      |     String     |
|  api-version    | API 的最新版本                        |     Date       |
|  |  |


## <a name="header"></a>頁首


|  名稱           |  值               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  授權  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>本文範例


### <a name="response"></a>回應

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
  }
```


### <a name="response-body-properties"></a>回應主體屬性

|  **名稱**             |    **說明**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | 供應項目的狀態。 如需可能值清單，請參閱下面的[供應項目狀態](#offer-status)。 |
|  messages             | 與供應項目相關聯的訊息陣列                                                    |
|  steps                | 供應項目發行期間，供應項目所歷經的步驟陣列                      |
|  estimatedTimeFrame   | 完成此步驟要花費的估計時間 (易記格式)                       |
|  id                   | 步驟的識別碼                                                                         |
|  stepName             | 步驟的名稱                                                                               |
|  description          | 步驟的說明                                                                        |
|  status               | 步驟的狀態。 如需可能值清單，請參閱下面的[步驟狀態](#step-status)。    |
|  messages             | 與步驟相關聯的訊息陣列                                                          |
|  processPercentage    | 步驟的完成百分比                                                              |
|  previewLinks         | *目前未實施*                                                                    |
|  liveLinks            | *目前未實施*                                                                    |
|  notificationEmails   | 已棄用的優惠遷移到合作夥伴中心。 遷移優惠的通知電子郵件將發送到「帳戶設置」中的「賣家聯繫資訊」下指定的電子郵件。<br><br>對於非遷移優惠,將通知操作進度的電子郵件地址的逗號分隔清單        |
|  |  |

### <a name="response-status-codes"></a>回應狀態碼

| **程式碼** |   **說明**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` - 已成功處理要求，並已傳回供應項目的最新狀態。 |
|  400     | `Bad/Malformed request` - 錯誤回應本文可能包含更多資訊。                 |
|  404     | `Not found` - 指定的實體不存在。                                                |
|  |  |

### <a name="offer-status"></a>供應項目狀態

|  **名稱**                    |    **說明**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | 供應項目從未發行。                          |
|  NotStarted                  | 供應項目是新的，且未啟動。                            |
|  WaitingForPublisherReview   | 供應項目正在等候發行者核准。                 |
|  執行中                     | 正在處理供應項目提交。                     |
|  成功                   | 已完成處理供應項目提交。               |
|  已取消                    | 已取消供應項目提交。                           |
|  失敗                      | 供應項目提交失敗。                                 |
|  |  |

### <a name="step-status"></a>步驟狀態

|  **名稱**                    |    **說明**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | 步驟尚未啟動。                        |
|  InProgress                  | 步驟正在執行。                             |
|  WaitingForPublisherReview   | 步驟正在等候發行者核准。      |
|  WaitingForApproval          | 步驟正在等候處理核准。        |
|  Blocked                     | 步驟遭到封鎖。                             |
|  已拒絕                    | 步驟遭到拒絕。                            |
|  [完成]                    | 步驟已完成。                            |
|  已取消                    | 步驟已取消。                           |
|  |  |
