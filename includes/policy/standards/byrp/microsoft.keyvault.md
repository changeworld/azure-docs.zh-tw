---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5bec718de3f4f4b0ee78bacf9fc41cbedb8e71df
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380237"
---
## <a name="azure-security-benchmark"></a>Azure 安全性效能評定

[Azure 安全性基準](../../../../articles/security/benchmarks/overview.md)提供如何在 Azure 上保護雲端解決方案的建議。 若要查看此服務如何完全對應到 Azure 安全性基準，請參閱 [Azure 安全性基準對應檔案](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - Azure 安全性基準](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路安全性 |1.1 |在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源 |[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|記錄和監視 |2.3 |啟用 Azure 資源的稽核記錄 |[應啟用 Key Vault 中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|安全設定 |7.11 |安全地管理 Azure 祕密 |[Key Vault 物件應該要可復原](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
|資料復原 |9.4 |確保備份和客戶管理的金鑰受到保護 |[Key Vault 物件應該要可復原](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure 基礎基準測試

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - CIS Microsoft Azure 基礎基準測試 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md)。
如需關於此合規性標準的詳細資訊，請參閱 [CIS Microsoft Azure基礎基準測試](https://www.cisecurity.org/benchmark/azure/)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|記錄和監視 |5.1.7 |確定 Azure KeyVault 的記錄是 [已啟用] |[應啟用 Key Vault 中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|其他安全性考量 |8.4 |確定金鑰保存庫可復原 |[Key Vault 物件應該要可復原](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

