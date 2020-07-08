---
title: 取出供應專案 API-Azure Marketplace
description: API，用來抓取發行者命名空間底下的供應專案摘要清單。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ee8d0c773a5ec4d362eae66e289838b9646247e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515889"
---
<a name="retrieve-offers"></a>擷取供應項目
===============

> [!NOTE]
> Cloud Partner 入口網站 API 已和合作夥伴中心整合，並會在將供應項目移轉到合作夥伴中心後繼續運作。 該整合會引進些微的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md)中所列的變更，以確保您的程式碼會在遷移至合作夥伴中心後繼續運作。

在發行者命名空間下擷取摘述的供應項目清單。

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>URI 參數
--------------

| **名稱**         |  **描述**                         |  **Data type** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | 發行者識別碼，例如 `contoso` |   String    |
|  api-version     | API 的最新版本                    |    日期        |
|  |  |


<a name="header"></a>Header
------

|  **名稱**        |         **ReplTest1**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  授權   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>本文範例
------------

### <a name="response"></a>回應

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>回應主體屬性

|  **名稱**       |       **描述**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | 指出供應項目類型                                                                                           |
|  publisherId    | 可唯一識別發行者的識別碼                                                                      |
|  status         | 供應項目的狀態。 如需可能值清單，請參閱下面的[供應項目狀態](#offer-status)。                         |
|  id             | 可在發行者命名空間中唯一識別供應項目的 GUID。                                                    |
|  version        | 供應項目的目前版本。 用戶端無法修改版本屬性。 每次發行時，它都會累加。 |
|  定義     | 包含工作負載實際定義的摘述檢視。 若要取得詳細定義，請使用[擷取特定供應項目](./cloud-partner-portal-api-retrieve-specific-offer.md) API。 |
|  changedTime    | 供應項目上次修改時間 (UTC 日期時間)                                                                              |
|  |  |


### <a name="response-status-codes"></a>回應狀態碼

| **程式碼**  |  **描述**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - 已成功處理要求，且發行者下的所有供應項目已傳回給用戶端。  |
|  400      | `Bad/Malformed request` - 錯誤回應本文可能包含更多資訊。                                    |
|  403      | `Forbidden` - 用戶端沒有所指定命名空間的存取權。                                          |
|  404      | `Not found` - 指定的實體不存在。                                                                 |
|  |  |


### <a name="offer-status"></a>供應項目狀態

|  **名稱**                    | **描述**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | 供應項目從未發行。                  |
|  NotStarted                  | 供應項目是新的，但未啟動。                 |
|  WaitingForPublisherReview   | 供應項目正在等候發行者核准。         |
|  執行中                     | 正在處理供應項目提交。             |
|  成功                   | 已完成處理供應項目提交。       |
|  已取消                    | 已取消供應項目提交。                   |
|  失敗                      | 供應項目提交失敗。                         |
|  |  |
