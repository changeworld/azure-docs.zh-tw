---
title: 微軟對等策略
titleSuffix: Azure
description: 微軟對等策略
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775221"
---
# <a name="peering-policy"></a>對等互連原則
Microsoft 對網路的一般要求如下說明。 它們適用于直接對等互連和 Exchange 對等互連請求。

## <a name="technical-requirements"></a>技術需求

* 完全冗余的網路，有足夠的容量來交換流量，而不會造成擁塞。
* 對等體將具有可公開路由的自治系統編號 （ASN）。
* IPv4 和 IPv6 都支援，Microsoft 希望在每個對等位置建立這兩種類型的會話。
* 不支援 MD5。
* **ASN 詳細資訊：**
    * Microsoft 管理 AS8075 以及以下 ASN：AS8068、AS8069、AS12076。 有關具有 AS8075 對等互連的完整 ASN 清單，請參閱 AS-SET MICROSOFT。
    * 與 Microsoft 同行的各方均同意在任何情況下都不接受 AS12076（快速路線）的路線，並應過濾掉所有對等方的 AS12076。
* **路由策略：**
    * 對等方將至少有一個公開可路由 /24。
    * 微軟將覆蓋收到的多退出歧視器 （MED）。
    * Microsoft 更喜歡從對等方接收 BGP 社區標記來指示路由發起。
    * 同行應將其路由註冊在公共互聯網路由註冊表 （IRR） 資料庫中，以便進行篩選，並將真誠地努力使此資訊保持最新。
    * 我們建議對等體在與 Microsoft 的點對點工作階段中設置最大首碼 1000 （IPv4） 和 100 （IPv6） 路由。
    * 除非事先明確約定，否則同行應宣佈與 Microsoft 對等的所有位置的一致路由。
    * 通常，AS8075 的點對點工作階段將通告所有 AS-MICROSOFT 路由。 AS8075 在非洲和亞洲的互連可能僅限於與各自區域相關的航線。
    * 任何一方都不會建立靜態路線、最後手段的路線，或者將流量發送給另一方，用於未通過 BGP 宣佈的路由。
    * 同行應遵守[MANRS](https://www.manrs.org/)的路由安全標準。

## <a name="operational-requirements"></a>業務要求
* 配備齊全的 24x7 網路運營中心 （NOC），能夠協助解決所有技術和性能問題、安全違規、拒絕服務攻擊或源自同行或其客戶的任何其他濫用行為。
* 同行在[PeeringDB](https://www.peeringdb.com)上應擁有完整和最新的設定檔，包括來自公司域和電話號碼的 24x7 NOC 電子郵件。 我們使用這些資訊來驗證同行的詳細資訊，如 NOC 資訊、技術聯繫資訊及其在對等設施的存在等。不接受個人雅虎、Gmail和熱郵帳戶。

## <a name="physical-connection-requirements"></a>物理連接要求
* 可在對等互連或交換對等互連中與 Microsoft 連接的位置列在[對等互連](https://www.peeringdb.com/net/694)中
* **交換對等互連：**
    * 互連必須使用適當的 10Gbps 光學元件在單模光纖上。
    * 當峰值利用率超過 50% 時，對等體應升級其埠。
* **直接對等互連：**
    * 互連必須使用適當的 10Gbps 或 100Gbps 光纖在單模光纖上。
    * Microsoft 將僅與 ISP 或網路服務提供者建立直接對等互連。
    * 當峰值利用率超過 50% 時，同行應升級其埠，並在每個地鐵中保持不同的容量，無論是在單個位置內，還是跨地鐵中的多個位置。
    * 每個直接對等互連由兩個連接組成，從位於對等器邊緣的對等路由器連接到兩個 Microsoft 邊緣路由器。 Microsoft 需要跨這些連接進行雙 BGP 會話。 對等體可以選擇不在其末尾部署冗余設備。

## <a name="traffic-requirements"></a>流量要求
* 對等互連的對等體必須至少具有 200Mb 的流量和小於 2Gb 的流量。  對於流量超過 2Gb 的直接對等互連，應進行審查。
* 對於直接對等互連，從網路到 Microsoft 的流量必須滿足低於最低要求。

    | 地理區域                      | 到微軟的最低流量   |
    | :----------------------- |:-------------------------------|
    | 非洲                   | 500 Mbps                       |
    | 亞太地區（印度除外）      |   2 Gbps                       |
    | 亞太地區（僅限印度）        | 500 Mbps                       |
    | 歐洲                   |   2 Gbps                       |
    | 拉塔姆                    |   2 Gbps                       |
    | 中東              | 500 Mbps                       |
    | NA                       |   2 Gbps                       |

* **多樣性：**
    * 在 NA、歐洲、亞太地區和 LATAM，如果可行，在至少三個地理位置不同的地點進行互連，並保持不同的容量，以便在每個地鐵內實現交通容錯移轉。
    * 在非洲、中東和印度，在盡可能多的不同地點相互連接。 必須保持足夠的多樣化能力，以確保區域內的交通保持暢通。

## <a name="next-steps"></a>後續步驟

* 要瞭解設置與 Microsoft 的直接對等互連的步驟，請按照[直接對等演練進行](walkthrough-direct-all.md)。
* 要瞭解與 Microsoft 設置 Exchange 對等互連的步驟，請按照[Exchange 對等演練進行](walkthrough-exchange-all.md)。