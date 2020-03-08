---
title: 整合應用程式與 Azure 虛擬網路
description: 將 Azure App Service 中的應用程式與 Azure 虛擬網路整合。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673217"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>將您的應用程式與 Azure 虛擬網路整合
本檔說明 Azure App Service 虛擬網路整合功能，以及如何使用[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)中的應用程式進行設定。 [Azure 虛擬網路][VNETOverview]（vnet）可讓您將許多 Azure 資源放在非網際網路可路由網路中。  

Azure App Service 有兩種變化。

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>啟用 VNet 整合 

1. 在 App Service 入口網站中，移至網路 UI。 在 [VNet 整合] 底下，選取 *[按一下這裡設定]* 。 

1. 選取 [新增 VNet]。  

   ![選取 VNet 整合][1]

1. 下拉式清單會包含您的訂用帳戶中相同區域內的所有 Resource Manager Vnet，且如下所示，這是所有其他區域中所有 Resource Manager Vnet 的清單。 選取您想要整合的 VNet。

   ![選取 VNet][2]

   * 如果 VNet 位於相同的區域中，則請建立新的子網，或選擇空白的既有子網。 

   * 若要選取另一個區域中的 VNet，您必須在已啟用點對站的情況下布建虛擬網路閘道。

   * 若要與傳統 VNet 整合，請選取 [**按一下這裡以連線到傳統 vnet]** ，而不是按一下 [vnet] 下拉式選。 選取所需的傳統 VNet。 目標 VNet 必須已經布建虛擬網路閘道，並啟用點對站。

    ![選取傳統 VNet][3]
    
整合期間，會重新啟動您的應用程式。  當整合完成時，您會看到與您整合之 VNet 的詳細資料。 

當您的應用程式與 VNet 整合之後，它將會使用您的 VNet 設定所在的相同 DNS 伺服器，除非 Azure DNS 私人區域。 您目前無法搭配 Azure DNS 私人區域使用 VNet 整合。

## <a name="regional-vnet-integration"></a>區域 VNet 整合

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>區域 VNet 整合的運作方式

App Service 中的應用程式會裝載于背景工作角色上。 基本和較高的定價方案是專用的主控方案，在同一背景工作角色上不會有其他客戶工作負載正在執行。 區域 VNet 整合的運作方式是使用委派子網中的位址掛接虛擬介面。 由於「來源」位址位於您的 VNet 中，因此它可以存取您 vnet 中的大部分內容，或透過您 vnet 中的 VM。 網路功能與在您的 VNet 中執行 VM 不同，這也是為什麼使用這項功能時無法使用某些網路功能的原因。

![區域 VNet 整合的運作方式][5]

啟用區域 VNet 整合時，您的應用程式仍會透過正常的相同通道，對網際網路進行輸出呼叫。 應用程式屬性入口網站中所列的輸出位址仍然是您的應用程式所使用的位址。 針對您的應用程式所做的變更，對服務端點保護服務或 RFC 1918 位址的呼叫會進入您的 VNet。 如果 WEBSITE_VNET_ROUTE_ALL 設定為1，則可以將所有輸出流量傳送至您的 VNet。 

此功能僅針對每個背景工作支援一個虛擬介面。  每個工作者一個虛擬介面，表示每個 App Service 方案一個區域 VNet 整合。 相同 App Service 方案中的所有應用程式都可以使用相同的 VNet 整合，但如果您需要應用程式來連線至其他 VNet，則需要建立另一個 App Service 方案。 使用的虛擬介面不是客戶可直接存取的資源。

由於這項技術的運作方式，與 VNet 整合搭配使用的流量不會顯示在網路監看員或 NSG 流量記錄中。  

## <a name="gateway-required-vnet-integration"></a>閘道所需的 VNet 整合

閘道所需的 VNet 整合支援連線至另一個區域中的 VNet，或連接至傳統 VNet。 閘道所需的 VNet 整合： 

