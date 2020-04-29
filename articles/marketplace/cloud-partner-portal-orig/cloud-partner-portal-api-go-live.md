---
title: 上線 |Azure Marketplace
description: Go Live API 會起始供應項目即時上市程序。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ef22f7720a4af2239c55d1a01f9d3f11c878d66e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256309"
---
# <a name="go-live"></a>Go Live

> [!NOTE]
> Cloud Partner 入口網站 Api 會與合作夥伴中心整合，並會在您的供應專案遷移至合作夥伴中心後繼續工作。 整合引進了微小的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)中所列的變更，以確保您的程式碼會在遷移至合作夥伴中心後繼續運作。

此 API 會啟動將應用程式推送至生產環境的程序。 此作業是通常是長時間執行。 此呼叫會使用 [Publish](./cloud-partner-portal-api-publish-offer.md) API 作業中的通知電子郵件清單。

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI 參數
--------------

|  **名稱**      |   **描述**                                                           | **資料類型** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 要擷取之供應項目的發行者識別碼，例如 `contoso`       |  字串       |
| offerId        | 要擷取之供應項目的供應項目識別碼                                   |  字串       |
| api-version    | API 的最新版本                                                   |  Date         |
|  |  |  |

## <a name="header"></a>頁首
------

|  **Name**       |     **ReplTest1**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| 授權   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>本文範例

### <a name="response"></a>回應

#### <a name="migrated-offers"></a>遷移的供應專案

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>非遷移供應專案

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>回應標頭

|  **Name**             |      **ReplTest1**                                                            |
|  --------             |      ----------                                                           |
| 位置    |  要取出此作業狀態的相對路徑            |
|  |  |

### <a name="response-status-codes"></a>回應狀態碼

| **錯誤碼** |  **說明**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`：已順利接受要求。 回應會包含用來追蹤作業狀態的位置。 |
|  400     | `Bad/Malformed request` - 在回應本文中找到其他錯誤資訊。 |
|  404     |  `Not found` - 指定的實體不存在。                                       |
|  |  |
