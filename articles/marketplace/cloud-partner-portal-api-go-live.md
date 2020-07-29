---
title: 上線 API-Azure Marketplace
description: Go Live API 會起始供應項目即時上市程序。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: d612b796f85c9eaab1600c55cde7e79acb49f352
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292941"
---
# <a name="go-live"></a>Go Live

> [!NOTE]
> Cloud Partner 入口網站 Api 會與整合，並會繼續在合作夥伴中心運作。 轉換會引進微小的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md)中所列的變更，以確保您的程式碼在轉換至合作夥伴中心後繼續運作。 只有在轉換至合作夥伴中心之前已整合的現有產品，才應該使用 CPP Api;新產品應使用合作夥伴中心提交 Api。

此 API 會啟動將應用程式推送至生產環境的程序。 此作業是通常是長時間執行。 此呼叫會使用 [Publish](./cloud-partner-portal-api-publish-offer.md) API 作業中的通知電子郵件清單。

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI 參數
--------------

|  **名稱**      |   **說明**                                                           | **Data type** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 要擷取之供應項目的發行者識別碼，例如 `contoso`       |  String       |
| offerId        | 要擷取之供應項目的供應項目識別碼                                   |  String       |
| api-version    | API 的最新版本                                                   |  Date         |
|  |  |  |

## <a name="header"></a>頁首
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
| 位置    |  要取出此作業狀態的相對路徑            |
|  |  |

### <a name="response-status-codes"></a>回應狀態碼

| **Code** |  **說明**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`：已順利接受要求。 回應會包含用來追蹤作業狀態的位置。 |
|  400     | `Bad/Malformed request` - 在回應本文中找到其他錯誤資訊。 |
|  404     |  `Not found` - 指定的實體不存在。                                       |
|  |  |
