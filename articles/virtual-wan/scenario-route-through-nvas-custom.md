---
title: 使用自訂設定透過 Nva 路由傳送流量
titleSuffix: Azure Virtual WAN
description: 此案例可協助您使用不同的 NVA 來進行網際網路系結流量，以透過 Nva 路由傳送流量。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e51d7d00120f6facb0fb53a8e379d157ae79ea4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938572"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>案例：使用自訂設定透過 Nva 路由傳送流量

當您使用 Azure 虛擬 WAN 虛擬中樞路由時，有許多選項可供您使用。 本文的重點是當您想要透過網路虛擬裝置來路由傳送流量時 (NVA) ，以在虛擬網路與分支之間進行通訊，並針對網際網路系結流量使用不同的 NVA。 如需詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a>設計

* 連線到虛擬中樞的虛擬網路 **輪輻**。 在本文稍後的圖表中 (例如，VNet 1、VNet 2 和 VNet 3。 ) 
* 適用于虛擬網路的 **服務 VNet** ，使用者已部署 NVA 來檢查非網際網路流量，以及可能具有輪輻所存取的一般服務。 例如，本文稍後的圖表中的 VNet 4 (。 )  
* 虛擬網路的 **周邊 VNet** ，使用者已在其中部署 NVA，以用來檢查網際網路系結流量。 例如，本文稍後的圖表中的 VNet 5 (。 ) 
* 由 Microsoft 管理的虛擬 WAN 中樞的 **中樞**。

下表摘要說明此案例中支援的連接：

| 寄件者          | 收件者|輪輻|服務 VNet|分支|網際網路|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **輪輻**| ->| 直接 |直接 | 透過服務 VNet |透過周邊 VNet |
| **服務 VNet**| ->| 直接 |n/a| 直接 | |
| **分支** | ->| 透過服務 VNet |直接| 直接 |  |

連接矩陣中的每個資料格都會說明連線是否直接透過虛擬 WAN 或透過 NVA 的其中一個虛擬網路流動。 

注意下列詳細資料：
* 輻條：
  * 輪輻可直接透過虛擬 WAN 中樞觸及其他輪輻。
  * 輪輻可透過指向服務 VNet 的靜態路由，來連接到分支。 它們不會從分支學習特定的前置詞，因為這些首碼更明確，而且會覆寫摘要。
  * 輪輻會透過直接的 VNet 對等互連，將網際網路流量傳送至周邊 VNet。
* 分支會經由指向服務 VNet 的靜態路由進入輪輻。 他們不會從覆寫摘要靜態路由的虛擬網路中學習特定的首碼。
* 服務 VNet 將類似于需要從每個虛擬網路和每個分支連線的共用服務 VNet。
* 周邊 VNet 不需要透過虛擬 WAN 進行連線，因為它所支援的唯一流量只會透過直接虛擬網路對等互連。 不過，若要簡化設定，請使用與周邊 VNet 相同的連接模型。

有三種不同的連接模式，會轉譯成三個路由表。 與不同虛擬網路的關聯如下：

* 輻條：
  * 相關聯的路由表： **RT_V2B**
  * 傳播至路由表： **RT_V2B** 和 **RT_SHARED**
* NVA Vnet (Service VNet 和 DMZ VNet) ：
  * 相關聯的路由表： **RT_SHARED**
  * 傳播至路由表： **RT_SHARED**
* 分支：
  * 相關聯的路由表： **預設值**
  * 傳播至路由表： **RT_SHARED** 和 **預設值**

> [!NOTE] 
> 請確定輪輻 Vnet 不會傳播到預設標籤。 這可確保從分支到輪輻 Vnet 的流量會轉送至 Nva。

這些靜態路由可確保進出虛擬網路和分支的流量會通過服務 VNet (VNet 4) 中的 NVA：

| 描述 | 路由表 | 靜態路由              |
| ----------- | ----------- | ------------------------- |
| 分支    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA 輪輻  | 預設     | 10.1.0.0/16-> vnet4conn  |

