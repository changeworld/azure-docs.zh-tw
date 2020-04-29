---
title: 取消作業 API |Azure Marketplace
description: 取消作業。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256428"
---
# <a name="cancel-operation"></a>取消作業

> [!NOTE]
> Cloud Partner 入口網站 Api 會與合作夥伴中心整合，並會在您的供應專案遷移至合作夥伴中心後繼續工作。 整合引進了微小的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)中所列的變更，以確保您的程式碼會在遷移至合作夥伴中心後繼續運作。

此 API 會取消目前正在供應項目上進行的作業。 使用[擷取作業 API](./cloud-partner-portal-api-retrieve-operations.md) 來取得要傳遞至此 API 的 `operationId`。 取消通常是一個同步作業，但在某些複雜的案例中，新的作業可能需要取消現有作業。 在此情況下，HTTP 回應本文會包含應用來查詢狀態的作業位置。

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 參數

--------------

|  **名稱**    |      **描述**                                  |    **資料類型**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  發行者識別碼，例如 `contoso`         |   字串          |
| offerId      |  供應項目識別碼                                     |   字串          |
| api-version  |  API 目前的版本                               |    Date           |
|  |  |  |

## <a name="header"></a>頁首
------

|  **Name**              |  **ReplTest1**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  授權         |  Bearer 您的權杖 |
|  |  |

## <a name="body-example"></a>本文範例
------------

### <a name="request"></a>要求

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>要求本文屬性

|  **名稱**                |  **描述**                                               |
|  --------                |  ---------------                                               |
|  notification-emails     | 以逗號分隔的電子郵件識別碼清單，這些電子郵件識別碼將會收到關於發行作業進度的通知。 |
|  |  |

### <a name="response"></a>回應

#### <a name="migrated-offers"></a>遷移的供應專案

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>非遷移供應專案

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>回應標頭

|  **Name**             |    **ReplTest1**                       |
|  ---------            |    ----------                      |
| 位置    | 要取出此作業狀態的相對路徑。 |
|  |  |

### <a name="response-status-codes"></a>回應狀態碼

| **錯誤碼**  |  **說明**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | 正常。 已成功處理要求，並同步取消作業。 |
|  202      | 已接受。 已成功處理要求，而且正在取消該作業。 取消作業的位置會在回應標頭中傳回。 |
|  400      | 錯誤/格式錯誤的要求。 錯誤回應本文可能會提供更多資訊。  |
|  403      | 禁止存取。 用戶端無權存取要求中指定的命名空間。 |
|  404      | 找不到。 指定的實體不存在。 |
|  |  |
