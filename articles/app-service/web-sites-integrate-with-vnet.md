---
title: 套用與 Azure 虛擬網路整合
description: 將 Azure 應用服務中的應用與 Azure 虛擬網路整合。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4866397af244ffb3c6aa9c7547b0a9413b10ccfd
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604871"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>套用與 Azure 虛擬網路整合

本文介紹 Azure 應用服務 VNet 集成功能以及如何在[Azure 應用服務](https://go.microsoft.com/fwlink/?LinkId=529714)中使用應用進行設置。 使用[Azure 虛擬網路][VNETOverview](VNet),可以將許多 Azure 資源放在非 Internet 可路由網路中。

Azure 應用服務有兩種變體:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>開啟 VNet 整合

1. 轉到應用服務門戶中的**網路**UI。 在**VNet 整合**下,選擇 **「按一次設定」。**

1. 選取 [新增 VNet]****。

   ![選擇 VNet 整合][1]

1. 下拉清單包含同一區域中訂閱中的所有 Azure 資源管理器虛擬網路。 下面是所有其他區域中的資源管理器虛擬網路的清單。 選擇要整合的 VNet。

   ![選擇 VNet][2]

   * 如果 VNet 位於同一區域中,請創建新子網或選擇空的預先存在的子網。
   * 要選擇另一個區域中的 VNet,必須啟用 VNet 閘道,並啟用指向網站。
   * 要與經典 VNet 整合,請選擇 **「 按一下此處連接到經典 VNet」** 而不是選擇**虛擬網路**下拉清單。 選擇您想要的經典虛擬網路。 目標 VNet 必須已啟用啟用點對點的虛擬網路閘道。

    ![選擇傳統 VNet][3]

整合期間，會重新啟動您的應用程式。 集成完成後,您將在要集成的 VNet 上看到詳細資訊。

## <a name="regional-vnet-integration"></a>區域 VNet 整合

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>區域 VNet 整合的工作原理

應用服務中的應用託管在輔助角色上。 基本和更高的定價計劃是專用的託管計劃,其中沒有其他客戶的工作負載在同一工作人員上運行。 區域 VNet 整合的工作原理是安裝具有委派子網中位址的虛擬介面。 由於 from 位址位於 VNet 中,因此它可以像 VNet 中的 VM 一樣在 VNet 中或透過 VNet 訪問大多數內容。 網路實現不同於在 VNet 中運行 VM。 這就是為什麼某些網路功能尚未用於此功能的原因。

![區域 VNet 整合的工作原理][5]

啟用區域 VNet 整合後,你的應用會透過與正常相同的管道向 Internet 進行出站呼叫。 應用屬性門戶中列出的出站位址是應用仍在使用的位址。 應用的更改是對服務終結點安全服務的調用,或 VNet 中輸入的 RFC 1918 位址。 如果WEBSITE_VNET_ROUTE_ALL設置為 1,則所有出站流量都可以發送到 VNet。

該功能僅支援每個工作人員一個虛擬介面。 每個工作人員一個虛擬介面意味著每個應用服務計劃的一個區域 VNet 集成。 同一應用服務計劃中的所有應用都可以使用相同的 VNet 整合。 如果需要應用連接到其他 VNet,則需要創建另一個應用服務計畫。 使用的虛擬介面不是客戶可以直接存取的資源。

由於此技術運行方式的性質,與 VNet 整合使用的流量不會顯示在 Azure 網路觀察程式或 NSG 串流日誌中。

## <a name="gateway-required-vnet-integration"></a>閘道所需的 VNet 整合

閘道所需的 VNet 整合支援連接到另一個區域中的 VNet 或經典虛擬網路。 閘道所需的 VNet 整合:

* 使應用一次只能連接到一個 VNet。
* 使最多五個 VNet 能夠整合到應用服務計畫中。
* 允許應用服務計畫中的多個應用使用同一 VNet,而不會影響應用服務計畫可以使用的總數。 如果同一應用服務計劃中有六個應用使用相同的 VNet,則這計為正在使用的一個 VNet。
* 由於閘道上的 SLA,支援 99.9% 的 SLA。
* 使應用能夠使用 VNet 配置的 DNS。
* 需要使用 SSTP 點對點 VPN 配置的基於虛擬網路路由的閘道才能連接到應用。

無法使用閘道所需的 VNet 整合:

* 使用 Linux 應用程式。
* 使用 Azure ExpressRoute 連接的 VNet。
* 訪問服務終結點安全資源。
* 具有同時支援 ExpressRoute 和點對點或網站到網站 VPN 的共存閘道。

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>在 Azure 虛擬網路中設定閘道 ###

若要建立閘道：

1. 在您的 VNet 中[建立閘道子網路][creategatewaysubnet]。  

1. [建立 VPN 閘道][creategateway]。 選取路由式 VPN 類型。

1. [設定點對點位址][setp2saddresses]。 如果閘道不在基本 SKU 中，則必須在點對站組態中停用 IKEV2，而且必須選取 SSTP。 點對點位址空間必須位於 RFC 1918 位址塊 10.0.0.0/8、172.16.0.0/12 和 192.168.0.0/16 中。

如果創建閘道以用於應用服務 VNet 整合,則無需上傳證書。 建立閘道可能需要 30 分鐘。 在預先接閘道之前,您將無法將應用與 VNet 整合。

### <a name="how-gateway-required-vnet-integration-works"></a>閘道要求的 VNet 整合的工作原理

閘道所需的 VNet 整合建構在點對點 VPN 技術之上。 點對點 VPN 限制對承載應用的虛擬機器的網路存取。 應用僅限於透過混合連接或透過 VNet 整合將流量發送到網路。 當應用配置了門戶以使用閘道所需的 VNet 整合時,將代表您管理複雜的協商,以在閘道和應用程式端創建和分配證書。 結果是,用於託管應用的工作人員能夠直接連接到所選 VNet 中的虛擬網路閘道。

![閘道要求的 VNet 整合的工作原理][6]

### <a name="access-on-premises-resources"></a>存取本地資源

應用程式可以透過與具有站對站連線的 VNnet 整合以存取內部部署資源。 如果使用閘道所需的 VNet 整合,請使用指向網站的位址區塊更新本地 VPN 閘道網路由。 第一次設定站對站 VPN 時，用來設定它的指令碼應該設定路由，包括點對站 VPN。 如果您在建立站對站 VPN 之後新增點對站 VPN，則需要手動更新路由。 有關如何執行此操作的詳細資訊因閘道而異,此處未對此進行說明。 不能使用網站到網站 VPN 連接配置 BGP。

區域 VNet 整合功能無需額外配置即可透過 VNet 到本地資源進行訪問。 您只需使用 ExpressRoute 或網站到網站 VPN 將 VNet 連接到本地資源即可。

> [!NOTE]
> 閘道所需的 VNet 整合功能不會將應用與具有 ExpressRoute 閘道的 VNet 整合。 即使 ExpressRoute 閘道配置為[共存模式][VPNERCoex],VNet 整合也不起作用。 如果需要透過 ExpressRoute 連線存取資源,請使用 VNet 中執行的區域 VNet 整合功能或[套用服務環境][ASE]。
> 
> 

### <a name="peering"></a>對等互連

如果對等互連區域 VNet 集成,則無需執行任何其他配置。

如果使用閘道所需的 VNet 整合進行對等互連,則需要配置一些其他專案。 若要設定對等互連以搭配您的應用程式使用：

1. 在您的應用程式連線到的 VNet 上新增對等互連連線。 新增對等互連連線時,啟用**允許虛擬網路存取**,然後選擇 **「允許轉發流量**」和「**允許閘道傳輸**」。
1. 在 VNet 上添加對等互連連接,該連接將與您連接到的 VNet 進行對等互連。 在目標 VNet 上新增對等互連連線時,啟用**允許虛擬網路存取**並選擇 **「允許轉發流量**」和「**允許遠端閘道**」。
1. 轉到門戶中的**應用服務計劃** > **網路** > **VNet 集成**UI。 選取您的應用程式連線到的 VNet。 在路由部分下,添加與應用連接到的 VNet 對等的 VNet 的位址範圍。

## <a name="manage-vnet-integration"></a>管理 VNet 整合

與 VNet 連接和斷開連接處於應用級別。 可能影響跨多個應用的 VNet 整合的操作處於應用服務計畫等級。 從**應用>網络** > **VNet 整合**門戶,您可以在 VNet 上獲取詳細資訊。 您可以在**應用服務計畫** > **網路** > **VNet 整合**門戶的應用服務計畫等級看到類似的資訊。

在 VNet 整合實體的應用檢視中,唯一可以採用的操作是斷開應用與當前連接到的 VNet 的連接。 若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]****。 與 VNet 斷開連接時,你的應用將重新啟動。 中斷連線不會變更您的 VNet。 子網或閘道未被刪除。 如果隨後要刪除 VNet,請先斷開應用與 VNet 的連接,然後刪除其中的資源,如閘道。

