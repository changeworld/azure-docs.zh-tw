---
title: Azure 服務匯流排的網路安全
description: 本文介紹了網路安全功能，如服務標記、IP 防火牆規則、服務終結點和專用終結點。
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: aschhab
ms.openlocfilehash: 95f8c2a3b47b59bab7df909be43dacdb1f9c58f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479276"
---
# <a name="network-security-for-azure-service-bus"></a>Azure 服務匯流排的網路安全 
本文介紹如何使用以下安全功能與 Azure 服務匯流排： 

- 服務標籤
- IP 防火牆規則
- 網路服務終結點
- 專用終結點（預覽）


## <a name="service-tags"></a>服務標籤
服務標記表示給定 Azure 服務的一組 IP 位址首碼。 Microsoft 管理服務標記包含的位址首碼，並在位址更改時自動更新服務標記，從而最大限度地減少頻繁更新網路安全規則的複雜性。 有關服務標記的詳細資訊，請參閱[服務標記概述](../virtual-network/service-tags-overview.md)。

可以使用服務標記在[網路安全性群組](../virtual-network/security-overview.md#security-rules)或[Azure 防火牆](../firewall/service-tags.md)上定義網路訪問控制項。 創建安全規則時，使用服務標記代替特定的 IP 位址。 通過在規則的相應*源*或*目標*欄位中指定服務標記名稱（例如 **，ServiceBus），** 可以允許或拒絕相應服務的流量。

| 服務標籤 | 目的 | 可以使用入站或出站嗎？ | 可以是區域性的嗎？ | 可與 Azure 防火牆一起使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **服務匯流排** | 使用高級服務層的 Azure 服務匯流排流量。 | 輸出 | 是 | 是 |


## <a name="ip-firewall"></a>IP 防火牆 
預設情況下，只要請求附帶有效的身份驗證和授權，服務匯流排命名空間即可從 Internet 訪問。 使用 IP 防火牆，您可以進一步將其限制為[CIDR（無類域間路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)標記法中的一組 IPv4 位址或 IPv4 位址範圍。

此功能在 Azure 服務匯流排應僅從某些已知網站訪問的情況下非常有用。 防火牆規則使您能夠配置規則以接受來自特定 IPv4 位址的流量。 例如，如果將服務匯流排與 [Azure 快速路由] [快速路由]一起使用，則可以創建**防火牆規則**，僅允許來自本地基礎結構 IP 位址或公司 NAT 閘道位址的流量。 

IP 防火牆規則在服務匯流排命名空間級別應用。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 任何來自某個 IP 位址的連線嘗試，只要不符合「服務匯流排」命名空間上的允許 IP 規則，系統就會將它視為未經授權而予以拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

有關詳細資訊，請參閱[如何為服務匯流排命名空間配置 IP 防火牆](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>網路服務終結點
服務匯流排與[虛擬網路 （VNet） 服務終結點](service-bus-service-endpoints.md)的集成使得對綁定到虛擬網路的工作負載（如虛擬機器）的消息傳遞功能進行安全訪問，並且網路流量路徑在兩端都受到保護。

一旦設定為繫結到至少一個虛擬網路子網路服務端點，個別的服務匯流排命名空間除了授權的虛擬網路以外，無法再接受任何位置的流量。 從虛擬網路的觀點而言，將服務匯流排命名空間繫結至服務端點，會設定從虛擬網路子網路到傳訊服務的隔離網路通道。

結果是繫結至子網路的工作負載與個別服務匯流排命名空間之間的私人和隔離關係，儘管傳訊服務端點的可觀察網路位址是在公用 IP 範圍中。

> [!IMPORTANT]
> 唯有[進階層](service-bus-premium-messaging.md)服務匯流排命名空間支援虛擬網路。
> 
> 將 VNet 服務終結點與服務匯流排一起使用時，不應在混合標準層和高級服務匯流排命名空間的應用程式中啟用這些終結點。 因為標準層不支援 VNet。 終結點僅限於高級層命名空間。

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 整合所實現的進階安全性案例 

需要緊密和劃分安全性，而且虛擬網路子網路分割各劃分服務的解決方案，通常仍然需要位於這些區間之各項服務之間的通訊路徑。

區間之間的任何立即 IP 路由，包括那些運輸 HTTPS over TCP/IP，會帶來上層網路的弱點危害風險。 傳訊服務提供完全隔離的通訊路徑，在其中，當訊息在對象之間轉換時，甚至會寫入到磁碟。 兩個都繫結到相同服務匯流排執行個體的不同虛擬網路，其中的工作負載可以透過訊息有效且可靠地通訊，同時保留各自的網路隔離界限完整性。
 
這表示您的安全性敏感雲端解決方案，不只獲得了 Azure 領先業界可靠和可擴充非同步傳訊功能支援，且現在可以使用傳訊來建立安全解決方案區間之間的通訊路徑，本質上會比任何對等通訊模式 (包括 HTTPS 和其他受 TLS 保護的通訊端通訊協定) 所能達成的更為安全。

### <a name="bind-service-bus-to-virtual-networks"></a>將服務匯流排綁定到虛擬網路

「虛擬網路規則」** 是防火牆安全性功能，可控制 Azure 服務匯流排伺服器是否接受來自特定虛擬網路子網路的連線。

將服務匯流排命名空間繫結至虛擬網路是一個雙步驟的程序。 您首先需要在虛擬網路子網上創建**虛擬網路服務終結點**，並為**Microsoft**啟用[它。](service-bus-service-endpoints.md) 一旦您新增服務端點，您就可使用「虛擬網路規則」**** 將服務匯流排命名空間與它繫結。

虛擬網路規則是服務匯流排命名空間與虛擬網路子網路的關聯。 當此規則存在時，繫結至子網路的所有工作負載都會獲得授與服務匯流排命名空間的存取權。 服務匯流排本身永遠不會建立輸出連線，不需要獲得存取權，因此永遠不會因為啟用這項規則而獲得授與子網路的存取權。

有關詳細資訊，請參閱[如何為服務匯流排命名空間配置虛擬網路服務終結點](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>專用終結點

Azure 專用連結服務使您能夠通過虛擬網路中的**專用終結點**訪問 Azure 服務（例如，Azure 服務匯流排、Azure 存儲和 Azure 宇宙資料庫）和 Azure 託管的客戶/合作夥伴服務。

專用終結點是一個網路介面，可私下安全地連接到由 Azure 專用連結提供支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

有關詳細資訊，請參閱什麼是[Azure 專用連結？](../private-link/private-link-overview.md)

> [!NOTE]
> Azure 服務匯流排**的高級**層支援此功能。 有關高級層的詳細資訊，請參閱[服務匯流排高級和標準消息級別](service-bus-premium-messaging.md)一文。
>
> 此功能當前處於**預覽狀態**。 


有關詳細資訊，請參閱[如何為服務匯流排命名空間配置專用終結點](private-link-service.md)


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [如何為服務匯流排命名空間配置 IP 防火牆](service-bus-ip-filtering.md)
- [如何為服務匯流排命名空間配置虛擬網路服務終結點](service-bus-service-endpoints.md)
- [如何為服務匯流排命名空間配置專用終結點](private-link-service.md)
