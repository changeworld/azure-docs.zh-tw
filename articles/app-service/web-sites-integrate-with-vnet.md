---
title: 將應用程式與 Azure 虛擬網路整合
description: 將 Azure App Service 中的應用程式與 Azure 虛擬網路整合。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 8f356cb935f1cf63408b6fbc604f139439022a4f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646622"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>將您的應用程式與 Azure 虛擬網路整合

此文章說明 Azure App Service VNet 整合功能，以及如何使用 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) 中的應用程式來加以設定。 使用 [Azure 虛擬網路][VNETOverview] (VNet)，您可以將許多 Azure 資源放在非網際網路可路由的網路中。 VNet 整合功能可讓您的應用程式透過 VNet 存取中的資源。 VNet 整合無法讓您私下存取您的應用程式。

Azure App Service 在 VNet 整合功能上有兩種變化：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>啟用 VNet 整合

1. 請在 App Service 入口網站中，移至 [網路] UI。 在 [VNet 整合] 下，選取 [按一下這裡可設定]。

1. 選取 [新增 VNet]。

   ![選取 VNet 整合][1]

1. 下拉式清單包含您訂用帳戶中相同區域內的所有 Azure Resource Manager 虛擬網路。 底下的清單會列出所有其他區域中的 Resource Manager 虛擬網路。 選取您要整合的 VNet。

   ![選取 VNet][2]

   * 如果 VNet 位於相同的區域中，請建立新的子網路或選取空白的預先存在子網路。
   * 若要選取另一個區域中的 VNet，您必須已佈建 VNet 閘道並啟用點對站功能。
   * 若要與傳統 VNet 整合，請選取 [按一下這裡以連線至傳統 VNet]，而不是選取 [虛擬網路] 下拉式清單。 選取您需要的傳統虛擬網路。 目標 VNet 必須已佈建虛擬網路閘道並啟用點對站功能。

    ![選取傳統 VNet][3]

整合期間，會重新啟動您的應用程式。 當整合完成時，您將會看到所整合 VNet 的詳細資料。

## <a name="regional-vnet-integration"></a>區域 VNet 整合

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>區域 VNet 整合的運作方式

App Service 中的應用程式會裝載在背景工作角色上。 基本與較高價格方案是專用型主控方案，也就是不會有其他客戶的工作負載在同一個背景工作角色上執行。 區域 VNet 整合的運作方式是使用委派子網路中的位址掛接虛擬介面。 由於「來源」位址位於您的 VNet 中，因此其可以像您 VNet 中的 VM 那樣，在您的 VNet 中或是透過 VNet，存取大部分的項目。 網路功能實作與在您的 VNet 中執行 VM 不同。 這就是為什麼此功能尚無法使用某些網路功能的原因。

![區域 VNet 整合的運作方式][5]

啟用區域 VNet 整合時，您的應用程式會透過平常使用的相同通道，對網際網路進行連出呼叫。 應用程式屬性入口網站中所列的連出位址就是您的應用程式仍會使用的位址。 您應用程式中改變的事項是，對服務端點安全服務的呼叫，或 RFC 1918 位址會進入您的 VNet。 如果 WEBSITE_VNET_ROUTE_ALL 設定為 1，所有連出流量都可以傳送進入您的 VNet。

此功能僅針對每一個背景工作角色支援一個虛擬介面。 「每個背景工作角色一個虛擬介面」表示每個 App Service 方案也只能有一個區域 VNet 整合。 同一個 App Service 方案中的所有應用程式都可以使用同一個 VNet 整合。 如果您需要某個應用程式連線至其他 VNet，就需要建立另一個 App Service 方案。 使用的虛擬介面並不是客戶可直接存取的資源。

基於此技術在運作方式上的先天特性，與 VNet 整合搭配使用的流量不會顯示在 Azure 網路監看員或 NSG 流程記錄檔中。

## <a name="gateway-required-vnet-integration"></a>需要閘道的 VNet 整合

需要閘道的 VNet 整合支援連線到其他區域中的 VNet，或連線到傳統虛擬網路。 需要閘道的 VNet 整合：

