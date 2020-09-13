---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d34a42a8af63ad5cf455b0d5b1c1478f166de2be
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90021710"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SignalR Service 應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |稽核未具備至少一個已核准私人端點連線的 Azure SignalR Service 資源。 虛擬網路中的用戶端可以安全地存取透過私人連結而擁有私人端點連線的資源。 如需詳細資訊，請瀏覽：[https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink)。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
