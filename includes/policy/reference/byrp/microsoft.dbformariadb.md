---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 62581c50503cd0541c2c27671c18dd937be1efe7
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806360"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |適用於 MariaDB 的 Azure 資料庫可讓您為資料庫伺服器選擇備援選項。 可以設定為異地備援備份儲存體，其中的資料不只會儲存在裝載您伺服器的區域內，也會複寫至配對的區域，以在發生區域失敗時提供復原選項。 您只可在伺服器建立期間，為備份設定異地備援儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB 伺服器應使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |使用虛擬網路形式的防火牆規則，讓流量從特定子網路進入適用於 MariaDB 的 Azure 資料庫，同時確保流量會保持在 Azure 界限內。 此原則可讓您在使用擁有虛擬網路服務端點的適用於 MariaDB 的 Azure 資料庫情況下進行稽核。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[MariaDB 伺服器應啟用私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |私人端點連線透過啟用與適用於 MariaDB 的 Azure 資料庫的私人連線，可強制執行安全通訊。 設定私人端點連線，僅存取來自已知網路的流量，並防止其他所有 IP 位址 (包括 Azure 內) 的存取。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[應為 MariaDB 伺服器停用公用網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |停用公用網路存取屬性可確保適用於 MariaDB 的 Azure 資料庫只能從私人端點存取，藉此改善安全性。 此設定會嚴格停用從 Azure IP 範圍外任何公用位址空間進行的存取，並拒絕所有符合 IP 或虛擬網路型防火牆規則的登入。 |Audit, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
