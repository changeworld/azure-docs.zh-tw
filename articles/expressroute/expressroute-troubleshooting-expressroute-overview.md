---
title: Azure 快速路由：驗證連接 - 故障排除指南
description: 此頁面提供 ExpressRoute 路線的端對端連線確認和疑難排解的指示。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330952"
---
# <a name="verifying-expressroute-connectivity"></a>確認 ExpressRoute 連線
本文將協助您確認 ExpressRoute 連線及針對連線問題進行疑難排解。 ExpressRoute 通過連接供應商通常提供的專用連線將本地網路擴展到 Microsoft 雲中。 ExpressRoute 連接傳統上涉及三個不同的網路區域，如下所示：

-   客戶網路
-   提供者網路
-   Microsoft 資料中心

> [!NOTE]
> 在 ExpressRoute 直接連接模型中（以 10/100 Gbps 頻寬提供），客戶可以直接連接到 Microsoft 企業邊緣 （MSEE） 路由器的埠。 因此，在直接連接模型中，只有客戶和 Microsoft 網路區域。
>


本文檔的目的是説明使用者識別是否存在連接問題以及存在連接問題的位置。 因此，説明尋求相應團隊的支援來解決問題。 如果需要 Microsoft 支援服務解決問題，請利用 [Microsoft 支援服務][Support]開啟支援票證。

> [!IMPORTANT]
> 本文件旨在協助診斷並修正簡單的問題。 它無法取代 Microsoft 支援服務。 如果無法使用本文提供的指引來解決問題，請利用 [Microsoft 支援服務][Support]開啟支援票證。
>
>

## <a name="overview"></a>總覽
下圖顯示使用 ExpressRoute 從客戶網路連至 Microsoft 網路的邏輯連線。
[![1]][1]

在上圖中，數字指出重要的網路點。 本文有時通過關聯的編號引用這些網路點。 根據快速路由連接模型 -雲交換主機位置、點對點乙太網連接或任意到任意 （IPVPN）），網路點 3 和 4 可以是交換器（第 2 層設備）或路由器（第 3 層設備）。 在直接連接模型中，沒有網路點 3 和 4;相反，CE （2） 通過暗光纖直接連接到 MsEE。 圖中的重要網路點分別是︰

1.  客戶計算裝置 (例如，伺服器或電腦)
2.  CE︰客戶邊緣路由器 
3.  PE (面對 CE)︰面對客戶邊緣路由器的提供者邊緣路由器/交換器。 在本文件中稱為 PE-CE。
4.  PE (面對 MSEE)︰面對 MSEE 的提供者邊緣路由器/交換器。 在本文件中稱為 PE-MSEE。
5.  MSEE：Microsoft Enterprise Edge (MSEE) ExpressRoute 路由器
6.  虛擬網路 (VNet) 閘道器
7.  Azure VNet 上的計算裝置

如果使用雲交換主機庫、點對點乙太網或直接連接模型，則 CE （2） 與 MSEE （5） 建立 BGP 對等互連。 

如果使用任意到任意 （IPVPN） 連接模型，PE-MSE （4） 與 MsEE （5） 建立 BGP 對等互連。 PE-MSEE 通過 IPVPN 服務提供者網路將從 Microsoft 接收的路由傳播回客戶網路。

> [!NOTE]
>為獲得高可用性，Microsoft 在 MsEE （5） 和 PE-MSE （4） 對之間建立完全冗余的並行連接。 還鼓勵客戶網路和 PE-C 對之間使用完全冗余並行網路路徑。 有關高可用性的詳細資訊，請參閱使用[ExpressRoute 設計高可用性][HA]的文章
>
>

以下是對 ExpressRoute 電路進行故障排除的邏輯步驟：

