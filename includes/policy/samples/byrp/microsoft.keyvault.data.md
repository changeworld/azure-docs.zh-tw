---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9ab710be9ef615d90793b760fd1cf7a5b6d7dec7
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86273964"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[管理允許的憑證金鑰類型](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1151cede-290b-4ba0-8b38-0ad145ac888f) |這個原則會管理憑證的允許金鑰類型。 |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_AllowedKeyTypes.json) |
|[管理橢圓曲線密碼編譯憑證的允許曲線名稱](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd78111f-4953-4367-9fd5-7e08808b54bf) |這個原則會管理橢圓曲線密碼編譯憑證的允許橢圓曲線名稱。 |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_EC_AllowedCurveNames.json) |
|[管理憑證存留期動作觸發程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12ef42cb-9903-4e39-9c26-422d29570417) |這個原則會管理憑證到期前的憑證存留期動作觸發程序設定。 |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_LifetimeAction.json) |
|[管理憑證有效期間](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |這個原則會管理憑證的最大有效期間 (月)。 |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[管理非整合式 CA 所發行的憑證](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa22f4a40-01d3-4c7d-8071-da157eeff341) |這個原則會管理由指定非整合式憑證授權單位發行的憑證。 |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_CustomCAs.json) |
|[管理整合式 CA 所發行的憑證](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e826246-c976-48f6-b03e-619bb92b3d82) |這個原則會管理由指定金鑰保存庫整合式憑證授權單位發行的憑證。 |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_SupportedCAs.json) |
|[管理在指定到期天數內的憑證](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff772fb64-8e40-40ad-87bc-7706e1949427) |這個原則會管理在指定到期天數內的憑證。 |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Expiry_ByDays.json) |
|[管理 RSA 憑證的最小金鑰大小](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcee51871-e572-4576-855c-047c820360f0) |這個原則會管理 RSA 憑證的最小金鑰大小。 |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_RSA_MinimumKeySize.json) |
