---
title: 發行供應專案-Azure Marketplace
description: 用來發佈指定供應項目的 API。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ada8725a8963fc14fd326c244dcf3d5bb3dd372f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515893"
---
# <a name="publish-an-offer"></a>發佈供應項目

> [!NOTE]
> Cloud Partner 入口網站 API 已和合作夥伴中心整合，並會在將供應項目移轉到合作夥伴中心後繼續運作。 該整合會引進些微的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md)中所列的變更，以確保您的程式碼會在遷移至合作夥伴中心後繼續運作。

啟動指定供應項目的發佈程序。 此呼叫是會長時間執行的作業。

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 參數
--------------

|  **名稱**      |    **描述**                               |  **Data type** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | 發行者識別碼，例如 `contoso`      |   String       |
|  offerId       | 供應項目識別碼                                 |   String       |
|  api-version   | API 的最新版本                        |   日期         |
|  |  |

## <a name="header"></a>Header
------

|  **名稱**        |    **ReplTest1**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  授權   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>本文範例
------------

### <a name="request"></a>要求

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>要求本文屬性

|  **名稱**               |   **描述**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | 以逗號分隔的電子郵件地址清單；系統會向這些地址傳送發佈作業進度通知。 |
|  |  |


### <a name="response"></a>回應

#### <a name="migrated-offers"></a>已移轉的供應項目

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>未移轉的供應項目

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>回應標頭

|  **名稱**             |    **ReplTest1**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| 位置    | 要取出此作業狀態的相對路徑     |
|  |  |


### <a name="response-status-codes"></a>回應狀態碼

| **程式碼** |  **描述**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`：已順利接受要求。 回應包含可用來追蹤已啟動之作業的位置。 |
| 400   | `Bad/Malformed request`：錯誤回應本文可能會提供更多資訊。                                                               |
| 422   | `Un-processable entity`：表示要發佈的實體無法通過驗證。                                                        |
| 404   | `Not found`：用戶端所指定的實體不存在。                                                                              |
|  |  |
