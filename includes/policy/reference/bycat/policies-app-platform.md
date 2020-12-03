---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6fb0c3dc7095c1fc48ba3ed26b3a2f7ab47ac789
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "96007533"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核未啟用分散式追蹤的 Azure Spring Cloud 執行個體](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Azure Spring Cloud 中的分散式追蹤工具可對應用程式中微服務之間的複雜相互關聯進行偵錯及監視。 分散式追蹤工具應啟用並處於健全狀態。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud 應使用網路插入](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud 執行個體應該使用虛擬網路插入來實現下列目的：1. 將 Azure Spring Cloud 與網際網路隔離。 2. 讓 Azure Spring Cloud 能夠與內部部署資料中心或其他虛擬網路中 Azure 服務內的系統進行互動。 3. 讓客戶得以控制 Azure Spring Cloud 的輸入和輸出網路通訊 |稽核、停用、拒絕 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
