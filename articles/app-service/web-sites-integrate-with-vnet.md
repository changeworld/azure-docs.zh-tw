---
title: 整合應用程式與 Azure 虛擬網路
description: 將 Azure App Service 中的應用程式與 Azure 虛擬網路整合。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 78b49b8b7e17f12d49825390a302e28a61e10d16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770846"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>將您的應用程式與 Azure 虛擬網路整合

本文說明 Azure App Service VNet 整合功能，以及如何在[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)中設定應用程式。 使用[Azure 虛擬網路][VNETOverview]（vnet），您可以將許多 Azure 資源放在非網際網路可路由網路中。

Azure App Service 有兩種變化：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>啟用 VNet 整合

> [!NOTE]
> 如果您的 Linux 應用程式功能表中的 [網路] 分頁已停用（呈現灰色），表示該功能目前無法使用。
>

1. 在 App Service 入口網站中，移至**網路**UI。 在 [ **VNet 整合**] 底下，選取 [**按一下這裡進行設定**]。

1. 選取 [新增 VNet]****。

   ![選取 VNet 整合][1]

1. 下拉式清單包含您的訂用帳戶中相同區域內的所有 Azure Resource Manager 虛擬網路。 底下的清單會列出所有其他區域中的 Resource Manager 虛擬網路。 選取您想要整合的 VNet。

   ![選取 VNet][2]

   * 如果 VNet 位於相同的區域中，請建立新的子網，或選取空白的預先存在的子網。
   * 若要選取另一個區域中的 VNet，您必須已布建 VNet 閘道，並啟用點對站。
   * 若要與傳統 VNet 整合，請選取 [**按一下這裡以連線到傳統 vnet]**，而不是選取 [**虛擬網路**] 下拉式清單。 選取您想要的傳統虛擬網路。 目標 VNet 必須已經布建虛擬網路閘道，並啟用點對站。

    ![選取傳統 VNet][3]

整合期間，會重新啟動您的應用程式。 當整合完成時，您會看到與您整合之 VNet 的詳細資料。

## <a name="regional-vnet-integration"></a>區域 VNet 整合

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>區域 VNet 整合的運作方式

App Service 中的應用程式會裝載于背景工作角色上。 基本和較高的定價方案是專用的主控方案，其中沒有其他客戶的工作負載在同一背景工作上執行。 區域 VNet 整合的運作方式是使用委派子網中的位址掛接虛擬介面。 由於「來源」位址位於您的 VNet 中，因此它可以存取您的 vnet 中的大部分專案，例如 VNet 中的 VM。 網路功能與在您的 VNet 中執行 VM 不同。 這就是為什麼這項功能尚無法使用某些網路功能的原因。

![區域 VNet 整合的運作方式][5]

啟用區域 VNet 整合時，您的應用程式會透過與正常相同的通道，對網際網路進行輸出呼叫。 應用程式屬性入口網站中所列的輸出位址是您的應用程式仍會使用的位址。 您的應用程式有哪些變更是對服務端點安全服務的呼叫，或 RFC 1918 位址會進入您的 VNet。 如果 WEBSITE_VNET_ROUTE_ALL 設定為1，則所有輸出流量都可以傳送至您的 VNet。

此功能僅針對每個背景工作支援一個虛擬介面。 每個工作者一個虛擬介面，表示每個 App Service 方案一個區域 VNet 整合。 相同 App Service 方案中的所有應用程式都可以使用相同的 VNet 整合。 如果您需要應用程式來連線至其他 VNet，則需要建立另一個 App Service 方案。 使用的虛擬介面不是客戶可直接存取的資源。

由於這項技術的運作方式，與 VNet 整合搭配使用的流量不會顯示在 Azure 網路監看員或 NSG 流量記錄中。

## <a name="gateway-required-vnet-integration"></a>閘道-必要的 VNet 整合

閘道所需的 VNet 整合支援連接到另一個區域中的 VNet 或傳統虛擬網路。 閘道-必要的 VNet 整合：

