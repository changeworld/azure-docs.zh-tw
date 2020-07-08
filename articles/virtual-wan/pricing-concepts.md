---
title: 關於虛擬 WAN 定價
titleSuffix: Azure Virtual WAN
description: 本文說明常見的虛擬 WAN 定價問題
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 2d2234ec333746c6f1da59346bdb74247deb616c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567315"
---
# <a name="about-virtual-wan-pricing"></a>關於虛擬 WAN 定價

Azure 虛擬 WAN 將多個網路和安全性服務結合在一套統一的架構中。 它是以中樞和輪輻架構為基礎，其中的中樞是由 Microsoft 管理，並使用中樞內提供的各種服務，例如 VPN、ExpressRoute、使用者 VPN （點對站）、防火牆、路由等等。

虛擬 WAN 中的每個服務都有定價。 因此，建議單一價格不適用於虛擬 WAN。 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)提供一個機制來衍生成本，這是以虛擬 WAN 中布建的服務為基礎。 本文討論有關虛擬 WAN 定價的常見問題。

>[!NOTE]
>如需目前的定價資訊，請參閱[虛擬 WAN 價格](https://azure.microsoft.com/pricing/details/virtual-wan/)。
>

## <a name="common-pricing-questions"></a><a name="questions"></a>常見的定價問題

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>什麼是縮放單位？

**縮放單位**提供虛擬中樞內站對站（S2S）、點對站（P2S）和 EXPRESSROUTE （ER）的匯總容量單位。 例如：

* **1 S2S VPN 縮放單位**意指虛擬中樞每小時的費用是 500 Mbps VPN 閘道的總容量（雙實例已部署為可復原）。
* **1 ER 縮放單位**表示虛擬中樞的總計 2 Gbps ER 閘道 $ 0.42/小時。
* **5 ER 縮放單位表示**虛擬中樞 VNet 內的總計為 10 Gbps ER 閘道，價格為 $ 0.42 * 5/小時。 ER 會從第6到第10個縮放單位遞增 $ 0.25/hr。

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>什麼是連接單位？

**連接單位**適用于任何連線到 Azure 閘道的內部部署/非 Microsoft 端點。 針對站對站 VPN，這表示分支。 針對使用者 VPN （點對站），這表示遠端使用者。 對於 ExpressRoute，這表示 ExpressRoute 線路連線。<br>例如：

* 在虛擬中樞內連線到 Azure VPN 的一個分支連接成本為 $ 0.05/小時。 因此，連接到 Azure 虛擬中樞的100分支連接會花費 $ 0.05 * 100/小時的成本。

* 連線到虛擬中樞的兩個 ExpressRoute 線路連線，會花費 $ 0.05 * 2/小時的費用。

* 連接到 Azure 虛擬中樞 P2S 閘道的三個遠端使用者連線，會花費 $ 0.03 * 3/小時的成本。

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>如何計算資料傳輸費用？

* 輸入 Azure 的任何流量都不會收費。 離開 Azure （透過 VPN、ExpressRoute 或點對站使用者 VPN 連線）的流量受限於標準[Azure 資料傳輸費用](https://azure.microsoft.com/pricing/details/bandwidth/)。

* 若為虛擬 WAN 中樞與遠端虛擬 WAN 中樞之間的資料傳輸費用，以及與來源中樞不同的區域中的遠端虛擬 WAN 集線器或 VNet，則適用于離開中樞之流量的資料傳輸費用。 範例：離開美國東部中樞的流量將會向「美國西部」中樞收取 $ 0.02/GB 的費用。 進入「美國西部」中樞的流量不收費。 下表顯示費用。

下表使用下列縮寫： {名：北美洲}、{EU：歐洲}、{MEA：中東東部}、{OC：大洋洲各國（澳大利亞中部和澳大利亞中部2）}、{LATAM：拉丁美洲} 

**大陸內定價（*）**

| 大陸內部| 價格（$/GB）|
|---|---|
| 指的是|$0.02 |
| EU 至 EU |$0.02 |
| 亞太地區（中國除外）|$0.10 |
| MEA 至 MEA|$0.16 |
| LATAM-LATAM |$0.16 |
| OC-OC|$0.12 |

**大陸間定價（*）**

| 大陸間| 價格（$/GB）|
|---|---|
| 從一到 EU 或 EU |$0.07 |
| 從 LATAM 到任何地方 |$0.17 |
| 從 MEA 到任何地方 |$0.17 |
| 從大洋洲各國到任何地方 |$0.12 |
| 從亞洲（中國除外）到任何位置 |$0.12 |

(*)從2020年8月1日起，部分費用可能會生效。

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>標準中樞費用和標準中樞處理費用之間有何差異？

虛擬 WAN 分為兩種：

* **基本的虛擬 WAN**，使用者可在其中部署多個中樞並享有 VPN 站對站連線能力。 基本的虛擬 WAN 沒有先進的功能，例如完整網狀架構中樞、ExpressRoute 連線、使用者 VPN/點對站 VPN 連線、VNet 對 VNet 可轉移連線、VPN 和 ExpressRoute 傳輸連線能力，或 Azure 防火牆等。基本虛擬 WAN 中的中樞不會有基本費用或資料處理費用。

* **標準虛擬 WAN**提供先進的功能，例如完整網狀架構中樞、ExpressRoute 連線、使用者 VPN/點對站 VPN 連線能力、VNet 對 VNet 可轉移連線、VPN 和 ExpressRoute 傳輸連線能力、Azure 防火牆等。所有虛擬中樞路由都是由路由器提供，可在虛擬中樞內啟用多個服務。 中樞的基本費用是以 $ 0.25/小時計價。 虛擬中樞路由器中的資料處理也會收取 VNet 對 VNet 傳輸連線的費用。 請參閱下圖。

 在下面的**範例**圖中，有兩個 VNET （vnet 1 和 vnet 2）。 假設有 1 Gbps 資料從 VNET 1 中的 VM 傳送到 VNET 2 中的另一個 VM。 適用下列費用：

* 虛擬中樞基本費用 $ 0.25/小時

* 1 Gbps 的虛擬中樞資料處理費用為 $ 0.02/GB

**範例**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="範例":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。

* 如需目前的定價，請參閱[虛擬 WAN 價格](https://azure.microsoft.com/pricing/details/virtual-wan/)。
