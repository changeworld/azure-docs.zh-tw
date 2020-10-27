---
title: 關於 Azure Key Vault 憑證存取控制
description: Azure Key Vault 憑證存取控制的概觀
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128527"
---
# <a name="certificate-access-control"></a>憑證存取控制

 憑證的存取控制是由 Key Vault 所管理，且在 Key Vault 的層級上提供的，即包含這些憑證的 Key Vault。 在相同 Key Vault 中，憑證的存取控制原則與金鑰及秘密的存取控制原則並不相同。 使用者可建立一或多個保存庫來保存憑證，以及維護適當區分和管理憑證的案例。  

 下列權限可以在金鑰保存庫上的祕密存取控制項目中使用 (以每個主體為基礎)，且密切地對映秘密物件上所允許的作業：  

- 憑證管理作業的權限
  - **get** ：取得目前憑證版本，或任何憑證版本
  - **list** ：列出目前憑證或憑證版本  
  - **update** ：更新憑證
  - **create** ：建立 Key Vault 憑證
  - **import** ：將憑證內容匯入至 Key Vault 憑證
  - **delete** ：刪除憑證、其原則及其所有版本  
  - **recover** ：復原已刪除的憑證
  - **backup** ：備份金鑰保存庫中的憑證
  - **restore** ：將備份的憑證還原至金鑰保存庫
  - **managecontacts** ：管理 Key Vault 憑證連絡人  
  - **manageissuers** ：管理 Key Vault 憑證授權單位/簽發者
  - **getissuers** ：取得憑證的授權單位/簽發者
  - **listissuers** ：列出憑證的授權單位/簽發者  
  - **setissuers** ：建立或更新 Key Vault 憑證的授權單位/簽發者  
  - **deleteissuers** ：刪除 Key Vault 憑證的授權單位/簽發者  
 
- 特殊權限作業的權限
  - **purge** ：清除 (永久刪除) 已刪除的憑證

如需詳細資訊，請參閱 [Key Vault REST API 參考中的憑證作業](/rest/api/keyvault)。 如需建立權限的相關資訊，請參閱[保存庫 - 更新存取原則](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="troubleshoot"></a>疑難排解
您可能會看到因為遺失存取原則所造成的錯誤。 例如 ```Error type : Access denied or user is unauthorized to create certificate``` 若要解決此錯誤，您必須新增憑證/建立權限。

## <a name="next-steps"></a>後續步驟

- [關於 Key Vault](../general/overview.md)
- [關於金鑰、密碼與憑證](../general/about-keys-secrets-certificates.md)
- [驗證、要求和回應](../general/authentication-requests-and-responses.md)
- [Key Vault 開發人員指南](../general/developers-guide.md)