* 讓應用程式一次只能連接到一個 VNet。
* 最多可在 App Service 方案內整合五個 Vnet。
* 允許 App Service 方案中的多個應用程式使用相同的 VNet，而不會影響 App Service 方案所能使用的總次數。 如果您在相同的 App Service 方案中，有六個應用程式使用相同的 VNet，則會計算為使用的一個 VNet。
* 因閘道上的 SLA 而支援99.9% 的 SLA。
* 可讓您的應用程式使用已設定 VNet 的 DNS。
* 需要以 SSTP 點對站 VPN 設定虛擬網路路由式閘道，才能將它連線到應用程式。

您不能使用閘道所需的 VNet 整合：

* 使用 Linux 應用程式。
* 使用與 Azure ExpressRoute 連線的 VNet。
* 存取服務端點保護的資源。
* 具有同時支援 ExpressRoute 和點對站或站對站 Vpn 的共存閘道。

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>在您的 Azure 虛擬網路中設定閘道 ###

若要建立閘道：

1. 在您的 VNet 中[建立閘道子網路][creategatewaysubnet]。  

1. [建立 VPN 閘道][creategateway]。 選取路由式 VPN 類型。

1. [設定點對站位址][setp2saddresses]。 如果閘道不在基本 SKU 中，則必須在點對站組態中停用 IKEV2，而且必須選取 SSTP。 點對站位址空間必須是 RFC 1918 位址區塊 10.0.0.0/8、172.16.0.0/12 和 192.168.0.0/16。

如果您建立用於 App Service VNet 整合的閘道，則不需要上傳憑證。 建立閘道可能需要 30 分鐘。 在布建閘道之前，您將無法將應用程式與 VNet 整合。

### <a name="how-gateway-required-vnet-integration-works"></a>閘道所需的 VNet 整合如何運作

閘道所需的 VNet 整合是以點對站 VPN 技術為基礎。 點對站 Vpn 會限制對裝載應用程式之虛擬機器的網路存取。 應用程式只能透過混合式連線或透過 VNet 整合，將流量傳送到網際網路。 當您的應用程式使用入口網站設定為使用閘道所需的 VNet 整合時，會代表您管理複雜的協商，以在閘道和應用程式端建立及指派憑證。 結果是，用來裝載應用程式的工作者可以直接連線到所選 VNet 中的虛擬網路閘道。

![閘道所需的 VNet 整合如何運作][6]

### <a name="access-on-premises-resources"></a>存取內部部署資源

應用程式可以透過與具有站對站連線的 VNnet 整合以存取內部部署資源。 如果您使用閘道所需的 VNet 整合，請使用您的點對站位址區塊來更新您的內部部署 VPN 閘道路由。 第一次設定站對站 VPN 時，用來設定它的指令碼應該設定路由，包括點對站 VPN。 如果您在建立站對站 VPN 之後新增點對站 VPN，則需要手動更新路由。 有關如何執行此動作的詳細資料會因閘道而有所不同，此處並未說明。 您不能使用站對站 VPN 連線來設定 BGP。

區域 VNet 整合功能不需要進行其他設定，就能透過 VNet 連線到內部部署資源。 您只需要使用 ExpressRoute 或站對站 VPN，將您的 VNet 連線到內部部署資源。

> [!NOTE]
> 閘道所需的 VNet 整合功能不會將應用程式與具有 ExpressRoute 閘道的 VNet 整合。 即使在[共存模式][VPNERCoex]中設定 ExpressRoute 閘道，VNet 整合仍無法正常執行。 如果您需要透過 ExpressRoute 連線存取資源，請使用區域 VNet 整合功能，或在您的 VNet 中執行的[App Service 環境][ASE]。
> 
> 

### <a name="peering"></a>對等互連

如果您使用與區域 VNet 整合的對等互連，則不需要執行任何額外的設定。

如果您使用閘道所需的 VNet 與對等互連整合，您必須設定一些額外的專案。 若要設定對等互連以搭配您的應用程式使用：

1. 在您的應用程式連線到的 VNet 上新增對等互連連線。 新增對等互連連線時，請啟用 [**允許虛擬網路存取**]，然後選取 [**允許轉送的流量**] 和 [**允許閘道傳輸**]。
1. 在要對等互連至您所連接之 VNet 的 VNet 上新增對等互連連線。 當您在目的地 VNet 上新增對等互連連線時，啟用 [**允許虛擬網路存取**]，然後選取 [**允許轉送的流量**] 和 [**允許遠端閘道**]。
1. 移至入口網站中的**App Service 方案** > **網路** > **VNet 整合**UI。 選取您的應用程式連線到的 VNet。 在 [路由] 區段下，新增與您的應用程式所連線的 VNet 對等互連之 VNet 的位址範圍。

