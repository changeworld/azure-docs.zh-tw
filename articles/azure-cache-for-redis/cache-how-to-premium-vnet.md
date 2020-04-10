---
title: 設定虛擬網路 - 用於雷瑞斯的進階 Azure 快取
description: 了解如何建立和管理進階層 Azure Cache for Redis 執行個體的虛擬網路支援
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 2821ee637b2562b5287dd3d59cf943b3dcb7ef97
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010880"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>如何設定進階 Azure Cache for Redis 的虛擬網路支援
Azure Cache for Redis 有不同的快取供應項目，可讓您彈性選擇快取大小和功能，包括叢集功能、持續性及虛擬網路支援等「進階」層功能。 VNet 是雲端中的私人網路。 當 Azure Cache for Redis 執行個體是以 VNet 設定時，它將無法公開定址，而只能從 VNet 中的虛擬機器和應用程式存取。 本文說明如何設定進階 Azure Cache for Redis 執行個體的虛擬網路支援。

> [!NOTE]
> Azure Cache for Redis 支援傳統與 Resource Manager VNet。
> 
> 

如需其他進階快取功能的相關資訊，請參閱 [Azure Cache for Redis 進階層簡介](cache-premium-tier-intro.md)。

## <a name="why-vnet"></a>為何使用 VNet？
[Azure 虛擬網路 (VNet)](https://azure.microsoft.com/services/virtual-network/)部署為 Redis 的 Azure 緩存以及子網、訪問控制策略和其他功能提供了增強的安全性和隔離性,以進一步限制訪問。

## <a name="virtual-network-support"></a>虛擬網路支援
虛擬網路 (VNet) 支援是在快取建立期間於 [新的 Azure Cache for Redis]**** 刀鋒視窗中設定的。 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

一旦選取進階定價層之後，您就可以藉由選取與您的快取相同的訂用帳戶和位置中的 VNet，來設定 Redis VNet。 若要使用新的 VNet，請先依照[使用 Azure 入口網站建立虛擬網路](../virtual-network/manage-virtual-network.md#create-a-virtual-network)或[使用 Azure 入口網站建立虛擬網路 (傳統)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) 中的步驟建立 VNet，然後返回 [新的 Azure Cache for Redis]**** 刀鋒視窗來建立和設定進階快取。

若要為新的快取設定 VNet，按一下 [新的 Azure Cache for Redis]**** 刀鋒視窗上的 [虛擬網路]****，然後從下拉式清單中選取想要的 VNet。

![虛擬網路][redis-cache-vnet]

從**子網**下拉清單中選擇所需的子網。  如果需要,請指定**靜態 IP 位址**。 **靜態 IP 位址**欄位是可選的,如果未指定,則從所選子網中選擇一個。

> [!IMPORTANT]
> 將 Azure Cache for Redis 部署到 Resource Manager VNet 時，快取必須位於專用子網路中，其中只能包含 Azure Cache for Redis 執行個體，不含其他任何資源。 如果嘗試將 Azure Cache for Redis 部署到含有其他資源的 Resource Manager VNet 子網路，部署將會失敗。
> 
> 

![虛擬網路][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure 會在每個子網路中保留一些 IP 位址，但這些位址無法使用。 子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> 除了 Azure VNET 基礎結構使用的 IP 位址外，子網路中的每個 Redis 執行個體都會為每個分區使用兩個 IP 位址，為負載平衡器使用一個其他的 IP 位址。 非叢集快取會視為有一個分區。
> 
> 

建立快取之後，您可以從 [資源]**** 功能表中按一下 [虛擬網路]****，以檢視 VNet 的設定。

![虛擬網路][redis-cache-vnet-info]

若要在使用 VNet 時連線到 Azure Cache for Redis 執行個體，請在連接字串中指定您的快取主機名稱，如下列範例所示：

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-cache-for-redis-vnet-faq"></a>Azure Cache for Redis VNet 常見問題集
下列清單包含 Azure Cache for Redis 調整相關常見問題的解答。

* Azure Cache for Redis 和 VNet 的某些常見錯誤設定有哪些？
* [如何確認我的快取是在 VNET 中運作？](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* 當我嘗試連線到 VNET 中的 Azure Cache for Redis 時，為什麼會收到錯誤，指出遠端憑證不正確呢？
* [可以搭配標準或基本快取使用 VNet 嗎？](#can-i-use-vnets-with-a-standard-or-basic-cache)
* 為什麼無法在某些子網路中建立 Azure Cache for Redis，但其他的可以？
* [子網路位址空間需求為何？](#what-are-the-subnet-address-space-requirements)
* [將快取裝載於 VNET 時，所有快取功能都可以正常運作嗎？](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Azure Cache for Redis 和 VNet 的某些常見錯誤設定有哪些？
Azure Cache for Redis 裝載在 VNet 時，會使用下表中的連接埠。 

>[!IMPORTANT]
>如果封鎖下表中的連接埠，快取可能無法正常運作。 在 VNet 中使用 Azure Cache for Redis 時，將其中一或多個連接埠封鎖是最常見的錯誤組態問題。
> 
> 

- [輸出連接埠需求](#outbound-port-requirements)
- [輸入連接埠需求](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>輸出連接埠需求

有九個出站埠要求。 這些範圍內的出站請求是從站到緩存正常運行所需的其他服務,或者用於節點間通信的 Redis 子網的內部。 對於異地複製,對於主緩存和輔助緩存的子網之間的通信,存在其他出站要求。

| 連接埠 | 方向 | 傳輸通訊協定 | 目的 | 本機 IP | 遠端 IP |
| --- | --- | --- | --- | --- | --- |
| 80、443 |輸出 |TCP |Azure 儲存體/PKI 上 Redis 的相依項目 (網際網路) | (Redis 子網路) |* |
| 443 | 輸出 | TCP | 對 Azure 金鑰保管庫的 Redis 依賴項 | (Redis 子網路) | Azure 金鑰庫<sup>1</sup> |
| 53 |輸出 |TCP/UDP |DNS 上 Redis 的相依項目 (網際網路/VNet) | (Redis 子網路) | 168.63.129.16 和 169.254.169.254 <sup>2</sup>和子網的任何自定義 DNS 伺服器<sup>3</sup> |
| 8443 |輸出 |TCP |Redis 內部通訊 | (Redis 子網路) | (Redis 子網路) |
| 10221-10231 |輸出 |TCP |Redis 內部通訊 | (Redis 子網路) | (Redis 子網路) |
| 20226 |輸出 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路) |
| 13000-13999 |輸出 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路) |
| 15000-15999 |輸出 |TCP |Redis 和異地複製的內部通訊 | (Redis 子網路) |(雷瑞斯子網)(地理複本對等子網) |
| 6379-6380 |輸出 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路) |

<sup>1</sup>您可以將服務標記「AzureKeyVault」與資源管理器網路安全組一起使用。

<sup>2</sup> Microsoft 擁有的這些 IP 位址用於解決服務 Azure DNS 的主機 VM。

<sup>3</sup>沒有自定義 DNS 伺服器的子網或忽略自訂 DNS 的較新的 redis 快取不需要。

#### <a name="geo-replication-peer-port-requirements"></a>異地複寫的對等連接埠需求

如果在 Azure 虛擬網路中的緩存之間使用異地複製,請注意,建議的配置是取消阻止整個子網的埠 15000-15999,在入站方向和出站方向中,以便子網中的所有副本元件即使在將來發生異地故障轉移時也能直接相互通信。

#### <a name="inbound-port-requirements"></a>輸入連接埠需求

有八項輸入連接埠範圍需求。 在這些範圍的輸入要求如下：從相同 VNET 中裝載的其他服務輸入，或是 Redis 子網路內部通訊。

| 連接埠 | 方向 | 傳輸通訊協定 | 目的 | 本機 IP | 遠端 IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |輸入 |TCP |對 Redis 進行的用戶端通訊，Azure 負載平衡 | (Redis 子網路) | (Redis 子網)、虛擬網路、Azure 負載均衡器<sup>1</sup> |
| 8443 |輸入 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路) |
| 8500 |輸入 |TCP/UDP |Azure 負載平衡 | (Redis 子網路) |Azure Load Balancer |
| 10221-10231 |輸入 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路)，Azure Load Balancer |
| 13000-13999 |輸入 |TCP |對 Redis 叢集的用戶端通訊，Azure 負載平衡 | (Redis 子網路) |虛擬網路，Azure Load Balancer |
| 15000-15999 |輸入 |TCP |用戶端通訊到 Redis 群集、Azure 負載平衡和異地複製 | (Redis 子網路) |虛擬網路,Azure 負載均衡器(地理副本對等子網) |
| 16001 |輸入 |TCP/UDP |Azure 負載平衡 | (Redis 子網路) |Azure Load Balancer |
| 20226 |輸入 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路) |

<sup>1</sup>您可以使用服務標記「AzureLoad平衡器」(資源管理器)(或"AZURE_LOADBALANCER"經典)來創作 NSG 規則。

#### <a name="additional-vnet-network-connectivity-requirements"></a>其他 VNET 網路連線需求

在虛擬網路中，可能一開始就不符合 Azure Cache for Redis 的一些網路連線需求。 Azure Cache for Redis 需要下列所有項目，在虛擬網路內使用時才能正確運作。

* 全球 Azure 儲存體端點的輸出網路連線。 這包括位於與 Azure Cache for Redis 執行個體相同區域中的端點，以及位於 **其他** Azure 區域的儲存體端點。 Azure 儲存體端點在下列 DNS 網域之下解析：table.core.windows.net**、blob.core.windows.net**、queue.core.windows.net** 和 file.core.windows.net**。 
* *ocsp.msocsp.com*、*mscrl.microsoft.com* 和 *crl.microsoft.com* 的輸出網路連線。 需要這種連接來支援 TLS/SSL 功能。
* 虛擬網路的 DNS 設定必須能夠解析前面幾點所提到的所有端點和網域。 確定已針對虛擬網路設定及維護有效的 DNS 基礎結構，即可符合 DNS 需求。
* 在下列 DNS 網域下解析之下列 Azure 監視端點的輸出網路連線︰shoebox2-black.shoebox2.metrics.nsatc.net、north-prod2.prod2.metrics.nsatc.net、azglobal-black.azglobal.metrics.nsatc.net、shoebox2-red.shoebox2.metrics.nsatc.net、east-prod2.prod2.metrics.nsatc.net、azglobal-red.azglobal.metrics.nsatc.net。

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>如何確認我的快取是在 VNET 中運作？

>[!IMPORTANT]
>連接到 VNET 中託管的 Redis 實例的 Azure 緩存時,快取客戶端必須位於同一 VNET 中,或者位於在同一 Azure 區域中啟用 VNET 對等互連的 VNET 中。 當前不支援全域 VNET 對等互連。 這包括任何測試應用程式或診斷 Ping 工具。 不論用戶端應用程式裝載在哪個位置，都必須設定網路安全性群組以允許用戶端的網路流量觸達 Redis 執行個體。
>
>

一旦如上一節中所述設定連接埠需求之後，您可以執行下列步驟來驗證快取是否正在運作。

- [重新啟動](cache-administration.md#reboot)所有的快取節點。 如果無法觸達所有必要的快取相依性連線 (如[輸入連接埠需求](cache-how-to-premium-vnet.md#inbound-port-requirements)和[輸出連接埠需求](cache-how-to-premium-vnet.md#outbound-port-requirements)中所述)，快取將無法順利重新啟動。
- 一旦快取節點重新啟動 (如 Azure 入口網站中的快取狀態所報告) 後，您可以執行下列測試：
  - 使用 [tcping](https://www.elifulkerson.com/projects/tcping.php)，從與快取位於相同 VNET 中的電腦偵測快取端點 (使用連接埠 6380)。 例如：
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    如果 `tcping` 工具報告連接埠已開啟，就可以在 VNET 中從用戶端使用快取進行連線。

  - 另一種測試的方式是建立測試快取用戶端 (可能是使用 StackExchange.Redis 的簡單主控台應用程式)，其會連線到快取，並從快取新增及擷取某些項目。 將範例用戶端應用程式安裝到與快取位於相同 VNET 的 VM 並加以執行，來驗證對快取的連線能力。


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>當我嘗試連線到 VNET 中的 Azure Cache for Redis 時，為什麼會收到錯誤，指出遠端憑證不正確呢？

當您嘗試連線到 VNET 中的 Azure Cache for Redis 時，會看到如下的憑證驗證錯誤：

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

原因可能是您正透過 IP 位址連線到主機。 建議您使用主機名稱。 換句話說，請使用下列內容：     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

避免使用類似下列連接字串的 IP 位址：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

如果您無法解析 DNS 名稱，某些用戶端程式庫就會包含像是 `sslHost` 的設定選項，其是由 StackExchange.Redis 用戶端所提供的。 這可讓您覆寫用於憑證驗證的主機名稱。 例如：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>可以搭配標準或基本快取使用 VNet 嗎？
VNet 僅適用於進階快取。

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>為什麼無法在某些子網路中建立 Azure Cache for Redis，但其他的可以？
如果您將 Azure Cache for Redis 部署到 Resource Manager VNet，快取就必須位於不含任何其他資源類型的專用子網路中。 如果嘗試將 Azure Cache for Redis 部署到含有其他資源的 Resource Manager VNet 子網路，部署將會失敗。 您必須先刪除子網路內的現有資源，然後才能建立新的 Azure Cache for Redis。

只要有足夠的可用 IP 位址，您就可以將多個類型的資源部署到傳統的 VNet。

### <a name="what-are-the-subnet-address-space-requirements"></a>子網路位址空間需求為何？
Azure 會在每個子網路中保留一些 IP 位址，但這些位址無法使用。 子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure VNET 基礎結構使用的 IP 位址外，子網路中的每個 Redis 執行個體都會為每個分區使用兩個 IP 位址，為負載平衡器使用一個其他的 IP 位址。 非叢集快取會視為有一個分區。

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>將快取裝載於 VNET 時，所有快取功能都可以正常運作嗎？
如果您的快取是 VNET 的一部分，只有在 VNET 中的用戶端可以存取快取。 因此，下列快取管理功能目前沒有作用。

* Redis 主控台 - 由於 Redis 主控台在您的本機瀏覽器 (位於 VNET 之外) 中執行，因此無法連接到您的快取。


## <a name="use-expressroute-with-azure-cache-for-redis"></a>搭配使用 ExpressRoute 與 Azure Cache for Redis

客戶可以將 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 循環連接至虛擬網路基礎結構，因而將其內部部署網路延伸至 Azure。 

根據預設，新建立的 ExpressRoute 循環並不會在 VNET 上執行強制通道 (預設路由的公告 0.0.0.0/0)。 如此一來，即可直接從 VNET 進行輸出網際網路連線，且用戶端應用程式能夠連線到其他的 Azure 端點，包括 Azure Cache for Redis。

不過，常見的客戶組態是使用強制通道 (公告預設路由)，以強制輸出網際網路流量來替代透過內部部署方式流動。 如果輸出流量遭內部部署封鎖，而使得 Azure Cache for Redis 執行個體無法與其相依項目通訊，此流量就會中斷與 Azure Cache for Redis 的連線。

解決方法是在子網路上定義包含 Azure Cache for Redis 的一 (或多個) 使用者定義路由 (UDR)。 UDR 會定義將使用的子網路特有路由，而非預設路由。

如果可能，建議使用下列設定：

* ExpressRoute 組態會通告 0.0.0.0/0 而且預設會使用強制通道將所有輸出流量傳送至內部部署。
* 對包含 Azure Cache for Redis 的子網路套用的 UDR 會定義 0.0.0.0/0，以及對公用網際網路的 TCP/IP 流量的有效路由，例如將下一個躍點類型設定為 'Internet'。

這些步驟的合併效果是子網路層級 UDR 會優先於 ExpressRoute 強制通道，因而確保來自 Azure Cache for Redis 的輸出網際網路存取。

基於效能考量，使用 ExpressRoute 從內部部署應用程式連線到 Azure Cache for Redis 執行個體不能當成一般使用案例 (為了達到最佳效能，Azure Cache for Redis 用戶端應位於 Azure Cache for Redis 所在的相同區域中)。

>[!IMPORTANT] 
>UDR 中定義的路由**必須**明確足以優先於 ExpressRoute 組態所通告的任何路由。 下列範例使用廣泛 0.0.0.0/0 位址範圍，因此使用更明確的位址範圍，有可能會不小心由路由通告所覆寫。

>[!WARNING]  
>**錯誤地針對從公用對等互連路徑至私人對等互連路徑的路由進行交叉通告**的 ExpressRoute 組態，不支援 Azure Cache for Redis。 已設定公用對等互連的 ExpressRoute 組態，會收到來自 Microsoft 的一大組 Microsoft Azure IP 位址範圍的路由通告。 如果這些位址範圍在私人對等互連路徑上錯誤地交叉通告，結果會是來自 Azure Cache for Redis 執行個體子網路的所有輸出網路封包，都會不正確地使用強制通道傳送至客戶的內部部署網路基礎結構。 此網路流量會中斷 Azure Cache for Redis。 此問題的解決方案是停止從公用對等互連路徑至私人對等互連路徑的交叉通告路由。


如需使用者定義路由的背景資訊，請參閱此 [概觀](../virtual-network/virtual-networks-udr-overview.md)。

如需 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。

## <a name="next-steps"></a>後續步驟
了解如何使用更多進階快取功能。

* [Azure Cache for Redis 進階層簡介](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
