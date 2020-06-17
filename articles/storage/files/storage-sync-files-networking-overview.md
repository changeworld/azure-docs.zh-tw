---
title: Azure 檔案同步的網路功能考量 | Microsoft Docs
description: Azure 檔案同步的網路功能選項概觀。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95fb967fa33a581096df65c3ed77e575aa3f6268
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466674"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure 檔案同步的網路功能考量
您可以透過兩種方式連線至 Azure 檔案共用：

- 直接透過 SMB 或 FileREST 通訊協定來存取共用。 此存取模式主要會在要盡可能減少所用的內部部署伺服器數目時使用。
- 使用 Azure 檔案同步在內部部署伺服器 (或 Azure VM) 上建立 Azure 檔案共用的快取，並使用針對您使用案例所選擇的通訊協定 (SMB、NFS、FTPS 等)，從內部部署伺服器存取檔案共用的資料。 此存取模式很方便，因為結合了內部部署效能與雲端級、無伺服器可連結服務 (例如 Azure 備份) 的最佳優勢。

此文章重點在於當您的使用案例適合使用 Azure 檔案同步在內部部署快取檔案，而不是直接透過 SMB 掛接 Azure 檔案共用時，應如何設定網路功能。 如需 Azure 檔案儲存體部署的網路功能考量相關詳細資訊，請參閱 [Azure 檔案儲存體的網路功能考量](storage-files-networking-overview.md)。

Azure 檔案同步的網路功能設定涵蓋兩個不同的 Azure 物件：儲存體同步服務與 Azure 儲存體帳戶。 儲存體帳戶是一種管理構造，所代表的是儲存體的共用集區，您可以在此集區中部署多個檔案共用，以及其他儲存體資源 (例如，Blob 容器或佇列)。 儲存體同步服務是一種管理結構，代表已註冊的伺服器，這是已和 Azure 檔案同步建立信任關係的 Windows 檔案伺服器，以及定義同步關係拓撲的同步群組。 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>使用 Azure 檔案同步將 Windows 檔案伺服器連線至 Azure 
若要設定及使用 Azure 檔案儲存體與 Azure 檔案同步，來與內部部署 Windows 檔案伺服器搭配使用，除了基本的網際網路連線之外，不需要任何特殊網路功能來連線 Azure。 若要部署 Azure 檔案同步，請在您想要與 Azure 同步的 Windows 檔案伺服器上安裝 Azure 檔案同步代理程式。 Azure 檔案同步代理程式會透過兩個通道與 Azure 檔案共用進行同步處理：

- FileREST 通訊協定，這是用來存取 Azure 檔案共用的 HTTPS 型通訊協定。 由於 FileREST 通訊協定會使用標準 HTTPS 進行資料傳輸，因此只有連接埠 443 必須能從外部存取。 Azure 檔案同步從您的內部部署 Windows 伺服器傳輸資料到 Azure 檔案共用時，不會使用 SMB 通訊協定。
- Azure 檔案同步同步處理通訊協定，這是用來交換同步處理知識 (也就是您環境中端點之間的檔案與資料夾的相關版本資訊) 的 HTTPS 型通訊協定。 此通訊協定也用來交換和環境中的檔案與資料夾有關的中繼資料，例如時間戳記與存取控制清單 (ACL)。 

由於 Azure 檔案儲存體在 Azure 檔案共用上提供直接的 SMB 通訊協定存取功能，因此客戶經常會想知道是否需要設定特殊的網路功能，才能使用 SMB 掛接 Azure 檔案共用，以供 Azure 檔案同步代理程式存取。 這不是必要的，但也不建議您這麼做 (除非您是系統管理員)，因為系統沒有快速變更偵測功能可偵測直接對 Azure 檔案共用進行的變更 (視 Azure 檔案共用中項目的大小與數目而定，變更可能超過 24 小時都不會被發現)。 如果您想要直接使用 Azure 檔案共用，也就是不使用 Azure 檔案同步在內部部署環境進行快取，您可以透過諮詢 [Azure 檔案儲存體網路功能概觀](storage-files-networking-overview.md)，來深入了解直接存取的網路功能考量。

雖然 Azure 檔案同步不需要任何特殊的網路功能設定，但某些客戶可能會希望設定進階網路功能設定，以支援下列情況：

- 與您組織的 Proxy 伺服器設定交互操作。
- 針對 Azure 檔案儲存體與 Azure 檔案同步服務，開啟您組織的內部部署防火牆。
- 透過 ExpressRoute 或 VPN 連線建立 Azure 檔案儲存體與 Azure 檔案同步之間的通道。

