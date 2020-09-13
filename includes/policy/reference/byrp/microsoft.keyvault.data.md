---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6818a7833f9a62254677716a6d491e71f8984e8c
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022410"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[憑證應由指定的整合式憑證授權單位單位發行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e826246-c976-48f6-b03e-619bb92b3d82) |指定可在金鑰保存庫（例如 Digicert 或 GlobalSign）中頒發證書的 Azure 整合式憑證授權單位單位，以管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_SupportedCAs.json) |
|[憑證應由指定的非整合式憑證授權單位單位發行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa22f4a40-01d3-4c7d-8071-da157eeff341) |指定可在金鑰保存庫中發行憑證的自訂或內部憑證授權單位單位，以管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_CustomCAs.json) |
|[憑證應具有指定的存留期動作觸發程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12ef42cb-9903-4e39-9c26-422d29570417) |藉由指定在其存留期的特定百分比或在到期前的特定天數內觸發憑證存留期動作，來管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_LifetimeAction.json) |
|[憑證應具有指定的最大有效期間](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |指定憑證在金鑰保存庫內有效的時間長度上限，以管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[憑證不應在指定的天數內到期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff772fb64-8e40-40ad-87bc-7706e1949427) |管理將在指定天數內到期的憑證，以確保您的組織有足夠的時間在到期前輪替憑證。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Expiry_ByDays.json) |
|[憑證應使用允許的金鑰類型](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1151cede-290b-4ba0-8b38-0ad145ac888f) |藉由限制允許的憑證金鑰類型，來管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_AllowedKeyTypes.json) |
|[使用橢圓曲線密碼編譯的憑證應該有允許的曲線名稱](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd78111f-4953-4367-9fd5-7e08808b54bf) |管理金鑰保存庫中所儲存之 ECC 憑證的允許橢圓曲線名稱。 您可以在中找到詳細資訊 [https://aka.ms/akvpolicy](https://aka.ms/akvpolicy) 。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_EC_AllowedCurveNames.json) |
|[使用 RSA 密碼編譯的憑證應該具有指定的最小金鑰大小](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcee51871-e572-4576-855c-047c820360f0) |針對儲存在金鑰保存庫中的 RSA 憑證指定最小金鑰大小，以管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_RSA_MinimumKeySize.json) |
