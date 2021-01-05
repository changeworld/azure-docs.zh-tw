---
title: ExpressRoute：將線路從傳統移至 Azure Resource Manager
description: 瞭解當您將 Azure ExpressRoute 線路從傳統移至 Azure Resource Manager 部署模型時，會發生什麼事。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807936"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>將 ExpressRoute 線路從傳統部署模型移至 Resource Manager 部署模型
本文概述當您將 Azure ExpressRoute 線路從傳統移至 Azure Resource Manager 部署模型時，會發生什麼事。

您可以使用單一 ExpressRoute 線路來連接在傳統和 Resource Manager 部署模型中部署的虛擬網路。

![連結至兩種部署模型中虛擬網路的 ExpressRoute 線路。](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>在傳統部署模型中建立的 ExpressRoute 線路
在傳統部署模型中建立的 ExpressRoute 線路必須先遷移至 Resource Manager 部署模型。 只有如此才能啟用傳統和 Resource Manager 部署模型的連線。 移動連接時不會遺失或中斷連接。 相同訂用帳戶和跨訂用帳戶中的傳統部署模型中的所有線路對虛擬網路連結都會保留下來。

成功完成移動之後，ExpressRoute 線路的行為會與 Resource Manager 部署模型中建立的 ExpressRoute 電路完全相同。 您現在可以在 Resource Manager 部署模型中建立虛擬網路的連線。

將 ExpressRoute 線路移至資源管理員部署模型之後，您只能在 Resource Manager 部署模型中進行管理。 管理對等互連、更新電路屬性和刪除線路的作業，只會透過 Resource Manager 部署模型提供。 請參閱下一節，以深入瞭解如何管理這兩種部署模型的存取權。

您不需要讓連線提供者將您的線路移至 Resource Manager 部署模型。

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>在 Resource Manager 部署模型中建立的 ExpressRoute 線路
您可讓在 Resource Manager 部署模型中建立的 ExpressRoute 線路能夠從這兩種部署模型進行存取。 您訂用帳戶中的任何 ExpressRoute 電路都可設定為可從這兩種部署模型進行存取。

* 在 Resource Manager 部署模型中建立的 ExpressRoute 線路預設無法存取傳統部署模型。
* 從傳統部署模型移至 Resource Manager 部署模型的 ExpressRoute 線路預設可以從這兩種部署模型進行存取。
* ExpressRoute 線路一律具有 Resource Manager 部署模型的存取權，無論是在 Resource Manager 或傳統部署模型中建立。 您可以遵循 [如何連結虛擬網路](expressroute-howto-linkvnet-arm.md)的指示，建立虛擬網路的連線。
* 傳統部署模型的存取權是由 ExpressRoute 線路中的 **allowClassicOperations** 參數所控制。

> [!IMPORTANT]
> 會套用 [服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md) 頁面上記載的所有配額。 例如，標準線路最多可以有 10 個跨傳統與 Resource Manager 部署模型的虛擬網路連結/連線。
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>控制傳統部署模型的存取權
您可以啟用 ExpressRoute 線路，以連結至這兩種部署模型中的虛擬網路。 若要這樣做，請在 ExpressRoute 線路上設定 **>allowclassicoperations** 參數。

將 **allowClassicOperations** 設定為 TRUE，可讓您從這兩種部署模型中的虛擬網路連結到 ExpressRoute 線路。 
* 若要連結傳統部署模型中的虛擬網路，請參閱 [如何連結傳統部署模型的虛擬網路](expressroute-howto-linkvnet-classic.md)。
* 若要連結 Resource Manager 部署模型中的虛擬網路，請參閱 [如何連結 Resource Manager 部署模型中的虛擬網路](expressroute-howto-linkvnet-arm.md)。

將 **allowClassicOperations** 設定為 FALSE，就會封鎖傳統部署模型中線路的存取。 不過，在傳統部署模型中連結的所有虛擬網路仍會保留下來。 ExpressRoute 線路不會顯示在傳統部署模型中。

## <a name="supported-operations-in-the-classic-deployment-model"></a>傳統部署模型中支援的作業
當 **allowClassicOperations** 設定為 TRUE 時，ExpressRoute 線路支援下列傳統作業。

* 取得 ExpressRoute 線路資訊
* 建立/更新/取得/刪除對傳統虛擬網路的虛擬網路連結
* 建立/更新/取得/刪除跨訂用帳戶連線的虛擬網路連結授權

不過，當 **>allowclassicoperations** 設定為 TRUE 時，您無法執行下列傳統作業：

* 建立/更新/取得/刪除 Azure 私人、Azure 公用和 Microsoft 對等的邊界閘道協定 (BGP) 對等
* 刪除 ExpressRoute 線路

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>傳統與 Resource Manager 部署模型之間的通訊
ExpressRoute 線路的作用就像是傳統與 Resource Manager 部署模型之間的橋樑。 如果兩個虛擬網路都連結到相同的線路，則兩個部署模型的虛擬網路之間的流量可以通過 ExpressRoute 線路。

彙總輸送量受限於虛擬網路閘道的輸送量容量。 在這種情況下，流量不會進入連線提供者的網路或您的網路。 虛擬網路之間的流量將會完全包含在 Microsoft 網路內。

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Azure 公用和 Microsoft 對等資源的存取權
您可以繼續存取通常可透過 Azure 公用對等和 Microsoft 對等存取的資源，而不需要中斷。  

## <a name="whats-supported"></a>支援的項目
本節說明 ExpressRoute 線路會支援的功能：

* 您可以使用單一 ExpressRoute 線路來存取在傳統和 Resource Manager 部署模型中部署的虛擬網路。
* 您可以將 ExpressRoute 線路從傳統部署模型移至 Resource Manager 部署模型。 移動之後，ExpressRoute 電路將繼續運作，就像在 Resource Manager 部署模型中建立的任何其他 ExpressRoute 線路一樣。
* 您只可以移動 ExpressRoute 線路。 線路連結、虛擬網路和 VPN 閘道無法透過這項作業移動。
* 在 ExpressRoute 線路移至 Resource Manager 部署模型後，您可以利用 Resource Manager 部署模型來管理 ExpressRoute 線路的生命週期。 這表示您可以執行作業，例如新增/更新/刪除對等互連、更新線路屬性 (例如頻寬、SKU 和計費類型) ，以及只刪除 Resource Manager 部署模型中的線路。
* ExpressRoute 線路的作用就像是傳統與 Resource Manager 部署模型之間的橋樑。 如果兩個虛擬網路都連結至相同的 ExpressRoute 線路，則傳統虛擬網路中的虛擬機器和 Resource Manager 虛擬網路中虛擬機器之間的流量可以透過 ExpressRoute 進行通訊。
* 傳統和 Resource Manager 部署模型中都支援跨訂用帳戶連線。
* 在您將 ExpressRoute 線路從傳統模型移至 Azure Resource Manager 模型之後，您可以[移轉已連結至 ExpressRoute 線路的虛擬網路](expressroute-migration-classic-resource-manager.md)。

## <a name="whats-not-supported"></a>不支援的內容
本節說明 ExpressRoute 線路不會支援的功能：

* 從傳統部署模型管理 ExpressRoute 線路的生命週期。
* Azure 角色型存取控制 (Azure RBAC) 傳統部署模型的支援。 您無法在傳統部署模型中執行 Azure RBAC 控制項至電路。 訂用帳戶的所有管理員/共同管理員都可以將虛擬網路連結至線路或取消其連結。

## <a name="configuration"></a>設定
請遵循 [將 ExpressRoute 線路從傳統部署模型移至 Resource Manager 部署模型](expressroute-howto-move-arm.md)中所描述的指示。

## <a name="next-steps"></a>後續步驟
* [將已連結至 ExpressRoute 線路的虛擬網路從傳統模型移轉至 Azure Resource Manager 模型](expressroute-migration-classic-resource-manager.md)
* 如需工作流程資訊，請參閱 [ExpressRoute 線路佈建工作流程和線路狀態](expressroute-workflows.md)。
* 若要設定 ExpressRoute 連線：
  
  * [建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md)
  * [設定路由](expressroute-howto-routing-arm.md)
  * [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)

