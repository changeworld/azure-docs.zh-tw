---
title: Azure 服務匯流排的網路安全性
description: 本文說明網路安全性功能，例如服務標記、IP 防火牆規則、服務端點和私人端點。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: db0dd89d1f902699c27b724609505ba681757454
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310468"
---
# <a name="network-security-for-azure-service-bus"></a>Azure 服務匯流排的網路安全性 
本文說明如何搭配 Azure 服務匯流排使用下列安全性功能： 

- 服務標籤
- IP 防火牆規則
- 網路服務端點
- 私人端點


## <a name="service-tags"></a>服務標籤
服務標籤代表來自指定 Azure 服務的一組 IP 位址前置詞。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤，而盡可能簡化網路安全性規則頻繁的更新。 如需服務標籤的詳細資訊，請參閱 [服務標記總覽](../virtual-network/service-tags-overview.md)。

您可以使用服務標記來定義 [網路安全性群組](../virtual-network/network-security-groups-overview.md#security-rules) 或 [Azure 防火牆](../firewall/service-tags.md)上的網路存取控制。 建立安全性規則時，請以服務標籤取代特定的 IP 位址。 藉由指定服務標籤名稱 (例如，在規則的適當*來源*或*目的地*欄位中指定服務卷**標) ，** 您可以允許或拒絕對應服務的流量。

| 服務標籤 | 目的 | 可以使用輸入還是輸出？ | 是否可為區域性？ | 是否可與 Azure 防火牆搭配使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | 使用進階服務層級的 Azure 服務匯流排流量。 | 輸出 | 是 | 是 |


> [!NOTE]
> 您只能對 **premium** 命名空間使用服務標記。 如果您使用的是 **標準** 命名空間，請使用您在執行下列命令時所看到的 IP 位址： `nslookup <host name for the namespace>` 。 例如： `nslookup contosons.servicebus.windows.net` 。 

## <a name="ip-firewall"></a>IP 防火牆 
根據預設，只要要求具備有效的驗證和授權，便可以從網際網路存取服務匯流排命名空間。 透過 IP 防火牆，您可以將其進一步限制為僅允許一組 IPv4 位址，或是使用 [CIDR (無類別網域間路由)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 標記法來設定 IPv4 位址範圍。

此功能在只應該從特定知名網站存取 Azure 服務匯流排的情況下，會很有幫助。 防火牆規則可讓您設定規則以接受源自特定 IPv4 位址的流量。 例如，如果您搭配 [Azure Express Route] [express route] 使用服務匯流排，您可以建立 **防火牆規則** ，以允許來自公司 NAT 閘道的內部部署基礎結構 IP 位址或位址的流量。 

IP 防火牆規則會套用在服務匯流排命名空間層級上。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 任何來自某個 IP 位址的連線嘗試，只要不符合「服務匯流排」命名空間上的允許 IP 規則，系統就會將它視為未經授權而予以拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

如需詳細資訊，請參閱 [如何設定服務匯流排命名空間的 IP 防火牆](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>網路服務端點
服務匯流排與 [虛擬網路 (VNet) 服務端點](service-bus-service-endpoints.md) 的整合可讓您安全地存取來自系結至虛擬網路之虛擬機器的工作負載（例如，系結至虛擬網路的虛擬機器），並將網路流量路徑安全地放在兩端。

一旦設定為繫結到至少一個虛擬網路子網路服務端點，個別的服務匯流排命名空間除了授權的虛擬網路以外，無法再接受任何位置的流量。 從虛擬網路的觀點而言，將服務匯流排命名空間繫結至服務端點，會設定從虛擬網路子網路到傳訊服務的隔離網路通道。

結果是繫結至子網路的工作負載與個別服務匯流排命名空間之間的私人和隔離關係，儘管傳訊服務端點的可觀察網路位址是在公用 IP 範圍中。

> [!IMPORTANT]
> 唯有[進階層](service-bus-premium-messaging.md)服務匯流排命名空間支援虛擬網路。
> 
> 使用 VNet 服務端點搭配服務匯流排時，您不應該在混合標準和進階層服務匯流排命名空間的應用程式中啟用這些端點。 因為標準層不支援 Vnet。 端點僅限進階層命名空間。

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 整合所實現的進階安全性案例 

需要緊密和劃分安全性，而且虛擬網路子網路分割各劃分服務的解決方案，通常仍然需要位於這些區間之各項服務之間的通訊路徑。

區間之間的任何立即 IP 路由，包括那些運輸 HTTPS over TCP/IP，會帶來上層網路的弱點危害風險。 傳訊服務提供完全隔離的通訊路徑，在其中，當訊息在對象之間轉換時，甚至會寫入到磁碟。 兩個都繫結到相同服務匯流排執行個體的不同虛擬網路，其中的工作負載可以透過訊息有效且可靠地通訊，同時保留各自的網路隔離界限完整性。
 
這表示您的安全性敏感雲端解決方案，不只獲得了 Azure 領先業界可靠和可擴充非同步傳訊功能支援，且現在可以使用傳訊來建立安全解決方案區間之間的通訊路徑，本質上會比任何對等通訊模式 (包括 HTTPS 和其他受 TLS 保護的通訊端通訊協定) 所能達成的更為安全。

### <a name="bind-service-bus-to-virtual-networks"></a>將服務匯流排系結至虛擬網路

「虛擬網路規則」** 是防火牆安全性功能，可控制 Azure 服務匯流排伺服器是否接受來自特定虛擬網路子網路的連線。

將服務匯流排命名空間繫結至虛擬網路是一個雙步驟的程序。 您必須先在虛擬網路子網上建立 **虛擬網路服務端點** ，並針對 **Microsoft** 進行啟用，如 [服務端點總覽](service-bus-service-endpoints.md)中所述。 一旦您新增服務端點，您就可使用「虛擬網路規則」**** 將服務匯流排命名空間與它繫結。

虛擬網路規則是服務匯流排命名空間與虛擬網路子網路的關聯。 當此規則存在時，繫結至子網路的所有工作負載都會獲得授與服務匯流排命名空間的存取權。 服務匯流排本身永遠不會建立輸出連線，不需要獲得存取權，因此永遠不會因為啟用這項規則而獲得授與子網路的存取權。

如需詳細資訊，請參閱 [如何設定服務匯流排命名空間的虛擬網路服務端點](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>私人端點

Azure Private Link 服務可供透過虛擬網路中的**私人端點**存取 Azure 服務 (例如 Azure 服務匯流排、Azure 儲存體和 Azure Cosmos DB)，以及 Azure 裝載的客戶/合作夥伴服務。

私人端點是一種網路介面，其可以私人且安全的方式連線至 Azure Private Link 所支援服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

如需詳細資訊，請參閱[何謂 Azure Private Link？](../private-link/private-link-overview.md)

> [!NOTE]
> Azure 服務匯流排的**進階**層支援這項功能。 如需進階層的詳細資訊，請參閱[服務匯流排進階和標準傳訊層級](service-bus-premium-messaging.md)一文。


如需詳細資訊，請參閱 [如何設定服務匯流排命名空間的私人端點。](private-link-service.md)


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [如何設定服務匯流排命名空間的 IP 防火牆](service-bus-ip-filtering.md)
- [如何設定服務匯流排命名空間的虛擬網路服務端點](service-bus-service-endpoints.md)
- [如何設定服務匯流排命名空間的私人端點](private-link-service.md)