---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3209fd179fa43f38c9b3d415a942a55b41d1346d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98860053"
---
## <a name="azure-security-benchmark"></a>Azure 安全性效能評定

[Azure 安全性基準](../../../../articles/security/benchmarks/overview.md)提供如何在 Azure 上保護雲端解決方案的建議。 若要查看此服務如何完全對應到 Azure 安全性基準，請參閱 [Azure 安全性基準對應檔案](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - Azure 安全性基準](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路安全性 |NS-1 |執行內部流量的安全性 |[Kubernetes Services 上應定義授權 IP 範圍](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|網路安全性 |NS-4 |保護應用程式和服務免于外部網路攻擊 |[Kubernetes Services 上應定義授權 IP 範圍](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|特殊權限存取 |PA-7 |遵循足夠的系統管理 (最低許可權準則)  |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|資料保護 |DP-4 |加密傳輸中的機密資訊 |[在 Kubernetes 叢集中強制使用 HTTPS 輸入](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a5b4dca-0b6f-4cf5-907c-56316bc1bf3d) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[您的叢集應安裝及啟用適用於 Kubernetes Service (AKS) 的 Azure 原則附加元件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a15ec92-a229-4763-bb14-0ea34a568f8d) |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_AzurePolicyAddOn_Audit.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[在 Kubernetes 叢集中不允許特殊權限的容器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[確保容器 CPU 及記憶體資源限制未超過 Kubernetes 叢集中指定的限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe345eecc-fa47-480f-9e88-67dcc122b164) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[確保容器只會接聽 Kubernetes 叢集中允許的連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F440b515e-a580-421e-abeb-b159a61ddcbc) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[確保 Kubernetes 叢集中只有允許的容器映像](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffebd0533-8e55-448f-b837-bd0e06f16469) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[確保服務只會接聽 Kubernetes 叢集中允許的連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F233a2a17-77ca-4fb1-9b6b-69223d272a44) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[Kubernetes 叢集中的容器不得共用主機處理序識別碼或主機 IPC 命名空間](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/BlockHostNamespace.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[Kubernetes 叢集中的容器只能使用允許的 AppArmor 設定檔](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/EnforceAppArmorProfile.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[Kubernetes 叢集中的容器只能使用允許的功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedCapabilities.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[Kubernetes 叢集中的容器應使用唯讀的根檔案系統執行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ReadOnlyRootFileSystem.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[Kubernetes 叢集中的 Pod hostPath 磁碟區只能使用允許的主機路徑](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AllowedHostPaths.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[Kubernetes 叢集中的 Pod 及容器只能使用核准的使用者與群組識別碼執行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AllowedUsersGroups.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[Kubernetes 叢集中的 Pod 只能使用核准的主機網路及連接埠範圍](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|狀況和弱點管理 |PV-2 |維持 Azure 服務的安全設定 |[Kubernetes 叢集不應允許容器提升權限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilegeEscalation.json) |
|狀況和弱點管理 |PV-7 |快速且自動補救軟體弱點 |[Kubernetes 服務應升級為不易受攻擊的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure 安全性效能評定 v1

[Azure 安全性基準](../../../../articles/security/benchmarks/overview.md)提供如何在 Azure 上保護雲端解決方案的建議。 若要查看此服務如何完全對應到 Azure 安全性基準，請參閱 [Azure 安全性基準對應檔案](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - Azure 安全性基準](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路安全性 |1.1 |在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源 |[Kubernetes Services 上應定義授權 IP 範圍](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|資料保護 |4.6 |使用 Azure RBAC 來控制資源的存取權 |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|弱點管理 |5.3 |部署自動化的第三方軟體修補程式管理解決方案 |[Kubernetes 服務應升級為不易受攻擊的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure 基礎基準測試

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - CIS Microsoft Azure 基礎基準測試 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md)。
如需關於此合規性標準的詳細資訊，請參閱 [CIS Microsoft Azure基礎基準測試](https://www.cisecurity.org/benchmark/azure/)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|其他安全性考量 |8.5 |在 Azure Kubernetes Services 中啟用角色型存取控制 (RBAC) |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC 層級3

若要查看所有 Azure 服務的可用 Azure 原則內建如何對應到此合規性標準，請參閱 [Azure 原則法規合規性-CMMC 層級 3](../../../../articles/governance/policy/samples/cmmc-l3.md)。
如需此合規性標準的詳細資訊，請參閱 [網路安全性成熟度模型認證 (CMMC) ](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|存取控制 |AC. 1.001 |限制資訊系統存取授權的使用者、代表授權使用者的處理常式，以及裝置 (包括) 其他資訊系統。 |[Kubernetes 叢集中的 Pod 只能使用核准的主機網路及連接埠範圍](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|存取控制 |AC. 1.001 |限制資訊系統存取授權的使用者、代表授權使用者的處理常式，以及裝置 (包括) 其他資訊系統。 |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|存取控制 |AC. 1.002 |將資訊系統存取許可權制為允許授權使用者執行的交易和函式類型。 |[Kubernetes 叢集中的 Pod 只能使用核准的主機網路及連接埠範圍](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|存取控制 |AC. 1.002 |將資訊系統存取許可權制為允許授權使用者執行的交易和函式類型。 |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|存取控制 |AC. 2.007 |使用最低許可權原則，包括特定的安全性功能和特殊許可權帳戶。 |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|存取控制 |AC. 2.016 |根據已核准的授權來控制 CUI 流程。 |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|組態管理 |CM. 2.062 |藉由設定組織系統只提供基本功能，來採用最少功能的原則。 |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|組態管理 |CM. 3.068 |限制、停用或防止使用不必要的程式、函式、連接埠、通訊協定和服務。 |[Kubernetes 叢集中的 Pod 只能使用核准的主機網路及連接埠範圍](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|風險評估 |2.143 |根據風險評估來補救弱點。 |[Kubernetes 服務應升級為不易受攻擊的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |
|系統與通訊保護 |SC. 1.175 |監視、控制和保護通訊 (也就是組織系統所傳輸或接收的資訊)，其範圍是組織系統的外部界限和關鍵內部界限。 |[Kubernetes 叢集中的 Pod 只能使用核准的主機網路及連接埠範圍](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|系統與通訊保護 |SC. 3.177 |使用 FIPS 驗證的密碼編譯來保護 CUI 的機密性。 |[Azure Kubernetes Service 叢集中的作業系統和資料磁碟都應該使用客戶自控金鑰加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d7be79c-23ba-4033-84dd-45e2a5ccdd67) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_CMK_Deny.json) |
|系統與通訊保護 |SC. 3.183 |預設拒絕網路通訊流量，並允許依例外狀況的網路通訊流量 (例如，[全部拒絕]、[允許例外狀況]) 。 |[Kubernetes 叢集中的 Pod 只能使用核准的主機網路及連接埠範圍](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|系統和資訊完整性 |1.210 |及時識別、報告和修正資訊和資訊系統瑕疵。 |[Kubernetes 服務應升級為不易受攻擊的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)。
如需此合規性標準的詳細資訊，請參閱 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|權限管理 |1149.01c2System.9 - 01.c |當組織所定義的內容允許使用者具有自行處理權時，組織會藉由讓授權的使用者決定企業合作夥伴的存取權，來促進檔案共用，並採用手動程序或自動化機制來協助使用者做出資訊共用/共同作業的決策。 |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|權限管理 |1153.01c3System.35 - 01.c |所有未明確要求的檔案系統存取都會停用，只有授權使用者能夠存取使用者履行工作職責時明確要求的項目。 |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|權責區分 |1229.09c1Organizational.1 - 09.c |權責區分是用來限制未經授權或無意間修改資訊和系統的風險。 |[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md)。
如需此合規性標準的詳細資訊，請參閱 [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|系統和資訊完整性 |3.14.1 |及時識別、報告及更正系統缺陷。 |[Kubernetes 服務應升級為不易受攻擊的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

