---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ef776c39592c4dec9c62139ed4abddf1d55610af
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98738495"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cosmos DB 帳戶應具有防火牆規則](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |應在您的 Azure Cosmos DB 帳戶上定義防火牆規則，以防止來自未經授權來源的流量。 若帳戶至少有一個已啟用虛擬網路篩選器定義之 IP 規則，系統會將其視為符合規範。 停用公用存取的帳戶也會視為符合規範。 |Audit, Deny, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure Cosmos DB 帳戶應使用客戶自控金鑰加密待用資料](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f905d99-2ab7-462c-a6b0-f709acca6c8f) |使用客戶自控金鑰來管理 Azure Cosmos DB 的待用加密。 根據預設，資料會使用服務管理的金鑰進行待用加密，但通常需要有客戶自控金鑰 (CMK) 才能符合法規合規性標準。 CMK 可讓您使用由您建立及擁有的 Azure Key Vault 金鑰來加密資料。 您對金鑰生命週期擁有完全的控制權和責任，包括輪替和管理。 在 [https://aka.ms/cosmosdb-cmk](https://aka.ms/cosmosdb-cmk) 深入了解 CMK 加密。 |Audit, Deny, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_CMK_Deny.json) |
|[Azure Cosmos DB 允許的位置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0473574d-2d43-4217-aefe-941fcdf7e684) |此原則可讓您限制貴組織在部署 Azure Cosmos DB 資源時可指定的位置。 它可用來強制執行地理合規性需求。 |[parameters('policyEffect')] |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json) |
|[應停用 Azure Cosmos DB 以金鑰為基礎的中繼資料寫入權限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4750c32b-89c0-46af-bfcb-2e4541a818d5) |此原則可讓您確認所有 Azure Cosmos DB 帳戶都停用以金鑰為基礎的中繼資料寫入權限。 |附加 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_DisableMetadata_Append.json) |
|[應限制 Azure Cosmos DB 輸送量](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b7ef78e-a035-4f23-b9bd-aff122a1b1cf) |此原則可讓您在透過資源提供者建立 Azure Cosmos DB 資料庫和容器時，限制組織可指定的最大輸送量。 這會封鎖自動調整資源的建立。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_MaxThroughput_Deny.json) |
|[為 Cosmos DB 帳戶部署進階威脅防護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |此原則會在多個 Cosmos DB 帳戶啟用進階威脅防護。 |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |
