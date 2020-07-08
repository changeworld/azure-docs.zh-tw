---
title: Microsoft 對等互連原則
titleSuffix: Azure
description: Microsoft 對等互連原則
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75775221"
---
# <a name="peering-policy"></a>對等互連原則
下列各節將說明 Microsoft 的一般網路需求。 這些會同時適用于直接對等互連和 Exchange 對等互連要求。

## <a name="technical-requirements"></a>技術需求

* 完全多餘的網路，具有足夠的容量來交換流量，而不受擁塞。
* 對等將擁有可公開路由傳送的自發系統編號（ASN）。
* 同時支援 IPv4 和 IPv6，而 Microsoft 預期會在每個對等互連位置中建立兩種類型的會話。
* 不支援 MD5。
* **ASN 詳細資料：**
    * Microsoft 會管理 AS8075 以及下列 Asn： AS8068、AS8069、AS12076。 如需具有 AS8075 對等互連的 Asn 完整清單，請參考 AS-SET MICROSOFT。
    * 所有與 Microsoft 的對等互連都同意在任何情況下都不接受來自 AS12076 （Express Route）的路由，而且應該篩選掉所有對等的 AS12076。
* **路由原則：**
    * 對等將至少有一個可公開路由/24。
    * Microsoft 將會覆寫已接收的多結束鑒別子（MED-V）。
    * Microsoft 偏好接收來自對等的 BGP 社區標記，以指出路由來源。
    * 對等預期會在公用網際網路路由登錄（IRR）資料庫中註冊其路由，以進行篩選，並妥善努力讓這項資訊保持在最新狀態。
    * 我們建議對等互連在與 Microsoft 的點對點工作階段上設定1000（IPv4）和100（IPv6）路由的最大首碼。
    * 除非事先同意，否則對等應該會在與 Microsoft 對等互連的所有位置中宣佈一致的路由。
    * 一般而言，具有 AS8075 的對等互連會話會通告所有 AS MICROSOFT 路由。 非洲和亞洲的 AS8075 互連可能僅限於與個別區域相關的路由。
    * 雙方都不會建立靜態路由、最後手段的路由，或將流量傳送至另一方，以取得未透過 BGP 宣告的路由。
    * 對等端應該遵守路由安全性的[MANRS](https://www.manrs.org/)業界標準。

## <a name="operational-requirements"></a>操作需求
* 完全配備全天候的網路營運中心（NOC），能夠協助解決所有的技術和效能問題、安全性違規、阻絕服務攻擊，或任何其他源自于對等或其客戶的濫用。
* 對等預期會在[PeeringDB](https://www.peeringdb.com)上擁有完整且最新的設定檔，包括來自公司網域和電話號碼的全天候 NOC 電子郵件。 我們會使用此資訊來驗證對等的詳細資料，例如 NOC 資訊、技術連絡人資訊，以及其在對等互連設施的狀態等。不接受個人 Yahoo、Gmail 和 hotmail 帳戶。

## <a name="physical-connection-requirements"></a>實體連接需求
* 您可以與 Microsoft 連線以進行直接對等互連或交換對等互連的位置列于[PeeringDB](https://www.peeringdb.com/net/694)
* **交換對等互連：**
    * 互連必須使用適當的10Gbps 光學器件，而不是透過單一模式的光纖。
    * 當尖峰使用率超過50% 時，對等應該會升級其埠。
* **直接對等互連：**
    * 互連必須使用適當的10Gbps 或100Gbps 光學器件，而不是透過單一模式的光纖。
    * Microsoft 只會與 ISP 或網路服務提供者建立直接的對等互連。
    * 當尖峰使用率超過50% 時，對等會預期會升級其埠，並在 metro 中的單一位置或跨多個位置維護每張 metro 的各種容量。
    * 每個直接對等互連都包含兩個來自對等的路由器（位於對等邊緣）之兩個 Microsoft edge 路由器的連線。 Microsoft 在這些連線中需要雙重 BGP 會話。 對等可能會選擇不要在其末端部署多餘的裝置。

## <a name="traffic-requirements"></a>流量需求
* 對等互連的交換必須至少有 200 Mb 的流量，且小於 2 Gb。  應檢查超過2Gb 直接對等互連的流量。
* 針對直接對等互連，從您的網路到 Microsoft 的流量必須符合以下最低需求。

    | 地理區域                      | 最小流量至 Microsoft   |
    | :----------------------- |:-------------------------------|
    | 非洲                   | 500 Mbps                       |
    | APAC （印度除外）      |   2 Gbps                       |
    | APAC （僅限印度）        | 500 Mbps                       |
    | 歐洲                   |   2 Gbps                       |
    | LATAM                    |   2 Gbps                       |
    | 中東              | 500 Mbps                       |
    | NA                       |   2 Gbps                       |

* **變化**
    * 在 NA、歐洲、APAC 和 LATAM 中，如果可行，請在至少三個不同的位置進行互連，並維護不同的容量，以允許在每個 metro 內容錯移轉的流量。
    * 在非洲、中東和印度，盡可能地在多個不同位置進行互連。 必須維持足夠的多樣化容量，以確保流量保留在區域中。

## <a name="next-steps"></a>後續步驟

* 若要瞭解設定與 Microsoft 直接對等互連的步驟，請遵循[直接對等的逐步](walkthrough-direct-all.md)解說。
* 若要瞭解設定與 Microsoft 交換對等互連的步驟，請遵循[Exchange 對等互連逐步](walkthrough-exchange-all.md)解說。