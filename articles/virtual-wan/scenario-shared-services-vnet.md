---
title: 案例：路由至共用服務 Vnet
titleSuffix: Azure Virtual WAN
description: 路由的案例-設定路由以存取共用服務 VNet，其中包含您想要讓每個 VNet 和分支存取的工作負載。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6045c491ea68d759b2a1739e20aa2f12b8520c87
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88006480"
---
# <a name="scenario-route-to-shared-services-vnets"></a>案例：路由至共用服務 Vnet

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此案例中，目標是要設定路由，以使用您想要讓每個 VNet 和分支 (VPN/ER/P2S) 存取的工作負載來存取**共用服務**VNet。 這些共用工作負載的範例可能包括網域控制站或檔案共用等服務的虛擬機器，或透過[Azure 私人端點](../private-link/private-endpoint-overview.md)內部公開的 azure 服務。

如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>設計

我們可以使用連線矩陣來摘要說明此案例的需求。 在矩陣中，每個資料格都會描述虛擬 WAN 連線是否 (流程的「來源」端，資料表中的資料列標頭) 學習目的地前置詞 (流程的「到」端，而資料表中的資料行標頭則) 特定的流量流程。

**連接矩陣**

| 寄件者             | 變更為：   |*隔離 Vnet*|*共用的 VNet*|*分支*|
|---|---|---|---|---|
|**隔離 Vnet**|&#8594;|                |        X        |       X      |
|**共用 Vnet**  |&#8594;|       X        |        X        |       X      |
|**分支**      |&#8594;|       X        |        X        |       X      |

類似于[隔離式 VNet 案例](scenario-isolate-vnets.md)，此連線矩陣會提供兩個不同的資料列模式，這會轉譯成兩個路由表 (共用服務 vnet，而分支的連線需求也) 相同。 虛擬 WAN 已經有預設路由表，因此我們將需要另一個自訂路由表，我們將在此範例中呼叫**RT_SHARED** 。

Vnet 將會與**RT_SHARED**的路由表相關聯。 因為它們需要連線到分支和共用服務 Vnet，所以共用服務 VNet 和分支必須傳播至**RT_SHARED** (否則，vnet 將不會學習) 的分支和共用 VNet 首碼。 因為這些分支一律會與預設路由表相關聯，且連接需求與共享服務 Vnet 相同，所以我們也會將共用服務 Vnet 與預設路由表產生關聯。

因此，這是最後的設計：

* 隔離的虛擬網路：
  * 相關聯的路由表： **RT_SHARED**
  * 傳播至路由表：**預設值**
* 共用服務虛擬網路：
  * 關聯的路由表：**預設值**
  * 傳播至路由表： **RT_SHARED**和**預設值**
* 網點
  * 關聯的路由表：**預設值**
  * 傳播至路由表： **RT_SHARED**和**預設值**

> [!NOTE]
> 如果您的虛擬 WAN 部署在多個區域，您必須在每個中樞建立**RT_SHARED**路由表，而且每個共用服務 VNet 和分支連線的路由都必須使用傳播標籤，傳播到每個虛擬中樞的路由表。

如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流程

若要設定此案例，請考慮下列步驟：

1. 識別**共用服務**VNet。
2. 建立自訂路由表。 在此範例中，我們將路由表參考為**RT_SHARED**。 如需建立路由表的步驟，請參閱[如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。 使用下列值做為指導方針：

   * **技術協會**
     * 對於**共用服務 VNet*以外*的 vnet**，請選取要隔離的 vnet。 這表示所有這些 Vnet (除了共用服務 VNet) 將能夠根據 RT_SHARED 路由表的路由連線到目的地。

   * **傳播**
      * 對於**分支**，除了您可能已經選取的任何其他路由表之外，還會將路由傳播到此路由表。 基於此步驟，RT_SHARED 路由表將會從 (VPN/ER/使用者 VPN) 的所有分支連線學習路由。
      * 針對 [ **vnet**]，選取 [**共用服務] VNet**。 基於此步驟，RT_SHARED 路由表將會學習來自共用服務 VNet 連線的路由。

這會導致路由設定如下圖所示：

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="共用服務 VNet" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
