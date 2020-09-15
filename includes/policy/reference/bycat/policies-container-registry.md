---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a49c45600096da787e4ef33d0930fd3d5099d297
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90005356"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應使用客戶自控金鑰 (CMK) 進行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |使用客戶自控金鑰 (CMK) 來稽核未啟用加密的容器登錄。 Azure 會使用服務管理的金鑰，自動將待用登錄內容加密。 您可以使用在 Azure Key Vault 中建立和管理的金鑰，以額外的加密層補充預設加密。 如需 CMK 加密的詳細資訊，請造訪：[https://aka.ms/acr/CMK](https://aka.ms/acr/CMK)。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |稽核未設定任何網路或防火牆 (IP) 規則且預設允許所有網路存取的容器登錄。 限制網路存取可保護容器登錄不受潛在威脅的影響。 至少具有一個 IP/防火牆規則或已設定虛擬網路的容器登錄會視為符合規範。 如需 Container Registry 網路規則的詳細資訊，請造訪：[https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) 和 [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet)。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[容器登錄應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |稽核不具備至少一個已核准私人端點連線的容器登錄。 虛擬網路中的用戶端可以安全地存取透過私人連結而擁有私人端點連線的資源。 接著可以停用公用存取，以確保只有私人連結可以用來連線到登錄。 如需詳細資訊，請瀏覽：[https://aka.ms/acr/private-link](https://aka.ms/acr/private-link)。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