### <a name="configuring-proxy-servers"></a>設定 Proxy 伺服器
許多組織都會使用 Proxy 伺服器，作為其內部部署網路內部資源與其網路外之資源 (例如在 Azure 中) 之間的媒介。 Proxy 伺服器非常適合用於許多應用程式，例如網路隔離與安全性，以及監視及記錄。 Azure 檔案同步可以與 Proxy 伺服器完全互通，不過，您必須使用 Azure 檔案同步手動設定環境的 Proxy 端點設定。這必須透過 PowerShell 使用 Azure 檔案同步伺服器 Cmdlet 來完成。 

如需有關如何使用 Proxy 伺服器設定 Azure 檔案同步的詳細資訊，請參閱[使用 Proxy 伺服器設定 Azure 檔案同步](storage-sync-files-firewall-and-proxy.md)。

### <a name="configuring-firewalls-and-service-tags"></a>設定防火牆與服務標籤
基於安全性目的，您可能會將檔案伺服器與大部分的網際網路位置隔離。 若要在您的環境中使用 Azure 檔案同步，您必須調整防火牆以向特定 Azure 服務開放。 若要這樣做，您可以透過稱為[服務標籤](../../virtual-network/service-tags-overview.md)的機制，擷取所需服務的 IP 位址範圍。

Azure 檔案同步需要下列服務的 IP 位址範圍，就如同它們的服務標籤所識別的那樣：

| 服務 | 描述 | 服務標籤 |
|---------|-------------|-------------|
| Azure 檔案同步 | 就像儲存體同步服務物件一樣，顧名思義，「Azure 檔案同步」服務會負責執行在 Azure 檔案共用與 Windows 檔案伺服器之間同步處理資料的核心活動。 | `StorageSyncService` |
| Azure 檔案 | 透過 Azure 檔案同步同步處理的所有資料都會儲存在 Azure 檔案共用中。 您 Windows 檔案伺服器上變更的檔案會複寫至您的 Azure 檔案共用，而您內部部署檔案伺服器上的階層式檔案則會在使用者要求時順暢地下載。 | `Storage` |
| Azure Resource Manager | Azure Resource Manager 是 Azure 的管理介面。 所有管理呼叫 (包括 Azure 檔案同步伺服器註冊與進行中的同步處理伺服器工作) 都是透過 Azure Resource Manager 進行的。 | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory (或 Azure AD) 包含授權對儲存體同步服務進行伺服器註冊時所需的使用者主體，以及授權 Azure 檔案同步存取您的雲端資源時所需的服務主體。 | `AzureActiveDirectory` |

如果您在 Azure 中使用 Azure 檔案同步，即使其是不同的區域，您也可以直接在您的網路安全性群組中使用服務標籤的名稱，以允許對該服務傳送流量。 若要深入了解如何執行此操作，請參閱[網路安全性群組](../../virtual-network/security-overview.md)。 

如果您是使用 Azure 檔案同步內部部署，就可利用服務標籤 API 來取得您防火牆允許清單的特定 IP 位址範圍。 有兩種方法可以取得此資訊：