* 讓應用程式一次只連線到 1 個 VNet。
* 讓您在一個 App Service 方案內最多整合五個 VNet。
* 可讓 App Service 方案中的多個應用程式使用相同的 VNet，而不會影響 App Service 方案所能使用的 VNet 總數。 如果您在某個 App Service 方案中，有六個應用程式使用同一個 VNet，會計算為使用一個 VNet。
* 由於閘道上有 SLA 的緣故，因此可支援高達 99.9% 的 SLA。
* 可讓您的應用程式使用設定 VNet 時所用的 DNS。
* 需要先以 SSTP 點對站 VPN 設定虛擬網路路由式閘道，其才能連線到應用程式。

您無法使用需要閘道的 VNet 整合：

* 來與 Azure ExpressRoute 連線的 VNet 搭配使用。
* 從 Linux 應用程式
* 存取服務端點安全資源。
* 與同時支援 ExpressRoute 和點對站或站對站 VPN 的共存閘道搭配使用。

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>在您的 Azure 虛擬網路中設定閘道 ###

若要建立閘道：

1. 在您的 VNet 中[建立閘道子網路][creategatewaysubnet]。

1. [建立 VPN 閘道][creategateway]。 選取路由式 VPN 類型。

1. [設定點對站位址][setp2saddresses]。 如果閘道不在基本 SKU 中，則必須在點對站組態中停用 IKEV2，而且必須選取 SSTP。 點對站位址空間必須是 RFC 1918 位址區塊 10.0.0.0/8、172.16.0.0/12 與 192.168.0.0/16。

如果您是為了要與 App Service VNet 整合搭配使用而建立閘道，不需要上傳憑證。 建立閘道可能需要 30 分鐘。 在佈建閘道之前，您無法將應用程式與 VNet 整合。

### <a name="how-gateway-required-vnet-integration-works"></a>需要閘道的 VNet 整合的運作方式

需要閘道的 VNet 整合是以點對站 VPN 技術為基礎而建置的。 點對站 VPN 會限制對裝載應用程式之虛擬機器進行的網路存取。 應用程式會限制為僅透過混合式連線或透過 VNet 整合，將流量向外傳送至網際網路。 當您搭配入口網站將應用程式設定為使用需要閘道的 VNet 整合時，系統會代表您管理複雜的交涉，以在閘道與應用程式端建立及指派憑證。 結果是，用來裝載應用程式的背景工作角色可以直接連線到所選 VNet 中的虛擬網路閘道。

![需要閘道的 VNet 整合的運作方式][6]

### <a name="access-on-premises-resources"></a>存取內部部署資源

應用程式可以透過與具有站對站連線的 VNnet 整合以存取內部部署資源。 如果您使用需要閘道的 VNet 整合，請使用您的點對站位址區塊來更新您的內部部署 VPN 閘道路由。 第一次設定站對站 VPN 時，用來設定它的指令碼應該設定路由，包括點對站 VPN。 如果您在建立站對站 VPN 之後新增點對站 VPN，則需要手動更新路由。 此作法的細節會根據閘道而有所不同，這裡不會加以討論。 您不能使用站對站 VPN 連線來設定 BGP。

不需要為區域 VNet 整合功能進行其他設定，就能透過 VNet 連線到內部部署資源。 您只需要使用 ExpressRoute 或站對站 VPN，將您的 VNet 連線到內部部署資源即可。

> [!NOTE]
> 需要閘道的 VNet 整合功能不會將應用程式與具有 ExpressRoute 閘道的 VNet 整合。 即使是在[共存模式][VPNERCoex]中設定 ExpressRoute 閘道，VNet 整合仍無法運作。 如果您需要透過 ExpressRoute 連線存取資源，請使用區域 VNet 整合功能在您 VNet 中執行的 [App Service 環境][ASE]。
>
>

### <a name="peering"></a>對等互連

如果您搭配區域 VNet 整合使用對等互連，並不需要執行任何額外的設定。

如果您搭配對等互連使用需要閘道的 VNet 整合，就需要設定幾個額外的項目。 若要設定對等互連以搭配您的應用程式使用：

1. 在您的應用程式連線到的 VNet 上新增對等互連連線。 新增對等互連連線時，請啟用 [允許虛擬網路存取] 並選取 [允許轉送的流量] 與 [允許閘道器傳輸]。
1. 在與您連線之 VNet 對等互連的 VNet 上新增對等互連連線。 當您在目的地 VNet 上新增對等互連連線時，請啟用 [允許虛擬網路存取] 並選取 [允許轉送的流量] 與 [允許遠端閘道]。
1. 移至入口網站中的 [App Service 方案] > [網路] > [VNet 整合] UI。 選取您的應用程式連線到的 VNet。 在路由區段下，新增與您的應用程式所連線的 VNet 對等互連之 VNet 的位址範圍。

## <a name="manage-vnet-integration"></a>管理 VNet 整合

與 VNet 連線及中斷連線是應用程式層級作業。 可跨多個應用程式影響 VNet 整合的作業則是 App Service 方案層級作業。 您可以從應用程式 > [網路] > [VNet 整合] 入口網站取得 VNet 的詳細資料。 您可以在 [App Service 方案] > [網路] > [VNet 整合] 入口網站中的 App Service 方案層級，查看類似的資訊。

在 VNet 整合執行個體的應用程式檢視中，您可以執行的唯一作業，就是將您的應用程式與目前連線的 VNet 中斷連線。 若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]。 當您中斷與 VNet 的連線時，您的應用程式會重新啟動。 中斷連線不會變更您的 VNet。 也不會移除子網路或閘道。 如果您想要刪除 VNet，請先中斷應用程式與 VNet 的連線，然後刪除其中的資源，例如閘道。

App Service 方案 VNet 整合 UI 會顯示您的 App Service 方案中，應用程式使用的所有 VNet 整合。 若要查看每個 VNet 的詳細資料，請選取您感興趣的 VNet。 您可以在這裡針對需要閘道的 VNet 整合執行兩個動作：

* **同步網路**：同步網路作業僅用於與閘道相依的 VNet 整合功能。 執行同步網路作業可確保您的憑證與網路資訊會保持同步。如果您新增或變更 VNet 的 DNS，請執行同步網路作業。 此作業會重新啟動任何使用此 VNet 的應用程式。 如果您是使用屬於不同訂用帳戶的應用程式與 VNet，此作業將無法運作。
* **新增路由**：新增路由會將連出流量驅使到您的 VNet。

### <a name="gateway-required-vnet-integration-routing"></a>需要閘道的 VNet 整合路由傳送
VNet 中所定義的路由用於將流量從您的應用程式導向至 VNet。 若要將額外的連出流量傳送到 VNet，請在這裡新增那些位址區塊。 此功能僅適用於需要閘道的 VNet 整合。 當您使用需要閘道的 VNet 整合時，路由表不會像影響區域 VNet 整合那樣，對您的應用程式流量造成相同影響。

### <a name="gateway-required-vnet-integration-certificates"></a>需要閘道的 VNet 整合憑證
啟用 [需要閘道的 VNet 整合] 時，需要交換憑證以確保連線的安全性。 除了憑證以外，還有 DNS 組態、路由，以及其他描述網路的類似項目。

如果憑證或網路資訊已變更，請選取 [同步網路]。 當您選取 [同步網路] 時，會導致應用程式與 VNet 之間的連線短暫中斷。 如果應用程式未重新啟動，失去連線會導致您的網站無法正常運作。

## <a name="pricing-details"></a>價格詳細資料
除了 App Service 方案定價層費用以外，區域 VNet 整合功能沒有任何其他額外費用。

使用需要閘道的 VNet 整合功能時，需支付三個相關費用：

* **App Service 方案定價層費用**：您的應用程式必須是「標準」、「進階」或「進階 V2」App Service 方案中包含的應用程式。 如需那些費用的詳細資訊，請參閱 [App Service 定價][ASPricing]。
* **資料傳輸成本**：即使 VNet 位於相同的資料中心，也需要對資料輸出付費。 [資料傳輸定價詳細資料][DataPricing]中會說明那些費用。
* **VPN 閘道成本**：點對站 VPN 所需的虛擬網路閘道需付費使用。 如需詳細資訊，請參閱 [VPN 閘道定價][VNETPricing]。

## <a name="troubleshooting"></a>疑難排解

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>自動化

CLI 支援可用於區域 VNet 整合。 若要存取下列命令，請[安裝 Azure CLI][installCLI]。

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

Powershell 支援區域 VNet 整合也可供使用，但您必須使用子網 resourceID 的屬性陣列來建立一般資源

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


針對需要閘道的 VNet 整合，您可以使用 PowerShell 將 App Service 與 Azure 虛擬網路整合。 如需隨時可執行的指令碼，請參閱[將 Azure App Service 中的應用程式連線到 Azure 虛擬網路](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) \(英文\)。


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli?view=azure-cli-latest%2f
[privateendpoints]: networking/private-endpoint.md