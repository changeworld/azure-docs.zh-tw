---
title: 上線 API-Azure Marketplace
description: Go Live API 會起始供應項目即時上市程序。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.openlocfilehash: f356291662851172a3c6917b44891ce901546dfa
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115701"
---
# <a name="go-live"></a>Go Live

> [!NOTE]
> Cloud Partner 入口網站 API 已和合作夥伴中心整合，並會在將供應項目移轉到合作夥伴中心後繼續運作。 該整合會引進些微的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md)中所列的變更，以確保您的程式碼會在遷移至合作夥伴中心後繼續運作。

此 API 會啟動將應用程式推送至生產環境的程序。 此作業是通常是長時間執行。 此呼叫會使用 [Publish](./cloud-partner-portal-api-publish-offer.md) API 作業中的通知電子郵件清單。

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI 參數
--------------

|  **名稱**      |   **描述**                                                           | **Data type** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 要擷取之供應項目的發行者識別碼，例如 `contoso`       |  String       |
| offerId        | 要擷取之供應項目的供應項目識別碼                                   |  String       |
| api-version    | API 的最新版本                                                   |  日期         |
|  |  |  |

## <a name="header"></a>Header
------

|  **名稱**       |     **ReplTest1**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| 授權   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>本文範例

### <a name="response"></a>回應

#### <a name="migrated-offers"></a>已移轉的供應項目

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>未移轉的供應項目

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>回應標頭

|  **名稱**             |      **ReplTest1**                                                            |
|  --------             |      ----------                                                           |
| Location    |  要取出此作業狀態的相對路徑            |
|  |  |

### <a name="response-status-codes"></a>回應狀態碼

| **程式碼** |  **描述**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`：已順利接受要求。 回應會包含用來追蹤作業狀態的位置。 |
|  400     | `Bad/Malformed request` - 在回應本文中找到其他錯誤資訊。 |
|  404     |  `Not found` - 指定的實體不存在。                                       |
|  |  |
