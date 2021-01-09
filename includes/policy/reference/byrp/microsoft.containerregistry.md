---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: abc714cfc5bd0978ae6947a95c8c07212adeb828
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049334"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應使用客戶自控金鑰 (CMK) 進行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |使用客戶管理的金鑰來管理您的登錄內容的待用加密。 根據預設，資料會使用服務管理的金鑰進行待用加密，但客戶管理的金鑰 (CMK) 通常需要符合法規合規性標準。 Cmk 可讓資料使用您所建立和擁有的 Azure Key Vault 金鑰進行加密。 您對金鑰生命週期擁有完全的控制權和責任，包括輪替和管理。 深入瞭解 CMK 加密 [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) 。 |Audit, Deny, Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |依預設，Azure container registry 會接受從任何網路上的主機透過網際網路連線。 若要保護您的登錄免于潛在威脅，只允許來自特定公用 IP 位址或位址範圍的存取。 如果您的登錄沒有 IP/防火牆規則或已設定的虛擬網路，則會出現在狀況不良的資源中。 請在這裡深入瞭解 Container Registry 網路規則： [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) 和這裡 [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) 。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[容器登錄應使用 private link](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link 可讓您將虛擬網路連線至 Azure 服務，而不需要來源或目的地的公用 IP 位址。 Private link 平臺會透過 Azure 骨幹網路來處理取用者與服務之間的連線能力。藉由將私人端點對應至容器登錄（而不是整個服務），您也會受到保護以防止資料洩漏風險。 若要深入瞭解，請參閱： [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) 。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Container Registry。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[應補救 Azure Container Registry 映像中的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |容器映像弱點評估會掃描您的登錄，以判斷每個推送的容器映像是否有安全性弱點，並公開每個映像的詳細結果 (由 Qualys 提供技術支援)。 解決這些弱點可以大幅改善容器的安全性狀態，並保護容器免於遭受攻擊。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |
