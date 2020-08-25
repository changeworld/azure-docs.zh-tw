---
title: 概念-網路互連能力
description: 瞭解 Azure VMware 解決方案中網路和互連能力的重要層面和使用案例。
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3420f6aa61ced7632175f3e12edda9de72639517
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750575"
---
# <a name="azure-vmware-solution-preview-networking-and-interconnectivity-concepts"></a>Azure VMware 解決方案預覽版網路和互連能力概念

Azure VMware Solution (AVS) 提供可供內部部署和 Azure 架構環境或資源的使用者和應用程式存取的 VMware 私用雲端環境。 Azure ExpressRoute 和 VPN 連線等服務會提供連線能力。 這些服務需要特定的網路位址範圍和防火牆埠來啟用服務。  

部署私用雲端時，會建立用於管理、布建和 vMotion 的私人網路。 它們可用來存取 vCenter 和 NSX-T 管理員以及虛擬機器 vMotion 或部署。 您可以從 Azure 中的 VNet 或從內部部署環境存取所有的私人網路。 ExpressRoute Global Reach 用來將私人雲端連線到內部部署環境，而此連線需要在您的訂用帳戶中具有 ExpressRoute 線路的 VNet 才能運作。

此外，在部署私用雲端時，會布建並提供網際網路和 Azure 服務的存取權，讓生產網路上的 Vm 可以取用它們。  預設會停用新私人雲端的網際網路存取，而且可以隨時啟用或停用。

互連能力的實用觀點是考慮這兩種類型的 AVS 私用雲端實施：

1. [**基本僅限 azure 的互連能力**](#azure-virtual-network-interconnectivity) 可讓您在 azure 中只使用單一虛擬網路來管理和使用您的私人雲端。 這項執行最適合不需要從內部部署環境存取的 AVS 評估或實施。

1. [**完整內部部署至私用雲端互連能力**](#on-premises-interconnectivity) 延伸了基本的僅限 Azure 的實作為，包括內部部署和 AVS 私用雲端之間的互連能力。
 
在本文中，我們將討論一些建立網路功能和互連能力的重要概念，包括需求和限制。 此外，我們也將討論這兩種 AVS 私用雲端互連能力實施類型的詳細資訊。 本文提供您需要知道的資訊，讓您設定網路以正確地使用 AVS。

## <a name="avs-private-cloud-use-cases"></a>AVS 私用雲端使用案例

AVS 私人雲端的使用案例包括：
- 雲端中的新 VMware VM 工作負載
- VM 工作負載高載至雲端 (內部部署到 AVS) 
- 將 VM 工作負載遷移至雲端 (內部部署到 AVS) 
- 嚴重損壞修復 (AVS 到 AVS 或內部部署到 AVS) 
- Azure 服務的使用量

> [!TIP]
> 所有適用于 AVS 服務的使用案例都會啟用內部部署至私人雲端連線。

## <a name="azure-virtual-network-interconnectivity"></a>Azure 虛擬網路互連能力

在虛擬網路對私用雲端的部署中，您可以管理您的 Azure VMware 解決方案私人雲端、取用私人雲端中的工作負載，以及透過 ExpressRoute 連線存取 Azure 服務。 

下圖顯示在私用雲端部署時建立的基本網路互連能力。 它會顯示 Azure 中的虛擬網路與私人雲端之間的邏輯 ExpressRoute 型網路。 互連能力滿足三個主要的使用案例：
* 可從 Azure 訂用帳戶中的 Vm 存取的 vCenter server 和 NSX-T 管理員的輸入存取，而不是從您的內部部署系統存取。 
* 從 Vm 到 Azure 服務的輸出存取。 
* 執行私用雲端之工作負載的輸入存取和耗用量。

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="基本虛擬網路對私人雲端的連線能力" border="false":::

## <a name="on-premises-interconnectivity"></a>內部部署互連能力

在虛擬網路和內部部署至完整私用雲端的內部部署中，您可以從內部部署環境存取 Azure VMware 解決方案私人雲端。 此實作為上一節所述之基本實作為的延伸。 如同基本的執行，需要 ExpressRoute 線路，但在此實行中，它是用來從內部部署環境連線到 Azure 中的私人雲端。 

下圖顯示內部部署至私用雲端互連能力，可啟用下列使用案例：
* 經常性/冷跨 vCenter vMotion
* 內部部署至 Azure VMware 解決方案私人雲端管理存取

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="虛擬網路和內部部署的完整私人雲端連線能力" border="false":::

若要完整互連能力至您的私人雲端，請啟用 ExpressRoute 全球存取範圍，然後要求授權金鑰和私用對等互連識別碼，以在 Azure 入口網站的全球存取範圍內。 授權金鑰和對等互連識別碼是用來在您的訂用帳戶中的 ExpressRoute 線路和新私人雲端的 ExpressRoute 線路之間建立全球存取範圍。 一旦連結之後，這兩個 ExpressRoute 線路會將內部部署環境之間的網路流量路由傳送至私人雲端。  請參閱教學課程，以針對要求和使用授權金鑰和對等互連識別碼的程式， [建立 ExpressRoute 全球存取範圍對等互連至私人雲端](tutorial-expressroute-global-reach-private-cloud.md) 。

## <a name="next-steps"></a>後續步驟 

- 深入瞭解網路連線 [能力的考慮和需求](tutorial-network-checklist.md)。 
- 瞭解 [私用雲端儲存體概念](concepts-storage.md)。


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

