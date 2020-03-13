---
title: Azure 服務的虛擬網路
titlesuffix: Azure Virtual Network
description: 了解將資源部署到擬網路的優點。 虛擬網路中的資源可以彼此通訊，並且可以內部部署資源，而不會產生周遊網際網路的流量。
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 24bcc7e698527cd39958c53b48a0b36404c36bb4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279659"
---
# <a name="virtual-network-integration-for-azure-services"></a>Azure 服務的虛擬網路整合

透過將 Azure 服務整合到 Azure 虛擬網路，可以啟用從虛擬機器或虛擬網路中的計算資源對服務進行私下存取。
您可以使用下列選項，在虛擬網路中整合 Azure 服務：
- 將服務的專用執行個體部署至虛擬網路。 然後，在虛擬網路和內部部署網路中，可以私下存取這些服務。
- 使用[私用連結](../private-link/private-link-overview.md)，從您的虛擬網路和內部部署網路私下存取特定的服務實例。

您也可以透過[服務端點](virtual-network-service-endpoints-overview.md)將虛擬網路擴充至服務，藉以使用公用端點來存取服務。 服務端點可讓服務資源安全地提供給虛擬網路。
 
## <a name="deploy-azure-services-into-virtual-networks"></a>將 Azure 服務部署至虛擬網路

若在[虛擬網路](virtual-networks-overview.md)中部署專用 Azure 服務，可以使用私人 IP 位址，私下與該服務資源通訊。

![部署在虛擬網路中的服務](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

部署在虛擬網路中的服務有下列功能：

- 虛擬網路中的資源可以透過私人 IP 位址，私下互相通訊。 例如，在虛擬網路中，直接在於虛擬機器上執行的 HDInsight 和 SQL Server 之間傳輸資料。
- 內部部署資源可以透過[站對站 VPN (VPN 閘道)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) 或 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，使用私人 IP 位址，存取虛擬網路中的資源。
- 虛擬網路則可以使用私人 IP 位址進行[對等互連](virtual-network-peering-overview.md)，來讓虛擬網路中的資源彼此通訊。
- 虛擬網路中的服務實例通常完全由 Azure 服務管理。 這包括監視資源的健全狀況和負載調整。
- 服務執行個體已部署至虛擬網路中的子網路。 子網的輸入和輸出網路存取，必須透過[網路安全性群組](security-overview.md#network-security-groups)，以服務所提供的指引來開啟。
- 某些服務也會對其部署所在的子網施加限制，以限制原則的應用、路由或結合相同子網內的 Vm 和服務資源。 請檢查每個服務的特定限制，因為它們可能會隨著時間而改變。 這類服務的範例包括 Azure NetApp Files、專用 HSM、Azure 容器實例 App Service。 
- (選擇性) 服務可能需要[委派的子網路](virtual-network-manage-subnet.md#add-a-subnet)，作為子網路可以裝載特定服務的明確識別項。 藉由委派，服務會取得明確的許可權，以在委派的子網中建立服務特定資源。
- 在[具有委派子網的虛擬網路](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet)上，請參閱 REST API 回應的範例。 使用委派子網模型的完整服務清單可透過[可用的委派](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list)API 取得。

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>可以部署至虛擬網路的服務

|類別|服務| 專用¹子網
|-|-|-|
| 計算 | 虛擬機器：[Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[雲端服務](https://msdn.microsoft.com/library/azure/jj156091)：僅限虛擬網路 (傳統)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| 否 <br/> 否 <br/> 否 <br/> 否²
| 網路 | [應用程式閘道 - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 防火牆](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[網路虛擬設備](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | 是 <br/> 是 <br/> 是 <br/> 否
|資料|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| 是 <br/> 是 <br/> 
|分析 | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |否² <br/> 否² <br/> 
| 身分識別 | [Azure Active Directory 網域服務](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |否 <br/>
| 容器 | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 容器執行個體 (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service 引擎](https://github.com/Azure/acs-engine)搭配 Azure 虛擬網路 CNI [外掛程式](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|否²<br/> 是 <br/><br/> 否
| Web | [API 管理](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service 環境](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|是 <br/> 是 <br/> 是
| 裝載 | [Azure 專用 HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|是 <br/> 是 <br/>
| | |

¹「專用」意指只有服務特定資源可以部署在此子網中，且無法與客戶 VM/VMSSs 結合 <br/> ²建議您最好將這些服務安裝在專用的子網中，而不是服務所加諸的必要需求。
