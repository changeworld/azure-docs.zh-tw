---
title: Azure ExpressRoute：將 VNet 連結至線路： CLI
description: 本文說明如何使用 Resource Manager 部署模型和 CLI 將虛擬網路 (VNet) 連結至 Azure ExpressRoute 線路。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 07/27/2020
ms.author: cherylmc
ms.openlocfilehash: 3d51409baf9d5ca6ce90f1367ac128d7d47a9757
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325248"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>使用 CLI 將虛擬網路連線到 ExpressRoute 線路

本文可協助您使用 CLI 將虛擬網路 (VNet) 連結到 Azure ExpressRoute 線路。 若要使用 Azure CLI 進行連結，您必須使用 Resource Manager 部署模型建立虛擬網路。 其可以位於相同的訂用帳戶中，或屬於另一個訂用帳戶。 如果您想要使用不同的方法將 VNet 連線到 ExpressRoute 線路，您可以從下列清單選取文章：

> [!div class="op_single_selector"]
> * [Azure 入口網站](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [影片-Azure 入口網站](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell （傳統）](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>組態必要條件

* 您需要最新版本的命令列介面 (CLI)。 如需詳細資訊，請參閱 [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

* 開始設定之前，請務必檢閱[必要條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)和[工作流程](expressroute-workflows.md)。

* 您必須擁有作用中的 ExpressRoute 線路。 
  * 遵循指示來 [建立 ExpressRoute 線路](howto-circuit-cli.md) ，並由您的連線提供者來啟用該線路。 
  * 確定您已針對循環設定了 Azure 私用對等。 請參閱 [設定路由](howto-routing-cli.md) 一文，以取得路由指示。 
  * 確定已設定 Azure 私用對等互連。 已開啟您的網路與 Microsoft 之間的 BGP 對等互連，讓您可以啟用端對端連線。
  * 請確定您有已建立且完整佈建的虛擬網路和虛擬網路閘道。 請遵循指示[為 ExpressRoute 設定虛擬網路閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)。 請務必使用 `--gateway-type ExpressRoute`。

* 您最多可以將 10 個虛擬網路連結至標準 ExpressRoute 電路。 在使用標準 ExpressRoute 電路時，所有虛擬網路都必須位於相同的地理政治區域內。 

* 單一 VNet 最多可連結到四個 ExpressRoute 線路。 使用下列程序來建立您要連線之每個 ExpressRoute 線路的新連線物件。 ExpressRoute 線路可以位於相同的訂用帳戶、不同的訂用帳戶或兩者的混合。

* 如果您已啟用 ExpressRoute 進階附加元件，則可連結 ExpressRoute 線路的地理政治區域以外的虛擬網路，或是將大量的虛擬網路連線到 ExpressRoute 線路。 如需有關進階附加元件的詳細資訊，請參閱[常見問題集](expressroute-faqs.md)。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>將相同訂用帳戶中的虛擬網路連接到線路

您可以使用範例，將虛擬網路閘道連線到 ExpressRoute 線路。 執行命令之前，請確定您已建立虛擬網路閘道，並準備好進行連結。

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>將不同訂用帳戶中的虛擬網路連接到線路

您可以讓多個訂用帳戶共用 ExpressRoute 線路。 下圖顯示簡單的圖解，示範多個訂用帳戶共用 ExpressRoute 線路的方式。

大型雲端內的每個較小型雲端，會用來代表屬於組織內不同部門的訂用帳戶。 組織內的每個部門都可以使用自己的訂用帳戶來部署它們的服務，但可共用單一 ExpressRoute 線路，以連接回內部部署網路。 單一部門 (在此範例中：IT) 可以擁有 ExpressRoute 循環。 組織內的其他訂用帳戶可以使用 ExpressRoute 電路。

> [!NOTE]
> ExpressRoute 線路擁有者需支付專用線路的連線和頻寬費用。 所有虛擬網路都會共用相同的頻寬。
> 
> 

![跨訂用帳戶的連線能力](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>系統管理 - 線路擁有者和線路使用者

「線路擁有者」是 ExpressRoute 線路資源的已授權「進階使用者」。 線路擁有者能夠建立可由「線路使用者」兌換的授權。 線路使用者是虛擬網路閘道的擁有者，與 ExpressRoute 線路位於不同的訂用帳戶內。 線路使用者可以兌換授權 (每個虛擬網路一個授權)。

線路擁有者能夠隨時修改及撤銷授權。 撤銷授權時，在存取權遭到撤銷的訂用帳戶中，所有連結連線均會遭到刪除。

### <a name="circuit-owner-operations"></a>線路擁有者作業

**建立授權**

線路擁有者會建立授權，這會建立授權金鑰讓線路使用者可用以將其虛擬網路閘道連線到 ExpressRoute 線路。 一個授權僅適用於一個連線。

下列範例示範如何建立授權：

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

回應包含授權金鑰和狀態：

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**檢閱授權**

線路擁有者可以透過執行下列範例，檢閱特定線路上發出的所有授權：

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**新增授權**

線路擁有者可以使用下列範例來新增授權：

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**刪除授權**

線路擁有者可以透過執行下列範例來撤銷/刪除使用者的授權：

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>線路使用者作業

線路使用者需要具備對等識別碼以及線路擁有者所提供的授權金鑰。 授權金鑰是 GUID。

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**兌換連線授權**

線路使用者可以執行下列範例來兌換連結授權：

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**釋出連線授權**

您可以藉由刪除將 ExpressRoute 線路連結到虛擬網路的連線來釋出授權。

## <a name="modify-a-virtual-network-connection"></a>修改虛擬網路連線
您可以更新虛擬網路連線的特定屬性。 

**更新連線權數**

您的虛擬網路可以連接到多個 ExpressRoute 線路。 您可能會收到來自多個 ExpressRoute 線路的相同前置詞。 若要選擇要使用哪一個連線來傳送目的為此前置詞的流量，您可以變更連線的 *RoutingWeight*。 流量將透過具有最高 *RoutingWeight* 的連線來傳送。

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

*RoutingWeight* 的範圍是從 0 到 32000。 預設值為 0。

## <a name="configure-expressroute-fastpath"></a>設定 ExpressRoute FastPath 
如果您的虛擬網路閘道是 Ultra 效能或 ErGw3AZ，您可以啟用[ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) 。 FastPath 可改善資料路徑效能，例如每秒封包數和內部部署網路與虛擬網路之間的每秒連線數。 

**在新連接上設定 FastPath**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**更新現有的連接以啟用 FastPath**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
