---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d51f9b70a48bdd5adafeeeafc266b7142a7171a4
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98859950"
---
## <a name="azure-security-benchmark"></a>Azure 安全性效能評定

[Azure 安全性基準](../../../../articles/security/benchmarks/overview.md)提供如何在 Azure 上保護雲端解決方案的建議。 若要查看此服務如何完全對應到 Azure 安全性基準，請參閱 [Azure 安全性基準對應檔案](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - Azure 安全性基準](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路安全性 |NS-1 |執行內部流量的安全性 |[應該在 Key Vault 上啟用防火牆](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |[1.0.2-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|網路安全性 |NS-2 |將私人網路連接在一起 |[應設定 Key Vault 的私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |[1.0.2-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|網路安全性 |NS-3 |建立 Azure 服務的私人網路存取 |[應設定 Key Vault 的私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |[1.0.2-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|網路安全性 |NS-4 |保護應用程式和服務免于外部網路攻擊 |[應該在 Key Vault 上啟用防火牆](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |[1.0.2-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|記錄與威脅偵測 |LT-4 |啟用 Azure 資源的記錄 |[應啟用 Key Vault 中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|備份及修復 |BR-4 |降低遺失金鑰的風險 |[金鑰保存庫應啟用清除保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
|備份及修復 |BR-4 |降低遺失金鑰的風險 |[金鑰保存庫應已啟用虛刪除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e66c121-a66a-4b1f-9b83-0fd99bf0fc2d) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_SoftDeleteMustBeEnabled_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure 安全性效能評定 v1

[Azure 安全性基準](../../../../articles/security/benchmarks/overview.md)提供如何在 Azure 上保護雲端解決方案的建議。 若要查看此服務如何完全對應到 Azure 安全性基準，請參閱 [Azure 安全性基準對應檔案](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - Azure 安全性基準](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路安全性 |1.1 |在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源 |[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|記錄和監視 |2.3 |啟用 Azure 資源的稽核記錄 |[應啟用 Key Vault 中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|安全設定 |7.11 |安全地管理 Azure 祕密 |[金鑰保存庫應啟用清除保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
|資料復原 |9.4 |確保備份和客戶管理的金鑰受到保護 |[金鑰保存庫應啟用清除保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure 基礎基準測試

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - CIS Microsoft Azure 基礎基準測試 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md)。
如需關於此合規性標準的詳細資訊，請參閱 [CIS Microsoft Azure基礎基準測試](https://www.cisecurity.org/benchmark/azure/)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|記錄和監視 |5.1.7 |確定 Azure KeyVault 的記錄是 [已啟用] |[應啟用 Key Vault 中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|其他安全性考量 |8.4 |確定金鑰保存庫可復原 |[金鑰保存庫應啟用清除保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC 層級3

若要查看所有 Azure 服務的可用 Azure 原則內建如何對應到此合規性標準，請參閱 [Azure 原則法規合規性-CMMC 層級 3](../../../../articles/governance/policy/samples/cmmc-l3.md)。
如需此合規性標準的詳細資訊，請參閱 [網路安全性成熟度模型認證 (CMMC) ](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|存取控制 |AC. 1.001 |限制資訊系統存取授權的使用者、代表授權使用者的處理常式，以及裝置 (包括) 其他資訊系統。 |[應該在 Key Vault 上啟用防火牆](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |[1.0.2-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|存取控制 |AC. 1.002 |將資訊系統存取許可權制為允許授權使用者執行的交易和函式類型。 |[應該在 Key Vault 上啟用防火牆](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |[1.0.2-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|組態管理 |CM. 2.064 |針對組織系統中所採用的資訊技術產品，建立並強制執行安全性設定。 |[應該在 Key Vault 上啟用防火牆](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |[1.0.2-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|事件回應 |IR. 2.093 |偵測和報告事件。 |[應該在 Key Vault 上啟用防火牆](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |[1.0.2-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|系統與通訊保護 |SC. 3.183 |預設拒絕網路通訊流量，並允許依例外狀況的網路通訊流量 (例如，[全部拒絕]、[允許例外狀況]) 。 |[應該在 Key Vault 上啟用防火牆](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |[1.0.2-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|系統與通訊保護 |SC. 3.187 |針對組織系統中採用的密碼編譯建立和管理密碼編譯金鑰。 |[應該在 Key Vault 上啟用防火牆](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |[1.0.2-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|系統與通訊保護 |SC. 3.187 |針對組織系統中採用的密碼編譯建立和管理密碼編譯金鑰。 |[金鑰保存庫應啟用清除保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
|系統與通訊保護 |SC. 3.187 |針對組織系統中採用的密碼編譯建立和管理密碼編譯金鑰。 |[金鑰保存庫應已啟用虛刪除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e66c121-a66a-4b1f-9b83-0fd99bf0fc2d) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_SoftDeleteMustBeEnabled_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)。
如需此合規性標準的詳細資訊，請參閱 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路中的隔離 |0805.01m1Organizational.12 - 01.m |組織的安全性閘道 (例如防火牆) 會強制執行安全性原則，並且會設定為可篩選網域之間的流量、封鎖未經授權的存取，以及用來維護內部有線、內部無線和外部網路部分 (例如網際網路) 之間的隔離 (包括 DMZ)，並且強制執行每個網域的存取控制原則。 |[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|網路中的隔離 |0806.01m2Organizational.12356 - 01.m |組織網路會透過定義的安全界限和一組分級的控制項，在邏輯及實體上進行分割，例如，可公開存取的系統元件子網路會根據組織需求，以邏輯方式與內部網路區隔；而流量則是根據風險評估及其各自的安全性需求，以所需的功能和資料/系統的分類為基礎來進行控制。 |[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|網路中的隔離 |0894.01m2Organizational.7 - 01.m |將實體伺服器、應用程式或資料遷移至虛擬化伺服器時，網路會與生產層級的網路隔離。 |[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|稽核記錄 |1211.09aa3System.4 - 09.aa |組織每九十 (90) 天會檢查一次每個已記錄的涵蓋資訊擷取，檢查該資料是否已清除或仍需要使用。 |[應啟用 Key Vault 中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|網路控制 |0865.09m2Organizational.13 - 09.m |組織 (i) 會使用互相連線安全性協定或其他正式合約，來授權從資訊系統到組織外部其他資訊系統的連線；(ii) 記錄每個連線、介面特性、安全性需求，以及所傳達資訊的性質；(iii) 採用「全部拒絕、允許例外」原則，允許從資訊系統連線到組織外部的其他資訊系統；以及 (iv) 套用預設的拒絕規則，以透過主機型防火牆或連接埠篩選工具在其端點 (工作站、伺服器等) 上阻斷所有流量，但明確允許的服務和連接埠除外。 |[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|商務持續性和風險評量 |1635.12b1Organizational.2 - 12.b |商務持續性的資訊安全性層面會 (i) 以識別事件 (或事件的順序) 為依據，這些事件可能導致組織的重要商務流程中斷 (例如設備故障、人為錯誤、遭竊、火災、自然災害、恐怖主義行動)；(ii) 接著是風險評估，用於判斷這類中斷的機率和影響 (以時間、損毀規模和復原期間來評估)；(iii) 根據風險評估的結果，開發商務持續性策略來識別商務持續性的整體方法；以及 (iv)，建立此策略後，就會由管理階層提供背書，並建立及支持實行這項策略的計畫。 |[金鑰保存庫應啟用清除保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

