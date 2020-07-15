---
title: Azure Security Benchmark 的法規合規性詳細資料
description: Azure Security Benchmark 法規合規性內建方案的詳細資料。 每個控制措施都會對應至一或多個可協助評量的 Azure 原則定義。
ms.date: 06/25/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: f480bee64b729ce35595047fc993588a3dba2236
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2020
ms.locfileid: "85414669"
---
# <a name="details-of-the-azure-security-benchmark-regulatory-compliance-built-in-initiative"></a>Azure Security Benchmark 法規合規性內建方案的詳細資料

下列文章詳細說明 Azure 原則法規合規性內建方案定義如何對應至 Azure Security Benchmark 中的**合規性領域**與**控制項**。
如需此合規性標準的詳細資訊，請參閱 [Azure Security Benchmark](../../../security/benchmarks/overview.md)。 若要了解「擁有權」，請參閱 [Azure 原則原則定義](../concepts/definition-structure.md#type)與[雲端中共同承擔的責任](../../../security/fundamentals/shared-responsibility.md)。

以下是 **Azure 安全性效能評定**控制項的對應。 使用右側的導覽區可直接跳到特定的**合規性領域**。 許多控制措施都是以 [Azure 原則](../overview.md)方案定義實作的。 若要檢閱完整方案定義，請在 Azure 入口網站中開啟 [原則]，然後選取 [定義] 頁面。
然後，尋找並選取 **Azure Security Benchmark** 法規合規性內建方案的定義。

此內建方案會部署為 [Azure Security Benchmark 藍圖範例](../../blueprints/samples/azure-security-benchmark.md)的一部分。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../overview.md)定義相關聯。
> 這些原則可協助您使用工具[存取合規性](../how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是 1：1 或完整對應。 因此，Azure 原則中的**符合規範**只是指原則定義本身，這不保證您完全符合所有控制措施需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性標準的合規性領域、控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/asb_audit.json) \(英文\)。

## <a name="network-security"></a>網路安全性

### <a name="protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**識別碼**：Azure Security Benchmark 1.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在網際網路對應虛擬機器中套用自適性網路強化建議](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 資訊安全中心會分析網際網路對向虛擬機器的流量模式，然後提供降低潛在攻擊面的網路安全性群組規則建議 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[所有網際網路流量都應透過您部署的 Azure 防火牆路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure 資訊安全中心已識別出您的部分子網路未受新一代防火牆的保護。 使用 Azure 防火牆或支援的新一代防火牆來限制對子網路的存取，以保護子網路免於遭受潛在威脅 |AuditIfNotExists, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[App Service 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |此原則會稽核任何未設定為使用虛擬網路服務端點的 App Service。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Kubernetes Services 上應定義授權 IP 範圍](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |僅將 API 存取權授與特定範圍內的 IP 位址，以限制對 Kubernetes Service 管理 API 的存取。 建議僅限存取授權 IP 範圍，以確保只有來自允許網路的應用程式可以存取叢集。 |Audit, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Container Registry。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Cosmos DB。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[事件中樞應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |此原則會稽核任何未設定為使用虛擬網路服務端點的事件中樞。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用網路安全性群組 (NSG) 限制對虛擬機器的存取，以保護您的虛擬機器遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[應停用虛擬機器上的 IP 轉送](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |在虛擬機器的 NIC 上啟用 IP 轉送可讓機器接收傳送到其他目的地的流量。 IP 轉送是極少需要的功能 (例如，當將 VM 作為網路虛擬設備使用時)，因此，這應經過網路安全性小組檢閱。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Key Vault。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Azure 資訊安全中心會依建議監視可能的網路 Just-In-Time (JIT) 存取 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[應關閉虛擬機器上的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |開放的遠端管理連接埠會使您的 VM 暴露在網際網路攻擊的高風險之下。 這些攻擊會嘗試對認證發動暴力密碼破解攻擊，來取得機器的系統管理員存取權。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[MariaDB 伺服器應啟用私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |此原則會稽核未設定為使用私人端點的 MariaDB 伺服器。 如需詳細資料，請造訪 [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink)。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[MySQL 伺服器應啟用私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |此原則會稽核未設定為使用私人端點的 MySQL 伺服器。 如需詳細資料，請造訪 [https://aka.ms/mysqlprivatelink](https://aka.ms/mysqlprivatelink)。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[PostgreSQL 伺服器應啟用私人端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |此原則會稽核未設定為使用私人端點的 PostgreSQL 伺服器。 如需詳細資料，請造訪 [https://aka.ms/pgprivatelink](https://aka.ms/pgprivatelink)。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[服務匯流排應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |此原則會稽核任何未設定為使用虛擬網路服務端點的服務匯流排。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |此原則會稽核任何未設定為使用虛擬網路服務端點的 SQL Server。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[儲存體帳戶應限制網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |應限制對儲存體帳戶的網路存取。 請設定網路規則，只允許來自認可之網路的應用程式存取儲存體帳戶。 若要允許來自特定網際網路或內部部署用戶端的連線，可將存取權授與來自特定 Azure 虛擬網路的流量，或授與公用網際網路 IP 位址範圍 |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[儲存體帳戶應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |此原則會稽核任何未設定為使用虛擬網路服務端點的儲存體帳戶。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[虛擬機器應該連線到已核准的虛擬網路](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |此原則會稽核任何連線到未核准之虛擬網路的虛擬機器。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[虛擬網路應該使用指定的虛擬網路閘道](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |此原則會稽核任何虛擬網路是否預設路由未指向指定的虛擬網路閘道。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |

### <a name="monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>監視和記錄 VNet、子網路與 NIC 的設定和流量

