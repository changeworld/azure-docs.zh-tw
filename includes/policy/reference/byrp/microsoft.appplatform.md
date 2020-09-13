---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3bbfc9d2045367d014ffc2c3cbc3f5696f9c1d6f
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90021722"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核未啟用分散式追蹤的 Azure Spring Cloud 執行個體](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Azure Spring Cloud 中的分散式追蹤工具可對應用程式中微服務之間的複雜相互關聯進行偵錯及監視。 分散式追蹤工具應啟用並處於健全狀態。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure 春季 Cloud 應使用網路插入](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure 春季雲端實例應使用虛擬網路插入，原因如下：1。 從網際網路找出 Azure 春天雲端。 2. 讓 Azure 春季雲端與內部部署資料中心或其他虛擬網路中的 Azure 服務的系統進行互動。 3. 讓客戶能夠控制 Azure 春季雲端的輸入和輸出網路通訊。 |Audit、Disabled、Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
