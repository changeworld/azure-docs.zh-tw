---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 78f239ec69ce196bc42970310154c242dd188537
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047133"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應使用客戶自控金鑰 (CMK) 進行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |使用客戶自控金鑰來管理登錄內容的待用加密。 根據預設，資料會使用服務管理的金鑰進行待用加密，但通常需要有客戶自控金鑰 (CMK) 才能符合法規合規性標準。 CMK 可讓您使用由您建立及擁有的 Azure Key Vault 金鑰來加密資料。 您對金鑰生命週期擁有完全的控制權和責任，包括輪替和管理。 在 [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) 深入了解 CMK 加密。 |Audit, Deny, Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |根據預設，Azure 容器登錄會接受任何網路上的主機透過網際網路的連線。 為了保護登錄不受潛在威脅的影響，請只允許來自特定公用 IP 位址或位址範圍的存取。 如果登錄沒有 IP/防火牆規則或已設定的虛擬網路，其會出現在狀況不良的資源中。 在以下位置深入了解 Container Registry 網路規則：[https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network)和[https://aka.ms/acr/vnet](https://aka.ms/acr/vnet)。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[容器登錄應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link 可讓您將虛擬網路連線到 Azure 服務，而不需要來源或目的地上的公用 IP 位址。 Private Link 平台會透過 Azure 骨幹網路處理取用者與服務之間的連線。藉由將私人端點對應至容器登錄而非整個服務，您也會受到保護，而免於遭受資料洩漏風險。 深入了解：[https://aka.ms/acr/private-link](https://aka.ms/acr/private-link)。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
