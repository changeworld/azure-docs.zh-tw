---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 468b2f73e07d48081c9ed67ca6720e57ac578570
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859807"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 MySQL 伺服器啟用自備金鑰資料保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |使用客戶管理的金鑰來加密適用於 MySQL 的 Azure 資料庫資料庫伺服器中的待用資料，可讓您在管理金鑰和資料時，區隔責任。 當您設定客戶管理的金鑰時，金鑰是用來保護及控制加密資料的金鑰存取權。 您擁有金鑰生命週期的完整控制權和責任，包括輪替和管理。 基於合規性考慮，有時需要使用客戶管理的金鑰。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 這項設定會強制一律啟用 SSL 以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |適用於 MySQL 的 Azure 資料庫可讓您為資料庫伺服器選擇 [冗余] 選項。 您可以將它設定為異地多餘的備份儲存體，其中資料不只會儲存在您的伺服器所裝載的區域內，也會複寫到配對的區域，以在發生區域失敗時提供修復選項。 只有在伺服器建立期間，才允許為備份設定異地複寫儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[MariaDB 伺服器應使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |以虛擬網路為基礎的防火牆規則可用來啟用來自特定子網的流量，以適用於 MySQL 的 Azure 資料庫，同時確保流量會留在 Azure 界限內。 如果適用於 MySQL 的 Azure 資料庫有使用中的虛擬網路服務端點，此原則會提供一種方式來進行審核。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[MySQL 伺服器應啟用私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |私人端點連線藉由啟用適用於 MySQL 的 Azure 資料庫的私人連線，來強制執行安全通訊。 設定私人端點連線，以存取只來自已知網路的流量，並防止從其他所有 IP 位址（包括在 Azure 內）進行存取。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[應為 MySQL 伺服器停用公用網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |停用公用網路存取屬性可確保您的適用於 MySQL 的 Azure 資料庫只能從私人端點存取，藉以改善安全性。 這項設定會嚴格停用從 Azure IP 範圍以外的任何公用位址空間進行存取，並拒絕所有符合 IP 或虛擬網路型防火牆規則的登入。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
