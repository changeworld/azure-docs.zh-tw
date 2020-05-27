---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4a57cbd9373dcb99cf312feeb35399138e0648db
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651372"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[應使用客戶管理金鑰 (CMK) 進行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |使用客戶管理的金鑰 (CMK) 來稽核未啟用加密的 Container Registry。 如需 CMK 加密的詳細資訊，請造訪：[https://aka.ms/acr/CMK](https://aka.ms/acr/CMK)。 |Audit, Disabled |1.0.0-preview |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |稽核未設定任何網路 (IP 或 VNET) 規則且預設允許所有網路存取的 Container Registry。 至少具有一個 IP/防火牆規則或已設定虛擬網路的 Container Registry 將視為符合規範。 如需 Container Registry 網路規則的詳細資訊，請造訪：[https://aka.ms/acr/vnet](https://aka.ms/acr/vnet)。 |Audit, Disabled |1.0.0-preview |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[容器登錄應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |不具備至少一個已核准私人端點連線的稽核容器登錄。 虛擬網路中的用戶端可以安全地存取透過私人連結而擁有私人端點連線的資源。 如需詳細資訊，請瀏覽：[https://aka.ms/acr/private-link](https://aka.ms/acr/private-link)。 |Audit, Disabled |1.0.0-preview |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
