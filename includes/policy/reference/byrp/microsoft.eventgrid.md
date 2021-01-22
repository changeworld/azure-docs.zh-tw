---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 45c31b519ed2e9db26a29743309744145c5bc6e4
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703518"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 事件方格網域應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link 可讓您將虛擬網路連線到 Azure 服務，而不需要來源或目的地上的公用 IP 位址。 Private Link 平台會透過 Azure 骨幹網路處理取用者與服務之間的連線。藉由將私人端點對應至事件方格網域而非整個服務，您也會受到保護，而免於遭受資料洩漏風險。深入了解：[https://aka.ms/privateendpoints](https://aka.ms/privateendpoints)。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Azure 事件方格主題應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link 可讓您將虛擬網路連線到 Azure 服務，而不需要來源或目的地上的公用 IP 位址。 Private Link 平台會透過 Azure 骨幹網路處理取用者與服務之間的連線。 藉由將私人端點對應至主題而非整個服務，您也會受到保護，而免於遭受資料洩漏風險。 深入了解：[https://aka.ms/privateendpoints](https://aka.ms/privateendpoints)。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