應用服務計畫 VNet 整合 UI 顯示應用服務計畫中應用使用的所有 VNet 整合。 要查看每個 VNet 的詳細資訊,請選擇您感興趣的 VNet。 您可以在此執行兩個操作,以便進行閘道所需的 VNet 整合:

* **同步網路**:同步網路操作僅用於閘道相關的 VNet 整合功能。 執行同步網路操作可確保證書和網路資訊同步。如果添加或更改 VNet 的 DNS,請執行同步網路操作。 此操作將重新啟動使用此 VNet 的任何應用。
* **添加路由**:添加路由會驅動出站流量到 VNet。

### <a name="gateway-required-vnet-integration-routing"></a>閘道所需的 VNet 整合路由
VNet 中定義的路由用於從應用將流量定向到 VNet。 要向 VNet 發送其他出站流量,請在此處添加這些位址塊。 此功能僅適用於閘道所需的 VNet 整合。 當您使用閘道所需的 VNet 整合時,路由表不會影響應用流量,就像它們使用區域 VNet 整合一那樣。

### <a name="gateway-required-vnet-integration-certificates"></a>閘道所需的 VNet 整合憑證
啟用閘道所需的 VNet 整合後,需要交換證書以確保連接的安全性。 除了憑證以外，還有 DNS 組態、路由，以及其他描述網路的類似項目。

