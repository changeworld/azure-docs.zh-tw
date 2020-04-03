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
ms.openlocfilehash: b721857f2fa76dcee144521fb34b34ce48b7bd95
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616913"
---
# <a name="virtual-network-integration-for-azure-services"></a>Azure 服務的虛擬網路整合

透過將 Azure 服務整合到 Azure 虛擬網路，可以啟用從虛擬機器或虛擬網路中的計算資源對服務進行私下存取。
您可以使用下列選項，在虛擬網路中整合 Azure 服務：
- 將服務的專用執行個體部署至虛擬網路。 然後，在虛擬網路和內部部署網路中，可以私下存取這些服務。
- 使用[專用連結](../private-link/private-link-overview.md)從虛擬網路和本地網路私下訪問服務的特定實例。

還可以通過[服務終結點](virtual-network-service-endpoints-overview.md)將虛擬網路擴展到服務,從而使用公共終結點訪問服務。 服務終結點允許將服務資源保護到虛擬網路。
 
## <a name="deploy-azure-services-into-virtual-networks"></a>將 Azure 服務部署至虛擬網路

若在[虛擬網路](virtual-networks-overview.md)中部署專用 Azure 服務，可以使用私人 IP 位址，私下與該服務資源通訊。

![部署在虛擬網路中的服務](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

部署在虛擬網路中的服務有下列功能：

- 虛擬網路中的資源可以透過私人 IP 位址，私下互相通訊。 例如，在虛擬網路中，直接在於虛擬機器上執行的 HDInsight 和 SQL Server 之間傳輸資料。
- 內部部署資源可以透過[站對站 VPN (VPN 閘道)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) 或 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，使用私人 IP 位址，存取虛擬網路中的資源。
- 虛擬網路則可以使用私人 IP 位址進行[對等互連](virtual-network-peering-overview.md)，來讓虛擬網路中的資源彼此通訊。
- 虛擬網路中的服務實例通常完全由 Azure 服務管理。 這包括監視資源的運行狀況和使用負載進行縮放。
- 服務執行個體已部署至虛擬網路中的子網路。 子網的入站和出站網路訪問必須通過[網路安全組](security-overview.md#network-security-groups)根據服務提供的指導打開。
- 某些服務還對其部署的子網施加限制,限制策略、路由的應用或將 VM 和服務資源合併到同一子網中。 與每個服務查詢特定限制,因為它們可能會隨時間而變化。 此類服務的範例包括 Azure NetApp 檔、專用 HSM、Azure 容器實例、應用服務。 
- (選擇性) 服務可能需要[委派的子網路](virtual-network-manage-subnet.md#add-a-subnet)，作為子網路可以裝載特定服務的明確識別項。 通過委派,服務獲得在委派子網中創建特定於服務的資源的顯式許可權。
- 請參閱[具有委派子網的虛擬網路上的](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet)REST API 回應範例。 可以使用委派子網模型的服務的完整清單可以通過[可用委派](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list)API 獲取。

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>可以部署至虛擬網路的服務

|類別|服務| 專用 1 子網
|-|-|-|
| 計算 | 虛擬機器：[Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[雲端服務](https://msdn.microsoft.com/library/azure/jj156091)：僅限虛擬網路 (傳統)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| 否 <br/> 否 <br/> 否 <br/> 否2
| 網路 | [應用程式閘道 - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 防火牆](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[網路虛擬裝置](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | 是 <br/> 是 <br/> 是 <br/> 否
|資料|[雷瑞斯緩存](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL 資料庫託管實例](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| 是 <br/> 是 <br/> 
|分析 | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |否2 <br/> 否2 <br/> 
| 身分識別 | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |否 <br/>
| 容器 | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 容器實體 (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service 引擎](https://github.com/Azure/acs-engine)搭配 Azure 虛擬網路 CNI [外掛程式](https://github.com/Azure/acs-engine/tree/master/examples/vnet)<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |否2<br/> 是 <br/><br/> 否 <br/> 是
| Web | [API 管理](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[套用服務環境](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|是 <br/> 是 <br/> 是 <br/> 是
| 裝載 | [Azure 專用 HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|是 <br/> 是 <br/>
| | |

1 "專用"意味著只能在此子網中部署特定於服務的資源,並且不能與客戶 VM/VMSS 結合使用 <br/> 2 建議將這些服務設於專用子網,而不是服務規定的強制性要求,作為最佳做法。
