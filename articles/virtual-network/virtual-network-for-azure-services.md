---
title: Azure 服務的虛擬網路
titlesuffix: Azure Virtual Network
description: 瞭解如何將專用的 Azure 服務部署到虛擬網路，並瞭解這些部署所提供的功能。
services: virtual-network
documentationcenter: na
author: mohnader
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: kumud
ms.openlocfilehash: 7e53127b51116c87dd8b92ea3cd87ebf1892d38b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999683"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>將專用 Azure 服務部署至虛擬網路

若在[虛擬網路](virtual-networks-overview.md)中部署專用 Azure 服務，可以使用私人 IP 位址，私下與該服務資源通訊。

![部署在虛擬網路中的服務](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

部署在虛擬網路中的服務有下列功能：

- 虛擬網路中的資源可以透過私人 IP 位址，私下互相通訊。 例如，在虛擬網路中，直接在於虛擬機器上執行的 HDInsight 和 SQL Server 之間傳輸資料。
- 內部部署資源可以透過[站對站 VPN (VPN 閘道)](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) 或 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，使用私人 IP 位址，存取虛擬網路中的資源。
- 虛擬網路則可以使用私人 IP 位址進行[對等互連](virtual-network-peering-overview.md)，來讓虛擬網路中的資源彼此通訊。
- 虛擬網路中的服務執行個體通常完全由 Azure 服務管理。 這包括監視資源的健康情況和負載調整。
- 服務執行個體已部署至虛擬網路中的子網路。 根據服務提供的指導，必須透過[網路安全性群組](security-overview.md#network-security-groups)，開放子網路的輸入和輸出網路存取。
- 某些服務也會對其部署所在的子網路施加限制，以限制原則的應用、路由或結合相同子網內的 VM 和服務資源。 請檢查每個服務的特定限制，因為它們可能會隨著時間而改變。 這類服務的範例包括 Azure NetApp Files、專用 HSM、Azure Container Instances、App Service。 
- (選擇性) 服務可能需要[委派的子網路](virtual-network-manage-subnet.md#add-a-subnet)，作為子網路可以裝載特定服務的明確識別項。 藉由委派，子網路取得明確的權限，可在委派的子網路中建立服務特定資源。
- 請在[具有委派子網的虛擬網路](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet)上參閱 REST API 回應的範例。 您可以透過[可用委派](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API，取得使用委派子網模型的完整服務清單。

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>可以部署至虛擬網路的服務

|類別|服務| 專用的<sup>1</sup> 個子網
|-|-|-|
| 計算 | 虛擬機器：[Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[雲端服務](https://msdn.microsoft.com/library/azure/jj156091) \(英文\)：僅虛擬網路 (傳統)<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| 否 <br/> 否 <br/> 否 <br/> 否<sup>2</sup>
| 網路 | [應用程式閘道 - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 防火牆](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  <br/> [Azure Bastion](../bastion/bastion-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[網路虛擬設備](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)| 是 <br/> 是 <br/> 是 <br/> 是 <br/> 否
|資料|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL 受控執行個體](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| 是 <br/> 是 <br/> 
|分析 | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |否<sup>2</sup> <br/> 否<sup>2</sup> <br/> 
| 身分識別 | [Azure Active Directory 網域服務](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |否 <br/>
| 容器 | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 容器執行個體 (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service 引擎](https://github.com/Azure/acs-engine)搭配 Azure 虛擬網路 CNI [外掛程式](https://github.com/Azure/acs-engine/tree/master/examples/vnet)<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |否<sup>2</sup><br/> 是 <br/><br/> 否 <br/> 是
| Web | [API 管理](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service 環境](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|是 <br/> 是 <br/> 是 <br/> 是
| 裝載 | [Azure 專用 HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|是 <br/> 是 <br/>
| Azure Spring Cloud | [在 Azure 虛擬網路中部署 (VNet 插入) ](../spring-cloud/spring-cloud-tutorial-deploy-in-azure-virtual-network.md)<br/>| 是 <br/>
| | |

<sup>1</sup>「專用」暗示只有服務特定資源可以部署在此子網路中，且無法與客戶 VM/VMSS 合併 <br/> 
<sup>2</sup> 建議您最好在專用子網路中具有這些服務，但這不是服務所強加的必要需求。
