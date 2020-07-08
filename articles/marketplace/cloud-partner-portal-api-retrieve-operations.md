---
title: 取出作業 API-Azure Marketplace
description: API，用來抓取供應專案上的所有作業，或取得指定之 operationId 的特定操作。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: bf980f7c60cd43a4a37a481b96dce737a91d03aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515888"
---
# <a name="retrieve-operations"></a>擷取作業

> [!NOTE]
> Cloud Partner 入口網站 API 已和合作夥伴中心整合，並會在將供應項目移轉到合作夥伴中心後繼續運作。 該整合會引進些微的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md)中所列的變更，以確保您的程式碼會在遷移至合作夥伴中心後繼續運作。

擷取供應項目上的所有作業，或取得指定之 operationId 的特定作業。 用戶端可以使用查詢參數來篩選正在執行的作業。

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


## <a name="uri-parameters"></a>URI 參數

|  **名稱**          |      **描述**                                                                                           | **Data type** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  發行者識別碼，例如 `Contoso`                                                                   |  String       |
|  offerId           |  供應項目識別碼                                                                                              |  String       |
|  operationId       |  可唯一識別供應項目作業的 GUID。 您可以使用此 API 擷取 operationId，並針對任何長時間執行的作業 (例如[發佈供應項目](./cloud-partner-portal-api-publish-offer.md) API)，在回應的 HTTP 標頭中傳回。  |   Guid   |
|  api-version       | API 的最新版本 |    日期      |
|  |  |  |

## <a name="header"></a>Header


|  **名稱**          |  **ReplTest1**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  授權     | `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>本文範例

### <a name="response"></a>回應

#### <a name="get-operations"></a>GET 作業

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>GET 作業

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
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
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>回應主體屬性

|  **名稱**                    |  **描述**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | 可唯一識別作業的 GUID                                                       |
|  submissionType              | 識別針對供應項目報告的作業類型，例如 `Publish/GoLive`      |
|  createdDateTime             | 建立作業的 UTC 日期時間                                                       |
|  lastActionDateTime          | 上次更新作業的 UTC 日期時間                                       |
|  status                      | 作業的狀態，可能是 `not started` \| `running` \| `failed` \| `completed` 。 一次只有一項作業可以有 `running` 狀態。 |
|  錯誤                       | 作業失敗的錯誤訊息                                                               |
|  |  |

### <a name="response-step-properties"></a>回應步驟屬性

|  **名稱**                    |  **描述**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| estimatedTimeFrame | 這項作業的預估持續時間 |
| id | 步驟進程的唯一識別碼 |
| description | 步驟的說明 |
| stepName | 步驟的易記名稱 |
| status | 步驟的狀態，可能是 `notStarted` \| `running` \| `failed` \|`completed` |
| messages | 在步驟期間遇到的任何通知或警告。 字串陣列 |
| System.componentmodel.progresschangedeventargs.progresspercentage | 從0到100的整數，表示步驟的進度 |
| | |

### <a name="response-status-codes"></a>回應狀態碼

| **程式碼**  |   **描述**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` - 已成功處理要求，並傳回要求的作業。        |
|  400      | `Bad/Malformed request` - 錯誤回應本文可能包含更多資訊。                    |
|  403      | `Forbidden` - 用戶端沒有所指定命名空間的存取權。                          |
|  404      | `Not found` - 指定的實體不存在。                                                 |
|  |  |
