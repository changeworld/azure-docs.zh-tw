---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c993bd756419d3ef3efa20e4397a282423870b60
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98859909"
---
## <a name="azure-security-benchmark"></a>Azure 安全性效能評定

[Azure 安全性基準](../../../../articles/security/benchmarks/overview.md)提供如何在 Azure 上保護雲端解決方案的建議。 若要查看此服務如何完全對應到 Azure 安全性基準，請參閱 [Azure 安全性基準對應檔案](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - Azure 安全性基準](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路安全性 |NS-1 |執行內部流量的安全性 |[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|網路安全性 |NS-2 |將私人網路連接在一起 |[容器登錄應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|網路安全性 |NS-3 |建立 Azure 服務的私人網路存取 |[容器登錄應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|資料保護 |DP-5 |加密待用的機密資料 |[應使用客戶自控金鑰 (CMK) 進行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|狀況和弱點管理 |PV-6 |執行軟體弱點評定 |[應補救 Azure Container Registry 映像中的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure 安全性效能評定 v1

[Azure 安全性基準](../../../../articles/security/benchmarks/overview.md)提供如何在 Azure 上保護雲端解決方案的建議。 若要查看此服務如何完全對應到 Azure 安全性基準，請參閱 [Azure 安全性基準對應檔案](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - Azure 安全性基準](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路安全性 |1.1 |在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源 |[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC 層級3

若要查看所有 Azure 服務的可用 Azure 原則內建如何對應到此合規性標準，請參閱 [Azure 原則法規合規性-CMMC 層級 3](../../../../articles/governance/policy/samples/cmmc-l3.md)。
如需此合規性標準的詳細資訊，請參閱 [網路安全性成熟度模型認證 (CMMC) ](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|存取控制 |AC. 1.001 |限制資訊系統存取授權的使用者、代表授權使用者的處理常式，以及裝置 (包括) 其他資訊系統。 |[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|存取控制 |AC. 1.002 |將資訊系統存取許可權制為允許授權使用者執行的交易和函式類型。 |[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|存取控制 |AC. 2.016 |根據已核准的授權來控制 CUI 流程。 |[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|組態管理 |CM. 3.068 |限制、停用或防止使用不必要的程式、函式、連接埠、通訊協定和服務。 |[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|風險評估 |2.143 |根據風險評估來補救弱點。 |[應補救 Azure Container Registry 映像中的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |
|系統與通訊保護 |SC. 1.175 |監視、控制和保護通訊 (也就是組織系統所傳輸或接收的資訊)，其範圍是組織系統的外部界限和關鍵內部界限。 |[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|系統與通訊保護 |SC. 3.177 |使用 FIPS 驗證的密碼編譯來保護 CUI 的機密性。 |[應使用客戶自控金鑰 (CMK) 進行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|系統與通訊保護 |SC. 3.183 |預設拒絕網路通訊流量，並允許依例外狀況的網路通訊流量 (例如，[全部拒絕]、[允許例外狀況]) 。 |[不應允許不受限制的網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)。
如需此合規性標準的詳細資訊，請參閱 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路中的隔離 |0805.01m1Organizational.12 - 01.m |組織的安全性閘道 (例如防火牆) 會強制執行安全性原則，並且會設定為可篩選網域之間的流量、封鎖未經授權的存取，以及用來維護內部有線、內部無線和外部網路部分 (例如網際網路) 之間的隔離 (包括 DMZ)，並且強制執行每個網域的存取控制原則。 |[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|網路中的隔離 |0806.01m2Organizational.12356 - 01.m |組織網路會透過定義的安全界限和一組分級的控制項，在邏輯及實體上進行分割，例如，可公開存取的系統元件子網路會根據組織需求，以邏輯方式與內部網路區隔；而流量則是根據風險評估及其各自的安全性需求，以所需的功能和資料/系統的分類為基礎來進行控制。 |[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|網路中的隔離 |0894.01m2Organizational.7 - 01.m |將實體伺服器、應用程式或資料遷移至虛擬化伺服器時，網路會與生產層級的網路隔離。 |[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|網路控制 |0868.09m3Organizational.18 - 09.m |組織會建立防火牆設定，以限制涵蓋資料環境所需項目的輸入和輸出流量。 |[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|網路控制 |0869.09m3Organizational.19 - 09.m |保護並同步路由器設定檔。 |[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|網路控制 |0870.09m3Organizational.20 - 09.m |拒絕 Proxy 的所有存取，但不包括明確需要的主機、連接埠和服務。 |[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|網路控制 |0871.09m3Organizational.22 - 09.m |授權的 DNS 伺服器會區分為內部和外部角色。 |[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