## <a name="manage-vnet-integration"></a>管理 VNet 整合

連接和中斷與 VNet 的連線是在應用層級。 會在多個應用程式之間影響 VNet 整合的作業位於 App Service 方案層級。 從應用程式 >**網路** > **VNet 整合**入口網站，您可以取得 VNet 的詳細資料。 您可以在**App Service 方案** > **網路** > **VNet 整合**入口網站中的 App Service 方案層級看到類似的資訊。

您可以在 VNet 整合實例的應用程式視圖中採取的唯一作業，就是將應用程式與目前連線的 VNet 中斷連接。 若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]****。 當您從 VNet 中斷連線時，您的應用程式會重新開機。 中斷連線不會變更您的 VNet。 不會移除子網或閘道。 如果您想要刪除 VNet，請先中斷應用程式與 VNet 的連線，並刪除其中的資源，例如閘道。

App Service 方案 VNet 整合 UI 會顯示您的 App Service 方案中，應用程式所使用的所有 VNet 整合。 若要查看每個 VNet 的詳細資料，請選取您想要的 VNet。 您可以在這裡針對閘道所需的 VNet 整合執行兩個動作：

* **同步網路**：同步處理網路作業僅用於與閘道相關的 VNet 整合功能。 執行同步網路作業可確保您的憑證和網路資訊同步。如果您新增或變更 VNet 的 DNS，請執行同步網路操作。 此操作會重新開機任何使用此 VNet 的應用程式。
* **新增路由**：新增路由會將輸出流量帶到您的 VNet。

### <a name="gateway-required-vnet-integration-routing"></a>閘道-必要的 VNet 整合路由
您的 VNet 中定義的路由會用來將流量從您的應用程式導向至您的 VNet。 若要將額外的輸出流量傳送至 VNet，請在這裡新增這些位址區塊。 這項功能僅適用于閘道所需的 VNet 整合。 當您使用閘道所需的 VNet 整合時，路由表不會影響您的應用程式流量，其方式與區域 VNet 整合相同。

### <a name="gateway-required-vnet-integration-certificates"></a>閘道-必要的 VNet 整合憑證
啟用閘道所需的 VNet 整合時，需要交換憑證以確保連線的安全性。 除了憑證以外，還有 DNS 組態、路由，以及其他描述網路的類似項目。

如果憑證或網路資訊已變更，請選取 [**同步網路**]。 當您選取 [**同步網路**] 時，您會導致應用程式與 VNet 之間的連線短暫中斷。 如果應用程式未重新啟動，失去連線會導致您的網站無法正常運作。

## <a name="pricing-details"></a>價格詳細資料
除了 App Service 方案定價層費用以外，區域 VNet 整合功能不需額外付費。

使用閘道所需的 VNet 整合功能時，有三個費用相關：

* **App Service 方案定價層費用**：您的應用程式必須是標準、Premium 或 PremiumV2 App Service 方案。 如需這些成本的詳細資訊，請參閱[App Service 定價][ASPricing]。
* **資料傳輸成本**：即使 VNet 位於相同的資料中心，也會收取資料輸出費用。 這些費用會在[資料傳輸定價詳細資料][DataPricing]中說明。
* **VPN 閘道成本**：點對站 VPN 需要虛擬網路閘道的成本。 如需詳細資訊，請參閱[VPN 閘道定價][VNETPricing]。

## <a name="troubleshooting"></a>疑難排解

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>自動化

適用于區域 VNet 整合的 CLI 支援。 若要存取下列命令，請[安裝 Azure CLI][installCLI]。

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

針對閘道所需的 VNet 整合，您可以使用 PowerShell 將 App Service 整合到 Azure 虛擬網路。 如需已準備好執行的腳本，請參閱[將 Azure App Service 中的應用程式連線到 Azure 虛擬網路](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)。


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
[privateendpoints]: networking/private-endpoint.md
