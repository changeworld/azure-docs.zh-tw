---
title: Azure 服務的虛擬網路整合以進行網路隔離
titlesuffix: Azure Virtual Network
description: 本文說明將 Azure 服務整合到虛擬網路的不同方法，可讓您安全地存取 Azure 服務。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: a29454f09268406511730c63dcc3bc3063b909c6
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218649"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>整合 Azure 服務與虛擬網路以進行網路隔離

Azure 服務的虛擬網路 (VNet) 整合，可讓您將服務的存取權鎖定至您的虛擬網路基礎結構。 VNet 基礎結構也包含對等互連的虛擬網路和內部部署網路。

VNet 整合可為 Azure 服務提供網路隔離的優點，並可透過下列一或多種方法來完成：
- [將服務的專用實例部署至虛擬網路](virtual-network-for-azure-services.md)。 然後，在虛擬網路和內部部署網路中，可以私下存取這些服務。
- 使用 [私人端點](../private-link/private-endpoint-overview.md) ，以私人且安全的方式將您連接到 [Azure Private Link](../private-link/private-link-overview.md)的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的虛擬網路。
- 透過 [服務端點](virtual-network-service-endpoints-overview.md)將虛擬網路擴充至服務，以使用公用端點來存取服務。 服務端點可保障虛擬網路一定可以使用服務資源。
- 使用 [服務](service-tags-overview.md) 標籤來允許或拒絕從公用 IP 端點流向 Azure 資源的流量。

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>將專用 Azure 服務部署至虛擬網路

若在虛擬網路中部署專用 Azure 服務，可以使用私人 IP 位址，私下與該服務資源通訊。

![將專用 Azure 服務部署至虛擬網路](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

將專用的 Azure 服務部署到您的虛擬網路提供下列功能：
- 虛擬網路中的資源可以透過私人 IP 位址，私下互相通訊。 例如，在虛擬網路中，直接在於虛擬機器上執行的 HDInsight 和 SQL Server 之間傳輸資料。
- 內部部署資源可以透過站對站 VPN (VPN 閘道) 或 ExpressRoute，使用私人 IP 位址，存取虛擬網路中的資源。
- 虛擬網路則可以使用私人 IP 位址進行對等互連，來讓虛擬網路中的資源彼此通訊。
- 虛擬網路中的服務執行個體通常完全由 Azure 服務管理。 這包括監視資源的健康情況，以及負載調整。
- 服務執行個體已部署至虛擬網路中的子網路。 根據服務提供的指導，必須透過網路安全性群組，開放子網路的輸入和輸出網路存取。
- 某些服務也會對其部署所在的子網路施加限制，以限制原則的應用、路由或結合相同子網內的 VM 和服務資源。 請檢查每個服務的特定限制，因為它們可能會隨著時間而改變。 這類服務的範例包括 Azure NetApp Files、專用 HSM、Azure Container Instances、App Service。
- (選擇性) 服務可能需要委派的子網路，作為子網路可以裝載特定服務的明確識別項。 藉由委派，子網路取得明確的權限，可在委派的子網路中建立服務特定資源。
- 請在具有委派子網的虛擬網路上參閱 REST API 回應的範例。 您可以透過可用委派 API，取得使用委派子網模型的完整服務清單。

如需可部署至虛擬網路的服務清單，請參閱將 [專用的 Azure 服務部署到虛擬網路](virtual-network-for-azure-services.md)。

## <a name="private-link-and-private-endpoints"></a>Private Link 和私人端點

您可以使用私人端點安全地透過私人連結，安全地將事件從虛擬網路直接輸入至 Azure 資源，而不需要通過公用網際網路。 私人端點是您虛擬網路中 Azure 服務的特殊網路介面。 當您為 Azure 資源建立私人端點時，它會在虛擬網路上的用戶端與您的 Azure 資源之間提供安全的連線能力。 私人端點會從您虛擬網路的 IP 位址範圍中指派 IP 位址。 私人端點與 Azure 服務之間的連接會使用安全的私用連結。

下列範例顯示事件方格資源私人端點的私用存取，可在虛擬網路上的用戶端與事件方格資源之間提供安全的連線能力。

![使用私人端點的 SQL DB 資源私用存取](./media/network-isolation/architecture-diagram.png)

如需 Private Link 的詳細資訊，以及支援的 Azure 服務清單，請參閱 [Private Link 是什麼？](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>服務端點
VNet 服務端點透過 Azure 骨幹網路上的優化路由，提供對 Azure 服務的安全和直接連線。 端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。 服務端點可讓 VNet 中的私人 IP 位址連接到 Azure 服務的端點，而不需要 VNet 上的公用 IP 位址。

![將 Azure 服務放到虛擬網路保護](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

如需詳細資訊，請參閱 [虛擬網路服務端點](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>服務標籤

服務標籤代表來自指定 Azure 服務的一組 IP 位址前置詞。 使用服務標籤，您可以定義 [網路安全性群組](./network-security-groups-overview.md#security-rules) 或 [Azure 防火牆](../firewall/service-tags.md)上的網路存取控制。 藉由指定服務標籤名稱 (例如，AzureEventGrid) 在規則的適當來源或目的地欄位中，您可以允許或拒絕對應服務的流量。

![使用服務標記來允許或拒絕流量](./media/network-isolation/service-tags.png)

您可以使用服務標籤進行網路隔離，以及在存取具有公用端點的 Azure 服務時，防止一般網際網路存取您的 Azure 資源。 建立輸入/輸出網路安全性群組規則，可拒絕進出於 **網際網路** 的流量，並允許 **AzureCloud** 或特定 Azure 服務的其他 [可用服務標籤](service-tags-overview.md#available-service-tags)的輸入/輸出流量。

如需服務標籤和支援它們的 Azure 服務的詳細資訊，請參閱 [服務標記總覽](service-tags-overview.md)

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [整合您的應用程式與 Azure 網路](../app-service/web-sites-integrate-with-vnet.md)。
- 瞭解如何 [使用服務標記來限制對資源的存取](tutorial-restrict-network-access-to-resources.md)。
- 瞭解如何 [使用 Azure Private Link 私下連接至 Azure Cosmos 帳戶](../private-link/tutorial-private-endpoint-cosmosdb-portal.md)。