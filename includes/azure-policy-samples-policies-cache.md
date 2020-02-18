---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 1d5e916d9f8256c002f6810d1fc7aedebeba6481
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170006"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[應該只允許對 Redis Cache 的安全連線](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |稽核只允許透過 SSL 連線到 Redis Cache。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |1.0.0 |
