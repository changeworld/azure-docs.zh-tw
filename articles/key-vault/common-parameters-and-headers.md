---
title: 通用參數和標頭
description: 參數和標頭通用於針對相關 Key Vault 資源執行的所有作業。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197584"
---
# <a name="common-parameters-and-headers"></a>通用參數和標頭

下列資訊通用於針對相關 Key Vault 資源執行的所有作業：

- 將 `{api-version}` 取代為 URI 中的 api-version。
- 將 `{subscription-id}` 取代為 URI 中的訂用帳戶識別碼
- 將 `{resource-group-name}` 取代為資源群組。 如需詳細資訊，請參閱使用資源群組來管理您的 Azure 資源
- 將 `{vault-name}` 取代為 URI 中您的金鑰保存庫名稱。
- 將 Content-Type 標頭設定為 application/json。
- 將 Authorization 標頭設定為您從 Azure Active Directory (AAD) 取得的 JSON Web 權杖。 如需詳細資訊，請參閱[驗證 Azure Resource Manager 要求](authentication-requests-and-responses.md)。

## <a name="common-error-response"></a>通用錯誤回應
服務將使用 HTTP 狀態碼指出成功或失敗。 此外，失敗包含下列格式的回應：

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|元素名稱 | 類型 | 描述 |
|---|---|---|
| 代碼 | string | 所發生錯誤的類型。|
| message | string | 造成錯誤之原因的描述。 |



## <a name="see-also"></a>另請參閱
 [Azure Key Vault REST API 參考](/rest/api/keyvault/)
