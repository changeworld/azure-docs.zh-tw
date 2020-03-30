---
title: Azure 虛擬網路服務端點
titlesuffix: Azure Virtual Network
description: 了解如何使用服務端點，啟用對虛擬網路中的 Azure 資源直接存取。
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244780"
---
# <a name="virtual-network-service-endpoints"></a>虛擬網路服務端點

虛擬網路 （VNet） 服務終結點擴展虛擬網路專用位址空間。 終結點還會通過直接連接將 VNet 的標識擴展到 Azure 服務。 端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。 從您的 VNet 到 Azure 服務的流量一定會保留在 Microsoft Azure 骨幹網路上。

此功能可用於以下 Azure 服務和區域。 *微軟資源\** 在括弧內。 在佈建服務的服務終結點時，從子網端啟用此資源：

**正式推出**

- **[Azure 存儲](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)***（Microsoft.存儲*）： 通常在所有 Azure 區域都可用。
- **[Azure SQL 資料庫](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**（*微軟.Sql*）： 通常在所有 Azure 區域都可用。
- **[Azure SQL 資料倉儲](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**（*Microsoft.Sql*）： 通常在所有 Azure 區域都可用。
- **[用於 PostgreSQL 伺服器](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** 的 Azure 資料庫 *（Microsoft.Sql*）： 通常在資料庫服務可用的 Azure 區域中可用。
- **[MySQL 伺服器的 Azure 資料庫](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)***（Microsoft.Sql*）： 通常在資料庫服務可用的 Azure 區域中可用。
- **[MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** 的 Azure 資料庫 （*Microsoft.Sql*）： 通常在資料庫服務可用的 Azure 區域中可用。
- **[Azure 宇宙資料庫](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**（*微軟.AzureCosmosDB*）： 通常在所有 Azure 區域都可用。
- **[Azure 金鑰保存庫](../key-vault/key-vault-overview-vnet-service-endpoints.md)**（*微軟.KeyVault*）： 通常在所有 Azure 區域都可用.
- **[Azure 服務匯流排](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**（*Microsoft.服務匯流排*）： 通常在所有 Azure 區域都可用。
- **[Azure 事件中心](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**（*Microsoft.事件Hub*）： 通常在所有 Azure 區域都可用。
- **[Azure 資料湖存儲第 1 代](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)***（Microsoft.AzureActiveDirectory）：* 通常在 ADLS Gen1 可用的所有 Azure 區域可用。
- **[Azure 應用服務](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)**：在應用服務可用的所有 Azure 區域中通常可用。

**公開預覽**

- **[Azure 容器註冊表](../container-registry/container-registry-vnet.md)**（*Microsoft.容器註冊表*）： 預覽在 Azure 容器註冊表可用的所有 Azure 區域都可用。

如需最新通知，請查看 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network)頁面。

## <a name="key-benefits"></a>主要權益

服務端點可提供下列優點：

- **Azure 服務資源的安全性提高了**：VNet 專用位址空間可能會重疊。 不能使用重疊空間來唯一地標識源自 VNet 的流量。 服務終結點通過將 VNet 標識擴展到服務，提供將 Azure 服務資源擴展到虛擬網路的能力。 在虛擬網路中啟用服務終結點後，可以添加虛擬網路規則，將 Azure 服務資源保護到虛擬網路。 規則添加通過完全刪除對資源的公共 Internet 訪問並允許僅從虛擬網路傳輸流量來提高安全性。
- **來自虛擬網路的 Azure 服務流量的最佳路由**：如今，虛擬網路中強制 Internet 流量到本地和/或虛擬裝置的任何路由也迫使 Azure 服務流量採用與 Internet 流量相同的路由。 服務端點可提供 Azure 流量的最佳路由。 

  端點一律會直接採用從虛擬網路到 Microsoft Azure 骨幹網路上服務的服務流量。 Keeping traffic on the Azure backbone network 可讓您透過強制通道，繼續稽核和監視來自虛擬網路的輸出網際網路流量，而不會影響服務流量。 有關使用者定義的路由和強制隧道的詳細資訊，請參閱[Azure 虛擬網路流量路由](virtual-networks-udr-overview.md)。
- **設定簡單且管理額外負荷較小**：虛擬網路中不再需要保留的公用 IP 位址，就可以透過 IP 防火牆保護 Azure 資源。 設置服務終結點不需要網路位址轉譯 （NAT） 或閘道設備。 您可以通過按一下子網來佈建服務終結點。 維護端點沒有額外的開銷。

## <a name="limitations"></a>限制

- 這項功能僅適用於透過 Azure Resource Manager 部署模型所部署的虛擬網路。
- 端點會在 Azure 虛擬網路中設定的子網路上啟用。 終結點不能用於從本地到 Azure 服務的流量。 有關詳細資訊，請參閱[從本地保護 Azure 服務訪問](#secure-azure-services-to-virtual-networks)
- 針對 Azure SQL，服務端點只適用於虛擬網路區域內的 Azure 服務流量。 對於 Azure 存儲，端點還擴展到包括部署虛擬網路以支援讀取存取異地冗余存儲 （RA-GRS） 和異地冗余存儲 （GRS） 流量的配對區域。 如需詳細資訊，請參閱 [Azure 配對區域](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)。
- 對於 Azure 資料湖存儲 （ADLS） 第 1 代，VNet 集成功能僅適用于同一區域內的虛擬網路。 另請注意，ADLS Gen1 的虛擬網路集成使用虛擬網路和 Azure 活動目錄 （Azure AD） 之間的虛擬網路服務終結點安全性在訪問權杖中生成其他安全聲明。 這些宣告隨後會用來對 Data Lake Storage Gen1 帳戶驗證虛擬網路並允許存取。 支援服務終結點的服務下列出的*Microsoft.AzureActiveDirectory*標記僅用於支援 ADLS 第 1 代的服務終結點。 Azure AD 不支援本機服務終結點。 有關 Azure 資料湖存儲第 1 代 VNet 集成的詳細資訊，請參閱[Azure 資料湖存儲 Gen1 中的網路安全](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="secure-azure-services-to-virtual-networks"></a>保護 Azure 服務到虛擬網路

- 虛擬網路服務端點可將您虛擬網路的身分識別提供給 Azure 服務。 在虛擬網路中啟用服務終結點後，可以添加虛擬網路規則，將 Azure 服務資源保護到虛擬網路。
- 現在，來自虛擬網路的 Azure 服務流量會使用公用 IP 位址作為來源 IP 位址。 透過服務端點，服務流量會在從虛擬網路存取 Azure 服務時，切換為使用虛擬網路私人位址作為來源 IP 位址。 此切換讓您不需要 IP 防火牆中使用的保留公用 IP 位址，即可存取服務。

   >[!NOTE]
   > 使用服務端點，服務流量子網路中虛擬機器的來源 IP 位址會從使用公用 IPv4 位址切換為使用私人 IPv4 位址。 使用 Azure 公用 IP 位址的現有 Azure 服務防火牆規則會停止使用此參數。 請先確定 Azure 服務防火牆規則允許這個參數，再設定服務端點。 設定服務端點時，您也可能遇到來自此子網路的服務流量暫時中斷。 
 
## <a name="secure-azure-service-access-from-on-premises"></a>保護來自本地的 Azure 服務訪問

  預設情況下，從本地網路無法訪問保護到虛擬網路的 Azure 服務資源。 如果需要允許來自內部部署的流量，您也必須允許內部部署或 ExpressRoute 中的公用 (通常是 NAT) IP 位址。 您可以通過 Azure 服務資源的 IP 防火牆配置添加這些 IP 位址。

  ExpressRoute：如果您使用[ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)進行公共對等互連或從您的場所對等互連，則需要標識您正在使用的 NAT IP 位址。 對於公共對等互連，每個 ExpressRoute 電路使用兩個 NAT IP 位址，預設情況下，當流量進入 Microsoft Azure 網路骨幹時，這些位址應用於 Azure 服務流量。 對於 Microsoft 對等互連，NAT IP 位址是客戶提供或由服務提供者提供的。若要允許存取您的服務資源，就必須在資源 IP 防火牆設定中允許這些公用 IP 位址。若要尋找您的公用對等互連 ExpressRoute 線路 IP 位址，請透過 Azure 入口網站[開啟有 ExpressRoute 的支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 有關用於 ExpressRoute 公共和微軟對等的 NAT 的詳細資訊，請參閱[ExpressRoute NAT 要求](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)。

![將 Azure 服務放到虛擬網路保護](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>組態

- 在虛擬網路中的子網上佈建服務終結點。 端點會使用在子網路內執行之任何類型的計算執行個體。
- 可以為子網上的所有受支援的 Azure 服務（例如 Azure 存儲或 Azure SQL 資料庫）配置多個服務終結點。
- 針對 Azure SQL Database，虛擬網路必須位於與 Azure 服務資源相同的區域中。 如果使用 GRS 和 RA-GRS Azure 儲存體帳戶，則主要帳戶必須位在與虛擬網路相同的區域中。 對於所有其他服務，都可以保護 Azure 服務資源到任何區域中的虛擬網路。 
- 端點設定所在的虛擬網路可以位於與 Azure 服務資源相同或不同的訂用帳戶中。 如需設定端點和保護 Azure 服務所需權限的詳細資訊，請參閱[佈建](#provisioning)。
- 對於支援的服務，您可以使用服務端點，將新的或現有資源放到虛擬網路保護。

### <a name="considerations"></a>考量

- 啟用服務終結點後，子網交換器中的虛擬機器的源 IP 位址。 源 IP 位址從使用公共 IPv4 位址切換到使用其專用 IPv4 位址，當從該子網與服務通信時。 在此切換期間，會關閉該服務的任何現有開放 TCP 連線。 在啟用或停用子網路服務的服務端點時，確定沒有任何重要工作正在執行中。 此外，確保在 IP 位址切換之後，您的應用程式可以自動連線到 Azure 服務。

  IP 位址切換只會影響來自您虛擬網路的服務流量。 對於分配給虛擬機器的公共 IPv4 位址或從公共 IPv4 位址定址的任何其他流量，都不會影響這些流量。 對於 Azure 服務，如果您有使用 Azure 公用 IP 位址的現有防火牆規則，則這些規則會停止使用切換至虛擬網路私人位址。
- 使用服務終結點時，Azure 服務的 DNS 條目將保持原樣，並繼續解析為分配給 Azure 服務的公共 IP 位址。

- 具有服務端點的網路安全性群組 (NSG)：
  - 預設情況下，NSG 允許出站 Internet 流量，也允許從 VNet 到 Azure 服務的流量。 此流量繼續按照現在的身份處理服務終結點。 
  - 如果要拒絕所有出站 Internet 流量，並且只允許流量訪問特定的 Azure 服務，則可以使用 NSG 中的[服務標記](security-overview.md#service-tags)執行此操作。 您可以在 NSG 規則中指定受支援的 Azure 服務作為目標，Azure 還提供每個標記基礎的 IP 位址的維護。 如需詳細資訊，請參閱 [Azure 服務標籤](security-overview.md#service-tags)。 

### <a name="scenarios"></a>案例

- **對等互連、已連線或多個虛擬網路**：若要將 Azure 服務放到一個虛擬網路或多個虛擬網路內的多個子網路保護，您可以獨立啟用每個子網路上的服務端點，並將 Azure 服務資源放到所有子網路保護。
- **篩選從虛擬網路到 Azure 服務的出站流量**：如果要檢查或篩選從虛擬網路發送到 Azure 服務的流量，則可以在虛擬網路中部署網路虛擬裝置。 接著，可以將服務端點套用到網路虛擬設備部署所在的子網路，只將 Azure 服務資源放到此子網路保護。 如果要使用網路虛擬裝置篩選將 Azure 服務訪問僅限制為特定的 Azure 資源，則此方案可能很有用。 如需詳細資訊，請參閱[使用網路虛擬設備輸出](/azure/architecture/reference-architectures/dmz/nva-ha)。
- **保護 Azure 資源到直接部署到虛擬網路中的服務**：可以直接將各種 Azure 服務部署到虛擬網路中的特定子網。 在受控服務子網路上設定服務端點，即可將 Azure 服務資源放到[受控服務](virtual-network-for-azure-services.md)子網路保護。
- **來自 Azure 虛擬機器的磁片流量**：託管和非託管磁片的虛擬機器磁片流量不受 Azure 存儲的服務終結點路由更改的影響。 此流量包括磁片 IO 以及裝載和卸載。 您可以限制 REST 對頁面 blob 的訪問，以便通過服務終結點和[Azure 存儲網路規則](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)選擇網路。 

### <a name="logging-and-troubleshooting"></a>記錄和疑難排解

將服務終結點配置為特定服務後，請通過以下方法驗證服務終結點路由是否有效： 
 
- 驗證服務診斷中任何服務要求的來源 IP 位址。 所有透過服務端點的新要求都會將要求的來源 IP 位址顯示為虛擬網路私人 IP 位址 (已指派給從您的虛擬網路提出要求的用戶端)。 未透過端點，此位址是 Azure 公用 IP 位址。
- 檢視子網路的任何網路介面上的有效路由。 服務的路由：
  - 顯示每個服務之位址前置詞範圍的更特定預設路由
  - 的 nextHopType 為 *VirtualNetworkServiceEndpoint*
  - 指示與任何強制隧道路由相比，與服務更直接的連接有效

>[!NOTE]
> 服務端點路由會覆寫 Azure 服務之位址前置詞相符項目的任何 BGP 或 UDR 路由。 有關詳細資訊，請參閱[使用有效路由進行故障排除](diagnose-network-routing-problem.md)。

## <a name="provisioning"></a>佈建

服務終結點可以由對虛擬網路的寫入存取權限的使用者在虛擬網路上獨立配置。 要保護 Azure 服務資源到 VNet，使用者必須具有*對 Microsoft.網路/虛擬網路/子網/joinViaService終結點/添加*子網的操作的許可權。 預設情況下，內置服務管理員角色包括此許可權。 您可以通過創建自訂角色來修改許可權。

有關內置角色的詳細資訊，請參閱[Azure 資源的內置角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 有關為自訂角色指派特定許可權的詳細資訊，請參閱 Azure[資源的自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

虛擬網路和 Azure 服務資源可以位在相同或不同的訂用帳戶中。 如果虛擬網路和 Azure 服務資源位在不同的訂用帳戶中，資源必須位在相同的 Active Directory (AD) 租用戶下。 

## <a name="pricing-and-limits"></a>價格和限制

使用服務終結點不收取額外費用。 Azure 服務的當前定價模型（Azure 存儲、Azure SQL 資料庫等）適用于當前。

虛擬網路中的服務終結點總數沒有限制。

某些 Azure 服務（如 Azure 存儲帳戶）可能會對用於保護資源的子網數實施限制。 有關詳細資訊，請參閱["後續步驟](#next-steps)"部分中有關各種服務的文檔。

## <a name="vnet-service-endpoint-policies"></a>VNet 服務終結點策略 

VNet 服務終結點策略允許您篩選到 Azure 服務的虛擬網路流量。 此篩選器僅允許在服務終結點上提供特定的 Azure 服務資源。 服務端點原則可針對流向 Azure 服務的虛擬網路流量提供更細微的存取控制。 有關詳細資訊，請參閱[虛擬網路服務終結點策略](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)。

## <a name="faqs"></a>常見問題集

有關常見問題解答，請參閱[虛擬網路服務終結點常見問題解答](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)。

## <a name="next-steps"></a>後續步驟

- [配置虛擬網路服務終結點](tutorial-restrict-network-access-to-resources.md)
- [將 Azure 存儲帳戶保護到虛擬網路](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [將 Azure SQL 資料庫保護到虛擬網路](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [將 Azure SQL 資料倉儲保護到虛擬網路](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [虛擬網路中的 Azure 服務集成](virtual-network-for-azure-services.md)
- [虛擬網路服務端點原則](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure 資源管理器範本](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

