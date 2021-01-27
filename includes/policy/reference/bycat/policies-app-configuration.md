---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a4a1627ddd5f02535d1221a6dd276e02a7ec3d78
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807579"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應用程式組態應使用客戶自控金鑰](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |客戶自控金鑰提供先進的資料保護，可讓您管理加密金鑰。 這通常需要符合合規性需求。 |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[應用程式設定應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link 可讓您將虛擬網路連線到 Azure 服務，而不需要來源或目的地上的公用 IP 位址。 Private Link 平台會透過 Azure 骨幹網路處理取用者與服務之間的連線。 藉由將私人端點對應至應用程式組態而非整個服務，您也會受到保護，而免於遭受資料洩漏風險。 深入了解：[https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint)。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
