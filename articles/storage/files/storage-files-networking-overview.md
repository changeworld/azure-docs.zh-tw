---
title: Azure 檔案儲存體的網路功能考量 | Microsoft Docs
description: Azure 檔案儲存體的網路功能選項概觀。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7d95cc08595296d697618cbb3ff0025c7c212a1f
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84296522"
---
# <a name="azure-files-networking-considerations"></a>Azure 檔案儲存體的網路功能考量 
您可以透過兩種方式連線至 Azure 檔案共用：

- 直接透過 SMB 或 FileREST 通訊協定來存取共用。 此存取模式主要會在要盡可能減少所用的內部部署伺服器數目時使用。
- 使用 Azure 檔案同步在內部部署伺服器上建立 Azure 檔案共用的快取，並使用針對使用案例所選擇的通訊協定 (SMB、NFS、FTPS 等)，從內部部署伺服器存取檔案共用的資料。 此存取模式很方便，因為結合了內部部署效能與雲端級、無伺服器可連結服務 (例如 Azure 備份) 的最佳優勢。

本文會著重在說明當您的使用案例要直接存取 Azure 檔案共用而非使用 Azure 檔案同步時，該如何設定網路功能。如需 Azure 檔案同步部署的網路考量詳細資訊，請參閱[設定 Azure 檔案同步 Proxy 和防火牆設定](storage-sync-files-firewall-and-proxy.md)。

Azure 檔案共用的網路功能設定是在 Azure 儲存體帳戶上進行的。 儲存體帳戶是一種管理構造，所代表的是儲存體的共用集區，您可以在此集區中部署多個檔案共用，以及其他儲存體資源 (例如，Blob 容器或佇列)。 儲存體帳戶會公開多個設定，以協助您保護針對檔案共用的網路存取：網路端點、儲存體帳戶防火牆設定和傳輸中加密。 

強烈建議您先閱讀[規劃 Azure 檔案儲存體部署](storage-files-planning.md)，再遵循此概念指南。

## <a name="accessing-your-azure-file-shares"></a>存取 Azure 檔案共用
當您在儲存體帳戶內部署 Azure 檔案共用時，透過儲存體帳戶的公用端點即可立即存取檔案共用。 這表示已完成驗證的要求 (例如，由使用者的登入身分識別所授權的要求) 便可從 Azure 內部或外部安全地產生。 

在許多客戶的環境中，最初將 Azure 檔案共用掛接到內部部署工作站時將會失敗，即使能從 Azure VM 掛接成功也一樣。 之所以會這樣，原因是許多組織和網際網路服務提供者 (ISP) 會將 SMB 用來通訊的連接埠 (連接埠445) 封鎖起來。 這種做法源自舊版和已過時 SMB 通訊協定版本的相關安全性指引。 雖然 SMB 3.0 是一種可在網際網路上安全使用的通訊協定，但舊版 SMB (尤其是 SMB 1.0) 卻不是。 Azure 檔案共用只能透過公用端點從外部透過 SMB 3.0 和 FileREST 通訊協定 (也是一種可在網際網路上安全使用的通訊協定) 來加以存取。

由於從內部部署環境存取 Azure 檔案共用的最簡單方式，就是對連接埠 445 開放內部部署網路，因此 Microsoft 建議您使用下列步驟從環境中移除 SMB 1.0：