現在您可以使用虛擬 WAN 來選取要將封包傳送至的正確連接。 您也需要使用虛擬 WAN 來選取接收這些封包時要採取的正確動作。 您可以使用連接路由表來進行這個動作，如下所示：

| 描述 | Connection | 靜態路由            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0。5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0。5 |

如需詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a>架構

以下是本文稍早所述的架構圖表。

有一個中樞稱為「 **中樞 1**」。

* **中樞 1** 直接連線至 NVA vnet **VNet 4** 和 **vnet 5**。

* Vnet 1、2、3和分支之間的流量預期會透過 **VNet 4 NVA** 10.4.0.5。

* Vnet 1、2和3的所有網際網路系結流量預期會透過 **VNet 5 NVA** 10.5.0.5。

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="網路架構的圖表。":::

## <a name="workflow"></a>工作流程

若要透過 NVA 設定路由，以下是要考慮的步驟：

1. 若要透過 VNet 5 進行網際網路系結的流量，您需要 Vnet 1、2和3，才能透過虛擬網路對等互連直接連線至 VNet 5。 您也需要在虛擬網路中為 0.0.0.0/0 和下一個躍點10.5.0.5 設定使用者定義的路由。 虛擬 WAN 目前不允許虛擬中樞內的下一個躍點 NVA 為 0.0.0.0/0。

1. 在 Azure 入口網站中，移至您的虛擬中樞，並建立名為 **RT_Shared** 的自訂路由表。 此資料表會透過從所有虛擬網路和分支連線的傳播來學習路由。 您可以在下圖中看到此空白資料表。

   * **路由：** 您不需要新增任何靜態路由。

   * **關聯：** 選取 [Vnet 4] 和 [5]，這表示這些虛擬網路的連接會與路由表 **RT_Shared** 建立關聯。

   * **傳播：** 因為您想要讓所有分支和虛擬網路連線將其路由動態傳播到此路由表，請選取 [分支] 和 [所有虛擬網路]。

1. 建立稱為 **RT_V2B** 的自訂路由表，以將流量從 vnet 1、2和3導向至分支。

   * **路由：** 新增分支的匯總靜態路由專案，並以下一個躍點作為 VNet 4 連接。 在 VNet 4 的連接中設定分支首碼的靜態路由，並指出下一個躍點是 VNet 4 中 NVA 的特定 IP。

   * **關聯：** 全選 Vnet 1、2和3。 這表示 VNet 連線1、2和3會與此路由表建立關聯，而且能夠透過此路由表中的傳播) 來學習 (靜態和動態的路由。

   * **傳播：** 連接會將路由傳播至路由表。 選取 Vnet 1、2和3，可將 Vnet 1、2和3的路由傳播到此路由表。 不需要將路由從分支連線傳播至 **RT_V2B**，因為分支虛擬網路流量會透過 VNet 4 中的 NVA 進行。
  
1. 編輯預設路由表 **DefaultRouteTable**。

   所有的 VPN、Azure ExpressRoute 和使用者 VPN 連線都會與預設路由表建立關聯。 所有 VPN、ExpressRoute 和使用者 VPN 連接都會將路由傳播至同一組路由表。

   * **路由：** 新增 Vnet 1、2和3的匯總靜態路由專案，並以下一個躍點作為 VNet 4 連線。 針對 vnet 1、2和3匯總的首碼，在 VNet 4 的連線中設定靜態路由，並指出下一個躍點是 VNet 4 中 NVA 的特定 IP。

   * **關聯：** 確定已選取分支 (VPN/ER/P2S) 的選項，以確保內部部署分支連線與預設路由表建立關聯。

   * **傳播來源：** 確定已選取分支 (VPN/ER/P2S) 的選項，以確保內部部署連接會將路由傳播到預設路由表。

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="工作流程的圖表。" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。
