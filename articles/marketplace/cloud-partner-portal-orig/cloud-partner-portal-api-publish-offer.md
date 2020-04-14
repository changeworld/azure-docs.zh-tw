---
title: 發佈產品/服務 |Azure 應用商店
description: 用來發佈指定供應項目的 API。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255935"
---
# <a name="publish-an-offer"></a>發佈供應項目

> [!NOTE]
> 雲合作夥伴門戶 API 與合作夥伴中心集成,在您的產品/服務遷移到合作夥伴中心後將繼續工作。 集成引入了小更改。 查看[雲合作夥伴門戶 API 參考](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)中列出的更改,以確保代碼在遷移到合作夥伴中心後繼續工作。

啟動指定供應項目的發佈程序。 此呼叫是會長時間執行的作業。

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 參數
--------------

|  **名稱**      |    **說明**                               |  **資料類型** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | 發行者識別碼，例如 `contoso`      |   String       |
|  offerId       | 供應項目識別碼                                 |   String       |
|  api-version   | API 的最新版本                        |   Date         |
|  |  |

## <a name="header"></a>頁首
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

|  **名稱**               |   **說明**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | 以逗號分隔的電子郵件地址清單；系統會向這些地址傳送發佈作業進度通知。 |
|  |  |


### <a name="response"></a>回應

#### <a name="migrated-offers"></a>遷移優惠

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>非遷移優惠

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>回應標頭

|  **名稱**             |    **ReplTest1**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Location    | 檢索此操作狀態的相對路徑     |
|  |  |


### <a name="response-status-codes"></a>回應狀態碼

| **程式碼** |  **說明**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`：已順利接受要求。 回應包含可用來追蹤已啟動之作業的位置。 |
| 400   | `Bad/Malformed request`：錯誤回應本文可能會提供更多資訊。                                                               |
| 422   | `Un-processable entity`：表示要發佈的實體無法通過驗證。                                                        |
| 404   | `Not found`：用戶端所指定的實體不存在。                                                                              |
|  |  |
