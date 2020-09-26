---
title: Azure 虛擬 WAN：關於中樞內的網路虛擬裝置
description: 在本文中，您將瞭解虛擬 WAN 中樞內的網路虛擬裝置。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: 1e4b8a2d801d7d7eccfaf558c3926ead1ab0a953
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313768"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>關於 Azure 虛擬 WAN hub 中的網路虛擬裝置 (預覽) 

Azure 虛擬 WAN 與網路夥伴合作，以建立自動化，讓您輕鬆地將客戶的客戶部署設備 (CPE) 連接到虛擬中樞內的 Azure VPN 閘道。 Azure 正與選取的網路合作夥伴合作，讓客戶可以部署協力廠商網路虛擬裝置 (NVA 直接) 至虛擬中樞。 這可讓想要將其分支 CPE 連線到虛擬中樞內相同品牌 NVA 的客戶，讓他們可以利用專屬的端對端 SD WAN 功能。

Barracuda 網路是第一個提供 NVA 供應專案的合作夥伴，可以使用其 [Barracuda CLOUDGEN WAN](https://www.barracuda.com/products/cloudgenwan) 產品直接部署到虛擬 WAN 中樞。 Azure 正與更多合作夥伴合作，因此預期會看到其他供應專案。

> [!NOTE]
> 只有可部署到虛擬 WAN 中樞的 NVA 供應專案可以部署到虛擬 WAN 中樞。 它們無法部署至 Azure 中的任意虛擬網路。

## <a name="how-does-it-work"></a><a name="how"></a>運作方式

可以直接部署到 Azure 虛擬 WAN 中樞的 Nva，是專門用在虛擬中樞的程式設計。 NVA 供應專案發佈至 Azure Marketplace 作為受控應用程式，客戶可以直接從 Azure Marketplace 部署供應專案，也可以透過 Azure 入口網站從虛擬中樞部署供應專案。

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="程序概觀":::

每個合作夥伴的 NVA 供應專案會根據其部署需求，提供稍微不同的體驗和功能。 不過，在虛擬 WAN 中樞內 NVA 的所有合作夥伴供應專案都有一些常見的專案。

* 透過 Azure Marketplace 提供的受控應用程式體驗。
* NVA 基礎結構以單位為基礎的容量和帳單。
* 透過 Azure 監視器呈現的健康情況計量。

### <a name="managed-application"></a><a name="managed"></a>受控應用程式

所有可部署到虛擬 WAN 中樞的 NVA 供應專案，都會有一個可在 Azure Marketplace 中使用的 **受控應用程式** 。 受控應用程式可讓合作夥伴執行下列作業：

* 打造 NVA 的自訂部署體驗。
* 提供特製化的 Resource Manager 範本，讓他們可以直接在虛擬 WAN 中樞內建立 NVA。
* 直接或透過 Azure Marketplace 計費軟體授權成本。
* 公開自訂屬性和資源計量。

NVA 合作夥伴可能會根據其設備部署、設定授權和管理需求來建立不同的資源。 當客戶在虛擬 WAN 中樞內建立 NVA 時（如同所有受控應用程式），其訂用帳戶中會建立兩個資源群組。

* **客戶資源群組** -這將包含受控應用程式的應用程式預留位置。 合作夥伴可以使用此屬性，公開他們在此處選擇的任何客戶內容。
* **受控資源群組** -客戶無法直接設定或變更此資源群組中的資源，因為這是由受管理應用程式的發行者所控制。 此資源群組會包含 **NetworkVirtualAppliances** 資源。

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="受控應用程式資源群組":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA 基礎結構單位

當您在虛擬 WAN 中樞內建立 NVA 時，您必須選擇要用來部署的 NVA 基礎結構單位數目。 **NVA 基礎結構單位**是虛擬 WAN 中樞內 NVA 的匯總頻寬容量單位。 **NVA 基礎結構單位**類似于 VPN[縮放單位](pricing-concepts.md#scale-unit)，就您考慮容量和調整大小的方式而言。

* 1 NVA 基礎結構單位代表所有進入此 NVA 之分支網站連線的匯總頻寬 500 Mbps，成本為每小時 $ 0.25/小時。
* 針對指定的 NVA 虛擬中樞部署，Azure 支援 1-80 NVA 基礎結構單位。
* 每個夥伴可能會提供不同的 NVA 基礎結構單位套件組合，這些都是所有支援的 NVA 基礎結構單位設定的子集。

類似于 VPN 縮放單位，如果您選擇 *1 個 NVA 基礎結構單位 = 500 Mbps*，則表示將會建立兩個冗余實例，每個實例都有 500 Mbps 的最大輸送量。 例如，如果您有五個分支，每個分支執行 10 Mbps，則您需要在前端有 50 Mbps 的彙總。 在評估支援中樞的分支數目所需的容量之後，應完成 NVA 的匯總容量規劃。

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>網路虛擬裝置設定流程

合作夥伴已努力提供在部署過程中自動設定 NVA 的體驗。 將 NVA 布建到虛擬中樞之後，NVA 所需的任何其他設定都必須透過 NVA 合作夥伴入口網站或管理應用程式來完成。 無法直接存取 NVA。

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>使用 Nva 的網站和連線資源

不同于 Azure VPN 閘道設定，您不需要建立 **網站** 資源、 **站對站** 連線資源或 **點對站** 連線資源，將您的分支網站連線至虛擬 WAN 中樞內的 NVA。 這是透過 NVA 合作夥伴進行管理。

您仍然需要建立中樞對 VNet 連線，以將您的虛擬 WAN 中樞連線到您的 Azure 虛擬網路。

## <a name="supported-regions"></a><a name="regions"></a>支援區域

虛擬中樞內的 NVA 可在下欄區域中預覽：

|地緣政治區域 | Azure 區域|
|---|---|
| 北美洲| 美國西部、美國中南部、美國東部2   |
| 南美洲 | 巴西南部 |
| 歐洲 | 西歐、英國南部|
|  中東 | 阿拉伯聯合大公國北部 |
| 亞洲 | 日本東部 |
| 澳大利亞 | 澳大利亞東部 |

## <a name="faq"></a>常見問題集

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>我是網路設備合作夥伴，而且想要在中樞內取得我們的 NVA。  我可以加入這個合作夥伴計畫嗎？

可惜的是，目前沒有任何新合作夥伴供應專案的容量。 請在11月向我們回頭看看！

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>我可以從 Azure Marketplace 將任何 NVA 部署到虛擬 WAN 中樞嗎？

否。 目前，只有 [Barracuda CLOUDGEN WAN](https://aka.ms/BarracudaMarketPlaceOffer) 可以部署到虛擬 WAN 中樞。

### <a name="what-is-the-cost-of-the-nva"></a>NVA 的成本為何？

您必須向 Barracuda 購買 Barracuda CloudGen WAN NVA 的授權。 如需授權的詳細資訊，請參閱 [Barracuda 的 CLOUDGEN WAN 頁面](https://www.barracuda.com/products/cloudgenwan)。 此外，您也會針對您所使用的 NVA 基礎結構單位，以及您使用的任何其他資源，產生費用給 Microsoft。 如需詳細資訊，請參閱 [定價概念](pricing-concepts.md)。

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>我可以將 NVA 部署至基本中樞嗎？

否。 如果您想要部署 NVA，您必須使用標準中樞。

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>我可以將 NVA 部署到安全的中樞嗎？

可以。 您可以使用 Azure 防火牆將 Barracuda CloudGen WAN 部署到中樞。

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>我可以將我的分公司中的任何 CPE 裝置連線到中樞內的 Barracuda CloudGen WAN NVA 嗎？

否。 Barracuda CloudGen WAN 只與 Barracuda CPE 裝置相容。 若要深入瞭解 CloudGen WAN 需求，請參閱 [Barracuda 的 CLOUDGEN wan 頁面](https://www.barracuda.com/products/cloudgenwan)。

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>中樞內的 NVA 支援哪些路由案例？

中樞內的 Nva 支援虛擬 WAN 支援的所有路由案例。

## <a name="next-steps"></a>後續步驟

若要深入瞭解虛擬 WAN，請參閱 [虛擬 Wan 總覽](virtual-wan-about.md) 文章。