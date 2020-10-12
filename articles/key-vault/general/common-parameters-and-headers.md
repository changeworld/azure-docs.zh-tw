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
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83005806"
---
# <a name="common-parameters-and-headers"></a>通用參數和標頭

下列資訊通用於針對相關 Key Vault 資源執行的所有作業：

- HTTP `Host` 標頭必須一律存在，而且必須指定保存庫主機名稱。 範例： `Host: contoso.vault.azure.net`. 請注意，大部分的用戶端技術都會填入 `Host` URI 的標頭。 例如， `GET https://contoso.vault.azure.net/secrets/mysecret{...}` 會將設定 `Host` 為 `contoso.vault.azure.net` 。 這表示，如果您使用原始 IP 位址存取 Key Vault `GET https://10.0.0.23/secrets/mysecret{...}` ，標頭的自動值 `Host` 將會錯誤，而且您必須手動確認 `Host` 標頭包含保存庫主機名稱。
- 將 `{api-version}` 取代為 URI 中的 api-version。
- 將 `{subscription-id}` 取代為 URI 中的訂用帳戶識別碼
- 將 `{resource-group-name}` 取代為資源群組。 如需詳細資訊，請參閱＜使用資源群組管理 Azure 資源＞。
- 將 `{vault-name}` 取代為 URI 中您的金鑰保存庫名稱。
- 將 Content-Type 標頭設定為 application/json。
- 將 Authorization 標頭設定為您從 Azure Active Directory (AAD) 取得的 JSON Web 權杖。 如需詳細資訊，請參閱 [驗證 Azure Resource Manager](authentication-requests-and-responses.md) 要求。

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

|元素名稱 | 類型 | 說明 |
|---|---|---|
| code | 字串 | 所發生錯誤的類型。|
| message | 字串 | 造成錯誤之原因的描述。 |



## <a name="see-also"></a>另請參閱
 [Azure Key Vault REST API 參考](/rest/api/keyvault/)
