---
title: Azure 轉送的網路安全性
description: 本文說明如何設定來自私人端點的存取權
services: service-bus-relay
author: spelluru
ms.service: service-bus-relay
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: d0a0d03680877ae78f5af939f8d47e4e426abadb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984768"
---
# <a name="network-security-for-azure-relay"></a>Azure 轉送的網路安全性 
本文說明如何搭配 Azure 轉送使用下列安全性功能： 

- IP 防火牆規則（預覽）
- 私人端點（預覽）

> [!NOTE]
> Azure 轉送不支援網路服務端點。 


## <a name="ip-firewall"></a>IP 防火牆 
根據預設，只要要求隨附有效的驗證和授權，就可以從網際網路存取轉送命名空間。 使用 IP 防火牆，您可以將它進一步限制為[CIDR （無類別網域間路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)標記法中的一組 ipv4 位址或 ipv4 位址範圍。

這項功能在應該只能從特定知名網站存取 Azure 轉送的情況下很有説明。 防火牆規則可讓您設定規則，以接受來自特定 IPv4 位址的流量。 例如，如果您使用轉送搭配[Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services)，您可以建立**防火牆規則**，只允許來自您內部部署基礎結構 IP 位址的流量。 

IP 防火牆規則會在轉送命名空間層級套用。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 從不符合轉送命名空間上允許之 IP 規則的 IP 位址進行的任何連線嘗試都會被視為未經授權而遭到拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

如需詳細資訊，請參閱[如何設定轉送命名空間的 IP 防火牆](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>私人端點

Azure**私用連結服務**可讓您透過虛擬網路中的私人端點，存取 azure 服務（例如 Azure 轉送、Azure 服務匯流排、Azure 事件中樞、Azure 儲存體和 Azure Cosmos DB）和 azure 託管的客戶/合作夥伴服務。 如需詳細資訊，請參閱[何謂 Azure Private Link (預覽)？](../private-link/private-link-overview.md)

**私人端點**是一種網路介面，可讓您在虛擬網路中執行的工作負載，私下且安全地連線到具有**私人連結資源**的服務（例如，轉送命名空間）。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute、VPN 連線或公用 IP 位址。 您的虛擬網路與服務之間的流量會透過 Microsoft 骨幹網路來進行，以消除公開網際網路的暴露。 您可以藉由允許連接到特定 Azure 轉送命名空間，在存取控制中提供細微性層級。

> [!NOTE]
> 這項功能目前為**預覽**狀態。 

如需詳細資訊，請參閱[如何設定私人端點](private-link-service.md)。


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [如何設定 IP 防火牆](ip-firewall-virtual-networks.md)
- [如何設定私用端點](private-link-service.md)