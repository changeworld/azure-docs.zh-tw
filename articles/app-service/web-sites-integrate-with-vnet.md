---
title: 將應用與 Azure 虛擬網路集成
description: 將 Azure 應用服務中的應用與 Azure 虛擬網路集成。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673217"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>將您的應用程式與 Azure 虛擬網路整合
本文檔介紹 Azure 應用服務虛擬網路集成功能以及如何在[Azure 應用服務](https://go.microsoft.com/fwlink/?LinkId=529714)中使用應用進行設置。 [Azure 虛擬網路][VNETOverview]（VNet） 允許您將許多 Azure 資源放在非 Internet 可路由網路中。  

Azure 應用服務有兩個變體。

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>啟用 VNet 集成 

1. 轉到應用服務門戶中的網路 UI。 在 VNet 集成下，選擇 *"按一下此處進行配置"。* 

1. 選取 [新增 VNet]****。  

   ![選擇 VNet 集成][1]

1. 下拉清單將包含同一區域中訂閱中的所有資源管理器 VNet，下面是所有其他區域中所有資源管理器 VNet 的清單。 選擇要集成的 VNet。

   ![選擇 VNet][2]

   * 如果 VNet 位於同一區域中，則創建新子網或選取空預先存在的子網。 

   * 要選擇另一個區域中的 VNet，必須啟用指向網站的虛擬網路閘道。

   * 要與經典 VNet 集成，請選擇"**按一下此處連接到經典 VNet"，** 而不是按一下 VNet 下拉清單。 選擇所需的經典 VNet。 目標 VNet 必須已預配了啟用指向網站的虛擬網路閘道。

    ![選擇經典 VNet][3]
    
整合期間，會重新啟動您的應用程式。  集成完成後，您將在集成的 VNet 上看到詳細資訊。 

應用與 VNet 集成後，它將使用 VNet 配置的相同 DNS 伺服器，除非它是 Azure DNS 私人區域。 當前無法使用 VNet 集成與 Azure DNS 私人區域。

## <a name="regional-vnet-integration"></a>區域 VNet 集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>區域 VNet 集成的工作原理

應用服務中的應用託管在輔助角色上。 基本和更高的定價計畫是專用的託管計畫，其中沒有其他客戶工作負載在同一工作人員上運行。 區域 VNet 集成的工作原理是安裝具有委派子網中位址的虛擬介面。 由於 from 位址位於 VNet 中，因此它可以像 VNet 中的 VM 一樣，訪問 VNet 中或通過 VNet 中的大多數內容。 網路實現不同于在 VNet 中運行 VM，這就是為什麼在使用此功能時某些網路功能尚不可用的原因。

![區域 VNet 集成的工作原理][5]

啟用區域 VNet 集成後，你的應用仍將通過與正常相同的管道向 Internet 進行出站呼叫。 應用屬性門戶中列出的出站位址仍然是應用使用的位址。 應用有哪些更改，對服務終結點安全服務的呼叫或 RFC 1918 位址將進入 VNet。 如果WEBSITE_VNET_ROUTE_ALL設置為 1，則所有出站流量都可以發送到您的 VNet。 

該功能僅支援每個工作人員一個虛擬介面。  每個工作人員一個虛擬介面意味著每個應用服務方案的一個區域 VNet 集成。 同一應用服務方案中的所有應用都可以使用相同的 VNet 集成，但如果需要應用連接到其他 VNet，則需要創建另一個應用服務方案。 使用的虛擬介面不是客戶可以直接存取的資源。

由於此技術運行方式的性質，與 VNet 集成一起使用的流量不會顯示在網路觀察程式或 NSG 流日誌中。  

## <a name="gateway-required-vnet-integration"></a>閘道需要 VNet 集成

閘道所需的 VNet 集成支援連接到另一個區域中的 VNet 或經典 VNet。 閘道所需的 VNet 集成： 

* 使應用一次只能連接到 1 個 VNet
* 使多達五個 VNet 集成到應用服務方案中 
* 允許應用服務方案中的多個應用使用同一 VNet，而不會影回應用服務方案可以使用的總數。  如果同一應用服務方案中有六個應用使用相同的 VNet，則這計為正在使用的 1 個 VNet。 
* 由於閘道上的 SLA 支援 99.9% 的 SLA
* 使應用能夠使用 VNet 配置的 DNS
* 需要使用 SSTP 點對點 VPN 配置的基於虛擬網路路由的閘道才能連接到應用。 

不能使用閘道所需的 VNet 集成：

* 使用 Linux 應用程式
* 與快速路由連接 VNet 
* 訪問服務終結點安全資源
* 具有同時支援 ExpressRoute 並指向網站/網站到網站 VPN 的共存閘道

### <a name="set-up-a-gateway-in-your-vnet"></a>在 VNet 中設定閘道 ###

若要建立閘道：

1. 在您的 VNet 中[建立閘道子網路][creategatewaysubnet]。  

1. [創建 VPN 閘道][creategateway]。 選取路由式 VPN 類型。

1. [將點設定為網站位址][setp2saddresses]。 如果閘道不在基本 SKU 中，則必須在網站配置點中禁用 IKEV2，並且必須選擇 SSTP。 點到網站位址空間必須在 RFC 1918 位址塊中，10.0.0.0/8、172.16.0.0/12、192.168.0.0/16

如果只是創建用於應用服務 VNet 集成的閘道，則無需上載證書。 建立閘道可能需要 30 分鐘。 在佈建閘道之前，您將無法將應用程式與 VNet 整合。 

### <a name="how-gateway-required-vnet-integration-works"></a>閘道要求 VNet 集成的工作原理

閘道需要 VNet 集成構建在點對點 VPN 技術之上。 指向網站 VPN 將網路訪問限制為僅託管應用程式的虛擬機器。 應用程式會限制為僅透過混合式連線或透過 VNet 整合，將流量傳送至網際網路。 當應用配置了門戶以使用所需的閘道 VNet 集成時，將代表您管理複雜的協商，以在閘道和應用程式端創建和分配證書。 最終結果是，用於託管應用的工作人員能夠直接連接到所選 VNet 中的虛擬網路閘道。 

![閘道要求 VNet 集成的工作原理][6]

### <a name="accessing-on-premises-resources"></a>存取內部部署資源

應用程式可以透過與具有站對站連線的 VNnet 整合以存取內部部署資源。 如果使用所需的閘道 VNet 集成，則需要使用點對點位址塊更新本地 VPN 閘道路由。 第一次設定站對站 VPN 時，用來設定它的指令碼應該設定路由，包括點對站 VPN。 如果您在建立站對站 VPN 之後新增點對站 VPN，則需要手動更新路由。 此做法的詳細資料會根據閘道而有所不同，不在這裡詳述。 不能使用網站到網站 VPN 連接配置 BGP。

區域 VNet 集成功能無需額外配置即可通過 VNet 和本地進行訪問。 您只需使用 ExpressRoute 或網站到網站 VPN 將 VNet 連接到本地。 

> [!NOTE]
> 閘道所需的 VNet 集成功能不會將應用與具有 ExpressRoute 閘道的 VNet 集成。 即使是在[共存模式][VPNERCoex]中設定 ExpressRoute 閘道，VNet 整合仍無法運作。 如果需要通過 ExpressRoute 連接訪問資源，則可以使用在 VNet 中運行的區域 VNet 集成功能或[應用服務環境][ASE]。 
> 
> 

### <a name="peering"></a>對等互連

如果使用區域 VNet 集成對等互連，則不需要執行任何其他配置。 

如果使用閘道所需的 VNet 集成與對等互連，則需要配置一些其他專案。 若要設定對等互連以搭配您的應用程式使用：

1. 在您的應用程式連線到的 VNet 上新增對等互連連線。 新增對等互連連線時，啟用 [允許虛擬網路存取]**** 並核取 [允許轉寄的流量]**** 和 [允許閘道器傳輸]****。
1. 在 VNet 上新增對等互連連線，該 VNet 與您連線的 VNet 對等互連。 在目的地 VNet 上新增對等互連連線時，啟用 [允許虛擬網路存取]**** 並核取 [允許轉寄的流量]**** 和 [允許遠端閘道器]****。
1. 移至入口網站中的 [App Service 方案] > [網路] > [VNet 整合 UI]。  選取您的應用程式連線到的 VNet。 在路由區段中，新增與您的應用程式所連線到的 VNet 對等互連之 VNet 的位址範圍。  

## <a name="managing-vnet-integration"></a>管理 VNet 集成 

與 VNet 連接和斷開連接處於應用級別。 可在多個應用程式之間影響 VNet 整合的作業是在 App Service 方案層級上。 從"網路> VNet 集成門戶的應用>，您可以在 VNet 上獲取詳細資訊。 您可以在 ASP > 網路> VNet 集成門戶的 ASP 級別看到類似的資訊。

您可以在 VNet 整合的應用程式檢視中採取的唯一作業，就是中斷連接目前連接到 VNet 的應用程式。 若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]****。 當您從 VNet 中斷連線時，您的應用程式將重新啟動。 中斷連線不會變更您的 VNet。 未刪除子網或閘道。 如果隨後要刪除 VNet，則需要首先斷開應用與 VNet 的連接，然後刪除其中的資源，如閘道。 

