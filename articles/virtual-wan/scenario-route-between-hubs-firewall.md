---
title: 案例： Azure 防火牆-Interhub 路由
titleSuffix: Azure Virtual WAN
description: 在具有 Azure 防火牆的多個中樞之間路由傳送的案例
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567283"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>案例： Azure 防火牆-Interhub （預覽）

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此案例中，目標是在包含 Azure 防火牆的多個中樞之間路由傳送。 如需虛擬中樞路由的相關資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="scenario-architecture"></a><a name="architecture"></a>案例架構

在此案例中，我們假設下列設定：

* 您在每個中樞都有多個具有 Azure 防火牆的中樞。
* 您使用的是安全的虛擬中樞。
* 已設定私人流量（VNet）、網際網路和分支流量的適當原則。
* VNet 對網際網路（V2I）、VNet 對分支（V2B）、分支對 VNet （B2V），全都會通過每個中樞的 Azure 防火牆。

其他考量：

* 針對具有 Azure 防火牆的中樞間案例，假設輪輻 Vnet 不會與個別的路由表產生關聯。 （例如與**路由表 A**相關聯的**vnet 1** ，以及與**路由表 B**相關聯的**vnet 2** ）。 所有 Vnet 都會與 Azure 防火牆的路由所在的相同路由表產生關聯。
* 透過 Azure 防火牆保護目前僅限於**分支 <-> VNet**和**網際網路**流量。 目前不支援透過 Azure 防火牆進行分支對分支流程。

**圖1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="結構":::

## <a name="scenario-workflow"></a><a name="workflow"></a>案例工作流程

若要設定此案例，請採取下列步驟來考慮：

### <a name="step-1"></a><a name="step-1"></a>步驟 1

假設您已透過 Azure 防火牆管理員保護連線，第一個步驟是確保所有分支和 VNet**連線都不會傳播。** 這是為了確保透過 Azure 防火牆設定流量的必要。

1. 選取中樞並編輯 [**無**] 路由表。
1. 更新**傳播**以選取所有分支和所有 vnet。

### <a name="step-2"></a><a name="step-2"></a>步驟2

為每個中樞設定自訂路由表。

1. 針對**中樞 1**，請建立路由表**RT_Hub1**。

    * 如果您使用 Azure 防火牆管理員，它會在中樞的預設路由表中，使用下一個躍點**AZFW1** ，自動建立 0.0.0.0/0 的路由。 我們會在步驟3中修改這項設定。 針對**RT_Hub1**，請使用下一個躍點**AZFW1**來明確建立 0.0.0.0/0 的專案。 此設定會透過 AZFW1 啟動 V2V、B2V 和 V2I。
    * 因為您想要透過來自**中樞 1**的**AZFW2** （而不是透過 AZFW1）來連線**中樞 2**分支和 Vnet，所以您需要為**中樞 2**分支（10.5.0.0/16->AZFW2）和 vnet （10.2.0.0/16->AZFW2）新增另一個2個匯總路由。

1. 針對**中樞 2**，請建立路由表**RT_Hub2**。

    * 如果您使用 Azure 防火牆管理員，它會在中樞的預設路由表中，使用下一個躍點**AZFW2** ，自動建立 0.0.0.0/0 的路由。 我們會在步驟3中修改這項設定。 針對**RT_Hub2**，請使用下一個躍點**AZFW2**來明確建立 0.0.0.0/0 的專案。 此設定會透過**AZFW2**啟動 V2V、B2V 和 V2I。
    * 由於您想要由中樞2的**AZFW2**來保護內部中樞流量，因此您不需要明確地新增與先前的中樞1步驟中的第二個專案類似的步驟。

### <a name="step-3"></a><a name="step-3"></a>步驟3

修改每個中樞的**預設路由表**，以將分支的靜態路由新增**至 VNet** （B2V），並透過 Azure 防火牆將分支**至網際網路**（B2I）流程。 目前不支援透過 Azure 防火牆進行分支到分支。

1. 針對**中樞1預設路由表**：

    * 透過**AZFW2 分支至中樞2分支**： 10.5.0.0/16->AZFW2。 此設定會設定中樞2防火牆的路由。
    * 透過**AZFW2 將分支至中樞 2 vnet**： 10.2.0.0/16->AZFW2。 此設定會設定中樞2防火牆的路由。
    * **分支至分支（本機）/分支對 VNet （本機）/分支至網際網路**： 0.0.0.0/0->AZFW1。

2. 針對 [**中樞 2] 預設路由表**：

    * 分支至分支（本機和遠端）/分支至 VNet （本機和遠端）/分支到網際網路： 0.0.0.0/0->AZFW2。

這會導致路由設定變更，如下圖所示

**圖2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="workflow":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
* 如需有關如何設定虛擬中樞路由的詳細資訊，請參閱[如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。
