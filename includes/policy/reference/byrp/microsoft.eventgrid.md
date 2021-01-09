---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f03688012100c31865fb25087f7b4049c087b43e
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050898"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 事件方格網域應使用 private link](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link 可讓您將虛擬網路連線至 Azure 服務，而不需要來源或目的地的公用 IP 位址。 Private link 平臺會透過 Azure 骨幹網路來處理取用者與服務之間的連線能力。藉由將私人端點對應至您的事件方格網域（而非整個服務），您也會受到保護以防止資料洩漏風險。若要深入瞭解，請參閱： [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) 。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Azure 事件方格主題應使用 private link](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link 可讓您將虛擬網路連線至 Azure 服務，而不需要來源或目的地的公用 IP 位址。 Private link 平臺會透過 Azure 骨幹網路來處理取用者與服務之間的連線能力。 藉由將私人端點對應至您的主題，而不是整個服務，您也會受到保護以防止資料洩漏風險。 若要深入瞭解，請參閱： [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) 。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