* 讓應用程式一次只連接到1個 VNet
* 最多可在 App Service 方案內整合五個 Vnet 
* 可讓 App Service 方案中的多個應用程式使用相同的 VNet，而不會影響 App Service 方案所能使用的總次數。  如果您在相同的 App Service 方案中，有六個使用相同 VNet 的應用程式，則會計算為使用1個 VNet。 
* 因閘道上的 SLA 而支援99.9% 的 SLA
* 讓您的應用程式能夠使用已設定 VNet 的 DNS
* 需要以 SSTP 點對站 VPN 設定虛擬網路路由式閘道，才能將它連線到應用程式。 

您無法使用閘道所需的 VNet 整合：

* 使用 Linux 應用程式
* 使用與 ExpressRoute 連線的 VNet 
* 存取服務端點受保護的資源
* 具有同時支援 ExpressRoute 和點對站/站對站 Vpn 的共存閘道

### <a name="set-up-a-gateway-in-your-vnet"></a>在 VNet 中設定閘道 ###

若要建立閘道：

1. 在您的 VNet 中[建立閘道子網][creategatewaysubnet]。  

1. [建立 VPN 閘道][creategateway]。 選取路由式 VPN 類型。

1. [設定點對站位址][setp2saddresses]。 如果閘道不在基本 SKU 中，則必須在點對站設定中停用 IKEV2，而且必須選取 SSTP。 點對站位址空間必須在 RFC 1918 位址區塊中，10.0.0.0/8、172.16.0.0/12、192.168.0.0/16

如果您只是建立要與 App Service VNet 整合搭配使用的閘道，則不需要上傳憑證。 建立閘道可能需要 30 分鐘。 在佈建閘道之前，您將無法將應用程式與 VNet 整合。 

### <a name="how-gateway-required-vnet-integration-works"></a>閘道所需的 VNet 整合運作方式

閘道所需的 VNet 整合是以點對站 VPN 技術為基礎。 點對站 Vpn 會將網路存取限制為僅裝載應用程式的虛擬機器。 應用程式會限制為僅透過混合式連線或透過 VNet 整合，將流量傳送至網際網路。 當您使用入口網站將應用程式設定為使用閘道所需的 VNet 整合時，會代表您管理複雜的協商，以在閘道和應用程式端建立及指派憑證。 最終結果是，用來裝載應用程式的工作者可以直接連線到所選 VNet 中的虛擬網路閘道。 

![閘道所需的 VNet 整合運作方式][6]

### <a name="accessing-on-premises-resources"></a>存取內部部署資源

應用程式可以透過與具有站對站連線的 VNnet 整合以存取內部部署資源。 如果您使用閘道所需的 VNet 整合，您需要使用點對站位址區塊來更新內部部署 VPN 閘道路由。 第一次設定站對站 VPN 時，用來設定它的指令碼應該設定路由，包括點對站 VPN。 如果您在建立站對站 VPN 之後新增點對站 VPN，則需要手動更新路由。 此做法的詳細資料會根據閘道而有所不同，不在這裡詳述。 您不能使用站對站 VPN 連線來設定 BGP。

區域 VNet 整合功能不需要額外的設定，即可透過您的 VNet 和內部部署進行。 您只需要使用 ExpressRoute 或站對站 VPN，將您的 VNet 連線到內部部署。 

> [!NOTE]
> 閘道所需的 VNet 整合功能不會將應用程式與具有 ExpressRoute 閘道的 VNet 整合。 即使在[共存模式][VPNERCoex]中設定 ExpressRoute 閘道，VNet 整合仍無法正常執行。 如果您需要透過 ExpressRoute 連線存取資源，您可以使用區域 VNet 整合功能，或在您的 VNet 中執行的[App Service 環境][ASE]。 
> 
> 

### <a name="peering"></a>對等互連

如果您使用與區域 VNet 整合的對等互連，則不需要執行任何額外的設定。 

如果您使用閘道所需的 VNet 與對等互連整合，您必須設定一些額外的專案。 若要設定對等互連以搭配您的應用程式使用：

