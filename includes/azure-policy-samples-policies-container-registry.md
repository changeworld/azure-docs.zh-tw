---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 888f8954ede7b733b89a4c3215a889eae6825e4d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624036"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[應使用客戶管理金鑰 (CMK) 進行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |使用客戶管理的金鑰 (CMK) 來稽核未啟用加密的 Container Registry。 如需有關 CMK 加密的詳細資訊，請造訪： https://aka.ms/acr/CMK 。 |Audit, Disabled |1.0.0-preview |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |稽核未設定任何網路 (IP 或 VNET) 規則且預設允許所有網路存取的 Container Registry。 至少具有一個 IP/防火牆規則或已設定虛擬網路的 Container Registry 將視為符合規範。 如需有關 Container Registry 網路規則的詳細資訊，請造訪： https://aka.ms/acr/vnet 。 |Audit, Disabled |1.0.0-preview |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
