---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 38b083cf4843c3f10db22d83be9e01241d2266a4
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022505"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[容器登錄應使用客戶管理的金鑰進行加密 (CMK) ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |使用客戶管理的金鑰來審核未啟用加密的容器登錄 (CMK) 。 Azure 會使用服務管理的金鑰自動加密待用登錄內容。 您可以使用在 Azure Key Vault 中建立和管理的金鑰，以額外的加密層補充預設加密。 如需 CMK 加密的詳細資訊，請造訪：[https://aka.ms/acr/CMK](https://aka.ms/acr/CMK)。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[容器登錄不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |未設定任何網路或防火牆 (IP) 規則的審核容器登錄，因此預設允許所有網路存取。 限制網路存取可保護容器登錄免于潛在的威脅。 至少有一個 IP/防火牆規則或已設定虛擬網路的容器登錄視為符合規範。 如需 Container Registry 網路規則的詳細資訊，請造訪： [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) 和 [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) 。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[容器登錄應使用 private links](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |未至少有一個已核准私人端點連接的容器登錄。 虛擬網路中的用戶端可以安全地存取透過私人連結而擁有私人端點連線的資源。 然後可以停用公用存取，以確保只有私用連結可以用來連接到登錄。 如需詳細資訊，請瀏覽：[https://aka.ms/acr/private-link](https://aka.ms/acr/private-link)。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Container Registry。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[應補救 Azure Container Registry 映射中的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |容器映射弱點評定會掃描您的登錄是否有每個推送的容器映射上的安全性弱點，並公開 Qualys) 所支援之每個映射的詳細結果 (。 解決這些弱點可以大幅改善容器的安全性狀態，並保護容器免於遭受攻擊。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |
