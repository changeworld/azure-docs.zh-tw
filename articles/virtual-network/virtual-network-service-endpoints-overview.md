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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244780"
---
# <a name="virtual-network-service-endpoints"></a>虛擬網路服務端點

虛擬網路（VNet）服務端點會擴充您的虛擬網路私人位址空間。 端點也會透過直接連線，將您 VNet 的身分識別延伸至 Azure 服務。 端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。 從您的 VNet 到 Azure 服務的流量一定會保留在 Microsoft Azure 骨幹網路上。

這項功能適用于下列 Azure 服務和區域。 *\** 資源是以括弧括住。 設定服務的服務端點時，在子網中啟用此資源：

**正式推出**

- **[Azure 儲存體](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** （*Microsoft 儲存體*）：已在所有 Azure 區域中正式推出。
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft .sql*）：已在所有 Azure 區域中正式推出。
- **[Azure SQL 資料倉儲](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft .sql*）：已在所有 Azure 區域中正式推出。
- **[適用於 PostgreSQL 的 Azure 資料庫 server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft .sql*）：已在可用資料庫服務的 Azure 區域中正式運作。
- **[適用於 MySQL 的 Azure 資料庫 server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft .sql*）：已在可用資料庫服務的 Azure 區域中正式運作。
- **[適用於 MariaDB 的 Azure 資料庫](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** （*Microsoft .sql*）：已在可用資料庫服務的 Azure 區域中正式推出。
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*AzureCosmosDB*）：已在所有 Azure 區域正式推出。
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** （*KeyVault*）：已在所有 Azure 區域正式推出。
- **[Azure 服務匯流排](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft 執行匯流排*）：已在所有 Azure 區域正式推出。
- **[Azure 事件中樞](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft EventHub*）：已在所有 Azure 區域中正式推出。
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*AzureActiveDirectory*）：已在可用 ADLS Gen1 的所有 Azure 區域中正式推出。
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** ：已在可用 App Service 的所有 Azure 區域中正式運作。

**公開預覽**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** （*ContainerRegistry*）：可在 Azure Container Registry 的所有 Azure 區域中使用預覽。

如需最新通知，請查看 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network)頁面。

## <a name="key-benefits"></a>主要權益

服務端點可提供下列優點：

- 已**改善您 Azure 服務資源的安全性**： VNet 私人位址空間可以重迭。 您不能使用重迭的空格來唯一識別源自您 VNet 的流量。 服務端點藉由將 VNet 身分識別延伸至服務，讓您能夠保護虛擬網路的 Azure 服務資源。 一旦您在虛擬網路中啟用服務端點，您可以新增虛擬網路規則，以將 Azure 服務資源保護到您的虛擬網路。 新增規則會藉由完全移除資源的公用網際網路存取，並只允許來自您虛擬網路的流量，來提供更好的安全性。
- **來自虛擬網路之 Azure 服務流量的最佳路由**：今天，虛擬網路中強制網際網路流量進入內部部署和/或虛擬裝置的任何路由也會強制 Azure 服務流量採用與網際網路流量相同的路由。 服務端點可提供 Azure 流量的最佳路由。 

  端點一律會直接採用從虛擬網路到 Microsoft Azure 骨幹網路上服務的服務流量。 Keeping traffic on the Azure backbone network 可讓您透過強制通道，繼續稽核和監視來自虛擬網路的輸出網際網路流量，而不會影響服務流量。 如需有關使用者定義路由和強制通道的詳細資訊，請參閱[Azure 虛擬網路流量路由](virtual-networks-udr-overview.md)。
- **設定簡單且管理額外負荷較小**：虛擬網路中不再需要保留的公用 IP 位址，就可以透過 IP 防火牆保護 Azure 資源。 設定服務端點時，不需要網路位址轉譯（NAT）或閘道裝置。 您可以透過簡單的按一下子網來設定服務端點。 維護端點沒有額外的負荷。

## <a name="limitations"></a>限制

- 這項功能僅適用於透過 Azure Resource Manager 部署模型所部署的虛擬網路。
- 端點會在 Azure 虛擬網路中設定的子網路上啟用。 端點無法用於從您的內部部署到 Azure 服務的流量。 如需詳細資訊，請參閱[從內部部署保護 Azure 服務存取](#secure-azure-services-to-virtual-networks)
- 針對 Azure SQL，服務端點只適用於虛擬網路區域內的 Azure 服務流量。 針對 Azure 儲存體，端點也會擴充為包含您部署虛擬網路的配對區域，以支援讀取權限異地多餘儲存體（RA-GRS）和異地多餘儲存體（GRS）流量。 如需詳細資訊，請參閱 [Azure 配對區域](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)。
- 針對 Azure Data Lake Storage （ADLS） Gen 1，VNet 整合功能僅適用于相同區域內的虛擬網路。 另請注意，ADLS Gen1 的虛擬網路整合會使用虛擬網路與 Azure Active Directory （Azure AD）之間的虛擬網路服務端點安全性，在存取權杖中產生額外的安全性宣告。 這些宣告隨後會用來對 Data Lake Storage Gen1 帳戶驗證虛擬網路並允許存取。 [服務支援服務端點] 底下所列的*AzureActiveDirectory*標記僅用於支援服務端點以 ADLS Gen 1。 Azure AD 不會以原生方式支援服務端點。 如需 Azure Data Lake 儲存第1代 VNet 整合的詳細資訊，請參閱[Azure Data Lake Storage Gen1 中的網路安全性](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="secure-azure-services-to-virtual-networks"></a>保護虛擬網路的 Azure 服務

- 虛擬網路服務端點可將您虛擬網路的身分識別提供給 Azure 服務。 一旦您在虛擬網路中啟用服務端點，您可以新增虛擬網路規則，以將 Azure 服務資源保護到您的虛擬網路。
- 現在，來自虛擬網路的 Azure 服務流量會使用公用 IP 位址作為來源 IP 位址。 透過服務端點，服務流量會在從虛擬網路存取 Azure 服務時，切換為使用虛擬網路私人位址作為來源 IP 位址。 此切換讓您不需要 IP 防火牆中使用的保留公用 IP 位址，即可存取服務。

   >[!NOTE]
   > 使用服務端點，服務流量子網路中虛擬機器的來源 IP 位址會從使用公用 IPv4 位址切換為使用私人 IPv4 位址。 使用 Azure 公用 IP 位址的現有 Azure 服務防火牆規則會停止使用此參數。 請先確定 Azure 服務防火牆規則允許這個參數，再設定服務端點。 設定服務端點時，您也可能遇到來自此子網路的服務流量暫時中斷。 
 
## <a name="secure-azure-service-access-from-on-premises"></a>從內部部署保護 Azure 服務存取

  根據預設，無法從內部部署網路連線到虛擬網路所保護的 Azure 服務資源。 如果需要允許來自內部部署的流量，您也必須允許內部部署或 ExpressRoute 中的公用 (通常是 NAT) IP 位址。 您可以透過 Azure 服務資源的 IP 防火牆設定來新增這些 IP 位址。

  ExpressRoute：如果您使用[expressroute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)進行公用對等互連或 Microsoft 對等互連，您將需要識別您所使用的 NAT IP 位址。 針對公用對等互連，每個 ExpressRoute 線路預設都會使用兩個 NAT IP 位址，而當流量進入 Microsoft Azure 網路骨幹時，就會套用至 Azure 服務流量。 針對 Microsoft 對等互連，NAT IP 位址是由服務提供者提供或提供的客戶。 若要允許存取您的服務資源，您必須在資源 IP 防火牆設定中允許這些公用 IP 位址。 若要尋找您的公用對等互連 ExpressRoute 線路 IP 位址，請透過 Azure 入口網站[開啟具有 ExpressRoute 的支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 如需有關適用于 ExpressRoute 公用和 Microsoft 對等互連的 NAT 詳細資訊，請參閱[EXPRESSROUTE NAT 需求](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)。

![將 Azure 服務放到虛擬網路保護](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>組態

- 在虛擬網路中的子網上設定服務端點。 端點會使用在子網路內執行之任何類型的計算執行個體。
- 您可以為子網上的所有支援的 Azure 服務（例如 Azure 儲存體或 Azure SQL Database）設定多個服務端點。
- 針對 Azure SQL Database，虛擬網路必須位於與 Azure 服務資源相同的區域中。 如果使用 GRS 和 RA-GRS Azure 儲存體帳戶，則主要帳戶必須位在與虛擬網路相同的區域中。 針對所有其他服務，您可以將 Azure 服務資源保護到任何區域中的虛擬網路。 
- 端點設定所在的虛擬網路可以位於與 Azure 服務資源相同或不同的訂用帳戶中。 如需設定端點和保護 Azure 服務所需權限的詳細資訊，請參閱[佈建](#provisioning)。
- 對於支援的服務，您可以使用服務端點，將新的或現有資源放到虛擬網路保護。

### <a name="considerations"></a>考量

- 啟用服務端點之後，子網中虛擬機器的來源 IP 位址會切換。 來源 IP 位址會在與該子網中的服務進行通訊時，從使用公用 IPv4 位址切換為使用其私人 IPv4 位址。 在此切換期間，會關閉該服務的任何現有開放 TCP 連線。 在啟用或停用子網路服務的服務端點時，確定沒有任何重要工作正在執行中。 此外，確保在 IP 位址切換之後，您的應用程式可以自動連線到 Azure 服務。

  IP 位址切換只會影響來自您虛擬網路的服務流量。 對指派給虛擬機器的公用 IPv4 位址的任何其他流量，並不會有任何影響。 對於 Azure 服務，如果您有使用 Azure 公用 IP 位址的現有防火牆規則，則這些規則會停止使用切換至虛擬網路私人位址。
- 透過服務端點，Azure 服務的 DNS 專案會保持現狀，並繼續解析為指派給 Azure 服務的公用 IP 位址。

- 具有服務端點的網路安全性群組 (NSG)：
  - 根據預設，Nsg 允許輸出網際網路流量，也允許從 VNet 到 Azure 服務的流量。 此流量會繼續以相同的方式使用服務端點。 
  - 如果您想要拒絕所有輸出網際網路流量，並僅允許特定 Azure 服務的流量，您可以使用 Nsg 中的[服務](security-overview.md#service-tags)標籤來執行此動作。 您可以在 NSG 規則中將支援的 Azure 服務指定為目的地，而 Azure 也會提供每個標籤基礎的 IP 位址維護。 如需詳細資訊，請參閱 [Azure 服務標籤](security-overview.md#service-tags)。 

### <a name="scenarios"></a>案例

- **對等互連、已連線或多個虛擬網路**：若要將 Azure 服務放到一個虛擬網路或多個虛擬網路內的多個子網路保護，您可以獨立啟用每個子網路上的服務端點，並將 Azure 服務資源放到所有子網路保護。
- **篩選從虛擬網路到 azure 服務的輸出流量**：如果您想要檢查或篩選從虛擬網路傳送至 azure 服務的流量，您可以在虛擬網路內部署網路虛擬裝置。 接著，可以將服務端點套用到網路虛擬設備部署所在的子網路，只將 Azure 服務資源放到此子網路保護。 如果您想要使用網路虛擬裝置篩選，將 Azure 服務從您的虛擬網路存取限制為僅限特定的 Azure 資源，此案例可能會很有説明。 如需詳細資訊，請參閱[使用網路虛擬設備輸出](/azure/architecture/reference-architectures/dmz/nva-ha)。
- **將 azure 資源放到直接部署至虛擬網路的服務保護**：您可以直接將各種 azure 服務部署到虛擬網路中的特定子網。 在受控服務子網路上設定服務端點，即可將 Azure 服務資源放到[受控服務](virtual-network-for-azure-services.md)子網路保護。
- **來自 Azure 虛擬機器的磁片流量**：受管理和非受控磁片的虛擬機器磁片流量不會受到 Azure 儲存體的服務端點路由變更影響。 此流量包括 diskIO，以及掛接和卸載。 您可以限制對分頁 blob 的 REST 存取，以透過服務端點選取網路，並[Azure 儲存體的網路規則](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 

### <a name="logging-and-troubleshooting"></a>記錄和疑難排解

將服務端點設定為特定服務後，請驗證服務端點路由是否有效，方法如下： 
 
- 驗證服務診斷中任何服務要求的來源 IP 位址。 所有透過服務端點的新要求都會將要求的來源 IP 位址顯示為虛擬網路私人 IP 位址 (已指派給從您的虛擬網路提出要求的用戶端)。 未透過端點，此位址是 Azure 公用 IP 位址。
- 檢視子網路的任何網路介面上的有效路由。 服務的路由：
  - 顯示每個服務之位址前置詞範圍的更特定預設路由
  - 的 nextHopType 為 *VirtualNetworkServiceEndpoint*
  - 表示相較于任何強制通道路由，與服務的更直接連線已生效

>[!NOTE]
> 服務端點路由會覆寫 Azure 服務之位址前置詞相符項目的任何 BGP 或 UDR 路由。 如需詳細資訊，請參閱[使用有效路由進行疑難排解](diagnose-network-routing-problem.md)。

## <a name="provisioning"></a>佈建

服務端點可以在虛擬網路上，獨立于具有虛擬網路寫入權限的使用者來設定。 若要保護 VNet 的 Azure 服務資源，使用者必須擁有所新增子網的*virtualNetworks/subnet/joinViaServiceEndpoint/action*許可權。 根據預設，內建的服務管理員角色包含此許可權。 您可以藉由建立自訂角色來修改許可權。

如需內建角色的詳細資訊，請參閱[Azure 資源的內建角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 如需將特定許可權指派給自訂角色的詳細資訊，請參閱[Azure 資源的自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

虛擬網路和 Azure 服務資源可以位在相同或不同的訂用帳戶中。 如果虛擬網路和 Azure 服務資源位在不同的訂用帳戶中，資源必須位在相同的 Active Directory (AD) 租用戶下。 

## <a name="pricing-and-limits"></a>價格和限制

使用服務端點不需要額外付費。 目前 Azure 服務（Azure 儲存體、Azure SQL Database 等）的定價模式適用于現狀。

虛擬網路中的服務端點總數沒有限制。

某些 Azure 服務（例如 Azure 儲存體帳戶）可能會強制執行用來保護資源的子網數目限制。 如需詳細資訊，請參閱[後續步驟](#next-steps)一節中各種服務的檔。

## <a name="vnet-service-endpoint-policies"></a>VNet 服務端點原則 

VNet 服務端點原則可讓您篩選 Azure 服務的虛擬網路流量。 此篩選器只允許透過服務端點的特定 Azure 服務資源。 服務端點原則可針對流向 Azure 服務的虛擬網路流量提供更細微的存取控制。 如需詳細資訊，請參閱[虛擬網路服務端點原則](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)。

## <a name="faqs"></a>常見問題集

如需常見問題，請參閱[虛擬網路服務端點常見問題](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)。

## <a name="next-steps"></a>後續步驟

- [設定虛擬網路服務端點](tutorial-restrict-network-access-to-resources.md)
- [保護虛擬網路的 Azure 儲存體帳戶](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [保護虛擬網路的 Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [保護虛擬網路的 Azure SQL 資料倉儲](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [虛擬網路中的 Azure 服務整合](virtual-network-for-azure-services.md)
- [虛擬網路服務端點原則](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

