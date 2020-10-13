---
title: 案例：使用自訂設定透過 Nva 路由傳送流量
titleSuffix: Azure Virtual WAN
description: 此案例可協助您透過 Nva 使用不同的 NVA 來路由傳送流量到網際網路系結的流量。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e1cf9faeab60264d491539256828151e496ade8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267494"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>案例：透過 Nva 路由傳送流量-自訂 (預覽) 

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此 NVA (網路虛擬裝置) 案例中，其目標是透過 NVA 路由傳送流量，以進行 Vnet 和分支之間的通訊，並針對網際網路系結流量使用不同的 NVA。 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>設計

在此案例中，我們將使用命名慣例：

* 「服務 VNet」適用于使用者已在 [ **圖 1** ] 中部署 NVA (VNet 4) 檢查非網際網路流量的虛擬網路。
* 「DMZ VNet」適用于已部署 NVA 的虛擬網路，以用來檢查網際網路系結流量 (「 **圖 1** 」中的 VNet 5) 。
* 「NVA 輪輻」適用于連線至 NVA VNet 的虛擬網路 (VNet 1、VNet 2 和「 **圖 1** 」中的 vnet 3) 。
* 適用于 Microsoft 管理的虛擬 WAN 中樞的「中樞」。

下列連接對照表摘要說明此案例中所支援的流程：

**連接矩陣**

| 來自          | 變更為：|*NVA 輪輻*|*服務 VNet*|*DMZ VNet*|*分支靜態*|
|---|---|---|---|---|---|
| **NVA 輪輻**| &#8594;|      X |            X |   對等互連 |    Static    |
| **服務 VNet**| &#8594;|    X |            X |      X    |      X       |
| **DMZ VNet** | &#8594;|       X |            X |      X    |      X       |
| **分支** | &#8594;|  Static |            X |      X    |      X       |

連線矩陣中的每個資料格都會描述虛擬 WAN 連線是否 (流程的「來源」端、資料列標頭) 學習目的地首碼 (流程的「到」端，以及在特定流量的斜體) 中的資料行標頭。 「X」表示連線是由虛擬 WAN 原生提供的，而「靜態」表示連線是由虛擬 WAN 使用靜態路由所提供。 讓我們詳細探討不同的資料列：

* NVA 輪輻：
  * 輪輻可直接透過虛擬 WAN 中樞觸及其他輪輻。
  * 輪輻可透過指向服務 VNet 的靜態路由，來連接到分支。 它們不應該從分支學習特定的前置詞 (否則這些首碼會更具體，並覆寫摘要) 。
  * 輪輻會透過直接的 VNet 對等互連，將網際網路流量傳送至 DMZ VNet。
* 分支：
  * 分支會經由指向服務 VNet 的靜態路由進入輪輻。 這些應用程式不應該從覆寫摘要靜態路由的 Vnet 學習特定的首碼。
* 服務 VNet 將類似于需要從每個 VNet 和每個分支連線的共用服務 VNet。
* DMZ VNet 實際上不需要透過虛擬 WAN 進行連線，因為它所支援的唯一流量將會透過直接 VNet 對等互連。 不過，我們將使用與 DMZ VNet 相同的連線模型來簡化設定。

因此，我們的連線矩陣提供三種不同的連接模式，可轉譯為三個路由表。 不同 Vnet 的關聯將如下所示：

* NVA 輪輻：
  * 相關聯的路由表： **RT_V2B**
  * 傳播至路由表： **RT_V2B** 和 **RT_SHARED**
* NVA Vnet (內部和網際網路) ：
  * 相關聯的路由表： **RT_SHARED**
  * 傳播至路由表： **RT_SHARED**
* 分支：
  * 相關聯的路由表： **預設值**
  * 傳播至路由表： **RT_SHARED** 和 **預設值**

我們需要這些靜態路由，以確保 VNet 對分支和分支對 VNet 流量會通過服務 VNet (VNet 4) 中的 NVA：

| 描述 | 路由表 | 靜態路由              |
| ----------- | ----------- | ------------------------- |
| 分支    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA 輪輻  | 預設     | 10.1.0.0/16-> vnet4conn  |

