---
title: 關於虛擬 WAN 定價
titleSuffix: Azure Virtual WAN
description: 本文說明常見的虛擬 WAN 定價問題
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 8ced9bb33be341d35904967092414676a6ffe3ac
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905531"
---
# <a name="about-virtual-wan-pricing"></a>關於虛擬 WAN 定價

Azure 虛擬 WAN 會將多個網路和安全性服務整合在一個統一的架構中。 它是以中樞和輪輻架構為基礎，其中中樞會以中樞內提供的各種服務（例如 VPN、ExpressRoute、使用者 VPN (點對站) 、防火牆、路由等等）進行管理。

虛擬 WAN 中的每個服務都是定價。 因此，建議使用單一價格並不適用于虛擬 WAN。 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)提供一種衍生成本的機制，此成本是以虛擬 WAN 中布建的服務為基礎。 本文討論關於虛擬 WAN 定價的常見問題。

>[!NOTE]
>如需最新的定價資訊，請參閱 [虛擬 WAN 定價](https://azure.microsoft.com/pricing/details/virtual-wan/)。
>

## <a name="common-pricing-questions"></a><a name="questions"></a>常見的定價問題

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>什麼是縮放單位？

**縮放單位** 會提供站對站 (S2S) 、點對站 (P2S) ，以及虛擬中樞內 EXPRESSROUTE (ER) 的匯總容量單位。 例如：

* **1 S2S VPN 縮放單位** 指的是 500 Mbps VPN 閘道的總容量， (在虛擬中樞的每小時成本中部署復原) 的雙實例。
* **1 ER 縮放單位** 指的是虛擬中樞的總 2 Gbps ER 閘道，成本為 $ 0.42/小時。
* **5 個 ER 縮放單位** 會在虛擬中樞 VNet 內表示總共 10 GBPS 的 er 閘道，價格為每小時 $ 0.42 * 5/小時。 ER 會從第6到第10個縮放單位遞增 $ 0.25/小時。

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>什麼是連接單元？

**連接單位** 會套用至任何連線至 Azure 閘道的內部部署/非 Microsoft 端點。 針對站對站 VPN，此值表示分支。 針對使用者 VPN (點對站) ，此值表示遠端使用者。 針對 ExpressRoute，此值表示 ExpressRoute 線路連接。<br>例如：

* 一個連接到虛擬中樞的 Azure VPN 的分支連線成本為 $ 0.05/小時。 因此，連接到 Azure 虛擬中樞的100分支連線會花費 $ 0.05 * 100/小時。

* 連線到虛擬中樞的兩個 ExpressRoute 線路連線，會有 $ 0.05 * 2/小時的費用。

* 連接到 Azure 虛擬中樞 P2S 閘道的三個遠端使用者連線，將會花費 $ 0.03 * 3/小時。

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>資料傳輸費用如何計算？

* 任何進入 Azure 的流量都不會收費。 透過 VPN、ExpressRoute 或點對站使用者) VPN 連線來離開 Azure (的流量，會受限於標準的 [azure 資料傳輸費用](https://azure.microsoft.com/pricing/details/bandwidth/)。

* 針對虛擬 WAN 中樞與遠端虛擬 WAN 中樞或不同于來源中樞之區域中的 VNet 之間的資料傳輸費用，資料傳輸費用適用于離開中樞的流量。 範例：離開美國東部中樞的流量將會向美國西部中樞收取 $ 0.02/GB 的費用。 進入「美國西部」中樞的流量不會收取任何費用。 所有中樞對中樞流量均受限於 Inter-Region (內部/內部大陸) 收取 [Azure 資料傳輸費用](https://azure.microsoft.com/pricing/details/bandwidth/)。 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>標準中樞費用和標準中樞處理費之間有何差異？

虛擬 WAN 分為兩種：

* **基本的虛擬 WAN**，使用者可在其中部署多個中樞，並享受 VPN 站對站連線能力。 基本的虛擬 WAN 沒有先進的功能，例如全網狀架構的中樞、ExpressRoute 連線、使用者 VPN/點對站 VPN 連線能力、VNet 對 VNet 的可轉移連線、VPN 和 ExpressRoute 傳輸連線能力，或 Azure 防火牆等等。基本虛擬 WAN 中的中樞沒有基本費用或資料處理費用。

* **標準虛擬 WAN** 提供先進的功能，例如全網狀架構的中樞、ExpressRoute 連線能力、使用者 Vpn/點對站 VPN 連線能力、VNet 對 VNet 的可轉移連線、VPN 和 ExpressRoute 傳輸連線能力、Azure 防火牆等。所有虛擬中樞路由都是由路由器提供，可啟用虛擬中樞內的多個服務。 中樞有基本費用，價格為每小時 $ 0.25。 虛擬中樞路由器中的資料處理也會收取 VNet 對 VNet 傳輸連線能力的費用。 請參閱下圖。

 在下列 **範例** 圖中，有兩個 VNET、vnet 1 和 vnet 2。 假設有 1 Gbps 資料從 VNET 1 中的 VM 傳送到 VNET 2 中的另一個 VM。 適用下列費用：

* 虛擬中樞基本費用 $ 0.25/小時

* 1 Gbps 的虛擬中樞資料處理費 $ 0.02/GB

**範例**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="範例":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。

* 如需最新定價，請參閱 [虛擬 WAN 定價](https://azure.microsoft.com/pricing/details/virtual-wan/)。