---
title: Azure 轉送的網路安全性
description: 本文說明如何搭配 Azure 轉送使用 IP 防火牆規則和私人端點。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 735a0b7dc303ad63cb301151e3e14e4488c856b1
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217238"
---
# <a name="network-security-for-azure-relay"></a>Azure 轉送的網路安全性 
本文說明如何搭配 Azure 轉送使用下列安全性功能： 

- IP 防火牆規則
- 私人端點 

> [!NOTE]
> Azure 轉送不支援網路服務端點。 


## <a name="ip-firewall"></a>IP 防火牆 
根據預設，只要要求具備有效的驗證和授權，便可以從網際網路存取轉送命名空間。 透過 IP 防火牆，您可以將其進一步限制為僅允許一組 IPv4 位址，或是使用 [CIDR (無類別網域間路由)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 標記法來設定 IPv4 位址範圍。

此功能在只應該從特定知名網站存取 Azure 轉送的情況下會很有幫助。 防火牆規則可讓您設定規則以接受源自特定 IPv4 位址的流量。 例如，如果您搭配 [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) 使用轉送，您可以建立 **防火牆規則** 以僅允許來自您內部部署基礎結構 IP 位址的流量。 

IP 防火牆規則會套用於轉送命名空間層級。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 從不符合轉送命名空間上允許之 IP 規則的 IP 位址嘗試進行的任何連線，都會被拒絕為未經授權。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

如需詳細資訊，請參閱 [如何設定轉送命名空間的 IP 防火牆](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>私人端點

Azure **Private Link 服務** 可讓您透過虛擬網路中的私人端點，存取各 Azure 服務 (例如 Azure 轉送、Azure 服務匯流排、Azure 事件中樞、Azure 儲存體和 Azure Cosmos DB)，以及 Azure 裝載的客戶/合作夥伴服務。 如需詳細資訊，請參閱[何謂 Azure Private Link？](../private-link/private-link-overview.md)

**私人端點** 是一種網路介面，其允許您在虛擬網路中執行的工作負載，透過私密又安全的方式，連線到具有 **Private Link 資源** 的服務 (例如，「轉送」命名空間)。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute、VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可以允許連線到特定 Azure 轉送命名空間，在存取控制中提供細微性層級。

如需詳細資訊，請參閱 [如何設定私人端點](private-link-service.md)


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [如何設定 IP 防火牆](ip-firewall-virtual-networks.md)
- [如何設定私人端點](private-link-service.md)