如果證書或網路資訊已更改,請選擇 **「同步網路**」。。 選擇**同步網路**時,會導致應用和 VNet 之間的連接短暫中斷。 如果應用程式未重新啟動，失去連線會導致您的網站無法正常運作。

## <a name="pricing-details"></a>價格詳細資料
除了應用服務計劃定價層費用之外,區域 VNet 集成功能無需支付額外費用。

三項費用與使用閘道所需的 VNet 整合功能有關:

* **應用服務計劃定價層費用**:你的應用必須處於標準、高級或高級 V2 應用服務計劃中。 有關這些成本的詳細資訊,請參閱[應用服務定價][ASPricing]。
* **數據傳輸成本**:即使 VNet 位於同一數據中心中,數據出口也會收取費用。 這些費用在[數據傳輸定價詳細資訊][DataPricing]中描述。
* **VPN 閘道成本**:點對點 VPN 需要虛擬網路閘道的成本。 有關詳細資訊,請參閱[VPN 閘道定價][VNETPricing]。

## <a name="troubleshooting"></a>疑難排解

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>自動化

CLI 支援可用於區域 VNet 整合。 要存取以下指令[,請安裝 Azure CLI][installCLI]。

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

對於閘道所需的 VNet 整合,可以使用 PowerShell 將應用服務與 Azure 虛擬網路整合。 有關即用即用腳稿,請參閱將[Azure 應用服務中的應用連線到 Azure 虛擬網路](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)。


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
