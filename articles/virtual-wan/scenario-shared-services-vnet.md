---
title: 案例：路由至共用服務 Vnet
titleSuffix: Azure Virtual WAN
description: 路由設定路由的案例，可使用您想要讓每個 VNet 和分支存取的工作負載來存取共用服務 VNet。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: c72ff14246f3f96980fe1de2fdef16794bf23e4e
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399985"
---
# <a name="scenario-route-to-shared-services-vnets"></a>案例：路由至共用服務 Vnet

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此案例中，目標是要設定路由以存取 **共用服務** VNet，以及您想要讓每個 VNet 和分支 (VPN/ER/P2S) 存取的工作負載。 這些共用工作負載的範例可能包括具有網域控制站或檔案共用等服務的虛擬機器，或透過 [Azure 私人端點](../private-link/private-endpoint-overview.md)在內部公開的 Azure 服務。

如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>設計

我們可以使用連接矩陣來摘要說明此案例的需求。 在矩陣中，每個資料格都會描述虛擬 WAN 連線是否 (流程的「來源」端、資料表中的資料列標頭) 學習目的地前置詞 (流程的「到」端，而資料表中的資料行標頭則是針對特定流量進行) 。 「X」表示虛擬 WAN 會提供連線能力：

**連接矩陣**

| 寄件者             | 變更為：   |*隔離 Vnet*|*共用的 VNet*|*分支*|
|---|---|---|---|---|
|**隔離 Vnet**|&#8594;|                |        X        |       X      |
|**共用 Vnet**  |&#8594;|       X        |        X        |       X      |
|**分支**      |&#8594;|       X        |        X        |       X      |

類似于 [隔離式 VNet 案例](scenario-isolate-vnets.md)，此連接矩陣提供兩種不同的資料列模式，可轉譯為兩個路由表 (共用服務 vnet，而分支的連線需求) 相同。 虛擬 WAN 已經有預設路由表，因此我們將需要另一個自訂路由表，我們會在此範例中呼叫 **RT_SHARED** 。

Vnet 會與 **RT_SHARED** 路由表建立關聯。 因為它們需要與分支的連線以及共用的服務 Vnet，所以共用服務 VNet 和分支必須傳播至 **RT_SHARED** (否則，vnet 將不會瞭解) 的分支和共用的 VNet 首碼。 由於分支一律會與預設路由表相關聯，而且共用服務 Vnet 的連線需求相同，因此我們也會將共用服務 Vnet 關聯至預設路由表。

因此，這是最後的設計：

* 隔離的虛擬網路：
  * 相關聯的路由表： **RT_SHARED**
  * 傳播至路由表： **預設**
* 共用服務虛擬網路：
  * 相關聯的路由表： **預設值**
  * 傳播至路由表： **RT_SHARED** 和 **預設值**
* 分支：
  * 相關聯的路由表： **預設值**
  * 傳播至路由表： **RT_SHARED** 和 **預設值**

> [!NOTE]
> 如果您的虛擬 WAN 部署于多個區域，您將需要在每個中樞內建立 **RT_SHARED** 路由表，而且每個共用服務 VNet 和分支連線的路由都必須使用傳播標籤傳播到每個虛擬中樞內的路由表。

如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流程

若要設定案例，請考慮下列步驟：

1. 識別 **共用服務** VNet。
2. 建立自訂路由表。 在此範例中，我們會將路由表稱為 **RT_SHARED**。 如需建立路由表的步驟，請參閱 [如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。 使用下列值作為指導方針：

   * **協會**
     * 針對**vnet （共用服務 VNet*除外***），請選取要隔離的 vnet。 這表示除了共用服務 VNet) 之外，所有這些 Vnet (都將能夠根據 RT_SHARED 路由表的路由來達到目的地。

   * **傳播**
      * 針對 **分支**，除了您可能已選取的任何其他路由表之外，還會將路由傳播至此路由表。 由於這個步驟，RT_SHARED 路由表會從所有分支連線學習路由， (VPN/ER/使用者 VPN) 。
      * 針對 **vnet**，請選取 **共用服務 VNet**。 基於這個步驟，RT_SHARED 路由表會從共用服務 VNet 連線學習路由。

這會導致下圖所示的路由設定：

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="共用服務 VNet" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。
