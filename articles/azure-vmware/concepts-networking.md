---
title: 概念-網路互連能力
description: 瞭解 Azure VMware 解決方案（AVS）中網路和互連能力的重要層面和使用案例
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 35d886fe0f6a68e522d7f2cf20b450b5d9afc199
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629217"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware 解決方案（AVS）預覽網路和互連能力概念

您的 Azure VMware 解決方案（AVS）私人雲端與內部部署環境或 Azure 中的虛擬網路之間的網路互連能力，可讓您存取和使用您的私用雲端。 本文將說明一些建立互連能力基礎的重要網路和互連能力概念。

在互連能力上有一個實用的觀點，就是考慮兩種類型的 AVS 私用雲端實作為，其中包含基本僅限 Azure 的互連能力和採用完整內部部署至私用雲端互連能力的實作為。

AVS 私用雲端的使用案例包括：
- 雲端中新的 VMware VM 工作負載
- VM 工作負載高載至雲端
- VM 工作負載遷移至雲端
- 嚴重損壞修復
- Azure 服務的耗用量

 所有的 AVS 服務使用案例都已啟用內部部署至私人雲端連線能力。 基本互連能力模型最適用于不需要從內部部署環境存取的 AVS 評估或實現。

下列各節將說明這兩種類型的 AVS 私用雲端互連能力。  最基本的互連能力是「Azure 虛擬網路連線能力」;它可讓您在 Azure 中只管理和使用單一虛擬網路的私人雲端。 「內部部署連線能力」中所述的互連能力會將虛擬網路連線擴充至，同時包含內部部署環境與 AVS 私人雲端之間的互連能力。

## <a name="azure-virtual-network-interconnectivity"></a>Azure 虛擬網路互連能力

下圖顯示在私用雲端部署時建立的基本網路互連能力。 它會顯示 Azure 中的虛擬網路與私人雲端之間的邏輯 ExpressRoute 型網路。 互連能力滿足三個主要使用案例：
- 針對 vCenter server 和 NSX-T manager 所在的管理網路進行輸入存取。
    - 可從 Azure 訂用帳戶內的 Vm 存取，而不是從您的內部部署系統。
- 從 Vm 到 Azure 服務的輸出存取。
- 執行私人雲端之工作負載的輸入存取和耗用量。

![基本虛擬網路到私人雲端連線能力](./media/concepts/adjacency-overview-drawing-single.png)

當您從您訂用帳戶中的虛擬網路建立連線到私人雲端的 ExpressRoute 線路時，會建立此虛擬網路到私人雲端案例的 ExpressRoute 線路。 對等互連會使用授權金鑰，以及您在 Azure 入口網站中要求的線路識別碼。 透過對等互連建立的 ExpressRoute 連線是私人雲端與虛擬網路之間的私用一對一連線。 您可以管理私用雲端、取用私人雲端中的工作負載，以及透過該 ExpressRoute 連線來存取 Azure 服務。

當您部署 AVS 私人雲端時，需要單一/22 私人網路位址空間。 此位址空間不應與您的訂用帳戶中的其他虛擬網路所使用的位址空間重迭。 在此位址空間內，系統會自動布建管理、布建和 vMotion 網路。 路由是以 BGP 為基礎，而且預設會針對每個私人雲端部署自動布建並啟用。

部署私人雲端時，系統會提供 vCenter 和 NSX-T 管理員的 IP 位址給您。 若要存取這些管理介面，您會在訂用帳戶的虛擬網路中建立其他資源。 教學課程中提供建立這些資源的程式，以及建立 ExpressRoute 私用對等。

您可以設計私用雲端邏輯網路，並使用 NSX-T 加以執行。 私用雲端隨附預先布建的 NSX-T。 第0層閘道 & 會為您預先布建第1層閘道。 您可以建立區段，並將它附加至現有的第1層閘道，或附加至您可以定義的新第1層閘道。 NSX-T 邏輯網路元件提供工作負載之間的東部連線能力，同時也提供與網際網路和 Azure 服務的北南部連線能力。 

## <a name="on-premises-interconnectivity"></a>內部部署互連能力

您也可以將內部部署環境連接到您的 AVS 私人雲端。 這種類型的互連能力是上一節所述基本互連能力的延伸。

![虛擬網路和內部部署的完整私人雲端連線能力](./media/concepts/adjacency-overview-drawing-double.png)

若要建立私人雲端的完整互連能力，您可以使用 Azure 入口網站，在私用雲端 ExpressRoute 線路與內部部署 ExpressRoute 線路之間啟用 ExpressRoute 全球範圍。 此設定會擴充基本連線能力，以包含從內部部署環境存取私人雲端的功能。

需要內部部署至 Azure 虛擬網路的 ExpressRoute 線路，才能從內部部署環境連線到 Azure 中的私人雲端。 此 ExpressRoute 線路位於您的訂用帳戶中，而不是私人雲端部署的一部分。 內部部署 ExpressRoute 線路已超出本檔的範圍。 如果您需要內部部署連線到您的私人雲端，您可以使用其中一個現有的 ExpressRoute 線路，或在 Azure 入口網站中購買一個。

一旦與全球接觸，這兩個 ExpressRoute 線路就會在您的內部部署環境與私人雲端之間路由傳送網路流量。 [內部部署至私人雲端] 互連能力會顯示在上圖中。 圖表中所表示的互連能力會啟用下列使用案例：

- 熱/非經常性存取跨 vCenter vMotion
- 內部部署至 AVS 私用雲端管理存取

若要啟用完整連線，可在 Azure 入口網站中要求全域延伸的授權金鑰和私人對等互連識別碼。 您可以使用金鑰和識別碼，在訂用帳戶中的 ExpressRoute 線路和新私人雲端的 ExpressRoute 線路之間建立全球範圍。 [建立私用雲端的教學](tutorial-create-private-cloud.md)課程提供您要求和使用金鑰和識別碼的程式。

解決方案的路由需求需要您規劃私人雲端網路位址空間，以避免與其他虛擬網路和內部部署網路重迭。 對於子網路，AVS 私人雲端需要至少 `/22` CIDR 網路位址區塊，如下所示。 此網路會補足您的內部部署網路。 若要連線到內部部署環境和虛擬網路，這必須是非重疊的網路位址區塊。

範例 `/22` CIDR 網路位址區塊：`10.10.0.0/22`

子網路：

| 網路使用量             | 子網路 | 範例        |
| ------------------------- | ------ | -------------- |
| 私人雲端管理            | `/24`    | `10.10.0.0/24`   |
| vMotion 網路       | `/24`    | `10.10.1.0/24`   |
| VM 工作負載 | `/24`   | `10.10.2.0/24`   |
| ExpressRoute 對等互連 | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>後續步驟 

下一步是瞭解私用[雲端儲存體的概念](concepts-storage.md)。

<!-- LINKS - external -->
[enable Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach

<!-- LINKS - internal -->

