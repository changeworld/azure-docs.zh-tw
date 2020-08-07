---
title: 案例：使用自訂設定透過 Nva 路由傳送流量
titleSuffix: Azure Virtual WAN
description: 此案例可協助您使用不同的 NVA，針對網際網路系結流量，透過 Nva 來路由傳送流量。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 5546fc63b01d1da6b4033e071ac071574ab9699a
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987187"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>案例：透過 Nva 路由傳送流量-自訂 (預覽) 

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此 NVA (網路虛擬裝置) 案例中，目標是透過 NVA 路由傳送 Vnet 與分支之間通訊的流量，並針對網際網路系結流量使用不同的 NVA。 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>設計

在此案例中，我們將使用命名慣例：

* [**圖 1** ] 中使用者已部署 NVA (VNet 4 的虛擬網路「服務 VNet」) 檢查非網際網路流量。
* 適用于虛擬網路的「DMZ VNet」，其中使用者已部署 NVA 以用來檢查 [**圖 1** ]) 中 (VNet 5 的網際網路系結流量。
* [**圖 1** ] 中 (vnet 1、vnet 2 和 vnet 3 連線到 NVA VNet 的虛擬網路「NVA 輪輻」) 。
* 適用于 Microsoft 管理的虛擬 WAN 中樞的「中樞」。

下列連接矩陣摘要說明此案例中支援的流程：

**連接矩陣**

| 寄件者          | 變更為：|*NVA 輪輻*|*服務 VNet*|*DMZ VNet*|*分支靜態*|
|---|---|---|---|---|---|
| **NVA 輪輻**| &#8594;|      X |            X |   對等互連 |    靜態    |
| **服務 VNet**| &#8594;|    X |            X |      X    |      X       |
| **DMZ VNet** | &#8594;|       X |            X |      X    |      X       |
| **分支** | &#8594;|  靜態 |            X |      X    |      X       |

連線矩陣中的每個資料格都會描述虛擬 WAN 連線是否 (流程的「來源」端，資料列標頭) 學習目的地前置詞 (流程的「到」端，特定流量的斜體) 中的資料行標頭。 讓我們深入探討不同的資料列：

* NVA 輪輻：
  * 輪輻會直接透過虛擬 WAN 集線器觸及其他輪輻。
  * 輪輻會透過指向服務 VNet 的靜態路由，取得分支的連接。 它們不應該從分支中學習特定的前置詞 (否則會更明確，並覆寫摘要) 。
  * 輪輻會透過直接 VNet 對等互連，將網際網路流量傳送至 DMZ VNet。
* 網點
  * 分支會透過指向服務 VNet 的靜態路由，進入輪輻。 它們不應該從覆寫摘要靜態路由的 Vnet 中學習特定的首碼。
* 服務 VNet 類似于需要可從每個 VNet 和每個分支連線的共用服務 VNet。
* DMZ VNet 不需要透過虛擬 WAN 進行連線，因為它所支援的唯一流量將會透過直接 VNet 對等互連。 不過，我們將使用與 DMZ VNet 相同的連線模型來簡化設定。

因此，我們的連線矩陣會提供三種不同的連線模式，這會轉譯成三個路由表。 與不同 Vnet 的關聯將如下所示：

* NVA 輪輻：
  * 相關聯的路由表： **RT_V2B**
  * 傳播至路由表： **RT_V2B**和**RT_SHARED**
* NVA Vnet (內部和網際網路) ：
  * 相關聯的路由表： **RT_SHARED**
  * 傳播至路由表： **RT_SHARED**
* 網點
  * 關聯的路由表：**預設值**
  * 傳播至路由表： **RT_SHARED**和**預設值**

我們需要這些靜態路由，以確保 VNet 對分支和分支對 VNet 流量會通過服務 VNet (VNet 4) 中的 NVA：

| 描述 | 路由表 | 靜態路由              |
| ----------- | ----------- | ------------------------- |
| 分支    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA 輪輻  | 預設值     | 10.1.0.0/16-> vnet4conn  |

現在虛擬 WAN 知道要將封包傳送到哪個連線，但連線需要知道接收這些封包時該怎麼做：這是使用連接路由表的位置。

| 描述 | Connection | 靜態路由            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0。5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0。5 |

此時，所有專案應該都已就緒。

如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a><a name="architecture"></a>架構

[**圖 1**] 中有一個中樞 hub **1**。

* **中樞 1**直接連線到 NVA vnet **VNet 4**和**vnet 5**。

* Vnet 1、2和3之間的流量，以及 (VPN/ER/P2S) 的分支，應透過**VNet 4 NVA** 10.4.0.5。

* 來自 Vnet 1、2和3的所有網際網路系結流量，都預期會透過**VNet 5 NVA** 10.5.0.5。

**圖1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="圖1":::

## <a name="workflow"></a><a name="workflow"></a>工作流程

若要設定透過 NVA 的路由，以下是要考慮的步驟：

1. 為了讓網際網路系結流量透過 VNet 5 進行，您需要 Vnet 1、2和3，才能透過 VNet 對等互連直接連線至 VNet 5。 您也需要在 Vnet 中設定 0.0.0.0/0 和 next 躍點10.5.0.5 的 UDR。 目前，虛擬 WAN 不允許虛擬中樞的下一個躍點 NVA 為 0.0.0.0/0。

1. 在 [Azure 入口網站中，流覽至您的虛擬中樞並建立自訂路由表， **RT_Shared**可透過從所有 Vnet 和分支連線的傳播學習路由。 在 [**圖 2**] 中，這會以空白的自訂路由表**RT_Shared**來描述。

   * **路由：** 您不需要新增任何靜態路由。

   * **關聯：** 選取 [Vnet 4] 和 [5]，這表示 Vnet 4 和5連接會與路由表**RT_Shared**建立關聯。

   * **傳播：** 因為您想讓所有分支和 VNet 連線動態地將其路由傳播到此路由表，所以請選取 [分支和所有 Vnet]。

1. 建立自訂路由表**RT_V2B** ，將流量從 vnet 1、2和3導向至分支。

   * **路由：** 在 [**圖) 2** ] 中新增分支的匯總靜態路由專案 (VPN/ER/P2S)  (10.2.0.0/16，並以下一個躍點作為 VNet 4 連線。 您也需要在 VNet 4 的連接中設定分支首碼的靜態路由，並將下一個躍點指定為 VNet 4 中 NVA 的特定 IP。

   * **關聯：** 選取 [所有 Vnet 1、2和 3]。 這表示 VNet 連線1、2和3將與此路由表建立關聯，而且能夠透過此路由表中的傳播) 學習 (靜態和動態的路由。

   * **傳播：** 連接會將路由傳播至路由表。 選取 Vnet 1、2和3，可將路由從 Vnet 1、2和3傳播到此路由表。 不需要將路由從分支連線傳播到 RT_V2B，因為分支 VNet 流量會透過 VNet 4 中的 NVA。
  
1. 編輯預設路由表**DefaultRouteTable**。

   所有 VPN、ExpressRoute 和使用者 VPN 連線都會與預設路由表建立關聯。 所有 VPN、ExpressRoute 和使用者 VPN 連線都會將路由傳播到相同的一組路由表。

   * **路由：** 在 [**圖) 2** ] 中新增 vnet 1、2和 3 (10.1.0.0/16 的匯總靜態路由專案，並將下一個躍點做為 VNet 4 連線。 您也需要在 vnet 4 的連線中為 VNet 1、2和3個匯總的首碼設定靜態路由，並將下一個躍點指定為 VNet 4 中 NVA 的特定 IP。

   * **關聯：** 請確定已選取 [ (VPN/ER/P2S) 的分支] 選項，確保內部部署分支連接與*defaultroutetable*相關聯。

   * **傳播來源：** 請確定已選取 [ (VPN/ER/P2S) 的分支] 選項，確保內部部署連接會將路由傳播至*defaultroutetable*。

**圖2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="圖2" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
