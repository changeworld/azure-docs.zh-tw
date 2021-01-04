---
title: 設定虛擬網路-Premium 層 Azure Cache for Redis 實例
description: 瞭解如何建立和管理高階 Azure Cache for Redis 實例的虛擬網路支援。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 9343bc424a0a38da173a56701528c4fd7549aabd
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734652"
---
# <a name="configure-virtual-network-support-for-a-premium-tier-azure-cache-for-redis-instance"></a>為高階層 Azure Cache for Redis 實例設定虛擬網路支援

「Azure Redis 快取」具有各種不同的快取供應項目，可讓您彈性選擇快取大小和功能。 高階層級功能包括叢集、持續性和虛擬網路支援。 虛擬網路是雲端中的私人網路。 當 Azure Cache for Redis 實例是以虛擬網路設定時，它無法公開定址，且只能從虛擬網路內的虛擬機器和應用程式存取。 本文說明如何為高階層 Azure Cache for Redis 實例設定虛擬網路支援。

> [!NOTE]
> Azure Cache for Redis 支援傳統部署模型和 Azure Resource Manager 虛擬網路。
> 

## <a name="why-virtual-network"></a>為何選擇虛擬網路？

[Azure 虛擬網路](https://azure.microsoft.com/services/virtual-network/) 部署可為您的 Azure Cache for Redis 實例提供增強的安全性和隔離，以及子網、存取控制原則及其他功能，以進一步限制存取。

## <a name="virtual-network-support"></a>虛擬網路支援

虛擬網路支援是在快取建立期間于 **新的 Azure Cache for Redis** 窗格上設定的。

1. 若要建立高階層快取，請登入 [Azure 入口網站](https://portal.azure.com) ，然後選取 [ **建立資源**]。 除了在 Azure 入口網站中建立快取，您也可以使用 Resource Manager 範本、PowerShell 或 Azure CLI 來建立它們。 如需有關如何建立 Azure Cache for Redis 實例的詳細資訊，請參閱 [建立](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)快取。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="顯示 [建立資源] 的螢幕擷取畫面。":::
   
1. 在 [ **新增** ] 頁面上，選取 [ **資料庫**]。 然後選取 [ **Azure Cache for Redis**]。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="顯示選取 Azure Cache for Redis 的螢幕擷取畫面。":::

1. 在 [ **新的 Redis** 快取] 頁面上，設定新的高階層快取的設定。
   
   | 設定      | 建議的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名稱** | 輸入全域唯一名稱。 | 快取名稱必須是介於1到63個字元之間的字串，其中只包含數位、字母或連字號。 名稱的開頭和結尾必須是數位或字母，且不可包含連續的連字號。 您的快取執行個體 *主機名稱* 將是 *\<DNS name>.redis.cache.windows.net*。 |
   | **訂用帳戶** | 從下拉式清單中選取訂用帳戶。 | 這個新的 Azure Cache for Redis 執行個體建立所在的訂用帳戶。 |
   | **資源群組** | 從下拉式清單中選取資源群組，或選取 [ **建立新** 的]，然後輸入新的資源組名。 | 要在其中建立快取和其他資源的資源組名。 將所有的應用程式資源放在一個資源群組中，您將可輕鬆地一併管理或刪除這些資源。 |
   | **位置** | 從下拉式清單中選取位置。 | 選取其他將使用快取的服務附近的[區域](https://azure.microsoft.com/regions/)。 |
   | **快取類型** |從下拉式清單中選取高階層快取，以設定進階層功能。 如需詳細資訊，請參閱 [Azure Cache for Redis 價格](https://azure.microsoft.com/pricing/details/cache/)。 |  快取的可用大小、效能和功能取決於定價層。 如需詳細資訊，請參閱 [Azure Cache for Redis 總覽](cache-overview.md)。 |

1. 選取 [ **網路** 功能] 索引標籤，或選取頁面底部的 [ **網路** ] 按鈕。

1. 在 [ **網路** ] 索引標籤上，選取 [ **虛擬網路** ] 作為您的連線方法。 若要使用新的虛擬網路，請先遵循 [使用 Azure 入口網站建立虛擬網路](../virtual-network/manage-virtual-network.md#create-a-virtual-network) 中的步驟，或 [使用 Azure 入口網站建立 (傳統) 的虛擬網路](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)，以建立虛擬網路。 然後返回新的 **Azure Cache for Redis** 窗格，以建立並設定您的高階層快取。

   > [!IMPORTANT]
   > 當您將 Azure Cache for Redis 部署至 Resource Manager 的虛擬網路時，快取必須位於不含任何其他資源的專用子網中，Azure Cache for Redis 實例除外。 如果您嘗試將 Azure Cache for Redis 實例部署到包含其他資源的 Resource Manager 虛擬網路子網，部署將會失敗。
   > 
   > 

   | 設定      | 建議的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **虛擬網路** | 從下拉式清單中選取您的虛擬網路。 | 選取與您的快取位於相同訂用帳戶和位置的虛擬網路。 |
   | **子網路** | 從下拉式清單中選取您的子網。 | 子網的位址範圍應為 CIDR 標記法 (例如 192.168.1.0/24) 。 其必須包含在虛擬網路的位址空間中。 |
   | **靜態 IP 位址** |  (選擇性) 輸入靜態 IP 位址。 | 如果您未指定靜態 IP 位址，則會自動選擇 IP 位址。 |

   > [!IMPORTANT]
   > Azure 會在每個子網路中保留一些 IP 位址，但這些位址無法使用。 子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > 除了 Azure 虛擬網路基礎結構所使用的 IP 位址之外，子網中的每個 Azure Cache for Redis 實例都會針對每個分區使用兩個 IP 位址，並針對負載平衡器使用一個額外的 IP 位址。 非叢集快取會被視為有一個分區。
   > 

1. 選取 [ **下一步： advanced** ] 索引標籤，或選取頁面底部的 [ **下一步： advanced** ] 按鈕。

1. 在高階層快取實例的 [ **Advanced** ] 索引標籤上，設定非 TLS 埠、叢集和資料持續性的設定。

1. 選取 [ **下一步：標記** ] 索引標籤，或選取頁面底部的 [ **下一步：標記** ] 按鈕。

1. （選擇性）在 [ **標記** ] 索引標籤上，如果您想要分類資源，請輸入名稱和值。

1. 選取 [檢閱 + 建立]。 您會進入 [ **審核] + [建立** ] 索引標籤，其中 Azure 會驗證您的設定。

1. 出現綠色 **驗證通過** 訊息之後，請選取 [ **建立**]。

建立快取需要一些時間。 您可以在 Azure Cache for Redis 的 [概觀] 頁面上監視進度。 當 [狀態] 顯示為 [執行中] 時，表示快取已可供使用。 建立快取之後，您可以從 [**資源**] 功能表中選取 [**虛擬網路**]，以查看虛擬網路的設定。

![虛擬網路][redis-cache-vnet-info]

當您使用虛擬網路時，若要連接到您的 Azure Cache for Redis 實例，請在連接字串中指定快取的主機名稱，如下列範例所示：

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
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
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Azure Cache for Redis 虛擬網路常見問題

下列清單包含「Azure Redis 快取」規模調整相關常見問題的解答。

* Azure Cache for Redis 和虛擬網路有哪些常見的錯誤設定問題？
* [如何確認我的快取可在虛擬網路中運作？](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* 當我嘗試連接至虛擬網路中的 Azure Cache for Redis 實例時，為什麼會收到錯誤，指出遠端憑證無效？
* [我可以使用虛擬網路搭配標準或基本快取嗎？](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* 為什麼在某些子網中建立 Azure Cache for Redis 實例會失敗，而不會在其他子網中失敗？
* [子網路位址空間需求為何？](#what-are-the-subnet-address-space-requirements)
* [當快取裝載于虛擬網路時，是否所有快取功能都能運作？](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Azure Cache for Redis 和虛擬網路有哪些常見的錯誤設定問題？

當 Azure Cache for Redis 裝載于虛擬網路時，會使用下表中的埠。

>[!IMPORTANT]
>如果系統封鎖了下表中的埠，快取可能無法正常運作。 當您在虛擬網路中使用 Azure Cache for Redis 時，將其中一或多個埠封鎖是最常見的錯誤設定問題。
> 

- [輸出連接埠需求](#outbound-port-requirements)
- [輸入連接埠需求](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>輸出連接埠需求

有九個輸出埠需求。 這些範圍內的輸出要求可能會輸出至快取運作所需的其他服務，或 Redis 子網內部的其他服務，以進行節點間通訊。 針對異地複寫，主要和複本快取的子網之間的通訊存在額外的輸出需求。

| 連接埠 | 方向 | 傳輸通訊協定 | 目的 | 本機 IP | 遠端 IP |
| --- | --- | --- | --- | --- | --- |
| 80、443 |輸出 |TCP |Redis Azure 儲存體/PKI (網際網路) 的相依性 | (Redis 子網路) |* <sup>億</sup> |
| 443 | 輸出 | TCP | Azure Key Vault 和 Azure 監視器的相依性 Redis | (Redis 子網路) | AzureKeyVault，AzureMonitor <sup>1</sup> |
| 53 |輸出 |TCP/UDP |Redis DNS (網際網路/虛擬網路) 的相依性 | (Redis 子網路) | 168.63.129.16 和 169.254.169.254 <sup>2</sup>以及子網<sup>3</sup>的任何自訂 DNS 伺服器 |
| 8443 |輸出 |TCP |Redis 內部通訊 | (Redis 子網路) | (Redis 子網路) |
| 10221-10231 |輸出 |TCP |Redis 內部通訊 | (Redis 子網路) | (Redis 子網路) |
| 20226 |輸出 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路) |
| 13000-13999 |輸出 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路) |
| 15000-15999 |輸出 |TCP |Redis 和異地複寫的內部通訊 | (Redis 子網路) | (Redis 子網)  (異地複本對等子網)  |
| 6379-6380 |輸出 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路) |

<sup>1</sup> 您可以使用服務標籤 AzureKeyVault 和 AzureMonitor 搭配 Resource Manager 的網路安全性群組 (nsg) 。

<sup>2</sup> 這些 IP 位址是由 Microsoft 所擁有，用來處理 Azure DNS 的主機 VM。

<sup>3</sup> 沒有自訂 dns 伺服器的子網或忽略自訂 dns 的較新 Redis 快取，不需要這份資訊。

<sup>4</sup> 如需詳細資訊，請參閱 [其他虛擬網路連接需求](#additional-virtual-network-connectivity-requirements)。

#### <a name="geo-replication-peer-port-requirements"></a>異地複寫的對等連接埠需求

如果您使用 Azure 虛擬網路中的快取之間的異地複寫，請將整個子網的埠15000-15999 解除封鎖兩個快取的輸入 *和* 輸出方向。 使用此設定時，子網中的所有複本元件都可以直接彼此通訊，即使有未來的異地容錯移轉。

#### <a name="inbound-port-requirements"></a>輸入連接埠需求

有八項輸入連接埠範圍需求。 這些範圍內的輸入要求可以從相同虛擬網路中裝載的其他服務輸入，或是從內部 Redis 子網通訊。

| 連接埠 | 方向 | 傳輸通訊協定 | 目的 | 本機 IP | 遠端 IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |輸入 |TCP |對 Redis 進行的用戶端通訊，Azure 負載平衡 | (Redis 子網路) |  (Redis 子網) ， (用戶端子網) ，AzureLoadBalancer <sup>1</sup> |
| 8443 |輸入 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路) |
| 8500 |連入 |TCP/UDP |Azure 負載平衡 | (Redis 子網路) | AzureLoadBalancer |
| 10221-10231 |輸入 |TCP |Redis 叢集的用戶端通訊，Redis 的內部通訊 | (Redis 子網路) | (Redis 子網) 、AzureLoadBalancer (用戶端子網)  |
| 13000-13999 |輸入 |TCP |對 Redis 叢集的用戶端通訊，Azure 負載平衡 | (Redis 子網路) |  (Redis 子網) ， (用戶端子網) ，AzureLoadBalancer |
| 15000-15999 |輸入 |TCP |Redis 叢集的用戶端通訊，Azure 負載平衡和異地複寫 | (Redis 子網路) |  (Redis 子網) ， (用戶端子網) ，AzureLoadBalancer， (異地複本對等子網)  |
| 16001 |連入 |TCP/UDP |Azure 負載平衡 | (Redis 子網路) | AzureLoadBalancer |
| 20226 |輸入 |TCP |Redis 內部通訊 | (Redis 子網路) |(Redis 子網路) |

<sup>1</sup> 您可以使用服務標記 AzureLoadBalancer 進行 Resource Manager，或使用傳統部署模型的 AZURE_LOADBALANCER 來撰寫 NSG 規則。

#### <a name="additional-virtual-network-connectivity-requirements"></a>額外的虛擬網路連接需求

在虛擬網路中，可能一開始不符合 Azure Cache for Redis 的網路連接需求。 Azure Cache for Redis 需要在虛擬網路內使用下列所有專案才能正常運作：

* 全球 Azure 儲存體端點的輸出網路連線。 包含在相同區域中的端點，會與 Azure Cache for Redis 實例和位於 *其他* Azure 區域中的儲存體端點位於相同區域中。 Azure 儲存體端點會在下列 DNS 網域下解析： *table.core.windows.net*、 *blob.core.windows.net*、 *queue.core.windows.net* 和 *file.core.windows.net*。
* *Ocsp.digicert.com*、 *crl4.digicert.com*、 *ocsp.msocsp.com*、 *mscrl.microsoft.com*、 *crl3.digicert.com*、 *cacerts.digicert.com*、 *oneocsp.microsoft.com* 和 *crl.microsoft.com* 的輸出網路連線能力。 這是支援 TLS/SSL 功能所需的連線能力。
* 虛擬網路的 DNS 設定必須能夠解析前面幾點所提到的所有端點和網域。 確定已針對虛擬網路設定及維護有效的 DNS 基礎結構，即可符合 DNS 需求。
* 下列 Azure 監視器端點的輸出網路連線能力，可在下列 DNS 網域下解析： *shoebox2-black.shoebox2.metrics.nsatc.net*、 *north-prod2.prod2.metrics.nsatc.net*、 *azglobal-black.azglobal.metrics.nsatc.net*、 *shoebox2-red.shoebox2.metrics.nsatc.net*、 *east-prod2.prod2.metrics.nsatc.net* 和 *azglobal-red.azglobal.metrics.nsatc.net*。

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>如何確認我的快取可在虛擬網路中運作？

>[!IMPORTANT]
>當您連線到虛擬網路中裝載的 Azure Cache for Redis 實例時，您的快取用戶端必須位於相同的虛擬網路中，或位於相同 Azure 區域內啟用虛擬網路對等互連的虛擬網路中。 目前不支援全域虛擬網路對等互連。 這項需求適用于任何測試應用程式或診斷 ping 工具。 無論用戶端應用程式裝載於何處，都必須設定 Nsg 或其他網路層，以便讓用戶端的網路流量到達 Azure Cache for Redis 實例。
>

依照上一節所述設定埠需求之後，您可以遵循下列步驟來確認快取是否正常運作：

- [重新啟動](cache-administration.md#reboot)所有的快取節點。 如果無法觸達所有必要的快取相依性（如 [輸入埠需求](cache-how-to-premium-vnet.md#inbound-port-requirements) 和 [輸出埠需求](cache-how-to-premium-vnet.md#outbound-port-requirements)所述），則快取將無法成功重新開機。
- 快取節點重新開機之後（如 Azure 入口網站的快取狀態所報告），您可以執行下列測試：
  - 使用 [tcping](https://www.elifulkerson.com/projects/tcping.php)，從位於相同虛擬網路內的電腦，使用埠6380來偵測快取端點。 例如：
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    如果 `tcping` 工具報告埠已開啟，則可以從虛擬網路中的用戶端連接快取。

  - 另一種測試的方式是建立測試快取用戶端 (可能是使用 StackExchange.Redis 的簡單主控台應用程式)，其會連線到快取，並從快取新增及擷取某些項目。 將範例用戶端應用程式安裝在與快取位於相同虛擬網路中的 VM 上。 然後執行它來確認對快取的連線能力。


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>當我嘗試連接至虛擬網路中的 Azure Cache for Redis 實例時，為什麼會收到錯誤，指出遠端憑證無效？

當您嘗試連接到虛擬網路中的 Azure Cache for Redis 實例時，您會看到憑證驗證錯誤，例如：

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

原因可能是您正以 IP 位址連接到主機。 建議您使用主機名稱。 換句話說，請使用下列字串：

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

避免使用類似下列連接字串的 IP 位址：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

如果您無法解析 DNS 名稱，某些用戶端程式庫 `sslHost` 會包含 >stackexchange.redis. Redis 用戶端所提供的設定選項，例如。 此選項可讓您覆寫用於憑證驗證的主機名稱。 例如：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>我可以使用虛擬網路搭配標準或基本快取嗎？

虛擬網路只能搭配 Premium 層快取使用。

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>為什麼在某些子網中建立 Azure Cache for Redis 實例會失敗，而不會在其他子網中失敗？

如果您要將 Azure Cache for Redis 實例部署至虛擬網路，快取必須位於不包含其他任何資源類型的專用子網中。 如果嘗試將 Azure Cache for Redis 實例部署到包含其他資源的 Resource Manager 虛擬網路子網，例如 Azure 應用程式閘道實例和輸出 NAT，則部署通常會失敗。 您必須先刪除其他類型的現有資源，才能建立新的 Azure Cache for Redis 實例。

您的子網中也必須有足夠的可用 IP 位址。

### <a name="what-are-the-subnet-address-space-requirements"></a>子網路位址空間需求為何？

Azure 會在每個子網路中保留一些 IP 位址，但這些位址無法使用。 子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure 虛擬網路基礎結構所使用的 IP 位址之外，子網中的每個 Azure Cache for Redis 實例都會針對每個叢集分區使用兩個 IP 位址，再加上其他複本的其他 IP 位址（如果有的話）。 負載平衡器會使用一個額外的 IP 位址。 非叢集快取會被視為有一個分區。

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>當快取裝載于虛擬網路時，是否所有快取功能都能運作？

當您的快取是虛擬網路的一部分時，只有虛擬網路中的用戶端可以存取快取。 因此，下列快取管理功能目前無法運作：

* **Redis 主控台**：由於 Redis 主控台是在您的本機瀏覽器中執行（通常是在未連線至虛擬網路的開發人員電腦上），因此無法連線至您的快取。

## <a name="use-expressroute-with-azure-cache-for-redis"></a>搭配使用 ExpressRoute 與 Azure Cache for Redis

客戶可以將 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 線路連線至其虛擬網路基礎結構。 如此一來，他們就能將內部部署網路延伸至 Azure。

根據預設，新建立的 ExpressRoute 線路不會在虛擬網路上執行預設路由（0.0.0.0/0)  (通告的強制通道。 如此一來，就可以直接從虛擬網路進行輸出網際網路連線。 用戶端應用程式可以連線至其他 Azure 端點，其中包含 Azure Cache for Redis 實例。

常見的客戶設定是使用強制通道 (通告預設路由) ，以強制輸出網際網路流量改為在內部部署流出。 如果輸出流量接著會在內部部署封鎖，讓 Azure Cache for Redis 實例無法與其相依性進行通訊，則此流量流程會中斷與 Azure Cache for Redis 的連線。

解決方法是在子網上定義一個或多個使用者定義的路由， (Udr) 包含 Azure Cache for Redis 實例。 UDR 會定義將使用的子網路特有路由，而非預設路由。

如果可行，請使用下列設定：

* ExpressRoute 設定會通告 0.0.0.0/0，且根據預設會強制通道所有內部部署的輸出流量。
* 套用至包含 Azure Cache for Redis 實例之子網的 UDR 會定義 0.0.0.0/0，以及將 TCP/IP 流量傳送至公用網際網路的工作路由。 例如，它會將下一個躍點類型設定為 [ *網際網路*]。

這些步驟的合併效果是子網層級的 UDR 優先于 ExpressRoute 強制通道，以確保來自 Azure Cache for Redis 實例的輸出網際網路存取。

基於效能考慮，使用 ExpressRoute 從內部部署應用程式連接到 Azure Cache for Redis 實例不是一般的使用案例。 為了達到最佳效能，Azure Cache for Redis 用戶端應該與 Azure Cache for Redis 實例位於相同的區域中。

>[!IMPORTANT]
>UDR 中定義的路由 *必須* 足夠明確，才能優先于 ExpressRoute 設定所通告的任何路由。 下列範例使用廣泛的 0.0.0.0/0 位址範圍，因此可能會不小心由使用更明確位址範圍的路由通告所覆寫。

>[!WARNING]
>未 *正確交叉通告從公用對等互連路徑至私人對等互連路徑之路由* 的 ExpressRoute 設定，不支援 Azure Cache for Redis。 已設定公用對等互連的 ExpressRoute 設定會從 Microsoft 收到一大組 Microsoft Azure IP 位址範圍的路由通告。 如果這些位址範圍在私人對等互連路徑上錯誤地交叉通告，結果會是來自 Azure Cache for Redis 執行個體子網路的所有輸出網路封包，都會不正確地使用強制通道傳送至客戶的內部部署網路基礎結構。 此網路流量會中斷 Azure Cache for Redis。 此問題的解決方案是停止從公用對等互連路徑至私人對等互連路徑的交叉通告路由。

Udr 上的背景資訊可用於 [虛擬網路流量路由](../virtual-network/virtual-networks-udr-overview.md)。

如需 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure Cache for Redis 功能。

* [Azure Cache for Redis Premium 服務層級](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
