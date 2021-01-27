---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7a07af27b15e4cd6f82cf77620e449cf5d475c8c
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807285"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[認知服務帳戶應啟用資料加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2bdd0062-9d75-436e-89df-487dd8e4b3c7) |此原則會稽核未使用資料加密的所有認知服務帳戶。 針對每個具有儲存體的認知服務帳戶，請使用客戶管理的金鑰或 Microsoft 管理的金鑰來啟用資料加密。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_Encryption_Audit.json) |
|[認知服務帳戶應使用客戶管理的金鑰來啟用資料加密 (CMK) ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67121cc7-ff39-4ab8-b7e3-95b84dab487d) |客戶自控金鑰 (CMK) 通常需要符合法規合規性標準。 CMK 可讓您使用由您建立及擁有的 Azure Key Vault 金鑰來加密儲存在認知服務中的資料。 您對金鑰生命週期擁有完全的控制權和責任，包括輪替和管理。 在 [https://aka.ms/cosmosdb-cmk](https://aka.ms/cosmosdb-cmk) 深入了解 CMK 加密。 |Audit, Deny, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_CustomerManagedKey_Audit.json) |
|[認知服務帳戶應限制網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |應限制對於認知服務帳戶的網路存取。 請設定網路規則，只允許來自認可之網路的應用程式存取認知服務帳戶。 若要允許來自特定網際網路或內部部署用戶端的連線，可將存取權授與來自特定 Azure 虛擬網路的流量，或授與公用網際網路 IP 位址範圍。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[認知服務帳戶應使用客戶擁有的儲存體](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F46aa9b05-0e60-4eae-a88b-1e9d374fa515) |此原則會稽核未使用客戶擁有之儲存體的所有認知服務帳戶。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_UserOwnedStorage_Audit.json) |
|[認知服務帳戶應使用客戶擁有的儲存體，或是啟用資料加密。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11566b39-f7f7-4b82-ab06-68d8700eb0a4) |此原則會稽核既未使用客戶擁有的儲存體、也未使用資料加密的所有認知服務帳戶。 針對每個具有儲存體的認知服務帳戶，請使用客戶擁有的儲存體或啟用資料加密。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_BYOX_Audit.json) |
|[應停用認知服務帳戶的公用網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |此原則會稽核您環境中已啟用公用網路存取的所有認知服務帳戶。 請停用公用網路存取，以便只允許來自私人端點的連線。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
