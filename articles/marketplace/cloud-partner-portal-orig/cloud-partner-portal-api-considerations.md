---
title: API 注意事項 |Azure 應用商店
description: 使用 Marketplace API 時的版本設定、錯誤處理與授權問題。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256326"
---
# <a name="api-considerations"></a>API 考量

<a name="api-versioning"></a>API 版本控制
--------------

> [!NOTE]
> 雲合作夥伴門戶 API 與合作夥伴中心集成,在您的產品/服務遷移到合作夥伴中心後將繼續工作。 集成引入了小更改。 查看[雲合作夥伴門戶 API 參考](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)中列出的更改,以確保代碼在遷移到合作夥伴中心後繼續工作。

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
