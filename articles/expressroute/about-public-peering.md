---
title: 創建和管理 Azure 快速路由公共對等互連
description: 瞭解和管理 Azure 公共對等互連
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481128"
---
# <a name="create-and-manage-expressroute-public-peering"></a>創建和管理 ExpressRoute 公共對等互連

> [!div class="op_single_selector"]
> * [文章 - 公共對等互連](about-public-peering.md)
> * [視訊 - 公用對等互連](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [文章 - 微軟對等互連](expressroute-circuit-peerings.md#microsoftpeering)
>

本文可説明您創建和管理 ExpressRoute 電路的公共對等路由配置。 您還可以檢查狀態、更新或刪除和取消預配對等互連。 本文適用于在棄用公共對等互連之前創建的資源管理器電路。 如果以前存在的電路（在棄用公共對等互連之前創建），則可以使用[Azure PowerShell、Azure](#powershell) [CLI](#cli)和[Azure 門戶](#portal)管理/配置公共對等互連。

>[!NOTE]
>公共對等互連被棄用。 您不能在新的 ExpressRoute 電路上創建公共對等互連。 如果您有新的 ExpressRoute 電路，請對 Azure 服務使用[Microsoft 對等互連](expressroute-circuit-peerings.md#microsoftpeering)。
>

## <a name="connectivity"></a>連線能力

一律會從您的 WAN 啟動連線到 Microsoft Azure 服務。 Microsoft Azure 服務將無法透過這個路由網域啟動連線到您的網路。 如果為 Azure 公共對等互連啟用了 ExpressRoute 電路，則可以通過電路訪問[Azure 中使用的公共 IP 範圍](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。

啟用公共對等互連後，可以連接到大多數 Azure 服務。 我們不允許您選擇性地選取已通告路由的服務。

* 在公共 IP 位址上提供了 Azure 存儲、SQL 資料庫和網站等服務。
* 通過公共對等路由域，您可以私下連接到在公共 IP 位址託管的服務，包括雲服務的 VIP。
* 您可以將公用對等網域連線到 DMZ，並從您的 WAN 連線到所有 Azure 服務的公用 IP 位址，而無需透過網際網路進行連線。

## <a name="services"></a><a name="services"></a>服務

本節顯示公共對等互連可用的服務。 由於公共對等互連被棄用，因此沒有計劃向公共對等互連添加新服務或附加服務。 如果使用公共對等互連，並且您要使用的服務僅支援 Microsoft 對等互連，則必須切換到 Microsoft 對等互連。 有關受支援服務的清單，請參閱[Microsoft 對等互連](expressroute-faqs.md#microsoft-peering)。

**支援：**

* Power BI
* 支援大部分 Azure 服務。 直接檢查要用於驗證支援的服務。

**不支援：**
  * CDN
  * Azure Front Door
  * 多重要素驗證伺服器（舊版）
  * 流量管理員

要驗證特定服務的可用性，可以檢查該服務的文檔，以查看是否有為該服務發佈的保留範圍。 然後，您可以查找目標服務的 IP 範圍，並與[Azure IP 範圍和服務標記 - 公共雲 XML 檔中](https://www.microsoft.com/download/details.aspx?id=56519)列出的範圍進行比較。 或者，您可以為相關服務打開支援票證以進行澄清。

## <a name="peering-comparison"></a><a name="compare"></a>對等互連比較

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Azure 公共對等互連具有與每個 BGP 會話關聯的 1 個 NAT IP 位址。 對於大於 2 個 NAT IP 位址，請轉到 Microsoft 對等互連。 Microsoft 對等互連允許您配置自己的 NAT 分配，以及使用路由篩選器進行選擇性首碼播發。 有關詳細資訊，請參閱移動到[Microsoft 對等互連](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)。
>

## <a name="custom-route-filters"></a>自訂路由篩選器

您可以在您的網路內定義自訂路由篩選條件，以便僅取用所需的路由。 如需路由組態的詳細資訊，請參閱 [路由](expressroute-routing.md) 頁面。

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure 電源外殼步驟


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

由於公共對等互連已棄用，因此無法在新的 ExpressRoute 電路上配置公共對等互連。

1. 驗證您有已預配且也啟用的 ExpressRoute 電路。 請使用下列範例：

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   回應如下列範例所示：

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. 設定線路的 Azure 公用對等。 進一步執行之前，請確定您具有下列資訊。

   * 主要連結的 /30 子網路。 這必須是有效的公用 IPv4 首碼。
   * 次要連結的 /30 子網路。 這必須是有效的公用 IPv4 首碼。
   * 供建立此對等的有效 VLAN ID。 請確定線路有沒有其他對等使用相同的 VLAN ID。
   * 對等的 AS 編號。 您可以使用 2 位元組和 4 位元組 AS 編號。
   * 選擇性：
   * MD5 雜湊 (如果選擇使用)。

   執行下列範例來為線路設定 Azure 公用對等互連

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   如果您選擇使用 MD5 雜湊，請使用下列範例：

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > 請確定您將 AS 編號指定為對等 ASN，而不是客戶 ASN。
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>取得 Azure 公用對等互連詳細資訊

您可以使用下列 Cmdlet 來取得組態詳細資料：

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>更新 Azure 公用對等組態

您可以使用下列範例來更新設定的任何部分。 在此範例中，線路的 VLAN ID 從 200 更新為 600。

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>刪除 Azure 公用對等

您可以執行下列範例來移除對等互連設定：

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Azure CLI 步驟


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. 檢查 ExpressRoute 線路，以確定已佈建且已啟用線路。 請使用下列範例：

   ```azurecli-interactive
   az network express-route list
   ```

   回應如下列範例所示：

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. 設定線路的 Azure 公用對等。 進一步執行之前，請確定您具有下列資訊。

   * 主要連結的 /30 子網路。 這必須是有效的公用 IPv4 首碼。
   * 次要連結的 /30 子網路。 這必須是有效的公用 IPv4 首碼。
   * 供建立此對等的有效 VLAN ID。 請確定線路有沒有其他對等使用相同的 VLAN ID。
   * 對等的 AS 編號。 您可以使用 2 位元組和 4 位元組 AS 編號。
   * **選用：** MD5 雜湊 (如果選擇使用)。

   執行下列範例來為線路設定 Azure 公用對等互連：

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   如果您選擇使用 MD5 雜湊，請使用下列範例：

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 請確定您將 AS 編號指定為對等 ASN，而不是客戶 ASN。

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>檢視 Azure 公用對等詳細資訊

您可以使用下列範例來取得設定詳細資料：

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

輸出類似於下列範例：

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>更新 Azure 公用對等組態

您可以使用下列範例來更新設定的任何部分。 在此範例中，線路的 VLAN ID 從 200 更新為 600。

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>刪除 Azure 公用對等

您可以執行下列範例來移除對等互連設定：

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure 入口網站步驟

要配置對等互連，請使用本文中包含的 PowerShell 或 CLI 步驟。 要管理對等互連，可以使用以下部分。 作為參考，這些步驟類似于[在門戶中管理 Microsoft 對等互連](expressroute-howto-routing-portal-resource-manager.md#msft)。

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>檢視 Azure 公用對等詳細資訊

通過在門戶中選擇對等互連來查看 Azure 公共對等互連的屬性。

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>更新 Azure 公用對等組態

選擇要對等的行，然後修改對等屬性。

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>刪除 Azure 公用對等

通過選擇刪除圖示刪除對等配置。

## <a name="next-steps"></a>後續步驟

下一步，[將虛擬網路連結到 ExpressRoute 電路](expressroute-howto-linkvnet-arm.md)。

* 如需 ExpressRoute 工作流程的詳細資訊，請參閱 [ExpressRoute 工作流程](expressroute-workflows.md)。
* 如需線路對等的詳細資訊，請參閱 [ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md)。
* 如需使用虛擬網路的詳細資訊，請參閱 [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。