---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 83b8e9d2bd65d04a5260495c5add3cb98288955c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235495"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |此原則會稽核所有未啟用異地備援備份之適用於 MariaDB 的 Azure 資料庫。 |Audit, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB 伺服器應使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |此原則會稽核未設定為使用虛擬網路服務端點的 MariaDB 伺服器。 如需詳細資料，請造訪 [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork)。 |AuditIfNotExists, Disabled |1.0.1 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[MariaDB 伺服器應啟用私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |此原則會稽核未設定為使用私人端點的 MariaDB 伺服器。 如需詳細資料，請造訪 [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink)。 |AuditIfNotExists, Disabled |1.0.1 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[應為 MariaDB 伺服器停用公用網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |此原則會在啟用公用網路存取的情況下，稽核環境中的 MariaDB 伺服器。 如需詳細資料，請造訪 [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542)。 |Audit, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
