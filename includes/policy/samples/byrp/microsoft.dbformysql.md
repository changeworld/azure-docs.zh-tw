---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 89e2fd3d649de0e5e2a60d81e80a71eac77a813e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660234"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[應為 MySQL 伺服器啟用自備金鑰資料保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |未啟用自備金鑰資料保護的情況下，此原則會稽核環境中的 MySQL 伺服器。 如需詳細資料，請造訪 [https://aka.ms/mysqlbyok](https://aka.ms/mysqlbyok)。 |AuditIfNotExists, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |此原則會針對未強制執行 SSL 連線的任何 MySQL 伺服器進行稽核。 適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 |Audit, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |此原則會稽核所有未啟用異地備援備份的適用於 MySQL 的 Azure 資料庫。 |Audit, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[MariaDB 伺服器應使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |此原則會稽核未設定為使用虛擬網路服務端點的 MySQL 伺服器。 如需詳細資料，請造訪 [https://aka.ms/mysqlvnet](https://aka.ms/mysqlvnet)。 |AuditIfNotExists, Disabled |1.0.1 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[MySQL 伺服器應啟用私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |此原則會稽核未設定為使用私人端點的 MySQL 伺服器。 如需詳細資料，請造訪 [https://aka.ms/mysqlprivatelink](https://aka.ms/mysqlprivatelink)。 |AuditIfNotExists, Disabled |1.0.1 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[應為 MySQL 伺服器停用公用網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |此原則會在啟用公用網路存取的情況下，稽核環境中的 MySQL 伺服器。 如需詳細資料，請造訪 [https://go.microsoft.com/fwlink/?linkid=2120014](https://go.microsoft.com/fwlink/?linkid=2120014)。 |Audit, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
