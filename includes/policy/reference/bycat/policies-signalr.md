---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0c7c1374ac98722a282a92dc7fbac8c80d94ef5b
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807555"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SignalR Service 應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link 可讓您將虛擬網路連線到 Azure 服務，而不需要來源或目的地上的公用 IP 位址。 Private Link 平台會透過 Azure 骨幹網路處理取用者與服務之間的連線。 藉由將私人端點對應至 SignalR 資源而非整個服務，您也會受到保護，而免於遭受資料洩漏風險。深入了解：[https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink)。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