ASP VNet 集成 UI 將顯示 ASP 中的應用使用的所有 VNet 集成。 若要查看每個 VNet 的詳細資料，按一下您感興趣的 VNet 即可。 對於閘道所需的 VNet 集成，您可以在此處執行兩個操作。

* **同步網路**。 同步網路操作僅適用于與閘道相關的 VNet 集成功能。 執行同步網路操作可確保證書和網路資訊同步。如果添加或更改 VNet 的 DNS，則需要執行**同步網路**操作。 這項作業將重新啟動任何使用此 VNet 的應用程式。
* **新增路由** 新增路由將驅動輸出流量進入 VNet。 

**閘道所需的 VNet 集成路由**VNet 中定義的路由用於從應用將流量定向到 VNet。 如果需要將額外的輸出流量傳送到 VNet，則可以在此處新增這些位址區塊。 此功能僅適用于閘道所需的 VNet 集成。 路由表在使用閘道時不會影回應用流量，就像使用區域 VNet 集成那樣需要 VNet 集成。

**閘道所需的 VNet 集成證書**啟用閘道要求 VNet 集成時，需要交換證書以確保連接的安全性。 除了憑證以外，還有 DNS 組態、路由，以及其他描述網路的類似項目。
如果憑證或網路資訊已變更，您需要按一下 [同步網路]。 按一下 [同步處理網路] 時，會導致應用程式與 VNet 之間的連線短暫中斷。 如果應用程式未重新啟動，失去連線會導致您的網站無法正常運作。 

## <a name="pricing-details"></a>價格詳細資料
區域 VNet 集成功能除 ASP 定價層費用外，無需支付額外費用。

使用閘道所需的 VNet 集成功能有三項相關費用：

* ASP 定價層費用 - 您的應用需要處於標準、高級或高級 V2 應用服務方案中。 您可以在這裡看到這些成本的詳細資料：[App Service 價格][ASPricing]。 
* 資料傳輸成本 - 即使 VNet 位於同一資料中心，資料出口也會收取費用。 [資料傳輸定價詳細資料][DataPricing]中會說明這些費用。 
* VPN 閘道成本 - 點對點 VPN 需要 VNet 閘道的成本。 如需詳細資訊，請參閱 [VPN 閘道定價][VNETPricing]頁面。

## <a name="troubleshooting"></a>疑難排解

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>自動化

CLI 支援區域 VNet 集成。 要訪問以下命令，[請安裝 Azure CLI][installCLI]。 

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

對於閘道所需的 VNet 集成，可以使用 PowerShell 將應用服務與 Azure 虛擬網路集成。 如需隨時可執行的指令碼，請參閱[將 Azure App Service 中的應用程式連線到 Azure 虛擬網路](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) \(英文\)。


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