1. 在您的應用程式連線到的 VNet 上新增對等互連連線。 新增對等互連連線時，啟用 [允許虛擬網路存取] 並核取 [允許轉寄的流量] 和 [允許閘道器傳輸]。
1. 在 VNet 上新增對等互連連線，該 VNet 與您連線的 VNet 對等互連。 在目的地 VNet 上新增對等互連連線時，啟用 [允許虛擬網路存取] 並核取 [允許轉寄的流量] 和 [允許遠端閘道器]。
1. 移至入口網站中的 [App Service 方案] > [網路] > [VNet 整合 UI]。  選取您的應用程式連線到的 VNet。 在路由區段中，新增與您的應用程式所連線到的 VNet 對等互連之 VNet 的位址範圍。  

## <a name="managing-vnet-integration"></a>管理 VNet 整合 

連接和中斷與 VNet 的連線是在應用層級。 可在多個應用程式之間影響 VNet 整合的作業是在 App Service 方案層級上。 從應用程式 > 網路 > VNet 整合入口網站中，您可以取得 VNet 的詳細資料。 您可以在 ASP > 網路 > VNet 整合入口網站中查看 ASP 層級的類似資訊。

您可以在 VNet 整合的應用程式檢視中採取的唯一作業，就是中斷連接目前連接到 VNet 的應用程式。 若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]。 當您從 VNet 中斷連線時，您的應用程式將重新啟動。 中斷連線不會變更您的 VNet。 不會移除子網或閘道。 如果您想要刪除 VNet，則需要先中斷應用程式與 VNet 的連線，並刪除其中的資源，例如閘道。 

ASP VNet 整合 UI 會顯示 ASP 中的應用程式所使用的所有 VNet 整合。 若要查看每個 VNet 的詳細資料，按一下您感興趣的 VNet 即可。 您可以在這裡針對閘道所需的 VNet 整合執行兩個動作。

* **同步網路**。 同步網路作業僅適用于與閘道相關的 VNet 整合功能。 執行同步網路作業可確保您的憑證和網路資訊同步。如果您新增或變更 VNet 的 DNS，則需要執行**同步網路**作業。 這項作業將重新啟動任何使用此 VNet 的應用程式。
* **新增路由** 新增路由將驅動輸出流量進入 VNet。 

**閘道所需的 VNet 整合路由**您的 VNet 中定義的路由會用來將流量從您的應用程式導向至您的 VNet。 如果需要將額外的輸出流量傳送到 VNet，則可以在此處新增這些位址區塊。 這項功能僅適用于閘道所需的 VNet 整合。 使用閘道所需的 VNet 整合時，路由表不會影響您的應用程式流量，其方式與區域 VNet 整合相同。

**閘道所需的 VNet 整合憑證**當閘道所需的 VNet 整合啟用時，會有必要的憑證交換，以確保連線的安全性。 除了憑證以外，還有 DNS 組態、路由，以及其他描述網路的類似項目。
如果憑證或網路資訊已變更，您需要按一下 [同步網路]。 按一下 [同步處理網路] 時，會導致應用程式與 VNet 之間的連線短暫中斷。 如果應用程式未重新啟動，失去連線會導致您的網站無法正常運作。 

## <a name="pricing-details"></a>價格詳細資料
除了 ASP 定價層費用以外，區域 VNet 整合功能不需額外付費。

使用閘道所需的 VNet 整合功能有三個相關費用：

* ASP 定價層費用-您的應用程式必須在標準、Premium 或 PremiumV2 App Service 方案中。 您可以在這裡看到這些成本的詳細資料： [App Service 定價][ASPricing]。 
* 資料傳輸成本-資料輸出需要付費，即使 VNet 位於相同的資料中心也一樣。 這些費用會在[資料傳輸定價詳細資料][DataPricing]中說明。 
* VPN 閘道成本-點對站 VPN 需要 VNet 閘道的成本。 詳細資料位於[VPN 閘道定價][VNETPricing]頁面。

## <a name="troubleshooting"></a>疑難排解

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

有適用于區域 VNet 整合的 CLI 支援。 若要存取下列命令，請[安裝 Azure CLI][installCLI]。 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

針對閘道所需的 VNet 整合，您可以使用 PowerShell 將 App Service 與 Azure 虛擬網路整合。 如需隨時可執行的指令碼，請參閱[將 Azure App Service 中的應用程式連線到 Azure 虛擬網路](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) \(英文\)。


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
