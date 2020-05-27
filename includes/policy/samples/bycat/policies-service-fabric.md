---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 22f6f040d333a526940a688c16277be568520f50
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651560"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric 使用主要叢集憑證，可為節點對節點的通訊提供三層保護 (None、Sign 及 EncryptAndSign)。 設定保護層級可確保所有節點對節點的訊息皆經過加密及數位簽署 |Audit, Deny, Disabled |1.1.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |稽核 Service Fabric 中僅透過 Azure Active Directory 的用戶端驗證使用方式 |Audit, Deny, Disabled |1.1.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