1. 確定組織的裝置上已移除或停用 SMB 1.0。 目前支援的 Windows 和 Windows Server 版本全都支援移除或停用 SMB 1.0，而從 Windows 10 版本 1709 開始，Windows 上就已預設不會安裝 SMB 1.0。 若要深入了解如何停用 SMB 1.0，請參閱我們的作業系統專屬頁面：
    - [保護 Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [保護 Linux](storage-how-to-use-files-linux.md#securing-linux)
2. 確定組織中沒有任何產品需要 SMB 1.0，並移除有此需要的產品。 我們會維護 [SMB1 Product Clearinghouse](https://aka.ms/stillneedssmb1)，裡面會有 Microsoft 已知需要 SMB 1.0 的所有第一方和第三方產品。 
3. (選擇性) 搭配使用第三方防火牆與組織的內部部署網路，以防止 SMB 1.0 流量離開組織界限。

如果組織需要根據政策或法規封鎖連接埠 445，或組織需要流向 Azure 的流量遵循決定性路徑，您可以使用 Azure VPN 閘道或 ExpressRoute 透過通道將流量傳送至 Azure 檔案共用。

> [!Important]  
> 即使您決定使用替代方法來存取 Azure 檔案共用，Microsoft 仍建議您從環境中移除 SMB 1.0。

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>透過虛擬私人網路或 ExpressRoute 以通道傳送流量
當您在內部部署網路與 Azure 之間建立網路通道時，您會將內部部署網路與 Azure 中的一或多個虛擬網路對等互連。 [虛擬網路](../../virtual-network/virtual-networks-overview.md) (或 VNet) 類似於您會在內部部署環中運作的傳統網路。 和 Azure 儲存體帳戶或 Azure VM 一樣，VNet 也是部署在資源群組中的 Azure 資源。 

Azure 檔案儲存體支援下列機制，以在內部部署工作站和伺服器與 Azure 之間以通道傳送流量：

- [Azure VPN 閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md)：VPN 閘道是特定的虛擬網路閘道類型，可透過網際網路在 Azure 虛擬網路與替代位置 (例如，內部部署環境) 之間傳送加密流量。 Azure VPN 閘道是一種 Azure 資源，可供部署在儲存體帳戶或其他 Azure 資源的資源群組中。 VPN 閘道會公開兩種不同的連線類型：
    - [點對站 (P2S) VPN](../../vpn-gateway/point-to-site-about.md) 閘道連線，這是 Azure 與個別用戶端之間的 VPN 連線。 此解決方案主要適用於不屬於組織內部部署網路的裝置，例如，想要能夠從家裡、咖啡廳或外出時所住的飯店掛接其 Azure 檔案共用的遠距工作者。 若要將 P2S VPN 連線用於 Azure 檔案儲存體，必須為每個要連線的用戶端設定 P2S VPN 連線。 若要簡化 P2S VPN 連線的部署，請參閱[在 Windows 上設定點對站 (P2S) VPN 以用於 Azure 檔案儲存體](storage-files-configure-p2s-vpn-windows.md)和[在 Linux 上設定點對站 (P2S) VPN 以用於 Azure 檔案儲存體](storage-files-configure-p2s-vpn-linux.md)使用。
    - [站對站 (S2S) VPN](../../vpn-gateway/design.md#s2smulti)，這是 Azure 與您組織內網路之間的 VPN 連線。 S2S VPN 連線可讓您一次就為組織網路上裝載的 VPN 伺服器或裝置設定 VPN 連線，而不必針對需要存取 Azure 檔案共用的每個用戶端裝置進行設定。 若要簡化 S2S VPN 連線的部署，請參閱[設定站對站 (S2S) VPN 以用於 Azure 檔案儲存體](storage-files-configure-s2s-vpn.md)。
- [ExpressRoute](../../expressroute/expressroute-introduction.md)，可讓您在 Azure 與不會周遊網際網路的內部部署網路之間建立已定義好的路由。 由於 ExpressRoute 會在您的內部部署資料中心與 Azure 之間提供專用路徑，因此若要將網路效能納入考量時，ExpressRoute 可能會很有用。 當組織的原則或法規需求要求您的雲端資源必須有確定的路徑時，ExpressRoute 也會是不錯的選擇。

無論您使用哪一種通道方法來存取 Azure 檔案共用，都需要備有機制來確保朝向儲存體帳戶的流量會通過通道進入，而不是通過一般的網際網路連線。 技術上而言，您可以路由傳送至儲存體帳戶的公用端點，不過這需要針對區域中的 Azure 儲存體叢集，將其所有的 IP 位址寫入程式碼，因為儲存體帳戶可能會隨時在儲存體叢集之間移動。 這也需要持續更新 IP 位址對應，因為隨時都會有新的叢集新增進來。

建議您不要在 VPN 路由規則中將儲存體帳戶的 IP 位址寫入程式碼，而是應該使用私人端點以便讓儲存體帳戶獲得來自 Azure 虛擬網路位址空間的 IP 位址。 因為建立通往 Azure 的通道會在內部部署網路與一或多個虛擬網路之間建立對等互連，因此這會實現正確且持久的路由機制。

### <a name="private-endpoints"></a>私人端點
除了儲存體帳戶的預設公用端點外，Azure 檔案儲存體還提供了擁有一或多個私人端點的選項。 私人端點是只能在 Azure 虛擬網路內存取的端點。 當您為儲存體帳戶建立私人端點時，儲存體帳戶便會從虛擬網路位址空間內獲得私人 IP 位址，情況非常類似於內部部署檔案伺服器或 NAS 裝置會獲得內部部署網路專用位址空間內的 IP 位址。 

個別的私人端點會與特定的 Azure 虛擬網路子網路相關聯。 儲存體帳戶可能會在多個虛擬網路中擁有私人端點。

搭配使用私人端點與 Azure 檔案儲存體將可讓您：
- 使用 VPN 或 ExpressRoute 連線與私人對等互連，安全地從內部部署網路連線到 Azure 檔案共用。
- 藉由設定儲存體帳戶防火牆來封鎖公用端點上的所有連線，以保護 Azure 檔案共用。 根據預設，建立私人端點並不會封鎖對公用端點的連線。
- 藉由讓您能夠封鎖從虛擬網路 (和對等互連界限) 外泄資料的可能，來提高虛擬網路的安全性。

若要建立私人端點，請參閱[設定 Azure 檔案儲存體的私人端點](storage-files-networking-endpoints.md)。

### <a name="private-endpoints-and-dns"></a>私人端點和 DNS
當您建立私人端點時，根據預設，我們也會建立與 `privatelink` 子網域對應的私人 DNS 區域，或更新現有的私人 DNS 區域。 嚴格來說，不需要建立私人 DNS 區域就能針對儲存體帳戶使用私人端點，但在使用 Active Directory 使用者主體掛接 Azure 檔案共用或從 FileREST API 存取時，一般還是會強烈建議並明確要求您這麼做。

> [!Note]  
> 本文會針對 Azure 公用區域使用儲存體帳戶 DNS 尾碼 `core.windows.net`。 此註解也適用於 Azure 主權雲端 (例如 Azure 美國政府雲端和 Azure 中國雲端)，只需替換為適合您環境的尾碼即可。 

在您的私人 DNS 區域中，我們會為 `storageaccount.privatelink.file.core.windows.net` 建立 A 記錄，並為儲存體帳戶 (其會遵循模式 `storageaccount.file.core.windows.net`) 的一般名稱建立 CNAME 記錄。 由於您的 Azure 私人 DNS 區域會連線到包含私人端點的虛擬網路，因此您可以在 Azure VM 中從 PowerShell 呼叫 `Resolve-DnsName` Cmdlet (在 Windows 和 Linux 中則為 `nslookup`) 來觀察 DNS 設定：

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

這反映了一個事實，那就是儲存體帳戶可以同時公開該公用端點和一或多個私人端點。 為確保儲存體帳戶名稱會解析為私人端點的私人 IP 位址，您必須變更內部部署 DNS 伺服器上的設定。 這可以透過數種方式來完成：

- 修改用戶端上的 hosts 檔案，讓 `storageaccount.file.core.windows.net` 解析為所想要私人端點的私人 IP 位址。 生產環境非常不建議這麼做，因為您會需要對想要掛接 Azure 檔案共用的每個用戶端進行這些變更，但系統並不會自動處理您對儲存體帳戶或私人端點所進行的變更。
- 在內部部署 DNS 伺服器中為 `storageaccount.file.core.windows.net` 建立 A 記錄。 這麼做的好處是，內部部署環境中的用戶端將能夠自動解析儲存體帳戶，而不需要設定每個用戶端，不過，此解決方案和修改 hosts 檔案一樣不方便，因為變更並不會加以放映。 雖然這個解決方案不怎麼方便，但在某些環境中卻可能是最佳選擇。
- 將 `core.windows.net` 區域從內部部署 DNS 伺服器轉送到 Azure 私人 DNS 區域。 您可以透過只能在連結至 Azure 私人 DNS 區域的虛擬網路內存取的特殊 IP 位址 (`168.63.129.16`)，來連線到 Azure 私人 DNS 主機。 若要解決這項限制，您可以在虛擬網路內執行會將 `core.windows.net` 轉送至 Azure 私人 DNS 區域的其他 DNS 伺服器。 為簡化這項設定，我們提供了 PowerShell Cmdlet，以在 Azure 虛擬網路中自動部署 DNS 伺服器，並視需要加以設定。 若要了解如何設定 DNS 轉送，請參閱[使用 Azure 檔案儲存體設定 DNS](storage-files-networking-dns.md)。

## <a name="storage-account-firewall-settings"></a>儲存體帳戶防火牆設定
防火牆是一種網路原則，可控制哪些要求得以存取儲存體帳戶的公用端點。 使用儲存體帳戶防火牆時，您可以將對於儲存體帳戶公用端點的存取，限制在特定 IP 位址或範圍或是虛擬網路內。 一般而言，大部分的儲存體帳戶防火牆原則都會將網路存取限制在一或多個虛擬網路內。 

有兩種方法可以將對於儲存體帳戶的存取限制在虛擬網路內：
- 為儲存體帳戶建立一或多個私人端點，並限制所有對公用端點的存取。 這可確保只有來自所要虛擬網路內的流量，才能存取儲存體帳戶內的 Azure 檔案共用。
- 將公用端點限制在一或多個虛擬網路內。 其運作方式是使用稱為「服務端點」  的虛擬網路功能。 當您透過服務端點來限制流往儲存體帳戶的流量時，您仍然可以透過公用 IP 位址來存取儲存體帳戶。

若要深入了解如何設定儲存體帳戶防火牆，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="encryption-in-transit"></a>傳輸中加密
根據預設，所有 Azure 儲存體帳戶都會啟用傳輸中加密。 這表示當您透過 SMB 掛接檔案共用或透過 FileREST 通訊協定 (例如，透過 Azure 入口網站、PowerShell/CLI 或 Azure SDK) 來存取檔案共用時，Azure 檔案儲存體只會在使用了 SMB 3.0 以上 (有加密功能) 或 HTTPS 來進行連線時，才會允許您建立連線。 不支援 SMB 3.0 的用戶端，或雖支援 SMB 3.0 但不支援 SMB 加密的用戶端，將無法在啟用了傳輸中加密的情況下掛接 Azure 檔案共用。 如需哪些作業系統支援 SMB 3.0 (有加密功能) 的詳細資訊，請參閱適用於 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和 [Linux](storage-how-to-use-files-linux.md) 的詳細文件。 所有目前版本的 PowerShell、CLI 和 SDK 都支援 HTTPS。  

您可以為 Azure 儲存體帳戶停用傳輸中加密。 停用加密時，Azure 檔案儲存體也會允許 SMB 2.1、SMB 3.0 (沒有加密功能) 以及透過 HTTP 的未加密 FileREST API 呼叫。 會停用傳輸中加密的主要原因，是為了支援必須在舊版作業系統上執行的繼承應用程式，例如 Windows Server 2008 R2 或舊版 Linux 散發套件。 Azure 檔案儲存體只會在與 Azure 檔案共用相同的 Azure 區域內允許 SMB 2.1 連線；Azure 檔案共用所在的 Azure 區域外 (例如，內部部署或不同 Azure 區域) 的 SMB 2.1 用戶端，則無法存取檔案共用。

如需傳輸中加密的詳細資訊，請參閱[在 Azure 儲存體中需要安全傳輸](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="see-also"></a>另請參閱
- [Azure 檔案儲存體概觀](storage-files-introduction.md)
- [規劃 Azure 檔案部署](storage-files-planning.md)