- 所有支援服務標籤之 Azure 服務的目前 IP 位址範圍清單，都會以 JSON 文件的形式，每週在 Microsoft 下載中心發佈。 每個 Azure 雲端都有自己的 JSON 文件，其中包含與該雲端相關的 IP 位址範圍：
    - [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Gov](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
- 服務標籤探索 API (預覽) 可讓您以程式設計方式擷取目前的服務標籤清單。 在預覽中，服務標籤探索 API 傳回的資訊，可能沒有從 Microsoft 下載中心上發佈的 JSON 文件所傳回的資訊那麼新。 您可以根據您的自動化喜好設定來使用 API 介面：
    - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

若要深入了解如何使用服務標籤 API 來擷取您服務的位址，請參閱 [Azure 檔案同步 IP 位址的允許清單](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses)。

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>透過虛擬私人網路或 ExpressRoute 以通道傳送流量
有些組織需要與 Azure 通訊，以透過網路通道 (例如虛擬私人網路 (VPN) 或 ExpressRoute) 來獲得一層額外的安全性，或確保與 Azure 的通訊遵循確定性的路由。 

當您在內部部署網路與 Azure 之間建立網路通道時，您會將內部部署網路與 Azure 中的一或多個虛擬網路對等互連。 [虛擬網路](../../virtual-network/virtual-networks-overview.md) (或 VNet) 類似於您會在內部部署環中運作的傳統網路。 和 Azure 儲存體帳戶或 Azure VM 一樣，VNet 也是部署在資源群組中的 Azure 資源。 

Azure 檔案儲存體與檔案同步支援下列機制，以在您的內部部署伺服器與 Azure 之間以通道傳送流量：

- [Azure VPN 閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md)：VPN 閘道是特定的虛擬網路閘道類型，可透過網際網路在 Azure 虛擬網路與替代位置 (例如，內部部署環境) 之間傳送加密流量。 Azure VPN 閘道是一種 Azure 資源，可供部署在儲存體帳戶或其他 Azure 資源的資源群組中。 由於 Azure 檔案同步是要與內部部署 Windows 檔案伺服器搭配使用的，因此您通常會使用[站對站 (S2S) VPN](../../vpn-gateway/design.md#s2smulti)，雖然在技術上，您可以使用[點對站 (P2S) VPN](../../vpn-gateway/point-to-site-about.md)。 

    站對站 (S2S) VPN 連線會連線您的 Azure 虛擬網路與您組織的內部部署網路。 S2S VPN 連線可讓您一次就為組織網路上裝載的 VPN 伺服器或裝置設定 VPN 連線，而不必針對需要存取 Azure 檔案共用的每個用戶端裝置進行設定。 若要簡化 S2S VPN 連線的部署，請參閱[設定站對站 (S2S) VPN 以用於 Azure 檔案儲存體](storage-files-configure-s2s-vpn.md)。

- [ExpressRoute](../../expressroute/expressroute-introduction.md)，可讓您在 Azure 與不會周遊網際網路的內部部署網路之間建立已定義好的路由。 由於 ExpressRoute 會在您的內部部署資料中心與 Azure 之間提供專用路徑，因此若要將網路效能納入考量時，ExpressRoute 可能會很有用。 當組織的原則或法規需求要求您的雲端資源必須有確定的路徑時，ExpressRoute 也會是不錯的選擇。

### <a name="private-endpoints"></a>私人端點
除了 Azure 檔案儲存體與檔案同步透過儲存體帳戶與儲存體同步服務提供的預設公用端點之外，Azure 檔案儲存體與檔案同步也提供每個資源都有一或多個私人端點的選項。 當您為 Azure 資源建立私人端點時，Azure 資源便會從您虛擬網路的位址空間內取得私人 IP 位址，情況非常類似於您的內部部署 Windows 檔案伺服器如何獲得您內部部署網路專用位址空間內的 IP 位址。 

> [!Important]  
> 若要在儲存體同步服務資源上使用私人端點，您必須使用 Azure 檔案同步代理程式 10.1 版或更新版本。 10.1 之前的代理程式版本不支援儲存體同步服務上的私人端點。 所有先前的代理程式版本都支援儲存體帳戶資源上的私人端點。

個別的私人端點會與特定的 Azure 虛擬網路子網路相關聯。 儲存體帳戶與儲存體同步服務可能會在超過一個虛擬網路中有私人端點。

使用私人端點可讓您：
- 使用 VPN 或 ExpressRoute 連線搭配私人對等互連，來安全地從內部部署網路連線到您的 Azure 資源。
- 透過停用 Azure 檔案儲存體與檔案同步的公用端點，以保護您的 Azure 資源。根據預設，建立私人端點並不會封鎖對公用端點的連線。
- 藉由讓您能夠封鎖從虛擬網路 (和對等互連界限) 外泄資料的可能，來提高虛擬網路的安全性。

若要建立私人端點，請參閱[設定 Azure 檔案同步的私人端點](storage-sync-files-networking-endpoints.md)。

### <a name="private-endpoints-and-dns"></a>私人端點和 DNS
當您建立私人端點時，根據預設，我們也會建立與 `privatelink` 子網域對應的私人 DNS 區域，或更新現有的私人 DNS 區域。 針對公用雲端區域，這些 DNS 區域為 `privatelink.afs.azure.net` (針對 Azure 檔案儲存體) 與 `privatelink.file.core.windows.net` (針對 Azure 檔案同步)。

> [!Note]  
> 本文會針對 Azure 公用區域使用儲存體帳戶 DNS 尾碼 `core.windows.net`。 此註解也適用於 Azure 主權雲端 (例如 Azure 美國政府雲端和 Azure 中國雲端)，只需替換為適合您環境的尾碼即可。 

當您建立儲存體帳戶與儲存體同步服務的私人端點時，我們會分別為它們在其各自的私人 DNS 區域中建立 A 記錄。 我們也會更新公用 DNS 項目，讓一般完整網域名稱為相關私人連結名稱的 CNAME。 這可讓完整網域名稱在要求者位於虛擬網路內部時，指向私人端點 IP 位址，並在要求者位於虛擬網路外部時，指向公用端點 IP 位址。 

針對 Azure 檔案儲存體，每個私人端點都會有對應至一個私人端點私人 IP 位址的單一完整網域名稱 (遵循模式 `storageaccount.privatelink.file.core.windows.net`)。 針對 Azure 檔案同步，每個私人端點都有四個完整網域名稱，用於 Azure 檔案同步所公開的四個不同端點：管理、同步 (主要)、同步 (次要) 和監視。 除非名稱包含非 ASCII 字元，否則這些端點的完整網域名稱通常會遵循儲存體同步服務的名稱。 例如，如果您的儲存體同步服務在美國西部 2 區域中的名稱為 `mysyncservice`，對等端點就會是 `mysyncservicemanagement.westus2.afs.azure.net`、`mysyncservicesyncp.westus2.afs.azure.net`、`mysyncservicesyncs.westus2.afs.azure.net` 與 `mysyncservicemonitoring.westus2.afs.azure.net`。 儲存體同步服務的每個私人端點都將包含 4 個不同的 IP 位址。 

由於您的 Azure 私人 DNS 區域會連線到包含私人端點的虛擬網路，因此您可以在 Azure VM 中從 PowerShell 呼叫 `Resolve-DnsName` Cmdlet (在 Windows 和 Linux 中則為 `nslookup`) 來觀察 DNS 設定：

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

在此範例中，儲存體帳戶 `storageaccount.file.core.windows.net` 會解析為私人端點的私人 IP 位址，而這正好是 `192.168.0.4`。

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

如果您從內部部署環境執行相同的命令，則會發現同一個儲存體帳戶名稱會轉而解析為儲存體帳戶的公用 IP 位址；`storageaccount.file.core.windows.net` 是 `storageaccount.privatelink.file.core.windows.net` 的 CNAME 記錄，而 `storageaccount.privatelink.file.core.windows.net` 又是儲存體帳戶裝載所在 Azure 儲存體叢集的 CNAME 記錄：

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

這反映出一個事實，也就是 Azure 檔案儲存體與 Azure 檔案同步都可以公開其的公用端點，以及每個資源的一或多個私人端點。 若要確保您資源的完整網域名稱會解析為私人端點私人 IP 位址，您必須變更內部部署 DNS 伺服器上的設定。 這可以透過數種方式來完成：

- 修改您用戶端上的 hosts 檔案，讓儲存體帳戶與儲存體同步服務的完整網域名稱解析成所需的私人 IP 位址。 強烈建議您不要在生產環境中這樣做，因為您將需要對需存取您私人端點的每個用戶端進行這些變更。 對您的私人端點/資源所做的變更 (刪除、修改等) 將不會自動處理。
- 使用您 Azure 資源的 A 記錄，在您的內部部署伺服器上，針對 `privatelink.file.core.windows.net` 與 `privatelink.afs.azure.net` 建立 DNS 區域。 這麼做的好處是，內部部署環境中的用戶端將能夠自動解析 Azure 資源，而不需要設定每個用戶端，不過，此解決方案和修改 hosts 檔案一樣不方便，因為變更並不會加以反映出來。 雖然這個解決方案不怎麼方便，但在某些環境中卻可能是最佳選擇。
- 將 `core.windows.net` 與 `afs.azure.net` 區域從內部部署 DNS 伺服器轉送到您的 Azure 私人 DNS 區域。 您可以透過只能在連結至 Azure 私人 DNS 區域的虛擬網路內存取的特殊 IP 位址 (`168.63.129.16`)，來連線到 Azure 私人 DNS 主機。 為因應此限制，您可以在虛擬網路內執行會將 `core.windows.net` 與 `afs.azure.net` 轉送至對等的 Azure 私人 DNS 區域的其他 DNS 伺服器。 為簡化這項設定，我們提供了 PowerShell Cmdlet，以在 Azure 虛擬網路中自動部署 DNS 伺服器，並視需要加以設定。 若要了解如何設定 DNS 轉送，請參閱[使用 Azure 檔案儲存體設定 DNS](storage-files-networking-dns.md)。

## <a name="encryption-in-transit"></a>傳輸中加密
系統一律會加密從 Azure 檔案同步代理程式連線到您的 Azure 檔案共用或儲存體同步服務的連線。 雖然 Azure 儲存體帳戶有一個設定，可讓您停用針對與 Azure 檔案儲存體 (以及從儲存體帳戶外管理的其他 Azure 儲存體服務) 的通訊要求進行傳輸中加密，但停用此設定並不會影響 Azure 檔案同步在與 Azure 檔案儲存體通訊時進行的加密。 根據預設，所有 Azure 儲存體帳戶都會啟用傳輸中加密。 

如需傳輸中加密的詳細資訊，請參閱[在 Azure 儲存體中需要安全傳輸](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="see-also"></a>另請參閱
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)