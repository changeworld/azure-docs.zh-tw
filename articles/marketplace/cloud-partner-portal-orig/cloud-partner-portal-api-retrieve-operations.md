---
title: 檢索操作 API |Azure 應用商店
description: 擷取供應項目上的所有作業，或取得指定之 operationId 的特定作業。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 4fc77407ae1c5854d3fe977da5a81f4226bf5305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280468"
---
<a name="retrieve-operations"></a>擷取作業
===================

擷取供應項目上的所有作業，或取得指定之 operationId 的特定作業。 用戶端可以使用查詢參數來篩選正在執行的作業。

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI 參數
--------------

|  **名稱**          |      **描述**                                                                                           | **資料類型** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  發行者識別碼，例如 `Contoso`                                                                   |  String       |
|  offerId           |  供應項目識別碼                                                                                              |  String       |
|  operationId       |  可唯一識別供應項目作業的 GUID。 您可以使用此 API 擷取 operationId，並針對任何長時間執行的作業 (例如[發佈供應項目](./cloud-partner-portal-api-publish-offer.md) API)，在回應的 HTTP 標頭中傳回。  |   Guid   |
|  filteredStatus    | 選用的查詢參數，用來依狀態 (例如 `running`) 篩選此 API 所傳回的集合。  |   String |
|  api-version       | API 的最新版本                                                                                           |    Date      |
|  |  |  |


<a name="header"></a>頁首
------

|  **名稱**          |  **價值**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  授權     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>本文範例
------------

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
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>回應主體屬性

|  **名稱**                    |  **描述**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | 可唯一識別作業的 GUID                                                       |
|  submissionType              | 識別針對供應項目報告的作業類型，例如 `Publish/GGoLive`      |
|  createdDateTime             | 建立作業的 UTC 日期時間                                                       |
|  lastActionDateTime          | 上次更新作業的 UTC 日期時間                                       |
|  status                      | `not started` \| `running` \|操作的狀態`failed`。 \| `completed` 一次只有一項作業可以有 `running` 狀態。 |
|  error                       | 作業失敗的錯誤訊息                                                               |
|  |  |


### <a name="response-status-codes"></a>回應狀態碼

| **代碼**  |   **描述**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` - 已成功處理要求，並傳回要求的作業。        |
|  400      | `Bad/Malformed request` - 錯誤回應本文可能包含更多資訊。                    |
|  403      | `Forbidden` - 用戶端沒有所指定命名空間的存取權。                          |
|  404      | `Not found` - 指定的實體不存在。                                                 |
|  |  |