**識別碼**：Azure Security Benchmark 1.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用網路監看員](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 案例層級監視可讓您在端對端網路層級檢視診斷問題。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="protect-critical-web-applications"></a>保護重要的 Web 應用程式

**識別碼**：Azure Security Benchmark 1.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS 不應允許每項資源存取您的 API 應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |跨原始資源共用 (CORS) 不應允許所有網域存取 API 應用程式。 請只允許必要網域與您的 API 應用程式互動。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |
|[CORS 不應允許每項資源存取函式應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |跨原始資源共用 (CORS) 不應允許所有網域存取函式應用程式。 請只允許必要網域與您的函式應用程式互動。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |
|[CORS 不應允許每項資源存取您的 Web 應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |跨原始資源共用 (CORS) 不應允許所有網域存取 Web 應用程式。 請只允許必要網域與您的 Web 應用程式互動。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[確定 Web 應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |用戶端憑證可讓應用程式針對連入要求來要求憑證。 只有具備有效憑證的用戶端才能存取該應用程式。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[應關閉 API 應用程式的遠端偵錯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |遠端偵錯需要在 API 應用程式上開啟輸入連接埠。 應關閉遠端偵錯。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[函式應用程式的遠端偵錯應關閉](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |遠端偵錯需要在函式應用程式上開啟輸入連接埠。 應關閉遠端偵錯。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[應關閉 Web 應用程式的遠端偵錯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |遠端偵錯需要在 Web 應用程式上開啟輸入連接埠。 應關閉遠端偵錯。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="deny-communications-with-known-malicious-ip-addresses"></a>拒絕與已知惡意 IP 位址通訊

**識別碼**：Azure Security Benchmark 1.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在網際網路對應虛擬機器中套用自適性網路強化建議](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 資訊安全中心會分析網際網路對向虛擬機器的流量模式，然後提供降低潛在攻擊面的網路安全性群組規則建議 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[所有網際網路流量都應透過您部署的 Azure 防火牆路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure 資訊安全中心已識別出您的部分子網路未受新一代防火牆的保護。 使用 Azure 防火牆或支援的新一代防火牆來限制對子網路的存取，以保護子網路免於遭受潛在威脅 |AuditIfNotExists, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[應啟用 Azure DDoS 保護標準](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |所有虛擬網路只要其子網路屬於使用公用 IP 的應用程式閘道，就應啟用 DDoS 保護標準。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Azure 資訊安全中心會依建議監視可能的網路 Just-In-Time (JIT) 存取 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="record-network-packets-and-flow-logs"></a>記錄網路封包和流量記錄

**識別碼**：Azure Security Benchmark 1.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用網路監看員](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 案例層級監視可讓您在端對端網路層級檢視診斷問題。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>使用自動化工具來監視網路資源設定並偵測變更

**識別碼**：Azure Security Benchmark 1.11 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[部署必要條件，以稽核 [系統管理範本 - 網路] 中的 Windows VM 設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F985285b7-b97a-419c-8d48-c88cc934c8d8) |此原則會建立客體設定指派，以稽核下列群組原則類別中設定不符合規範的 Windows 虛擬機器：[系統管理範本 - 網路]。 其也會建立系統指派的受控識別，並部署客體設定的 VM 擴充功能。 此原則應只搭配計畫中的對應稽核原則使用。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministrativeTemplatesNetwork_Deploy.json) |
|[部署必要條件，以稽核 [安全性選項 - Microsoft 網路伺服器] 中的 Windows VM 設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86880e5c-df35-43c5-95ad-7e120635775e) |此原則會建立客體設定指派，以稽核下列群組原則類別中設定不符合規範的 Windows 虛擬機器：[安全性選項 - Microsoft 網路伺服器]。 其也會建立系統指派的受控識別，並部署客體設定的 VM 擴充功能。 此原則應只搭配計畫中的對應稽核原則使用。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_Deploy.json) |
|[部署必要條件，以稽核 [安全性選項 - 網路存取] 中的 Windows VM 設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56a3ab2-89d1-44de-ac0d-2ada5962e22a) |此原則會建立客體設定指派，以稽核下列群組原則類別中設定不符合規範的 Windows 虛擬機器：[安全性選項 - 網路存取]。 其也會建立系統指派的受控識別，並部署客體設定的 VM 擴充功能。 此原則應只搭配計畫中的對應稽核原則使用。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_Deploy.json) |
|[部署必要條件，以稽核 [安全性選項 - 網路安全性] 中的 Windows VM 設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36e17963-7202-494a-80c3-f508211c826b) |此原則會建立客體設定指派，以稽核下列群組原則類別中設定不符合規範的 Windows 虛擬機器：[安全性選項 - 網路安全性]。 其也會建立系統指派的受控識別，並部署客體設定的 VM 擴充功能。 此原則應只搭配計畫中的對應稽核原則使用。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkSecurity_Deploy.json) |
|[顯示 [系統管理範本 - 網路] 中的 Windows VM 設定稽核結果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7229bd6a-693d-478a-87f0-1dc1af06f3b8) |此原則應只搭配計畫中的對應部署原則使用。 此定義可讓 Azure 原則處理下列群組原則類別中設定不符合規範的 Windows 虛擬機器稽核結果：[系統管理範本 - 網路]。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministrativeTemplatesNetwork_Audit.json) |
|[顯示 [安全性選項 - Microsoft 網路用戶端] 中的 Windows VM 設定稽核結果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffcbc55c9-f25a-4e55-a6cb-33acb3be778b) |此原則應只搭配計畫中的對應部署原則使用。 此定義可讓 Azure 原則處理下列群組原則類別中設定不符合規範的 Windows 虛擬機器稽核結果：[安全性選項 - Microsoft 網路用戶端]。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkClient_Audit.json) |
|[顯示 [安全性選項 - Microsoft 網路伺服器] 中的 Windows VM 設定稽核結果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fe4ef56-7576-4dc4-8e9c-26bad4b087ce) |此原則應只搭配計畫中的對應部署原則使用。 此定義可讓 Azure 原則處理下列群組原則類別中設定不符合規範的 Windows 虛擬機器稽核結果：[安全性選項 - Microsoft 網路伺服器]。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_Audit.json) |
|[顯示 [安全性選項 - 網路安全性] 中的 Windows VM 設定稽核結果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c028d2a-1889-45f6-b821-31f42711ced8) |此原則應只搭配計畫中的對應部署原則使用。 此定義可讓 Azure 原則處理下列群組原則類別中設定不符合規範的 Windows 虛擬機器稽核結果：[安全性選項 - 網路安全性]。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkSecurity_Audit.json) |

## <a name="logging-and-monitoring"></a>記錄和監視

### <a name="configure-central-security-log-management"></a>設定中央安全性記錄管理

**識別碼**：Azure Security Benchmark 2.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的訂用帳戶上應啟用 Log Analytics 監視代理程式的自動化佈建](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |啟用 Azure 監視器記錄監視代理程式的自動化佈建，以收集安全性資料 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure 監視器記錄設定檔應收集 [寫入]、[刪除] 和 [動作] 分類的記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |此原則可確保記錄設定檔會收集「寫入」、「刪除」和「動作」類別的記錄 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[Azure 監視器應從所有區域收集活動記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |此原則會稽核不從所有 Azure 支援區域 (包括全球) 匯出活動的 Azure 監視器記錄設定檔。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
|[部署必要條件，以稽核 Log Analytics 代理程式未如預期連線的 Windows VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68511db2-bd02-41c4-ae6b-1900a012968a) |此原則會建立客體設定指派，以稽核其 Log Analytics 代理程式未連線到指定工作區的 Windows 虛擬機器。 其也會建立系統指派的受控識別，並部署客體設定的 VM 擴充功能。 此原則應只搭配計畫中的對應稽核原則使用。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_Deploy.json) |
|[顯示 Log Analytics 代理程式未如預期連線的 Windows VM 稽核結果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa030a57e-4639-4e8f-ade9-a92f33afe7ee) |此原則應只搭配計畫中的對應部署原則使用。 此定義可讓 Azure 原則處理 Log Analytics 代理程式未連線到指定工作區的 Windows 虛擬機器稽核結果。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_Audit.json) |
|[Log Analytics 代理程式應該安裝在虛擬機器擴展集上](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |此原則會稽核任何 Windows/Linux 虛擬機器擴展集是否未安裝 Log Analytics 代理程式。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[Log Analytics 代理程式應該安裝在虛擬機器上](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |此原則會稽核任何 Windows/Linux 虛擬機器是否未安裝 Log Analytics 代理程式。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="enable-audit-logging-for-azure-resources"></a>啟用 Azure 資源的稽核記錄

**識別碼**：Azure Security Benchmark 2.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核診斷設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |稽核所選資源類型的診斷設定 |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[應啟用 SQL 伺服器上的稽核](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |應在 SQL Server 上啟用稽核，以追蹤伺服器上所有資料庫的活動，並儲存在稽核記錄中。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[應在 App Service 中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |稽核應用程式中診斷記錄的啟用情形。 如果發生安全性事件或網路遭到損害，這可讓您重新建立活動線索供調查之用 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[應在 Azure Data Lake Store 中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[應啟用 Azure 串流分析中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[應啟用 Batch 帳戶中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[應在 Data Lake Analytics 中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[應啟用事件中樞內的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[應在 IoT 中樞內啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[應啟用 Key Vault 中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[應在 Logic Apps 中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[應在搜尋服務中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[應在服務匯流排中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[應在虛擬機器擴展集中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |建議啟用記錄，以在發生事件或外洩需要調查時，能夠重新建立活動線索。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[SQL 審核設定應已設定動作群組來擷取重要活動](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |AuditActionsAndGroups 屬性至少應包含 SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP、FAILED_DATABASE_AUTHENTICATION_GROUP、BATCH_COMPLETED_GROUP，才可確保完整記錄稽核 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="collect-security-logs-from-operating-systems"></a>從作業系統收集安全性記錄

**識別碼**：Azure Security Benchmark 2.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的訂用帳戶上應啟用 Log Analytics 監視代理程式的自動化佈建](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |啟用 Azure 監視器記錄監視代理程式的自動化佈建，以收集安全性資料 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[部署必要條件，以稽核 Log Analytics 代理程式未如預期連線的 Windows VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68511db2-bd02-41c4-ae6b-1900a012968a) |此原則會建立客體設定指派，以稽核其 Log Analytics 代理程式未連線到指定工作區的 Windows 虛擬機器。 其也會建立系統指派的受控識別，並部署客體設定的 VM 擴充功能。 此原則應只搭配計畫中的對應稽核原則使用。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_Deploy.json) |
|[顯示 Log Analytics 代理程式未如預期連線的 Windows VM 稽核結果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa030a57e-4639-4e8f-ade9-a92f33afe7ee) |此原則應只搭配計畫中的對應部署原則使用。 此定義可讓 Azure 原則處理 Log Analytics 代理程式未連線到指定工作區的 Windows 虛擬機器稽核結果。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_Audit.json) |
|[Log Analytics 代理程式應該安裝在虛擬機器擴展集上](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |此原則會稽核任何 Windows/Linux 虛擬機器擴展集是否未安裝 Log Analytics 代理程式。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[Log Analytics 代理程式應該安裝在虛擬機器上](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |此原則會稽核任何 Windows/Linux 虛擬機器是否未安裝 Log Analytics 代理程式。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="configure-security-log-storage-retention"></a>設定安全性記錄儲存體保留期

**識別碼**：Azure Security Benchmark 2.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 伺服器應設定有 90 天以上的稽核保留天數。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |對稽核保留期間設為 90 天以下的 SQL 伺服器進行稽核。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="enable-alerts-for-anomalous-activity"></a>啟用異常活動的警示

**識別碼**：Azure Security Benchmark 2.7 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 SQL 受控執行個體上啟用進階資料安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |在沒有進階資料安全性的情況下稽核 SQL 受控執行個體。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[應在 SQL 伺服器上啟用進階資料安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |在沒有「進階資料安全性」的情況下稽核 SQL 伺服器 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbda18df3-5e41-4709-add9-2554ce68c966) |建議您在 SQL 受控執行個體上啟用所有進階威脅防護類型。 啟用所有類型可避免面臨 SQL 插入式攻擊、資料庫弱點和任何其他異常活動。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureThreatDetectionTypes_Audit.json) |
|[在 SQL 伺服器的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe756b945-1b1b-480b-8de8-9a0859d5f7ad) |建議您在 SQL 伺服器上啟用所有進階威脅防護類型。 啟用所有類型可避免面臨 SQL 插入式攻擊、資料庫弱點和任何其他異常活動。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureThreatDetectionTypes_Audit.json) |

### <a name="centralize-anti-malware-logging"></a>集中化反惡意程式碼記錄

**識別碼**：Azure Security Benchmark 2.8 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在虛擬機器擴展集上安裝端點保護解決方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |稽核虛擬機器擴展集上端點保護解決方案的存在與健康狀態，以免其遭受威脅及弱點的傷害。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Azure 的 Microsoft Antimalware 應設定為自動更新保護簽章](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |此原則會稽核未設定自動更新 Microsoft Antimalware 保護簽章的任何 Windows 虛擬機器。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[在 Azure 資訊安全中心中監視缺少的 Endpoint Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Azure 資訊安全中心會依建議監視未安裝 Endpoint Protection 代理程式的伺服器 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="identity-and-access-control"></a>身分識別與存取控制

### <a name="maintain-an-inventory-of-administrative-accounts"></a>維護系統管理帳戶的清查

**識別碼**：Azure Security Benchmark 3.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應針對您的訂用帳戶指定最多 3 位擁有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建議您最多指定 3 位訂用帳戶擁有者，以降低遭入侵擁有者導致資料外洩的可能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除。  已取代帳戶是已封鎖而無法登入的帳戶。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |具有擁有者權限的外部帳戶應從您訂用帳戶移除，以避免未受監視的存取。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[應將一個以上的擁有者指派給您的訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |建議指定多位訂用帳戶擁有者，如此才能設定系統管理員存取備援。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="use-dedicated-administrative-accounts"></a>使用專用的系統管理帳戶

**識別碼**：Azure Security Benchmark 3.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應針對您的訂用帳戶指定最多 3 位擁有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建議您最多指定 3 位訂用帳戶擁有者，以降低遭入侵擁有者導致資料外洩的可能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[部署必要條件，以稽核 Administrators 群組包含任何指定成員的 Windows VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F144f1397-32f9-4598-8c88-118decc3ccba) |此原則會建立客體設定指派，以稽核 Administrators 群組包含任何指定成員的 Windows 虛擬機器。 其也會建立系統指派的受控識別，並部署客體設定的 VM 擴充功能。 此原則應只搭配計畫中的對應稽核原則使用。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_Deploy.json) |
|[部署必要條件以稽核 Administrators 群組不包含任何指定成員的 Windows VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93507a81-10a4-4af0-9ee2-34cf25a96e98) |此原則會建立客體設定指派，以稽核 Administrators 群組不包含所有指定成員的 Windows 虛擬機器。 其也會建立系統指派的受控識別，並部署客體設定的 VM 擴充功能。 此原則應只搭配計畫中的對應稽核原則使用。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_Deploy.json) |
|[部署必要條件以稽核 Administrators 群組不只包含指定成員的 Windows VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb821191b-3a12-44bc-9c38-212138a29ff3) |此原則會建立客體設定指派，以稽核 Administrators 群組不只包含指定成員的 Windows 虛擬機器。 其也會建立系統指派的受控識別，並部署客體設定的 VM 擴充功能。 此原則應只搭配計畫中的對應稽核原則使用。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_Deploy.json) |
|[顯示其中 Administrators 群組包含了任一指定成員的 Windows VM 稽核結果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbde62c94-ccca-4821-a815-92c1d31a76de) |此原則應只搭配計畫中的對應部署原則使用。 此定義可讓 Azure 原則處理 Administrators 群組包含任一指定成員的 Windows 虛擬機器稽核結果。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_Audit.json) |
|[從其中 Administrators 群組不包含任何指定成員的 Windows VM 顯示稽核結果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3b44e5d-1456-475f-9c67-c66c4618e85a) |此原則應只搭配計畫中的對應部署原則使用。 此定義可讓 Azure 原則處理 Administrators 群組不包含所有指定成員的 Windows 虛擬機器稽核結果。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_Audit.json) |
|[顯示其中 Administrators 群組不只包含指定成員的 Windows VM 稽核結果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc7cda28-f867-4311-8497-a526129a8d19) |此原則應只搭配計畫中的對應部署原則使用。 此定義可讓 Azure 原則處理 Administrators 群組不只包含指定成員的 Windows 虛擬機器稽核結果。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_Audit.json) |
|[應將一個以上的擁有者指派給您的訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |建議指定多位訂用帳戶擁有者，如此才能設定系統管理員存取備援。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**識別碼**：Azure Security Benchmark 3.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |具備寫入權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |具備擁有者權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |具備讀取權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="use-azure-active-directory"></a>使用 Azure Active Directory

**識別碼**：Azure Security Benchmark 3.9 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應針對 SQL 伺服器佈建 Azure Active Directory 管理員](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |為 SQL Server 稽核 Azure Active Directory 系統管理員的佈建情況，以啟用 Azure AD 驗證。 Azure AD 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[確定已在 API 應用程式上啟用 [向 Azure Active Directory 註冊]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86d97760-d216-4d81-a3ad-163087b2b6c3) |App Service 中的受控服務識別可藉由從應用程式刪除祕密 (例如連接字串中的認證)，讓應用程式更加安全。 在應用程式服務中向 Azure Active Directory 註冊時，應用程式會安全地連線到其他 Azure 服務，而不需要使用者名稱和密碼 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_AD_Enabled.json) |
|[確定已在函式應用程式上啟用 [向 Azure Active Directory 註冊]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0473e7a-a1ba-4e86-afb2-e829e11b01d8) |App Service 中的受控服務識別可藉由從應用程式刪除祕密 (例如連接字串中的認證)，讓應用程式更加安全。 在應用程式服務中向 Azure Active Directory 註冊時，應用程式會安全地連線到其他 Azure 服務，而不需要使用者名稱和密碼 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_AD_Enabled.json) |
|[確定已在 Web 應用程式上啟用 [向 Azure Active Directory 註冊]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa81768c-cb87-4ce2-bfaa-00baa10d760c) |App Service 中的受控服務識別可藉由從應用程式刪除祕密 (例如連接字串中的認證)，讓應用程式更加安全。 在應用程式服務中向 Azure Active Directory 註冊時，應用程式會安全地連線到其他 Azure 服務，而不需要使用者名稱和密碼 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_AD_Enabled.json) |
|[Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |稽核 Service Fabric 中僅透過 Azure Active Directory 的用戶端驗證使用方式 |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="regularly-review-and-reconcile-user-access"></a>定期檢閱並協調使用者存取

**識別碼**：Azure Security Benchmark 3.10 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[已取代帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |已取代帳戶應該從您的訂用帳戶中移除。  已取代帳戶是已封鎖而無法登入的帳戶。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除。  已取代帳戶是已封鎖而無法登入的帳戶。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |具有擁有者權限的外部帳戶應從您訂用帳戶移除，以避免未受監視的存取。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[具有讀取權限的外部帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |請移除您訂用帳戶中，具有讀取權限的外部帳戶，以避免出現未受監視的存取。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[應從訂用帳戶移除具有寫入權限的外部帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |請移除您訂用帳戶中，具有寫入權限的外部帳戶，以避免出現未受監視的存取。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

## <a name="data-protection"></a>資料保護

### <a name="maintain-an-inventory-of-sensitive-information"></a>維護敏感性資訊的清查

**識別碼**：Azure Security Benchmark 4.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應分類 SQL 資料庫中的敏感性資料](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Azure 資訊安全中心會監視您 SQL 資料庫的資料探索與分類掃描結果，並為資料庫中敏感性資料的分類提供建議，以提升監視的成效及安全性 |AuditIfNotExists, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |

### <a name="encrypt-all-sensitive-information-in-transit"></a>加密傳輸中的所有敏感性資訊

**識別碼**：Azure Security Benchmark 4.4 **擁有權**：共用

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |此原則會針對未強制執行 SSL 連線的任何 MySQL 伺服器進行稽核。 適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |此原則會針對未強制執行 SSL 連線的任何 PostgreSQL 伺服器進行稽核。 適用於 PostgreSQL 的 Azure 資料庫偏好使用安全通訊端層 (SSL)，來將用戶端應用程式連接到 PostgreSQL 服務。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[只有在 API 應用程式中才應該要求 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |啟用 FTPS 強制執行以增強安全性 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[只有在函式應用程式中才應該要求 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |啟用 FTPS 強制執行以增強安全性 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[Web 應用程式中應該要求 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |啟用 FTPS 強制執行以增強安全性 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |
|[函式應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[您的 API 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[您的函式應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[您的 Web 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |稽核只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[應啟用儲存體帳戶的安全傳輸](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |稽核儲存體帳戶中安全傳輸的需求。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Web 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="use-an-active-discovery-tool-to-identify-sensitive-data"></a>使用作用中探索工具來識別敏感性資料

**識別碼**：Azure Security Benchmark 4.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 SQL 受控執行個體上啟用進階資料安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |在沒有進階資料安全性的情況下稽核 SQL 受控執行個體。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[應在 SQL 伺服器上啟用進階資料安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |在沒有「進階資料安全性」的情況下稽核 SQL 伺服器 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[應分類 SQL 資料庫中的敏感性資料](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Azure 資訊安全中心會監視您 SQL 資料庫的資料探索與分類掃描結果，並為資料庫中敏感性資料的分類提供建議，以提升監視的成效及安全性 |AuditIfNotExists, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |

### <a name="use-azure-rbac-to-control-access-to-resources"></a>使用 Azure RBAC 來控制資源的存取權

**識別碼**：Azure Security Benchmark 4.6 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核自訂 RBAC 規則的使用方式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |稽核內建角色，例如「擁有者、參與者、讀取者」，而不是自訂的 RBAC 角色，這些角色容易發生錯誤。 使用自訂角色會視為例外狀況，而且需要嚴格審查和威脅模型化 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |若要提供使用者可執行之動作的精細篩選，請使用角色型存取控制 (RBAC) 來管理 Kubernetes Service 叢集中的權限，並設定相關的授權原則。 |Audit, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="encrypt-sensitive-information-at-rest"></a>加密待用的敏感性資訊

**識別碼**：Azure Security Benchmark 4.8 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應加密自動化帳戶變數](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |儲存敏感性資料時，請務必為自動化帳戶變數資產啟用加密 |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[應在虛擬機器上套用磁碟加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Azure 資訊安全中心會依建議監視未啟用磁碟加密的 VM |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric 使用主要叢集憑證，可為節點對節點的通訊提供三層保護 (None、Sign 及 EncryptAndSign)。 設定保護層級可確保所有節點對節點的訊息皆經過加密及數位簽署 |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[SQL 受控執行個體的 TDE 保護裝置應以您自己的金鑰加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |具有自備金鑰支援的透明資料加密 (TDE)，能夠增加 TDE 保護裝置的透明度及控制權、透過 HSM 支援的外部服務提高安全性，以及提升職權劃分。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL 伺服器的 TDE 保護裝置應以您自己的金鑰加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |具有自備金鑰支援的透明資料加密 (TDE)，能夠增加 TDE 保護裝置的透明度及控制權、透過 HSM 支援的外部服務提高安全性，以及提升職權劃分。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[應在 SQL 資料庫上啟用透明資料加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |應啟用透明資料加密，以保護待用資料，並滿足合規性需求 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[未連結的磁碟應加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |此原則會稽核任何未啟用加密的未連結磁碟。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="log-and-alert-on-changes-to-critical-azure-resources"></a>針對重要 Azure 資源的變更留下記錄和發出警示

**識別碼**：Azure Security Benchmark 4.9 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 監視器應從所有區域收集活動記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |此原則會稽核不從所有 Azure 支援區域 (包括全球) 匯出活動的 Azure 監視器記錄設定檔。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

## <a name="vulnerability-management"></a>弱點管理

### <a name="run-automated-vulnerability-scanning-tools"></a>執行自動化弱點掃描工具

**識別碼**：Azure Security Benchmark 5.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[預覽\] 應在虛擬機器上啟用弱點評估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |監視 Azure 資訊安全中心弱點評估在虛擬機器上偵測到的弱點 |AuditIfNotExists, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[應在 SQL 受控執行個體上啟用弱點評定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |稽核每個未啟用週期性弱點評估掃描的 SQL 受控執行個體。 弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[弱點評估應於您的 SQL 伺服器上啟用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |稽核未啟用週期性弱點評估掃描的 Azure SQL 伺服器。 弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="deploy-automated-operating-system-patch-management-solution"></a>部署自動化的作業系統修補程式管理解決方案

**識別碼**：Azure Security Benchmark 5.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[確定 '.NET Framework' 在作為 API 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc2e7ca55-f62c-49b2-89a4-d41eb661d2f0) |.NET Framework 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 Web 應用程式的最新 .NET Framework 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_NETFrameWork_Latest.json) |
|[確定 '.NET Framework' 在作為函式應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10c1859c-e1a7-4df3-ab97-a487fa8059f6) |.NET Framework 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 Web 應用程式的最新 .NET Framework 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_NETFrameWork_Latest.json) |
|[確定 '.NET Framework' 在作為 Web 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F843664e0-7563-41ee-a9cb-7522c382d2c4) |.NET Framework 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 Web 應用程式的最新 .NET Framework 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_NETFrameWork_Latest.json) |
|[應在虛擬機器擴展集上安裝系統更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |稽核是否遺漏了任何應安裝的系統安全性更新和重大更新，以確保您的 Windows 及 Linux 虛擬機器擴展集安全無虞。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[您應在機器上安裝系統更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Azure 資訊安全中心會依建議監視伺服器上缺少的安全性系統更新 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="deploy-automated-third-party-software-patch-management-solution"></a>部署自動化的第三方軟體修補程式管理解決方案

**識別碼**：Azure Security Benchmark 5.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[確定 'Java' 在作為 API 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Java 會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 API 應用程式的最新 Python 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[確定 'Java' 在作為函式應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Java 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於函式應用程式的最新 Java 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[確定 'Java' 在作為 Web 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Java 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 Web 應用程式的最新 Java 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |
|[確定 'PHP' 在作為 API 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |PHP 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 API 應用程式的最新 PHP 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[確定 'PHP' 在作為函式應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab965db2-d2bf-4b64-8b39-c38ec8179461) |PHP 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於函式應用程式的最新 PHP 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_PHP_Latest.json) |
|[確定 'PHP' 在作為 Web 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |PHP 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 Web 應用程式的最新 PHP 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |
|[確定 'Python' 在作為 API 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Python 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 API 應用程式的最新 Python 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[確定 'Python' 在作為函式應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Python 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於函式應用程式的最新 Python 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[確定 'Python' 在作為 Web 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Python 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 Web 應用程式的最新 Python 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |
|[Kubernetes 服務應升級為不易受攻擊的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |將您的 Kubernetes 服務叢集升級為較新的 Kubernetes 版本，以防禦您目前 Kubernetes 版本中的已知弱點。 Kubernetes 版本 1.11.9 +、1.12.7+、1.13.5+ 及 1.14.0+ 中已修補弱點 CVE-2019-9946 |Audit, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

### <a name="use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>使用風險評等程序來排定所發現弱點的補救優先順序

**識別碼**：Azure Security Benchmark 5.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應補救容器安全性設定中的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在已安裝 Docker 且在 Azure 資訊安全中心顯示為建議的電腦上，稽核安全性設定中的弱點。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[您應在機器上修復安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azure 資訊安全中心會依建議監視不符合設定基準的伺服器 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[應修復虛擬機器擴展集上安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[應修復 SQL 資料庫的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |監視弱點評定掃描結果及如何補救資料庫弱點的建議。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[弱點評量解決方案應修復弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Azure 資訊安全中心會依建議監視弱點評定解決方案偵測到的弱點，以及不具弱點評定解決方案的 VM。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

## <a name="inventory-and-asset-management"></a>清查和資產管理

### <a name="use-only-approved-applications"></a>僅使用已核准的應用程式

**識別碼**：Azure Security Benchmark 6.8 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在機器上啟用將安全應用程式列入允許清單的自適性應用程式控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |啟用應用程式控制項，以定義在您的電腦上執行的已知安全應用程式清單，並在其他應用程式執行時發出警示。 這有助於強化機器，以防範惡意程式碼的攻擊。 為了簡化設定和維護規則的程序，資訊安全中心使用機器學習來分析在每個機器上執行的應用程式，並建議已知安全的應用程式清單。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AppWhitelistMonitoring_Audit.json) |

### <a name="use-only-approved-azure-services"></a>僅使用已核准的 Azure 服務

**識別碼**：Azure Security Benchmark 6.9 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[儲存體帳戶應移轉至新的 Azure Resource Manager 資源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37e0d2fe-28a5-43d6-a273-67d37d1f5606) |在您的儲存體帳戶使用新的 Azure Resource Manager 以加強安全性，除了增強存取控制 (RBAC)、改善稽核、提供以 Azure Resource Manager 為基礎的部署及治理、受控身分識別的存取、金鑰保存庫的祕密存取、以 Azure AD 為基礎的驗證，還支援標記和資源群組，讓安全性管理更加輕鬆 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |
|[虛擬機器應遷移到新的 Azure Resource Manager 資源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |在您的虛擬機器使用新 Azure Resource Manager 以加強安全性，除了增強存取控制 (RBAC)、改善稽核、提供以 Azure Resource Manager 為基礎的部署及治理、受控身分識別的存取、金鑰保存庫的祕密存取、以 Azure AD 為基礎的驗證，還支援標記和資源群組，讓安全性管理更加輕鬆。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="implement-approved-application-list"></a>實作已核准的應用程式清單

**識別碼**：Azure Security Benchmark 6.10 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在機器上啟用將安全應用程式列入允許清單的自適性應用程式控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |啟用應用程式控制項，以定義在您的電腦上執行的已知安全應用程式清單，並在其他應用程式執行時發出警示。 這有助於強化機器，以防範惡意程式碼的攻擊。 為了簡化設定和維護規則的程序，資訊安全中心使用機器學習來分析在每個機器上執行的應用程式，並建議已知安全的應用程式清單。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AppWhitelistMonitoring_Audit.json) |

## <a name="secure-configuration"></a>安全設定

### <a name="maintain-secure-azure-resource-configurations"></a>維護安全的 Azure 資源設定

**識別碼**：Azure Security Benchmark 7.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[預覽\]：Kubernetes Services 上應定義 Pod 安全性原則](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3abeb944-26af-43ee-b83d-32aaf060fb94) |移除不必要的應用程式權限來定義 Pod 安全性原則，以減少攻擊媒介。 建議您將 Pod 安全性原則設定為僅允許 Pod 存取有權存取的資源。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnablePSP_KubernetesService_Audit.json) |

### <a name="maintain-secure-operating-system-configurations"></a>維護安全的作業系統設定

**識別碼**：Azure Security Benchmark 7.4 **擁有權**：共用

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應補救容器安全性設定中的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在已安裝 Docker 且在 Azure 資訊安全中心顯示為建議的電腦上，稽核安全性設定中的弱點。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[您應在機器上修復安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azure 資訊安全中心會依建議監視不符合設定基準的伺服器 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[應修復虛擬機器擴展集上安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="implement-automated-configuration-monitoring-for-azure-services"></a>為 Azure 服務實作自動化的設定監視

**識別碼**：Azure Security Benchmark 7.9 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[預覽\]：Kubernetes Services 上應定義 Pod 安全性原則](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3abeb944-26af-43ee-b83d-32aaf060fb94) |移除不必要的應用程式權限來定義 Pod 安全性原則，以減少攻擊媒介。 建議您將 Pod 安全性原則設定為僅允許 Pod 存取有權存取的資源。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnablePSP_KubernetesService_Audit.json) |

### <a name="implement-automated-configuration-monitoring-for-operating-systems"></a>為作業系統實作自動化的設定監視

**識別碼**：Azure Security Benchmark 7.10 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應補救容器安全性設定中的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在已安裝 Docker 且在 Azure 資訊安全中心顯示為建議的電腦上，稽核安全性設定中的弱點。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[您應在機器上修復安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azure 資訊安全中心會依建議監視不符合設定基準的伺服器 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[應修復虛擬機器擴展集上安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="manage-azure-secrets-securely"></a>安全地管理 Azure 祕密

**識別碼**：Azure Security Benchmark 7.11 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault 物件應該要可復原](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |此原則會稽核金鑰保存庫物件是否無法復原。 虛刪除功能可在即使已完成刪除作業後，仍有效地保留資源達指定保留期間 (90天)，並同時提供已刪除物件的外觀。 「清除保護」開啟時，必須等到 90 天的保留期間過後，才能清除處於已刪除狀態的保存庫或物件。 這些保存庫和物件仍可復原，使客戶確信系統會遵循保留原則。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="manage-identities-securely-and-automatically"></a>安全且自動地管理身分識別 

**識別碼**：Azure Security Benchmark 7.12 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 應用程式應該使用受控識別](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |使用受控識別來增強驗證安全性 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[函式應用程式應該使用受控識別](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |使用受控識別來增強驗證安全性 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Web 應用程式應該使用受控識別](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |使用受控識別來增強驗證安全性 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

## <a name="malware-defense"></a>惡意程式碼防禦

### <a name="use-centrally-managed-anti-malware-software"></a>使用集中管理的反惡意程式碼軟體

**識別碼**：Azure Security Benchmark 8.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在虛擬機器擴展集上安裝端點保護解決方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |稽核虛擬機器擴展集上端點保護解決方案的存在與健康狀態，以免其遭受威脅及弱點的傷害。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[在 Azure 資訊安全中心中監視缺少的 Endpoint Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Azure 資訊安全中心會依建議監視未安裝 Endpoint Protection 代理程式的伺服器 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-anti-malware-software-and-signatures-are-updated"></a>確保更新反惡意程式碼軟體和簽章

**識別碼**：Azure Security Benchmark 8.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 的 Microsoft Antimalware 應設定為自動更新保護簽章](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |此原則會稽核未設定自動更新 Microsoft Antimalware 保護簽章的任何 Windows 虛擬機器。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |

## <a name="data-recovery"></a>資料復原

### <a name="ensure-regular-automated-back-ups"></a>確保會定期自動備份

**識別碼**：Azure Security Benchmark 9.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |此原則有助於在所有虛擬機器都啟用 Azure 備份服務時進行稽核。 Azure 備份是符合成本效益的一鍵備份解決方案，能夠簡化資料復原，且較其他雲端備份服務易於啟用。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |此原則會稽核所有未啟用異地備援備份之適用於 MariaDB 的 Azure 資料庫。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |此原則會稽核所有未啟用異地備援備份的適用於 MySQL 的 Azure 資料庫。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |此原則會稽核所有未啟用異地備援備份的適用於 PostgreSQL 的 Azure 資料庫。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[應為 Azure SQL Database 啟用長期異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |此原則會稽核所有未啟用長期異地備援備份的 Azure SQL Database。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>執行完整的系統備份，並備份客戶管理的任何金鑰

**識別碼**：Azure Security Benchmark 9.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |此原則有助於在所有虛擬機器都啟用 Azure 備份服務時進行稽核。 Azure 備份是符合成本效益的一鍵備份解決方案，能夠簡化資料復原，且較其他雲端備份服務易於啟用。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |此原則會稽核所有未啟用異地備援備份之適用於 MariaDB 的 Azure 資料庫。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |此原則會稽核所有未啟用異地備援備份的適用於 MySQL 的 Azure 資料庫。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |此原則會稽核所有未啟用異地備援備份的適用於 PostgreSQL 的 Azure 資料庫。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[應為 Azure SQL Database 啟用長期異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |此原則會稽核所有未啟用長期異地備援備份的 Azure SQL Database。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="ensure-protection-of-backups-and-customer-managed-keys"></a>確保備份和客戶管理的金鑰受到保護

**識別碼**：Azure Security Benchmark 9.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault 物件應該要可復原](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |此原則會稽核金鑰保存庫物件是否無法復原。 虛刪除功能可在即使已完成刪除作業後，仍有效地保留資源達指定保留期間 (90天)，並同時提供已刪除物件的外觀。 「清除保護」開啟時，必須等到 90 天的保留期間過後，才能清除處於已刪除狀態的保存庫或物件。 這些保存庫和物件仍可復原，使客戶確信系統會遵循保留原則。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

## <a name="incident-response"></a>事件回應

### <a name="provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**識別碼**：Azure Security Benchmark 10.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為您的訂用帳戶提供安全性連絡人電子郵件地址](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |輸入電子郵件地址，以在 Azure 資訊安全中心偵測到資源洩露時收到通知 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |
|[應為您的訂用帳戶提供安全性連絡人電話號碼](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4d66858-c922-44e3-9566-5cdb7a7be744) |輸入要在 Azure 資訊安全中心偵測到資源洩露時，用來接收通知的電話號碼 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_phone_number.json) |
|[SQL 受控執行個體的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3965c43d-b5f4-482e-b74a-d89ee0e0b3a8) |請確認已在進階資料安全性設定中的 [警訊接收者] 欄位提供電子郵件地址。 當在 SQL 受控執行個體上偵測到異常活動時，此電子郵件地址就會收到警訊通知。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurityEmails_Audit.json) |
|[SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9677b740-f641-4f3c-b9c5-466005c85278) |請確認已在進階資料安全性伺服器設定中的 [警訊接收者] 欄位提供電子郵件地址。 當在 SQL 伺服器上偵測到異常活動時，此電子郵件地址就會收到警訊通知。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurityEmails_Audit.json) |
|[應在 SQL 受控執行個體進階資料安全性設定中，啟用傳給系統管理員和訂用帳戶擁有者的電子郵件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faeb23562-188d-47cb-80b8-551f16ef9fff) |稽核 SQL 受控執行個體進階威脅防護設定中是否已啟用 [傳給系統管理員和訂用帳戶擁有者的電子郵件通知]。 此設定可確保在 SQL 受控執行個體上偵測到的異常活動都會盡快回報給系統管理員。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurityEmailAdmins_Audit.json) |
|[應在 SQL 伺服器進階資料安全性設定中啟用傳給系統管理員和訂用帳戶擁有者的通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc8343d2f-fdc9-4a97-b76f-fc71d1163bfc) |稽核 SQL 伺服器進階威脅防護設定中是否已啟用 [以電子郵件傳送通知給系統管理員和訂用帳戶擁有者]。 這可確保在 SQL 伺服器上偵測到的異常活動都會盡快回報給系統管理員。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurityEmailAdmins_Audit.json) |

> [!NOTE]
> 特定 Azure 原則定義的可用性可能因 Azure Government 和其他國家雲端而異。

## <a name="next-steps"></a>後續步驟

有關 Azure 原則的其他文章：

- [法規合規性](../concepts/regulatory-compliance.md)概觀。
- 請參閱[方案定義結構](../concepts/initiative-definition-structure.md)。
- 在 [Azure 原則範例](./index.md)檢閱其他範例。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
