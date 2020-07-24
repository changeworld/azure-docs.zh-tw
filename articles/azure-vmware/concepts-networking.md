---
title: 概念-網路互連能力
description: 瞭解 Azure VMware 解決方案（AVS）中網路和互連能力的重要層面和使用案例
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c0416da9c745ccf92970ff39f623a782d5784983
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062833"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware 解決方案（AVS）預覽網路和互連能力概念

您的 Azure VMware 解決方案（AVS）私人雲端與內部部署環境或 Azure 中的虛擬網路之間的網路互連能力，可讓您存取和使用您的私用雲端。 在本文中，我們將討論一些主要概念，以建立網路和互連能力的基礎。

在互連能力上有一個實用的觀點，就是考慮兩種類型的 AVS 私用雲端實施：

1. [**基本的僅限 azure 互連能力**](#azure-virtual-network-interconnectivity)可讓您在 azure 中僅使用單一虛擬網路來管理和使用私人雲端。 此執行最適合用於不需要從內部部署環境存取的 AVS 評估或實現。

1. [**完整的內部部署至私用雲端互連能力**](#on-premises-interconnectivity)擴充了基本的僅限 Azure 的實作為，以包含內部部署與 AVS 私人雲端之間的互連能力。
 
您可以在下列各節中，找到有關需求的詳細資訊，以及兩種類型的 AVS 私用雲端互連能力實施。

## <a name="avs-private-cloud-use-cases"></a>AVS 私用雲端使用案例

AVS 私用雲端的使用案例包括：
- 雲端中新的 VMware VM 工作負載
- VM 工作負載高載至雲端（僅限內部部署至 AVS）
- VM 工作負載遷移至雲端（僅限內部部署至 AVS）
- 嚴重損壞修復（avs 至 AVS 或內部部署至 AVS）
- Azure 服務的耗用量

 所有的 AVS 服務使用案例都已啟用內部部署至私人雲端連線能力。 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>虛擬網路和 ExpressRoute 線路需求
 
當您從訂用帳戶中的虛擬網路建立連線時，ExpressRoute 線路會透過對等互連建立，並使用您在 Azure 入口網站中要求的授權金鑰和對等互連識別碼。 對等互連是私人雲端與虛擬網路之間的私用一對一連線。

> [!NOTE] 
> ExpressRoute 線路不是私人雲端部署的一部分。 內部部署 ExpressRoute 線路已超出本檔的範圍。 如果您需要內部部署連線到您的私人雲端，您可以使用其中一個現有的 ExpressRoute 線路，或在 Azure 入口網站中購買一個。

部署私人雲端時，您會收到 vCenter 和 NSX-T Manager 的 IP 位址。 若要存取這些管理介面，您必須在訂用帳戶的虛擬網路中建立額外的資源。 您可以在教學課程中找到建立這些資源及建立 ExpressRoute 私用對等的程式。

私用雲端邏輯網路隨附預先布建的 NSX-T。 第0層閘道和第1層閘道會為您預先布建。 您可以建立區段，並將它附加至現有的第1層閘道，或將它附加至您定義的新第1層閘道。 NSX-T 邏輯網路元件提供工作負載之間的東部連線能力，同時也提供與網際網路和 Azure 服務的北南部連線能力。 

## <a name="routing-and-subnet-requirements"></a>路由和子網需求

路由是以邊界閘道協定（BGP）為基礎，預設會針對每個私人雲端部署自動布建並啟用。 針對 AVS 私人雲端，您必須規劃私用雲端網路位址空間，其中的子網至少須有/22 個前置長度 CIDR 網路位址區塊，如下表所示。 位址區塊不應與您的訂用帳戶和內部部署網路中的其他虛擬網路所使用的位址區塊重迭。 在此位址區塊內，系統會自動布建管理、布建和 vMotion 網路。

範例 `/22` CIDR 網路位址區塊：`10.10.0.0/22`

子網路：

| 網路使用量             | 子網路 | 範例        |
| ------------------------- | ------ | -------------- |
| 私人雲端管理  | `/24`  | `10.10.0.0/24` |
| vMotion 網路           | `/24`  | `10.10.1.0/24` |
| VM 工作負載              | `/24`  | `10.10.2.0/24` |
| ExpressRoute 對等互連      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Azure 虛擬網路互連能力

在虛擬網路到私用雲端的執行中，您可以管理您的 AVS 私人雲端、取用私人雲端中的工作負載，以及透過 ExpressRoute 連線存取 Azure 服務。 

下圖顯示在私用雲端部署時建立的基本網路互連能力。 它會顯示 Azure 中的虛擬網路與私人雲端之間的邏輯 ExpressRoute 型網路。 互連能力滿足三個主要使用案例：
* 可從 Azure 訂用帳戶中的 Vm，而不是從內部部署系統存取的 vCenter server 和 NSX-T 管理員的輸入存取權。 
* 從 Vm 到 Azure 服務的輸出存取。 
* 執行私人雲端之工作負載的輸入存取和耗用量。

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="基本虛擬網路對私人雲端的連線能力" border="false":::

## <a name="on-premises-interconnectivity"></a>內部部署互連能力

在虛擬網路和內部部署至完整私用雲端的部署中，您可以從內部部署環境存取您的 AVS 私用雲端。 此實作為上一節所述的基本實作為擴充。 就像基本的執行方式一樣，ExpressRoute 線路是必要的，但在此實行中，它是用來從內部部署環境連接到 Azure 中的私人雲端。 

下圖顯示 [內部部署至私人雲端] 互連能力，可啟用下列使用案例：
* 熱/非經常性存取跨 vCenter vMotion
* 內部部署至 AVS 私用雲端管理存取

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="虛擬網路和內部部署的完整私人雲端連線能力" border="false":::

如需完整互連能力至您的私人雲端，請啟用 ExpressRoute 全球觸達，然後要求授權金鑰和私用對等互連識別碼，以便在 Azure 入口網站中達到全球範圍。 授權金鑰和對等互連識別碼是用來在訂用帳戶中的 ExpressRoute 線路和新私人雲端的 ExpressRoute 線路之間建立全球範圍。 連結之後，這兩個 ExpressRoute 線路會將內部部署環境之間的網路流量路由傳送到您的私人雲端。  如需如何要求和使用授權金鑰和對等互連識別碼的程式，請參閱[建立 ExpressRoute 全球對等互連至私人雲端的教學](tutorial-expressroute-global-reach-private-cloud.md)課程。


## <a name="next-steps"></a>接下來的步驟 

下一步是瞭解私用[雲端儲存體的概念](concepts-storage.md)。

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
