---
title: Azure 事件中心的網路安全性
description: 本文介紹如何配置來自專用終結點的訪問
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 46e6a9ecc2ed09aed1076f12c1f61a966485bdad
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422773"
---
# <a name="network-security-for-azure-event-hubs"></a>Azure 事件中心的網路安全性 
本文介紹如何使用以下安全功能與 Azure 事件中心: 

- 服務標籤
- IP 防火牆規則
- 網路服務終結點
- 專用終結點(預覽)


## <a name="service-tags"></a>服務標籤
服務標記表示給定 Azure 服務的一組 IP 位址前綴。 Microsoft 管理服務標記包含的位址首碼,並在位址更改時自動更新服務標記,從而最大限度地減少頻繁更新網路安全規則的複雜性。 有關服務標記的詳細資訊,請參閱[服務標記概述](../virtual-network/service-tags-overview.md)。

可以使用服務標記在[網路安全組](../virtual-network/security-overview.md#security-rules) 或 [Azure 防火牆](../firewall/service-tags.md)上定義網路訪問控制項。 建立安全規則時,使用服務標記代替特定的 IP 位址。 通過在規則的相應*源* 或 *目標* 欄位中指定服務標記名稱(例如**EventHub),** 可以允許或拒絕相應服務的流量。

| 服務標籤 | 目的 | 可以使用入站或出站嗎? | 可以是區域性的嗎? | 可與 Azure 防火牆一起使用? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **事件中心** | Azure 事件中心。 | 輸出 | 是 | 是 |


## <a name="ip-firewall"></a>IP 防火牆 
默認情況下,只要請求附帶有效的身份驗證和授權,事件中心命名空間即可從 Internet 訪問。 使用 IP 防火牆,您可以進一步將其限制為[CIDR(無類域間路由)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法中的一組 IPv4 位址或 IPv4 位址範圍。

此功能在 Azure 事件中心應僅從某些已知網站訪問的情況下非常有用。 防火牆規則使您能夠配置規則以接受來自特定 IPv4 位址的流量。 例如,如果將事件中心與 Azure[快速路由](/azure/expressroute/expressroute-faqs#supported-services)一起使用 ,則可以創建**防火牆規則**,僅允許來自本地基礎結構 IP 位址的流量。 

IP 防火牆規則在事件中心命名空間級別應用。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 任何來自某個 IP 位址的連線嘗試，只要不符合「事件中樞」命名空間上的允許 IP 規則，系統就會將其視為未經授權而予以拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

有關詳細資訊,請參閱[如何為事件中心配置 IP 防火牆](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>網路服務終結點
將事件中樞與[虛擬網路 (VNet) 服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)整合，可以安全存取來自虛擬機器 (繫結至虛擬網路) 等工作負載的傳訊功能，而且兩端的網路流量路徑都能受到保護。

一旦配置為至少綁定到一個虛擬網路子網服務終結點,相應的事件中心命名空間將不再接受來自虛擬網路中的經過授權的子網的流量。 從虛擬網路的角度來看，將事件中樞命名空間繫結至服務端點，會設定從虛擬網路子網路到傳訊服務的隔離式網路通道。 

最終這會在繫結至子網路的工作負載與各自的事件中樞命名空間之間，建立私人且隔離的關係，儘管傳訊服務端點顯示的網路位址位於公用 IP 範圍中。 此行為有一個例外。 默認情況下,啟用服務終結點可在[IP](event-hubs-ip-filtering.md)防火牆`denyall`中啟用與虛擬網路關聯的規則。 您可以在 IP 防火牆中添加特定的 IP 位址,以啟用對事件中心公共終結點的訪問。 

> [!IMPORTANT]
> 事件中樞的**標準**和**專用**層級支援虛擬網路。 **基本**層不支援它。

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 整合所實現的進階安全性案例 

需要嚴格和分門別類安全性的解決方案,以及虛擬網路子網在隔離服務之間提供分段時,仍然需要位於這些隔離區中的服務之間的通信路徑。

區間之間的任何立即 IP 路由，包括那些運輸 HTTPS over TCP/IP，會帶來上層網路的弱點危害風險。 消息傳遞服務提供絕緣通信路徑,其中消息甚至在各方之間轉換時寫入磁碟。 兩個都繫結到相同事件中樞執行個體的不同虛擬網路，當中的工作負載可以透過訊息有效且可靠地通訊，並且保留各自的網路隔離界限完整性。
 
這表示您的安全性敏感雲端解決方案，不只獲得 Azure 可靠、可擴充的領先業界非同步傳訊功能的存取權，而且它們現在可以使用傳訊來建立安全解決方案區間之間的通訊路徑，這些區間本質上比任何對等通訊模式 (包括 HTTPS 和其他 TLS 保護的通訊端通訊協定) 更為安全。

### <a name="bind-event-hubs-to-virtual-networks"></a>將事件中心繫結到虛擬網路

「虛擬網路規則」**** 是防火牆安全性功能，可控制 Azure 事件中樞命名空間是否接受來自特定虛擬網路子網路的連線。

將事件中樞命名空間繫結至虛擬網路是一個雙步驟程序。 首先需要在虛擬網路的子網上創建**虛擬網路服務終結點**,併為**Microsoft.EventHub**啟用它,如[服務終結點概述](../virtual-network/virtual-network-service-endpoints-overview.md)文章中所述。 一旦您新增服務端點，便會使用**虛擬網路規則**將事件中樞命名空間與其繫結。

虛擬網路規則是「事件中樞」命名空間與虛擬網路子網路的關聯。 此規則存在時，繫結至該子網路的所有工作負載都會獲得事件中樞命名空間的存取權。 事件中心本身永遠不會建立出站連接,不需要訪問,因此永遠不會通過啟用此規則被授予對子網的訪問許可權。

有關詳細資訊,請參閱[如何為事件中心配置虛擬網路服務終結點](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>專用終結點

[Azure 專用連結服務](../private-link/private-link-overview.md)使您能夠透過虛擬網路中的**專用終結點**訪問 Azure 服務(例如,Azure 事件中心、Azure 儲存和 Azure 宇宙資料庫)和 Azure 託管的客戶/合作夥伴服務。

專用終結點是一個網路介面,可私下安全地連接到由 Azure 專用連結提供支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

> [!NOTE]
> 此功能僅支援**專用**層。 有關專用層的詳細資訊,請參閱[活動中心專用概述](event-hubs-dedicated-overview.md)。 
>
> 此功能目前處於**預覽狀態**。 


有關詳細資訊,請參閱[如何為事件中心配置專用終結點](private-link-service.md)


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [如何為事件中心設定 IP 防火牆](event-hubs-ip-filtering.md)
- [如何為事件中心設定虛擬網路服務終結點](event-hubs-service-endpoints.md)
- [如何為事件中心設定專用終結點](private-link-service.md)