---
title: 'Azure 虛擬 WAN：在中樞內建立網路虛擬裝置 (NVA) '
description: 在本教學課程中，您將瞭解如何在虛擬 WAN 中樞中部署網路虛擬裝置。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: 83267b1bebd501871277ea3e40b7fa9ba38f33cd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313751"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>如何在 Azure 虛擬 WAN hub 中建立網路虛擬裝置 (預覽版) 

本教學課程說明如何使用虛擬 WAN，透過 **網路虛擬裝置** 在 azure 中連線到您在 azure 中的資源 (NVA) 。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)。

本文中的步驟可協助您在虛擬 WAN 中樞內建立 **Barracuda CLOUDGEN WAN** 網路虛擬裝置。 若要完成此練習，您必須擁有 Barracuda Cloud 部署裝置 (CPE) 以及您在開始之前部署到中樞的 Barracuda CloudGen WAN 設備的授權。

如需 **CISCO SD-wan** 在 AZURE 虛擬 WAN 內的部署檔，請以下列電子郵件地址傳送電子郵件給 cisco： vwan_public_preview@cisco.com 。


## <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 取得 Barracuda CloudGen WAN 閘道的授權。 若要深入瞭解如何進行此作業，請參閱 [Barracuda CLOUDGEN WAN 檔](https://www.barracuda.com/products/cloudgenwan)

* 您有一個要連線的虛擬網路。 驗證沒有任何內部部署網路的子網路與您要連線的虛擬網路子網路重疊。 若要在 Azure 入口網站中建立虛擬網路，請參閱[快速入門](../virtual-network/quick-create-portal.md)。

* 您的虛擬網路沒有任何虛擬網路閘道。 如果您的虛擬網路有閘道 (VPN 或 ExpressRoute)，則必須移除所有閘道。 此設定需要將虛擬網路改為連線到虛擬 WAN 中樞閘道。

* 取得中樞區域的 IP 位址範圍。 中樞是虛擬 WAN 建立和使用的虛擬網路。 您為中樞區域指定的位址範圍不能與任何連線的現有虛擬網路重疊。 也不能與連線至內部部署的位址範圍重疊。 如果您不熟悉位於內部部署網路設定的 IP 位址範圍，請與能夠提供那些詳細資料的人員協調。

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>建立虛擬 WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>建立中樞。

中樞是一種虛擬網路，可包含站對站、ExpressRoute、點對站或網路虛擬裝置功能的閘道。 中樞建立好時，您就需要支付中樞費用，即使您未連結任何網站也是如此。 如果您選擇建立站對站 VPN 閘道，則需要30分鐘的時間，才能在虛擬中樞內建立站對站 VPN 閘道。 不同于站對站、ExpressRoute 或點對站，您必須先建立中樞，才能將網路虛擬裝置部署至中樞 VNet。

1. 找出您所建立的虛擬 WAN。 在 [ **虛擬 WAN** ] 頁面的 [連線 **能力** ] 區段下，選取 [ **中樞**]。
1. 在 [ **中樞** ] 頁面上，選取 [+ 新增中樞] 以開啟 [ **建立虛擬中樞** ] 頁面。

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="基本概念":::
1. 在 [建立虛擬中樞]**** 頁面的 [基本]**** 索引標籤中，完成以下欄位：

   **專案詳細資料**

   * 區域 (先前稱為位置)
   * 名稱
   * 中樞私人位址空間。 建立中樞的最小位址空間為 /24，這表示在建立期間從 /25 到 /32 的任何範圍都會產生錯誤。 Azure 虛擬 WAN 是 Microsoft 所提供的受控服務，可在虛擬中樞內針對不同的閘道/服務建立適當的子網。  (例如：網路虛擬裝置、VPN 閘道、ExpressRoute 閘道、使用者 VPN/點對站閘道、防火牆、路由等 ) 。 使用者不需要為虛擬中樞內的服務明確規劃子網位址空間，因為 Microsoft 會將其作為服務的一部分。
1. 選取 [檢閱 + 建立]**** 以進行驗證。
1. 選取 [建立]**** 以建立中樞。

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>在中樞內建立網路虛擬裝置

在此步驟中，您將在中樞內建立網路虛擬裝置。 每個 NVA 夥伴產品的每個 NVA 程式都會不同。 在此範例中，我們要建立 Barracuda CloudGen WAN 閘道。

1. 找出您在上一個步驟中建立的虛擬 WAN 中樞，並加以開啟。

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="虛擬中樞":::
1. 尋找 [網路虛擬裝置] 磚，然後選取 [ **建立** ] 連結。
1. 在 [ **網路虛擬裝置** ] 分頁上，選取 [ **Barracuda CloudGen WAN**]，然後選取 [ **建立** ] 按鈕。

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="選取 NVA":::
1. 這會帶您前往 Barracuda CloudGen WAN 閘道的 Azure Marketplace 供應專案。 閱讀條款，然後在您準備就緒時選取 [ **建立** ] 按鈕。

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Barracuda NVA 基本概念":::
1. 在 [ **基本** ] 頁面上，您將需要提供下列資訊：

   * **訂** 用帳戶-選擇您用來部署虛擬 WAN 和中樞的訂用帳戶。
   * **資源群組** -選擇您用來部署虛擬 WAN 和中樞的相同資源群組。
   * **區域** -選擇虛擬中樞資源所在的相同區域。
   * **應用程式名稱** -BARRACUDA NextGen WAN 是受控應用程式。 選擇可讓您輕鬆識別此資源的名稱，因為它會在您的訂用帳戶中出現時呼叫。
   * **受控資源群組** -這是受控資源群組的名稱，Barracuda 會在其中部署其所管理的資源。 此名稱應預先填入。
1. 選取 [ **下一步： CLOUDGEN WAN 閘道]** 按鈕。

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen WAN 閘道":::
1. 請在此提供下列資訊：

   * **虛擬 Wan 中樞** -您想要部署此 NVA 的虛擬 wan 中樞。
   * **NVA 基礎結構單位** -指出您要用來部署此 NVA 的 NVA 基礎結構單位數目。 選擇您想要在所有將透過此 NVA 連線至此中樞的分支網站上提供的匯總頻寬容量量。
   * **權杖** Barracuda 需要您在這裡提供驗證權杖，才能將您自己識別為此產品的已註冊使用者。 您必須從 Barracuda 取得。
1. 選取 [ **審核] 和 [建立** ] 按鈕以繼續。
1. 在此頁面上，系統會要求您接受共同管理員存取合約的條款。 這是受控應用程式的標準，發行者會在此部署中存取某些資源。 勾選 [ **我同意上述的條款及條件** ] 方塊，然後選取 [ **建立**]。

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>將 VNet 連線到中樞

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>後續步驟

* 若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
* 若要深入瞭解虛擬 WAN 中樞內的 Nva，請參閱 [關於虛擬 wan hub 中的網路虛擬裝置 (預覽) ](about-nva-hub.md)。
