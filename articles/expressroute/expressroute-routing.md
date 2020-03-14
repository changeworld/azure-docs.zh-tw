---
title: Azure ExpressRoute：路由需求
description: 此頁面提供用來設定和管理 ExpressRoute 循環路由的詳細需求。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: cherylmc
ms.openlocfilehash: 3eafb8aff5525f668e6fe0bddb261b1117b5e38b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273042"
---
# <a name="expressroute-routing-requirements"></a>ExpressRoute 路由需求
若要使用 ExpressRoute 連線到 Microsoft 雲端服務，您必須設定和管理路由。 有些連線提供者會以受控服務形式提供路由的設定和管理。 請洽詢您的連線服務提供者，以查看他們是否提供這類服務。 如果沒有，您必須遵循下列需求：

如需必須設定才能進行連線的路由工作階段的說明，請參閱[線路和路由網域](expressroute-circuit-peerings.md)一文。

> [!NOTE]
> Microsoft 不支援任何高可用性組態的路由器備援通訊協定 (例如 HSRP、VRRP)。 我們依賴每個對等互連的一組備援 BGP 工作階段來取得高可用性。
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>用於對等互連的 IP 位址
您必須保留一些 IP 位址來設定您的網路與 Microsoft 的 Enterprise Edge (MSEEs) 路由器之間的路由。 本節提供需求清單並描述必須如何取得和使用這些 IP 位址的相關規則。

### <a name="ip-addresses-used-for-azure-private-peering"></a>用於 Azure 私人對等互連的 IP 位址
您可以使用私人 IP 位址或公用 IP 位址來設定對等互連。 用來設定路由的位址範圍不得與用來在 Azure 中建立虛擬網路的位址範圍重疊。 

