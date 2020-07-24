---
title: Azure 虛擬 WAN：關於中樞內的網路虛擬裝置
description: 在本文中，您將瞭解虛擬 WAN 中樞中的網路虛擬裝置。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: ad7c7fb5111ce700a5725336b7c9db788e178c4c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096369"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>關於 Azure 虛擬 WAN 中樞中的網路虛擬裝置（預覽）

Azure 虛擬 WAN 與網路合作夥伴合作，以建立自動化功能，讓其客戶部署設備（CPE）輕鬆地連接到虛擬中樞的 Azure VPN 閘道。 Azure 正與精選網路合作夥伴合作，讓客戶可以將協力廠商網路虛擬裝置（NVA）直接部署到虛擬中樞。 這可讓想要將其 branch CPE 連線到虛擬中樞內相同品牌 NVA 的客戶，讓他們可以利用專屬的端對端 SD WAN 功能。

Barracuda Networks 是第一個提供 NVA 供應專案的合作夥伴，可以使用其[Barracuda CLOUDGEN WAN](https://www.barracuda.com/products/cloudgenwan)產品直接部署到虛擬 WAN 中樞。 Azure 正與更多合作夥伴合作，因此預期會看到其他供應專案。

> [!NOTE]
> 只有可供部署至虛擬 WAN 中樞的 NVA 供應專案，才可以部署到虛擬 WAN 中樞。 它們無法部署到 Azure 中的任意虛擬網路。

## <a name="how-does-it-work"></a><a name="how"></a>如何運作？

可直接部署到 Azure 虛擬 WAN 中樞的 Nva 專門設計成用於虛擬中樞。 NVA 供應專案會發佈到 Azure Marketplace 做為受控應用程式，而客戶可以直接從 Azure Marketplace 部署供應專案，也可以透過 Azure 入口網站從虛擬中樞部署供應專案。

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="程序概觀":::

每個合作夥伴的 NVA 供應專案會根據其部署需求而有稍微不同的經驗和功能。 不過，在虛擬 WAN 中樞內 NVA 的所有合作夥伴供應專案中，都有一些常見的情況。

* 透過 Azure Marketplace 提供的受控應用程式體驗。
* NVA 基礎結構以單位為基礎的容量和計費。
* 透過 Azure 監視器呈現的健康情況計量。

### <a name="managed-application"></a><a name="managed"></a>受控應用程式

所有可部署到虛擬 WAN 中樞的 NVA 供應專案都有**受控應用程式**，可在 Azure Marketplace 中使用。 受控應用程式可讓合作夥伴執行下列動作：

* 為其 NVA 打造自訂的部署體驗。
* 提供特製化的 Resource Manager 範本，讓他們可以直接在虛擬 WAN 中樞中建立 NVA。
* 直接或透過 Azure Marketplace 帳單軟體授權成本。
* 公開自訂屬性和資源計量。

NVA 合作夥伴可能會根據其設備部署、設定授權和管理需求，來建立不同的資源。 當客戶在虛擬 WAN 中樞（例如所有受控應用程式）中建立 NVA 時，其訂用帳戶中會建立兩個資源群組。

* **客戶資源群組**-這會包含受控應用程式的應用程式預留位置。 合作夥伴可以使用此資訊來公開其在此處選擇的任何客戶屬性。
* **受控資源群組**-客戶無法直接設定或變更此資源群組中的資源，因為這是由受控應用程式的發行者所控制。 此資源群組會包含**NetworkVirtualAppliances**資源。

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="受控應用程式資源群組":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA 基礎結構單位

當您在虛擬 WAN 中樞建立 NVA 時，您必須選擇您想要用來部署它的 NVA 基礎結構單位數目。 **NVA 基礎結構單位**是虛擬 WAN 中樞內 NVA 的匯總頻寬容量單位。 **NVA 基礎結構單位**類似于 VPN[縮放單位](pricing-concepts.md#scale-unit)，就像您對容量和大小的考慮。

* 1 NVA 基礎結構單位代表所有進入此 NVA 之分支網站連線的匯總頻寬 500 Mbps，其費用為每小時 $ 0.25 美元。
* Azure 支援指定 NVA 虛擬中樞部署的 1-80 NVA 基礎結構單位。
* 每個合作夥伴可能會提供不同的 NVA 基礎結構單位組合，這些組合是所有支援的 NVA 基礎結構單元設定的子集。

類似于 VPN 縮放單位，如果您選擇*1 個 NVA 基礎結構單位 = 500 Mbps*，則表示將會建立兩個冗余實例，每個都有 500 Mbps 的最大輸送量。 例如，如果您有五個分支，每個分支執行 10 Mbps，則您需要在前端有 50 Mbps 的彙總。 在評估支援中樞分支數目所需的容量之後，應完成 NVA 的匯總容量規劃。

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>網路虛擬裝置設定程式

合作夥伴致力於提供在部署過程中自動設定 NVA 的體驗。 將 NVA 布建到虛擬中樞之後，NVA 可能需要的任何額外設定都必須透過 NVA 合作夥伴入口網站或管理應用程式來完成。 無法直接存取 NVA。

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>使用 Nva 的網站和連線資源

不同于 Azure VPN 閘道設定，您不需要建立**網站**資源、**站對站**連線資源，或**點對站**連線資源，就能將您的分支網站連線到虛擬 WAN 中樞中的 NVA。 這全都是透過 NVA 合作夥伴來管理。

您仍然需要建立中樞對 VNet 連線，以將您的虛擬 WAN 中樞連線到您的 Azure 虛擬網路。

## <a name="supported-regions"></a><a name="regions"></a>支援的區域

在下欄區域中，虛擬中樞內的 NVA 可供預覽：

|地緣政治區域 | Azure 區域|
|---|---|
| 北美洲| 美國西部、美國中南部、美國東部2   |
| 南美洲 | 巴西南部 |
| 歐洲 | 西歐，英國南部|
|  中東 | 阿拉伯聯合大公國北部 |
| Asia | 日本東部 |
| 澳大利亞 | 澳大利亞東部 |

## <a name="faq"></a>常見問題集

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>我是網路設備合作夥伴，而且想要在中樞內取得 NVA。  我可以加入此合作夥伴計畫嗎？

可惜的是，目前沒有任何新合作夥伴優惠的容量可供您使用。 請在11月回頭洽詢我們！

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>我可以將任何 NVA 從 Azure Marketplace 部署到虛擬 WAN 中樞嗎？

不可以。 此時，只有[Barracuda CLOUDGEN WAN](https://aka.ms/BarracudaMarketPlaceOffer)可以部署到虛擬 WAN 中樞。

### <a name="what-is-the-cost-of-the-nva"></a>NVA 的成本為何？

您必須從 Barracuda 購買 Barracuda CloudGen WAN NVA 的授權。 如需授權的詳細資訊，請參閱[Barracuda 的 CLOUDGEN WAN 頁面](https://www.barracuda.com/products/cloudgenwan)。 此外，您也會針對您所取用的 NVA 基礎結構單位以及您使用的其他任何資源，收取 Microsoft 費用。 如需詳細資訊，請參閱[定價概念](pricing-concepts.md)。

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>我可以將 NVA 部署到基本中樞嗎？

不可以。 如果您想要部署 NVA，您必須使用標準中樞。

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>我可以將 NVA 部署到安全中樞嗎？

是。 Barracuda CloudGen WAN 可以部署到具有 Azure 防火牆的中樞。

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>我可以將分公司的任何 CPE 裝置連線到中樞內的 Barracuda CloudGen WAN NVA 嗎？

不可以。 Barracuda CloudGen WAN 只能與 Barracuda CPE 裝置相容。 若要深入瞭解 CloudGen WAN 需求，請參閱[Barracuda 的 CLOUDGEN wan 頁面](https://www.barracuda.com/products/cloudgenwan)。

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>中樞內的 NVA 支援哪些路由案例？

中樞內的 Nva 支援虛擬 WAN 支援的所有路由案例。

## <a name="next-steps"></a>後續步驟

若要深入瞭解虛擬 WAN，請參閱[虛擬 Wan 總覽](virtual-wan-about.md)文章。