---
title: Azure 事件中樞的網路安全性
description: 本文說明如何設定來自私人端點的存取權
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 46e6a9ecc2ed09aed1076f12c1f61a966485bdad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422773"
---
# <a name="network-security-for-azure-event-hubs"></a>Azure 事件中樞的網路安全性 
本文說明如何搭配 Azure 事件中樞使用下列安全性功能： 

- 服務標籤
- IP 防火牆規則
- 網路服務端點
- 私人端點（預覽）


## <a name="service-tags"></a>服務標籤
服務標記代表來自指定 Azure 服務的一組 IP 位址首碼。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標籤，將經常更新網路安全性規則的複雜度降到最低。 如需服務標記的詳細資訊，請參閱[服務標記總覽](../virtual-network/service-tags-overview.md)。

您可以使用服務標記來定義[網路安全性群組](../virtual-network/security-overview.md#security-rules) 或 [Azure 防火牆](../firewall/service-tags.md)上的網路存取控制。 當您建立安全性規則時，請使用服務標記來取代特定的 IP 位址。 藉由在規則適當的 [*來源* ] 或 [ *目的地* ] 欄位中指定服務標籤名稱（例如**EventHub**），您可以允許或拒絕對應服務的流量。

| 服務標記 | 目的 | 可以使用輸入或輸出嗎？ | 可以是地區嗎？ | 可以與 Azure 防火牆搭配使用嗎？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure 事件中樞。 | 輸出 | 是 | 是 |


## <a name="ip-firewall"></a>IP 防火牆 
根據預設，只要要求隨附有效的驗證和授權，就可以從網際網路存取事件中樞的命名空間。 使用 IP 防火牆，您可以將它進一步限制為[CIDR （無類別網域間路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)標記法中的一組 ipv4 位址或 ipv4 位址範圍。

這項功能在應該只能從特定知名網站存取 Azure 事件中樞的情況下很有説明。 防火牆規則可讓您設定規則，以接受來自特定 IPv4 位址的流量。 例如，如果您將事件中樞與[Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services)搭配使用，您可以建立**防火牆規則**，只允許來自您內部部署基礎結構 IP 位址的流量。 

IP 防火牆規則會在事件中樞命名空間層級套用。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 任何來自某個 IP 位址的連線嘗試，只要不符合「事件中樞」命名空間上的允許 IP 規則，系統就會將其視為未經授權而予以拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

如需詳細資訊，請參閱[如何設定事件中樞的 IP 防火牆](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>網路服務端點
將事件中樞與[虛擬網路 (VNet) 服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)整合，可以安全存取來自虛擬機器 (繫結至虛擬網路) 等工作負載的傳訊功能，而且兩端的網路流量路徑都能受到保護。

一旦設定為系結到至少一個虛擬網路子網服務端點，個別的事件中樞命名空間就不會再接受來自虛擬網路中任何來自于授權子網的流量。 從虛擬網路的角度來看，將事件中樞命名空間繫結至服務端點，會設定從虛擬網路子網路到傳訊服務的隔離式網路通道。 

最終這會在繫結至子網路的工作負載與各自的事件中樞命名空間之間，建立私人且隔離的關係，儘管傳訊服務端點顯示的網路位址位於公用 IP 範圍中。 這種行為有例外。 啟用服務端點時，根據預設，會啟用`denyall`與虛擬網路相關聯的[IP 防火牆](event-hubs-ip-filtering.md)中的規則。 您可以在 IP 防火牆中新增特定的 IP 位址，以啟用事件中樞公用端點的存取權。 

> [!IMPORTANT]
> 事件中樞的**標準**和**專用**層級支援虛擬網路。 **基本**層不支援此功能。

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 整合所實現的進階安全性案例 

需要嚴格且劃分的安全性，以及虛擬網路子網在劃分服務之間提供分割的解決方案，仍然需要位於這些區間之服務之間的通訊路徑。

區間之間的任何立即 IP 路由，包括那些運輸 HTTPS over TCP/IP，會帶來上層網路的弱點危害風險。 訊息服務會提供隔離的通訊路徑，其中訊息甚至會在合作物件之間轉換時寫入至磁片。 兩個都繫結到相同事件中樞執行個體的不同虛擬網路，當中的工作負載可以透過訊息有效且可靠地通訊，並且保留各自的網路隔離界限完整性。
 
這表示您的安全性敏感雲端解決方案，不只獲得 Azure 可靠、可擴充的領先業界非同步傳訊功能的存取權，而且它們現在可以使用傳訊來建立安全解決方案區間之間的通訊路徑，這些區間本質上比任何對等通訊模式 (包括 HTTPS 和其他 TLS 保護的通訊端通訊協定) 更為安全。

### <a name="bind-event-hubs-to-virtual-networks"></a>將事件中樞系結至虛擬網路

「虛擬網路規則」**** 是防火牆安全性功能，可控制 Azure 事件中樞命名空間是否接受來自特定虛擬網路子網路的連線。

將事件中樞命名空間繫結至虛擬網路是一個雙步驟程序。 您必須先在虛擬網路的子網上建立**虛擬網路服務端點**，並如[服務端點總覽](../virtual-network/virtual-network-service-endpoints-overview.md)一文中所述，針對**Microsoft** node.js 加以啟用。 一旦您新增服務端點，便會使用**虛擬網路規則**將事件中樞命名空間與其繫結。

虛擬網路規則是「事件中樞」命名空間與虛擬網路子網路的關聯。 此規則存在時，繫結至該子網路的所有工作負載都會獲得事件中樞命名空間的存取權。 事件中樞本身永遠不會建立輸出連線，也不需要取得存取權，因此永遠不會藉由啟用此規則來授與您子網的存取權。

如需詳細資訊，請參閱[如何設定事件中樞的虛擬網路服務端點](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>私人端點

[Azure 私用連結服務](../private-link/private-link-overview.md)可讓您透過虛擬網路中的**私人端點**，存取 azure 服務（例如 Azure 事件中樞、Azure 儲存體和 Azure Cosmos DB）和 azure 託管的客戶/合作夥伴服務。

私人端點是一種網路介面，可讓您私下且安全地連線到 Azure 私用連結所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

> [!NOTE]
> 只有**專用**層才支援這項功能。 如需專用層的詳細資訊，請參閱[事件中樞專用的總覽](event-hubs-dedicated-overview.md)。 
>
> 這項功能目前為**預覽**狀態。 


如需詳細資訊，請參閱[如何設定事件中樞的私人端點。](private-link-service.md)


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [如何設定事件中樞的 IP 防火牆](event-hubs-ip-filtering.md)
- [如何設定事件中樞的虛擬網路服務端點](event-hubs-service-endpoints.md)
- [如何設定事件中樞的私人端點](private-link-service.md)