* 您必須為路由介面保留一個 /29 子網路或兩個 /30 子網路。
* 用於路由的子網路可以是私人 IP 位址或公用 IP 位址。
* 子網路不得與客戶保留以便用於 Microsoft Cloud 的範圍衝突。
* 如果使用 /29 子網路，它會分割成兩個 /30 子網路。 
  * 第一個 /30 子網路用於主要連結，而第二個 /30 子網路用於次要連結。
  * 針對每個 /30 子網路，您必須在您的路由器上使用 /30 子網路的第一個 IP 位址。 Microsoft 會使用 /30 子網路的第二個 IP 位址來設定 BGP 工作階段。
  * 您必須設定兩個 BGP 工作階段，我們的 [可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 才會有效。  

#### <a name="example-for-private-peering"></a>私人對等互連範例
如果您選擇使用 a.b.c.d/29 來設定對等互連，它會分割成兩個 /30 子網路。 在下列範例中，請注意如何使用 a.b.c.d/29 子網路：

* a.b.c.d/29 會分割成 a.b.c.d/30 和 a.b.c.d+4/30 並透過佈建 API 向下傳遞至 Microsoft。
  * 您可使用 a.b.c.d+1 作為主要 PE 的 VRF IP，而 Microsoft 將使用 a.b.c.d+2 作為主要 MSEE 的 VRF IP。
  * 您可使用 a.b.c.d+5 作為次要 PE 的 VRF IP，而 Microsoft 將使用 a.b.c.d+6 作為次要 MSEE 的 VRF IP。

請考慮您選取 192.168.100.128/29 來設定私人互連的情況。 192.168.100.128/29 包含從 192.168.100.128 至 192.168.100.135 的位址，其中︰

* 192.168.100.128/30 將會指派給 link1 (提供者使用 192.168.100.129，而 Microsoft 使用 192.168.100.130)。
* 192.168.100.132/30 將會指派給 link2 (提供者使用 192.168.100.133，而 Microsoft 使用 192.168.100.134)。

### <a name="ip-addresses-used-for-microsoft-peering"></a>用於 Microsoft 對等互連的 IP 位址
您必須使用自己的公用 IP 位址來設定 BGP 工作階段。 Microsoft 必須能夠透過路由網際網路登錄和網際網路路由登錄來驗證 IP 位址的擁有權。

* 針對 Microsoft 對等互連的已公告公用前置詞列在入口網站中的 IP，將會建立 Microsoft 核心路由器的 ACL，以允許來自這些 IP 的輸入流量。 
* 您必須使用唯一的 /29 (IPv4) 或 /125 (IPv6) 子網路或兩個 /30 (IPv4) 或 /126 (IPv6) 子網路來為每個 ExpressRoute 循環 (如果您有多個) 的每個對等互連設定 BGP 對等互連。
* 如果使用 /29 子網路，它會分割成兩個 /30 子網路。
* 第一個 /30 子網路用於主要連結，而第二個 /30 子網路用於次要連結。
* 針對每個 /30 子網路，您必須在您的路由器上使用 /30 子網路的第一個 IP 位址。 Microsoft 會使用 /30 子網路的第二個 IP 位址來設定 BGP 工作階段。
* 如果使用 /125 子網路，它會分割成兩個 /126 子網路。
* 第一個 /126 子網路用於主要連結，而第二個 /126 子網路用於次要連結。
* 針對每個 /126 子網路，您必須在您的路由器上使用 /126 子網路的第一個 IP 位址。 Microsoft 會使用 /126 子網路的第二個 IP 位址來設定 BGP 工作階段。
* 您必須設定兩個 BGP 工作階段，我們的 [可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 才會有效。

### <a name="ip-addresses-used-for-azure-public-peering"></a>用於 Azure 公用對等互連的 IP 位址

> [!NOTE]
> 新的線路無法使用 Azure 公用對等互連。
> 

您必須使用自己的公用 IP 位址來設定 BGP 工作階段。 Microsoft 必須能夠透過路由網際網路登錄和網際網路路由登錄來驗證 IP 位址的擁有權。 

* 您必須使用唯一的 /29 子網路或兩個 /30 子網路來為每個 ExpressRoute 循環 (如果您有多個) 的每個對等互連設定 BGP 對等互連。 
* 如果使用 /29 子網路，它會分割成兩個 /30 子網路。 
  * 第一個 /30 子網路用於主要連結，而第二個 /30 子網路用於次要連結。
  * 針對每個 /30 子網路，您必須在您的路由器上使用 /30 子網路的第一個 IP 位址。 Microsoft 會使用 /30 子網路的第二個 IP 位址來設定 BGP 工作階段。
  * 您必須設定兩個 BGP 工作階段，我們的 [可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 才會有效。

## <a name="public-ip-address-requirement"></a>公用 IP 位址需求

### <a name="private-peering"></a>私人對等互連
您可以選擇使用公用或私人 IPv4 位址進行私人對等互連。 我們會提供流量的端對端隔離，因此在私人對等互連的情況下不可能發生位址與其他客戶重疊。 這些位址不會向網際網路公告。 

### <a name="microsoft-peering"></a>Microsoft 對等互連
Microsoft 對等路徑可讓您連線到 Microsoft 雲端服務。 服務清單包括 Office 365 服務，例如 Exchange Online、SharePoint Online、商務用 Skype 和 Microsoft 團隊。 Microsoft 支援在 Microsoft 對等上的雙向連線能力。 以 Microsoft 雲端服務為目的地的流量，必須使用有效的公用 IPv4 位址，才能進入 Microsoft 網路。

確定已在下列其中一個登錄中註冊您的 IP 位址和 AS 號碼：

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

如果在前述的登錄中沒有指派前置詞和 AS 號碼給您，您必須開啟支援案例，手動驗證您的前置詞和 ASN。 支援需要文件，例如可證明允許您使用資源的授權書。

Microsoft 對等互連允許使用私人 AS 號碼，但也需要進行手動驗證。 此外，我們會針對接收到的前置詞，移除 AS PATH 中的私用 AS 編號。 因此，您無法在 AS PATH 中附加私用 AS 編號以[影響 Microsoft 對等互連的路由](expressroute-optimize-routing.md)。 

> [!IMPORTANT]
> 請勿將同一個公用 IP 路由公告到公用網際網路及透過 ExpressRoute 進行公告。 若要減少造成非對稱式路由的設定不正確的風險，強烈建議 [NAT IP 位址](expressroute-nat.md)透過 ExpressRoute 向 Microsoft 公告的範圍不是向網際網路公告的範圍。 如果這不可能達成，務必確保透過 ExpressRoute 公告比網際網路連線的範圍更明確的範圍。 除了適用於 NAT 的公用路由，您也可以透過 ExpressRoute 來公告內部部署網路中伺服器所使用的公用 IP 位址，其會與 Microsoft 內的 Office 365 端點進行通訊。 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>公用對等互連 (已被取代 - 不適用於新的線路)
Azure 公用對等路徑可讓您連接到裝載於 Azure 中的所有服務的公用 IP 位址。 其中包括 [ExpessRoute 常見問題集](expressroute-faqs.md) 列出的服務，以及由 ISV 裝載於 Microsoft Azure 上的任何服務。 連線到公用對等的 Microsoft Azure 服務時，一律是從您的網路出發到 Microsoft 網路。 您必須將公用 IP 位址使用於目的地為 Microsoft 網路的流量。

> [!IMPORTANT]
> 透過 Microsoft 對等互連可存取所有 Azure PaaS 服務。
>   

公用對等互連允許私人 AS 號碼。

## <a name="dynamic-route-exchange"></a>動態路由交換
路由交換將會透過 eBGP 通訊協定。 MSEEs 與您的路由器之間會建立 EBGP 工作階段。 不一定需要驗證 BGP 工作階段。 如有必要，也可以設定 MD5 雜湊。 如需設定 BGP 工作階段的資訊，請參閱[設定路由](how-to-routefilter-portal.md)和[線路佈建工作流程和線路狀態](expressroute-workflows.md)。

## <a name="autonomous-system-numbers"></a>自發系統號碼
Microsoft 會使用 AS 12076 進行 Azure 公用、Azure 私人和 Microsoft 對等互連。 我們已保留 65515 至 65520 的 ASN，以供內部使用。 同時支援 16 和 32 位元號碼。

資料傳輸對稱沒有任何相關需求。 轉送與返回路徑可能會周遊不同的路由器配對。 相同的路由必須從橫跨多個屬於您的循環配對的任一端公告。 路由計量不需要完全相同。

## <a name="route-aggregation-and-prefix-limits"></a>路由彙總與前置詞限制
我們支援透過 Azure 私人對等互連向我們公告最多 4000 個前置詞。 如果已啟用 ExpressRoute 進階附加元件，則可增加至 10,000 個前置詞。 我們接受每個 BGP 工作階段最多 200 個前置詞進行 Azure 公用和 Microsoft 對等互連。 

如果前置詞數目超過此限制，則會捨棄 BGP 工作階段。 我們只會接受私人對等互連連結上的預設路由。 提供者必須從 Azure 公用和 Microsoft 對等互連路徑中篩選出預設路由和私人 IP 位址 (RFC 1918)。 

## <a name="transit-routing-and-cross-region-routing"></a>傳輸路由和跨區域路由
ExpressRoute 不能設定為傳輸路由器。 您必須依賴連線提供者的傳輸路由服務。

## <a name="advertising-default-routes"></a>公告預設路由
只有 Azure 私人對等互連工作階段允許預設路由。 在這種情況下，我們會將所有流量從相關聯的虛擬網路路由傳送到您的網路。 在私人對等互連中公告預設路由，將會導致來自 Azure 的網際網路路徑遭到封鎖。 您必須依賴您的公司網路邊緣，為 Azure 中裝載的服務往返路由傳送網際網路的流量。 

 若要能夠連接其他 Azure 服務和基礎結構服務，您必須確定已備妥下列其中一個項目︰

* 啟用 Azure 公用對等互連以將流量路由傳送至公用端點。
* 您可使用使用者定義的路由，讓需要網際網路連線的每個子網路進行網際網路連線。

> [!NOTE]
> 公告預設路由會中斷 Windows 和其他 VM 授權啟用。 請依照 [這裡](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) 的指示來解決這個問題。
> 
> 

## <a name="bgp"></a>BGP 社群支援
本節提供 BGP 社群如何搭配 ExpressRoute 使用的概觀。 Microsoft 將會公告公用和 Microsoft 對等互連路徑中的路由並為路由標記適當的社群值。 這麼做的基本原理和社群值的詳細資料如下所述。 不過，Microsoft 不接受任何標記至向 Microsoft 公告之路由的社群值。

如果您要在地理政治區域內的任何一個對等互連位置透過 ExpressRoute 連接到 Microsoft，您必須能夠存取地理政治界限內所有區域中的所有 Microsoft 雲端服務。 

例如，如果您在阿姆斯特丹透過 ExpressRoute 連接到 Microsoft，您必須能夠存取在北歐和西歐裝載的所有 Microsoft 雲端服務。 

如需地理政治地區、相關聯的 Azure 區域和對應的 ExpressRoute 對等互連位置的詳細清單，請參閱 [ExpressRoute 合作夥伴和對等互連位置](expressroute-locations.md) 網頁。

您可以針對每個地理政治區域購買多個 ExpressRoute 循環。 如果擁有多個連線，您即可因為異地備援而有明顯的高可用性優勢。 具有多個 ExpressRoute 循環的情況下，您會從 Microsoft 收到同一組有關 Microsoft 對等互連和公用對等互連路徑的前置詞。 這表示您將會有多個路徑可從您的網路連到 Microsoft。 這可能會導致在您的網路中做出次佳的路由決策。 因此，您可能會遇到次佳的不同服務連線體驗。 您可以依賴社群值來做出適當的路由決策，以提供[最佳路由給使用者](expressroute-optimize-routing.md)。

| **Microsoft Azure 區域** | **區域 BGP 社區** | **儲存體 BGP 社區** | **SQL BGP 社區** | **Cosmos DB BGP 社區** |
| --- | --- | --- | --- | --- |
| **北美洲** | |
| 美國東部 | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 |
| 美國東部 2 | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 |
| 美國西部 | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 |
| 美國西部 2 | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 |
| 美國中西部 | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 |
| 美國中北部 | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 |
| 美國中南部 | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 |
| 美國中部 | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 |
| 加拿大中部 | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 |
| 加拿大東部 | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 |
| **南美洲** | |
| 巴西南部 | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 |
| **歐洲** | |
| 北歐 | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 |
| 西歐 | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 |
| 英國南部 | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 |
| 英國西部 | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 |
| 法國中部 | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 |
| 法國南部 | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 |
| 瑞士北部 | 12076:51038 | 12076:52038 | 12076:53038 | 12076:54038 | 
| 瑞士西部 | 12076:51039 | 12076:52039 | 12076:53039 | 12076:54039 | 
| 德國北部 | 12076:51040 | 12076:52040 | 12076:53040 | 12076:54040 | 
| 德國中西部 | 12076:51041 | 12076:52041 | 12076:53041 | 12076:54041 | 
| 挪威東部 | 12076:51042 | 12076:52042 | 12076:53042 | 12076:54042 | 
| 挪威西部 | 12076:51043 | 12076:52043 | 12076:53043 | 12076:54043 | 
| **亞太地區** | |
| 東亞 | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 |
| 東南亞 | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 |
| **日本** | |
| 日本東部 | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 |
| 日本西部 | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 |
| **澳大利亞** | |
| 澳大利亞東部 | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 |
| 澳大利亞東南部 | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 |
| **澳洲政府** | |
| 澳大利亞中部 | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 |
| 澳大利亞中部 2 | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 |
| **印度** | |
| 印度南部 | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 |
| 印度西部 | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 |
| 印度中部 | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 |
| **南韓** | |
| 南韓南部 | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 |
| 南韓中部 | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 |
| **南非**| |
| 南非北部 | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 |
| 南非西部 | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 |
| **阿拉伯聯合大公國**| |
| 阿拉伯聯合大公國北部 | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 |
| 阿拉伯聯合大公國中部 | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 |


所有 Microsoft 公告的路由會都加上適當的社群值。 

> [!IMPORTANT]
> 全域前置詞會加上適當的社群值。
> 
> 

### <a name="service-to-bgp-community-value"></a>服務對 BGP 社區值
除了上述各項，Microsoft 也將根據其所屬的服務加上標記及前置詞。 這只適用於 Microsoft 對等互連。 下表提供服務與 BGP 社群值的對應。 如需最新值的完整清單，您可以執行 ' AzBgpServiceCommunity ' Cmdlet。

| **服務** | **BGP 社群值** |
| --- | --- |
| Exchange Online * * | 12076:5010 |
| SharePoint Online * * | 12076:5020 |
| 商務用 Skype Online * * | 12076:5030 |
| CRM Online * * * |12076:5040 |
| Azure 全域服務* | 12076:5050 |
| Azure Active Directory |12076:5060 |
| 其他 Office 365 線上服務 * * | 12076:5100 |

\* Azure 全域服務目前僅包含 Azure DevOps。 \
\* * Microsoft 所需的授權，請參閱[設定 microsoft 對等互連的路由篩選](how-to-routefilter-portal.md)\
CRM Online 支援 Dynamics 8.2 和以下的。 針對較高的版本，請選取 Dynamics 部署的地區群組。

> [!NOTE]
> Microsoft 不接受任何您在向 Microsoft 通告的路由上設定的 BGP 社群值。
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>在國家雲端中的 BGP 社群支援

| **國家雲端 Azure 區域**| **BGP 社群值** |
| --- | --- |
| **US Gov** |  |
| US Gov 亞利桑那州 | 12076:51106 |
| US Gov 愛荷華州 | 12076:51109 |
| US Gov 維吉尼亞州 | 12076:51105 |
| US Gov 德克薩斯州 | 12076:51108 |
| US DoD 中部 | 12076:51209 |
| US DoD 東部 | 12076:51205 |


| **國家雲端中的服務** | **BGP 社群值** |
| --- | --- |
| **US Gov** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| 商務用 Skype Online |12076:5130 |
| 其他 Office 365 Online 服務 |12076:5200 |

## <a name="next-steps"></a>後續步驟
* 設定 ExpressRoute 連線。
  
  * [建立及修改電路](expressroute-howto-circuit-arm.md)
  * [建立和修改對等互連組態](expressroute-howto-routing-arm.md)
  * [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)
