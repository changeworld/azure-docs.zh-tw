---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4602b1b9602a191fba8583a01ac203c236836ca2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859286"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[憑證應由指定的整合式憑證授權單位發行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e826246-c976-48f6-b03e-619bb92b3d82) |藉由指定可在金鑰保存庫中發行憑證的 Azure 整合式憑證授權單位 (例如 Digicert 或 GlobalSign)，來管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_SupportedCAs.json) |
|[憑證應由指定的非整合式憑證授權位單位發行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa22f4a40-01d3-4c7d-8071-da157eeff341) |藉由指定可在金鑰保存庫中發行憑證的自訂或內部憑證授權單位，來管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_CustomCAs.json) |
|[憑證應具有指定的存留期動作觸發程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12ef42cb-9903-4e39-9c26-422d29570417) |指定憑證存留期動作要在達到其存留期的特定百分比時觸發，或在到期前特定天數時觸發，以管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_LifetimeAction.json) |
|[憑證應具有指定的有效期間上限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |藉由指定憑證在金鑰保存庫中的有效期間上限，來管理組織的合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[憑證不應在指定的天數內到期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff772fb64-8e40-40ad-87bc-7706e1949427) |管理將在指定天數內到期的憑證，以確保到期之前，您的組織有足夠的時間輪替憑證。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Expiry_ByDays.json) |
|[憑證應使用允許的金鑰類型](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1151cede-290b-4ba0-8b38-0ad145ac888f) |限制允許憑證使用的金鑰類型，以管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_AllowedKeyTypes.json) |
|[使用橢圓曲線密碼編譯的憑證應該有允許的曲線名稱](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd78111f-4953-4367-9fd5-7e08808b54bf) |針對金鑰保存庫中儲存的 ECC 憑證，管理允許的橢圓曲線名稱。 如需詳細資訊，請參閱 [https://aka.ms/akvpolicy](https://aka.ms/akvpolicy)。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_EC_AllowedCurveNames.json) |
|[使用 RSA 密碼編譯的憑證應具有指定的最小金鑰大小](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcee51871-e572-4576-855c-047c820360f0) |為儲存在金鑰保存庫中的 RSA 憑證指定最小金鑰大小，以管理您的組織合規性需求。 |Audit, Deny, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_RSA_MinimumKeySize.json) |
