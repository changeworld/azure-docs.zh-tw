---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 2ae61445013a00eb4aee0a5df7ae6d356206290c
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79381922"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[應該只允許對 Redis Cache 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |稽核只允許透過 SSL 連線到 Redis Cache。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