* [驗證電路配置和狀態](#verify-circuit-provisioning-and-state)
  
* [確認對等互連組態](#validate-peering-configuration)
  
* [驗證 ARP](#validate-arp)
  
* [確認 MSEE 上的 BGP 和路由](#validate-bgp-and-routes-on-the-msee)
  
* [確認流量](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>驗證電路配置和狀態
預配 ExpressRoute 電路在 CE/PE-MsEE （2）/（4） 和 MsEE （5） 之間建立冗余的第 2 層連接。 如需有關如何建立、修改、佈建、確認 ExpressRoute 路線的詳細資訊，請參閱[建立和修改 ExpressRoute 路線][CreateCircuit]一文。

>[!TIP]
>服務機碼可唯一識別 ExpressRoute 路線。 如果您需要 Microsoft 或 ExpressRoute 合作夥伴的説明來排除 ExpressRoute 問題，請提供服務金鑰以輕鬆識別電路。
>
>

### <a name="verification-via-the-azure-portal"></a>透過 Azure 入口網站進行確認
在 Azure 門戶中，打開 ExpressRoute 電路邊欄選項卡。 在邊欄選項卡![的 3][3]節中，ExpressRoute 要點如下螢幕截圖所示：

![4][4]    

在 ExpressRoute [基本資料] 中，[路線狀態]** 指出 Microsoft 端路線的狀態。 [提供者狀態]** 指出在服務提供者端是否「已佈建/未佈建」** 路線。 

[路線狀態]** 必須是 [已啟用]**，且[提供者狀態]** 必須是 [已佈建]**，ExpressRoute 路線才能運作。

> [!NOTE]
> 配置 ExpressRoute 電路後，如果*電路狀態*未處於啟用狀態，請與[Microsoft 支援][Support]。 另一方面，如果*提供程式狀態*處於未預配狀態，請與服務提供者聯繫。
>
>

### <a name="verification-via-powershell"></a>透過 PowerShell 進行確認
若要列出資源群組中的所有 ExpressRoute 路線，使用下列命令：

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>如果要查找資源組的名稱，可以使用命令*Get-AzResourceGroup*列出訂閱中的所有資源組來獲取它
>


若要選取資源群組中的特定 ExpressRoute 路線，使用下列命令：

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

範例回應：

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

若要確認 ExpressRoute 路線是否在運作，請特別注意下列欄位︰

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> 配置 ExpressRoute 電路後，如果*電路狀態*未處於啟用狀態，請與[Microsoft 支援][Support]。 另一方面，如果*提供程式狀態*處於未預配狀態，請與服務提供者聯繫。
>
>

## <a name="validate-peering-configuration"></a>確認對等互連組態
服務提供者完成快速路由電路的預配後，可以通過 CE/MSEE-PE （2）/（4） 和 MSEE （5） 之間的 ExpressRoute 電路創建多個基於 eBGP 的路由配置。 每個 ExpressRoute 電路可以具有：Azure 專用對等互連（Azure 中專用虛擬網路的流量）和/或 Microsoft 對等互連（PaS 和 SaaS 的公共終結點的流量）。 如需有關如何建立及修改路由組態的詳細資訊，請參閱[建立和修改 ExpressRoute 路線的路由][CreatePeering]一文。

### <a name="verification-via-the-azure-portal"></a>透過 Azure 入口網站進行確認

> [!NOTE]
> 在 IPVPN 連接模型中，服務提供者負責配置對等互連（第 3 層服務）。 在這樣的模型中，在服務提供者配置對等互連後，如果對等互連在門戶中為空，請嘗試使用門戶上的刷新按鈕刷新電路配置。 此操作將從電路中提取當前路由配置。 
>

在 Azure 門戶中，可以在 ExpressRoute 電路邊欄選項卡下檢查 ExpressRoute 電路對等互連的狀態。 在邊欄選項卡![的 3][3]節中，ExpressRoute 對等互連將列出，如下圖所示：

![5][5]

在前面的示例中，如所述 Azure 專用對等互連被預配，而 Azure 公共和對等互連未預配。 成功預配的對等互連上下文也將列出主點對點子網。 /30 子網用於 SEE 和 CE/PE-MSEE 的介面 IP 位址。 對於預配的對等互連，清單還指示上次修改配置的人員。 

> [!NOTE]
> 如果啟用對等互連失敗，請檢查分配的主子網和輔助子網是否與連結的 CE/PE-MSEE 上的配置匹配。 還要檢查在 MsEE 上是否使用了正確的*VlanId、AzureASN*和*PeerASN，* 以及這些值是否映射到連結的 CE/PE-MSEE 上使用的值。 *AzureASN* 如果選擇了 MD5 雜湊，則 MSEE 和 PE-MSEE/CE 對上的共用金鑰應相同。 出於安全原因，不會顯示以前配置的共用金鑰。 如果您需要更改 MSEE 路由器上的這些配置中的任何一個，請參閱為[ExpressRoute 電路創建和修改路由][CreatePeering]。  
>

> [!NOTE]
> 在為介面分配的 /30 子網上，Microsoft 將選擇 MSEE 介面子網的第二個可用 IP 位址。 因此，請確保子網的第一個可用 IP 位址已在對等 CE/PE-MSEE 上分配。
>


### <a name="verification-via-powershell"></a>透過 PowerShell 進行確認
若要取得 Azure 私人對等互連的組態詳細資料，使用下列命令︰

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

以下是已成功設定私人對等互連的回應範例︰

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 成功啟用的對等互連內容也會列出主要和次要位址前置詞。 /30 子網用於 SEE 和 CE/PE-MSEE 的介面 IP 位址。

若要取得 Azure 公用對等互連的組態詳細資料，使用下列命令︰

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

若要取得 Microsoft 對等互連的組態詳細資料，使用下列命令︰

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

如未設定對等互連，會顯示錯誤訊息。 未設定所述對等互連 (在此範例中是 Azure 公用互對等連) 的回應範例︰

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> 如果啟用對等互連失敗，請檢查分配的主子網和輔助子網是否與連結的 CE/PE-MSEE 上的配置匹配。 還要檢查在 MsEE 上是否使用了正確的*VlanId、AzureASN*和*PeerASN，* 以及這些值是否映射到連結的 CE/PE-MSEE 上使用的值。 *AzureASN* 如果選擇了 MD5 雜湊，則 MSEE 和 PE-MSEE/CE 對上的共用金鑰應相同。 出於安全原因，不會顯示以前配置的共用金鑰。 如果您需要更改 MSEE 路由器上的這些配置中的任何一個，請參閱為[ExpressRoute 電路創建和修改路由][CreatePeering]。  
>
>

> [!NOTE]
> 在為介面分配的 /30 子網上，Microsoft 將選擇 MSEE 介面子網的第二個可用 IP 位址。 因此，請確保子網的第一個可用 IP 位址已在對等 CE/PE-MSEE 上分配。
>

## <a name="validate-arp"></a>驗證 ARP

ARP 表提供特定對等互連的 IP 位址和 MAC 位址的映射。 適用於 ExpressRoute 線路對等互連的 ARP 表格能提供各個介面 (主要和次要) 的下列資訊：
* 內部部署路由器介面 IP 位址到 MAC 位址的對應
* ExpressRoute 路由器介面 IP 位址到 MAC 位址的對應
* 映射 ARP 表的年齡可説明驗證第 2 層配置並解決基本第 2 層連接問題。


有關如何查看 ExpressRoute 對等互連的 ARP 表以及如何使用這些資訊解決第 2 層連接問題，請參閱[資源管理器部署模型文檔中獲取 ARP 表][ARP]。


## <a name="validate-bgp-and-routes-on-the-msee"></a>確認 MSEE 上的 BGP 和路由

要從*專用*路由上下文*的主*路徑上的 MSEE 獲取路由表，請使用以下命令：

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

以下是回應範例：

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> 如果 EBGP 對等互連在 MSEE 和 CE/PE-MSEE 之間處於"活動"或"空閒"狀態，請檢查分配的主子子網和輔助對等子網是否與連結的 CE/PE-MSEE 上的配置匹配。 還要檢查在 MsEE 上是否使用了正確的*VlanId、AzureAsn*和*PeerAsn，* 以及這些值是否映射到連結的 PE-MSEE/CE 上使用的值。 *AzureAsn* 如果選擇了 MD5 雜湊，則 MSEE 和 CE/PE-MSEE 對上的共用金鑰應相同。 如果您需要更改 MSEE 路由器上的這些配置中的任何一個，請參閱為[ExpressRoute 電路創建和修改路由][CreatePeering]。
>


> [!NOTE]
> 如果某些目標無法通過對等互連到達，請檢查 MsEE 的路由表，瞭解相應的對等上下文。 如果路由表中存在匹配的首碼（可以是 NATed IP），則檢查路徑上是否存在阻止流量的防火牆/NSG/ACL。
>


下面的示例顯示不存在的對等互連的命令的回應：

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>確認流量
若要取得對等互連內容的主要和次要路徑的合併流量統計資料，包括進和出的位元組，使用下列命令︰

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

此命令的輸出範例如下：

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

對等互連不存在時，命令的輸出範例如下︰

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>後續步驟
如需詳細資訊或協助，請看看下列連結：

- [Microsoft 支援服務][Support]
- [創建和修改快速路由電路][CreateCircuit]
- [建立和修改 ExpressRoute 路線的路由][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "邏輯 Express 路由連線"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "所有資源圖示"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "概觀圖示"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute 基本資料螢幕擷取畫面範例"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute 基本資料螢幕擷取畫面範例"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





