---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c4619610cc8e54f9fbe556e3d12668a680a2e261
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91859239"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |適用於 MariaDB 的 Azure 資料庫可讓您為資料庫伺服器選擇 [冗余] 選項。 您可以將它設定為異地多餘的備份儲存體，其中資料不只會儲存在您的伺服器所裝載的區域內，也會複寫到配對的區域，以在發生區域失敗時提供修復選項。 只有在伺服器建立期間，才允許為備份設定異地複寫儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB 伺服器應使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |以虛擬網路為基礎的防火牆規則可用來啟用來自特定子網的流量，以適用於 MariaDB 的 Azure 資料庫，同時確保流量會留在 Azure 界限內。 如果適用於 MariaDB 的 Azure 資料庫有使用中的虛擬網路服務端點，此原則會提供一種方式來進行審核。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[MariaDB 伺服器應啟用私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |私人端點連線藉由啟用適用於 MariaDB 的 Azure 資料庫的私人連線，來強制執行安全通訊。 設定私人端點連線，以存取只來自已知網路的流量，並防止從其他所有 IP 位址（包括在 Azure 內）進行存取。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[應為 MariaDB 伺服器停用公用網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |停用公用網路存取屬性可確保您的適用於 MariaDB 的 Azure 資料庫只能從私人端點存取，藉以改善安全性。 這項設定會嚴格停用從 Azure IP 範圍以外的任何公用位址空間進行存取，並拒絕所有符合 IP 或虛擬網路型防火牆規則的登入。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
