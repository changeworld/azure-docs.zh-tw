---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: de9a10450815a6807e3f43cb2972ec7a55490281
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051017"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 MySQL 伺服器啟用自備金鑰資料保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |使用客戶管理的金鑰來管理您的 MySQL 伺服器的待用加密。 根據預設，資料會使用服務管理的金鑰進行待用加密，但客戶管理的金鑰 (CMK) 通常需要符合法規合規性標準。 Cmk 可讓資料使用您所建立和擁有的 Azure Key Vault 金鑰進行加密。 您對金鑰生命週期擁有完全的控制權和責任，包括輪替和管理。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |適用於 MySQL 的 Azure 資料庫可讓您為資料庫伺服器選擇備援選項。 可以設定為異地備援備份儲存體，其中的資料不只會儲存在裝載您伺服器的區域內，也會複寫至配對的區域，以在發生區域失敗時提供復原選項。 您只可在伺服器建立期間，為備份設定異地備援儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[應為適用於 MySQL 的 Azure 資料庫伺服器啟用基礎結構加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a58212a-c829-4f13-9872-6371df2fd0b4) |為適用於 MySQL 的 Azure 資料庫伺服器啟用基礎結構加密，讓資料有更高層級的安全保證。 啟用基礎結構加密時，待用資料會使用與 FIPS 140-2 相容的 Microsoft 管理金鑰進行兩次加密。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_InfrastructureEncryption_Audit.json) |
|[MariaDB 伺服器應使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |使用虛擬網路形式的防火牆規則，讓流量從特定子網路進入適用於 MySQL 的 Azure 資料庫，同時確保流量會保持在 Azure 界限內。 此原則可讓您在使用擁有虛擬網路服務端點的適用於 MySQL 的 Azure 資料庫情況下進行稽核。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[MySQL 伺服器應啟用私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |私人端點連線透過啟用與適用於 MySQL 的 Azure 資料庫的私人連線，可強制執行安全通訊。 設定私人端點連線，僅存取來自已知網路的流量，並防止其他所有 IP 位址 (包括 Azure 內) 的存取。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[應為 MySQL 彈性伺服器停用公用網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9299215-ae47-4f50-9c54-8a392f68a052) |停用公用網路存取屬性可確保適用於 MySQL 的 Azure 資料庫彈性伺服器只能從私人端點存取，藉此改善安全性。 此設定會嚴格停用從 Azure IP 範圍外任何公用位址空間進行的存取，並拒絕所有符合 IP 或虛擬網路型防火牆規則的登入。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_FlexibleServers_DisablePublicNetworkAccess_Audit.json) |
|[應為 MySQL 伺服器停用公用網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |停用 [公用網路存取] 屬性來改善安全性，並確保您的適用於 MySQL 的 Azure 資料庫只能從私人端點存取。 此設定會嚴格停用從 Azure IP 範圍外任何公用位址空間進行的存取，並拒絕所有符合 IP 或虛擬網路型防火牆規則的登入。 |Audit, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