現在虛擬 WAN 知道要將封包傳送至哪個連線，但連線必須知道接收這些封包時該怎麼辦：這是使用連接路由表的位置。

| 描述 | Connection | 靜態路由            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0。5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0。5 |

到目前為止，一切都應該就緒。

如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a><a name="architecture"></a>架構

**圖 1**中有一個中樞，也就是**中樞 1**。

* **中樞 1** 直接連線至 NVA vnet **VNet 4** 和 **vnet 5**。

* Vnet 1、2、3和分支之間的流量 (VPN/ER/P2S) 預期會透過 **VNet 4 NVA** 10.4.0.5。

* Vnet 1、2和3的所有網際網路系結流量預期會透過 **VNet 5 NVA** 10.5.0.5。

**圖1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="圖1":::

## <a name="workflow"></a><a name="workflow"></a>工作流程

若要透過 NVA 設定路由，以下是要考慮的步驟：

1. 為了讓網際網路系結流量通過 VNet 5，您需要 Vnet 1、2和3，才能透過 VNet 對等互連直接連線至 VNet 5。 您也需要在 Vnet 中設定 0.0.0.0/0 和下一個躍點10.5.0.5 的 UDR。 虛擬 WAN 目前不允許在虛擬中樞內 NVA 0.0.0.0/0 的下一個躍點。

1. 在 Azure 入口網站中，流覽至您的虛擬中樞，並建立自訂路由表 **RT_Shared** ，以透過來自所有 Vnet 和分支連線的傳播來學習路線。 在 [ **圖 2**] 中，這會描述為空白的自訂路由表 **RT_Shared**。

   * **路由：** 您不需要新增任何靜態路由。

   * **關聯：** 選取 [Vnet 4] 和 [5]，這表示 Vnet 4 和5連接會與路由表 **RT_Shared**建立關聯。

   * **傳播：** 因為您想要讓所有分支和 VNet 連線動態地將其路由傳播到此路由表，請選取 [分支] 和 [所有 Vnet]。

1. 建立自訂路由表 **RT_V2B** ，以將流量從 vnet 1、2和3導向至分支。

   * **路由：** 在 [ **圖) 2** ] 中新增分支的匯總靜態路由專案 (VPN/ER/P2S)  (10.2.0.0/16，並以下一個躍點作為 VNet 4 連線。 您也需要在 VNet 4 的連接中設定分支首碼的靜態路由，並指出下一個躍點是 VNet 4 中 NVA 的特定 IP。

   * **關聯：** 全選 Vnet 1、2和3。 這表示 VNet 連線1、2和3會與此路由表建立關聯，而且能夠透過此路由表中的傳播) 來學習 (靜態和動態的路由。

   * **傳播：** 連接會將路由傳播至路由表。 選取 Vnet 1、2和3可將路由從 Vnet 1、2和3傳播到此路由表。 不需要將路由從分支連線傳播至 RT_V2B，因為分支 VNet 流量會透過 VNet 4 中的 NVA 進行。
  
1. 編輯預設路由表 **DefaultRouteTable**。

   所有的 VPN、ExpressRoute 和使用者 VPN 連接都會與預設路由表建立關聯。 所有 VPN、ExpressRoute 和使用者 VPN 連接都會將路由傳播至同一組路由表。

   * **路由：** 在 **圖 2** 中新增 vnet 1、2和 3 (10.1.0.0/16 的匯總靜態路由專案，) 下一個躍點作為 VNet 4 連線。 您也需要針對 vnet 1、2和3匯總的首碼，在 VNet 4 的連線中設定靜態路由，並指出下一個躍點是 VNet 4 中 NVA 的特定 IP。

   * **關聯：** 確定已選取分支 (VPN/ER/P2S) 的選項，以確保內部部署分支連線與 *defaultroutetable*相關聯。

   * **傳播來源：** 確定已選取分支 (VPN/ER/P2S) 的選項，以確保內部部署連接會將路由傳播至 *defaultroutetable*。

**圖2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="圖1" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。
