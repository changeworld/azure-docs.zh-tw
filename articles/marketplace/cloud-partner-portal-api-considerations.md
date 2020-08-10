---
title: API 考慮-Azure Marketplace
description: 使用 Marketplace API 時的版本設定、錯誤處理與授權問題。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0920bec160874f27c8b1b6f2132951b57719b31c
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035275"
---
# <a name="api-considerations"></a>API 考量

<a name="api-versioning"></a>API 版本控制
--------------

> [!NOTE]
> Cloud Partner 入口網站 Api 會與整合，並會繼續在合作夥伴中心運作。 轉換會引進微小的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md)中所列的變更，以確保您的程式碼在轉換至合作夥伴中心後繼續運作。 只有在轉換至合作夥伴中心之前已整合的現有產品，才應該使用 CPP Api;新產品應使用合作夥伴中心提交 Api。

同時可能會提供多個版本的 API。 用戶端必須透過提供 `api-version` 參數做為查詢字串的一部分，以指出希望叫用的版本。

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

對於具有未知或無效 API 版本之要求的回應是 HTTP 代碼 400。 此錯誤會在回應主體中傳回已知 API 版本集合。

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Errors
------

API 會以對應的 HTTP 狀態碼與選擇性的額外資訊 (在回應中序列化為 JSON) 回應錯誤。
當您收到錯誤時 (特別是 400 類別的錯誤)，解決根本原因之前請勿重試要求。 例如，在上面相同的範例回應中，請在重新傳送要求之前修正 API 版本參數。

<a name="authorization-header"></a>驗證標頭
--------------------

針對此參考中的所有 API，您必須隨著從 Azure Active Directory (Azure AD) 取得的持有人權杖傳遞授權標頭。 需要此標頭才能接收有效的回應；若此標頭不存在，將會傳回 `401 Unauthorized` 錯誤。 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
