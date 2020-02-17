---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 6f0bc57bf73ae1bb44f022e399c941418ccafc03
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169902"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[必須將自訂的 IPsec/IKE 原則套用至所有 Azure 虛擬網路閘道連線](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VPNGateways_CustomIpSecPolicies_Audit.json) |此原則可確保所有 Azure 虛擬網路閘道連線都使用自訂的網際網路通訊協定安全性 (Ipsec)/網際網路金鑰交換 (IKE) 原則。 支援的演算法和金鑰強度 - https://aka.ms/AA62kb0 |Audit, Disabled |1.0.0 |
|[App Service 應該使用虛擬網路服務端點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |此原則會稽核任何未設定為使用虛擬網路服務端點的 App Service。 |AuditIfNotExists, Disabled |1.0.0 |
|[Azure VPN 閘道不應使用「基本」SKU](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VPNGateways_BasicSKU_Audit.json) |此原則可確保 VPN 閘道不使用「基本」SKU。 |Audit, Disabled |1.0.0 |
|[Cosmos DB 應該使用虛擬網路服務端點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Cosmos DB。 |Audit, Disabled |1.0.0 |
|[於虛擬網路建立時部署網路監看員](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |此原則會在具有虛擬網路的區域中建立網路監看員資源。 您必須確定名為 networkWatcherRG 的資源群組是否存在，其將用來部署網路監看員執行個體。 |DeployIfNotExists |1.0.0 |
|[事件中樞應該使用虛擬網路服務端點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |此原則會稽核任何未設定為使用虛擬網路服務端點的事件中樞。 |AuditIfNotExists, Disabled |1.0.0 |
|[閘道子網路不應設定網路安全性群組](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |若閘道子網路中設定了網路安全性群組，此原則將予以拒絕。 為閘道子網路指派網路安全性群組，將導致閘道停止運作。 |deny |1.0.0 |
|[Key Vault 應該使用虛擬網路服務端點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Key Vault。 |Audit, Disabled |1.0.0 |
|[網路介面應停用 IP 轉送](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkIPForwardingNic_Deny.json) |此原則會拒絕已啟用 IP 轉送的網路介面。 IP 轉送的設定會使 Azure 停止檢查網路介面的來源和目的地。 這應該由網路安全性小組來檢閱。 |deny |1.0.0 |
|[網路介面不應設定公用 IP](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkPublicIPNic_Deny.json) |此原則會拒絕設定了任何公用 IP 的網路介面。 公用 IP 位址可讓網際網路資源對 Azure 資源進行輸入通訊，以及讓 Azure 資源對網際網路進行輸出通訊。 這應該由網路安全性小組來檢閱。 |deny |1.0.0 |
|[應啟用網路監看員](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 案例層級監視可讓您在端對端網路層級檢視診斷問題。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。 |auditIfNotExists |1.0.0 |
|[應禁止從網際網路存取 RDP](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |此原則會稽核任何允許從網際網路存取 RDP 的網路安全性規則 |Audit, Disabled |1.0.0 |
|[服務匯流排應該使用虛擬網路服務端點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |此原則會稽核任何未設定為使用虛擬網路服務端點的服務匯流排。 |AuditIfNotExists, Disabled |1.0.0 |
|[SQL Server 應該使用虛擬網路服務端點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |此原則會稽核任何未設定為使用虛擬網路服務端點的 SQL Server。 |AuditIfNotExists, Disabled |1.0.0 |
|[應禁止從網際網路存取 SSH](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |此原則會稽核任何允許從網際網路存取 SSH 的網路安全性規則 |Audit, Disabled |1.0.0 |
|[儲存體帳戶應該使用虛擬網路服務端點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |此原則會稽核任何未設定為使用虛擬網路服務端點的儲存體帳戶。 |Audit, Disabled |1.0.0 |
|[虛擬機器應該連線到已核准的虛擬網路](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |此原則會稽核任何連線到未核准之虛擬網路的虛擬機器。 |Audit, Deny, Disabled |1.0.0 |
|[虛擬網路應該使用指定的虛擬網路閘道](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |此原則會稽核任何虛擬網路是否預設路由未指向指定的虛擬網路閘道。 |AuditIfNotExists, Disabled |1.0.0 |
