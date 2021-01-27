---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0789a0d7a13884f273bb6538a87c1b841782246d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807261"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure HDInsight 叢集應使用客戶管理的金鑰來加密待用資料](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |使用客戶管理的金鑰 (CMK) 管理 Azure HDInsight 叢集的待用加密。 根據預設，客戶資料會使用服務管理的金鑰進行加密，但通常需要有 CMK 才能符合法規合規性標準。 CMK 可讓您使用由您建立及擁有的 Azure Key Vault 金鑰來加密資料。 您對金鑰生命週期擁有完全的控制權和責任，包括輪替和管理。 在 [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) 深入了解 CMK 加密。  |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight 叢集應使用主機加密來加密待用資料](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |在主機上啟用加密有助於保護您的資料，以符合組織的安全性和合規性承諾。 當您在主機上啟用加密時，儲存在 VM 主機上的資料會在待用時加密，並將流量加密至儲存體服務。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure HDInsight 叢集應該在傳輸中使用加密來加密 Azure HDInsight 叢集節點之間的通訊](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |在 Azure HDInsight 叢集節點之間傳輸時，資料可能會遭到篡改。 啟用傳輸中加密會解決在此傳輸期間誤用和篡改的問題。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
