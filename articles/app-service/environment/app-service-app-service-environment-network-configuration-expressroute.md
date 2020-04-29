---
title: 設定 Azure ExpressRoute v1
description: 使用 Azure ExpressRoute 的 PowerApps App Service 環境的網路設定。 本檔僅為使用舊版 v1 ASE 的客戶提供。
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fc11c6932d625b119ad933f5d4d128b4355530c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804430"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>搭配 Azure ExpressRoute 之 PowerApps 的 App Service 環境網路設定詳細資料

客戶可以將 [Azure ExpressRoute][ExpressRoute] 線路連接至其虛擬網路基礎結構，以將其內部部署網路延伸至 Azure。 「App Service 環境」會建立在[虛擬網路][virtualnetwork]基礎結構的子網路中。 在「App Service 環境」上執行的應用程式會針對只能透過 ExpressRoute 連線存取的後端資源，建立安全連線。  

您可以在下列案例中建立「App Service 環境」：
- Azure Resource Manager 虛擬網路。
- 傳統部署模型虛擬網路。
- 使用公用位址範圍或 RFC1918 位址空間 (亦即私人位址) 的虛擬網路。 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>需要的網路連線

「App Service 環境」具有網路連線需求，而在連線至 ExpressRoute 的虛擬網路中可能一開始並不符合這些需求。

「App Service 環境」需要有下列網路連線設定，才能正常運作：

* 在連接埠 80 和 443 上透過網路連出至全球「Azure 儲存體」端點的能力。 這些端點位於與「App Service 環境」相同的區域中，也位於其他 Azure 區域中。 「Azure 儲存體」端點會在下列 DNS 網域下解析：table.core.windows.net、blob.core.windows.net、queue.core.windows.net 及 file.core.windows.net。  

* 位於連接埠 445 的 Azure 檔案服務的輸出網路連線

* 透過網路連出至位於與「App Service 環境」相同區域之 Azure SQL Database 端點的能力。 SQL Database 端點會在 database.windows.net 網域下解析，這需要連接埠 1433、11000-11999 及 14000-14999 的開放存取權。 如需有關 SQL Database V12 連接埠使用方式的詳細資料，請參閱 [ADO.NET 4.5 超過 1433 以外的連接埠](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)。

* 透過網路連出至 Azure 管理平面端點 (Azure 傳統部署模型和 Azure Resource Manager 端點) 的能力。 對這些端點的連線能力包括 management.core.windows.net 和 management.azure.com 網域。 

* 透過網路連出至 ocsp.msocsp.com、mscrl.microsoft.com 及 crl.microsoft.com 網域的能力。 需要連線至這些網域，才能支援 TLS 功能。

* 虛擬網路的 DNS 設定必須能夠解析本文中所提及的所有端點和網域。 如果無法解析端點，建立「App Service 環境」時就會失敗。 所有現有的「App Service 環境」都會標示為狀況不良。

* 需要有連接埠 53 的輸出存取，才能與 DNS 伺服器通訊。

* 如果 VPN 閘道的另一端有自訂 DNS 伺服器存在，則必須可從包含「App Service 環境」的子網路連線至該 DNS 伺服器。 

* 連出網路路徑不可經過內部公司 Proxy，也不可在內部部署環境使用強制通道。 這些動作會變更來自「App Service 環境」的有效連出網路流量 NAT 位址。 變更「App Service 環境」連出網路流量的 NAT 位址會導致許多端點的連線失敗。 「App Service 環境」的建立會失敗。 所有現有的「App Service 環境」都會標示為狀況不良。

* 必須允許透過連入網路存取「App Service 環境」的必要連接埠。 如需詳細資料，請參閱[如何控制 App Service 環境的連入流量][requiredports]。

為了符合 DNS 需求，請務必為虛擬網路設定及維護有效的 DNS 基礎結構。 如果 DNS 設定在建立「App Service 環境」後已變更，開發人員可以強制「App Service 環境」套用新的 DNS 設定。 您可以在 [Azure 入口網站][NewPortal]中，使用「App Service 環境」管理底下的 [重新啟動]**** 圖示，來觸發輪流的環境重新啟動。 此重新啟動會讓環境套用新的 DNS 設定。

為了符合連入網路存取需求，請在「App Service 環境」子網路上設定[網路安全性群組 (NSG)][NetworkSecurityGroups]。 NSG 可允許必要的存取權以[控制 App Service 環境的連入流量][requiredports]。

## <a name="outbound-network-connectivity"></a>連出網路連線

新建立的 ExpressRoute 線路預設會通告一個允許連出網際網路連線的預設路由。 「App Service 環境」可以使用此設定來連線至其他 Azure 端點。

常見的客戶設定是定義其自己的預設路由 (0.0.0.0/0)，以強制連出網際網路流量在內部部署環境中流動。 此流量必然會中斷「App Service 環境」。 連出流量不是在內部部署環境中遭到封鎖，就是經由 NAT 變成一組無法再與各種 Azure 端點搭配運作的無法辨識位址。

解決方法是在子網路上定義一或多個包含「App Service 環境」的使用者定義路由 (UDR)。 UDR 會定義子網路特定路由，系統將採用此特定路由，而非預設路由。

如果可行，請使用下列設定：

* ExpressRoute 設定會通告 0.0.0.0/0。 此設定預設會使用強制通道來傳送內部部署環境中的所有連出流量。
* 套用至包含「App Service 環境」之子網路的 UDR 會使用網際網路的下一個躍點類型定義 0.0.0.0/0。 本文稍後會提供此設定的範例。

此設定的組合效果是會讓子網路層級的 UDR 優先於 ExpressRoute 強制通道。 確保了來自「App Service 環境」的連出網際網路存取權。

> [!IMPORTANT]
> UDR 中定義的路由必須足夠明確，其優先順序才會高於 ExpressRoute 設定所通告的任何路由。 下一節所述的範例使用廣泛的 0.0.0.0/0 位址範圍。 路由通告如果使用更明確的位址範圍，便可能不小心覆寫此範圍。
> 
> 不支援使用「App Service 環境」搭配會將路由從公用對等互連路徑交叉通告至私人對等互連路徑的 ExpressRoute 設定。 已設定公用對等互連的 ExpressRoute 設定會從 Microsoft 收到一大組 Microsoft Azure IP 位址範圍的路由通告。 如果在私人對等互連路徑上交叉通告這些位址範圍，則來自「App Service 環境」子網路的所有連出網路封包都會透過強制通道傳送至客戶的內部部署網路基礎結構。 「App Service 環境」目前不支援此網路流量。 但有一個解決方案，就是停止將路由從公用對等互連路徑交叉通告至私人對等互連路徑。
> 
> 

如需有關使用者定義路由的背景資訊，請參閱[虛擬網路流量路由][UDROverview]。  

若要了解如何建立及設定使用者定義的路由，請參閱[使用 PowerShell 以路由表路由傳送網路流量][UDRHowTo]。

## <a name="udr-configuration"></a>UDR 設定

本節示範「App Service 環境」的範例 UDR 設定。

### <a name="prerequisites"></a>先決條件

* 從 [Azure 下載頁面][AzureDownloads]安裝 Azure PowerShell。 選擇日期為 2015 年 6 月或之後的下載項目。 在 [**命令列工具** > ] [**Windows PowerShell**] 底下，選取 [**安裝**] 以安裝最新的 PowerShell Cmdlet。

* 建立一個專供「App Service 環境」使用的唯一子網路。 唯一子網路可確保套用至子網路的 UDR 只會為「App Service 環境」開放連出流量。

> [!IMPORTANT]
> 請只在完成設定步驟之後才部署「App Service 環境」。 這些步驟可確保在您嘗試部署「App Service 環境」之前，連出網路連線已可供使用。

### <a name="step-1-create-a-route-table"></a>步驟1：建立路由表

在「美國西部」Azure 區域建立一個名為 **DirectInternetRouteTable** 的路由表，如以下程式碼片段所示：

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>步驟2：在資料表中建立路由

將路由新增至路由表以啟用連出網際網路存取。  

設定對網際網路的連出存取。 定義 0.0.0.0/0 的路由，如以下程式碼片段所示：

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 是一個廣泛的位址範圍。 此範圍會由 ExpressRoute 所通告的更明確位址範圍所覆寫。 具有 0.0.0.0/0 路由的 UDR 應該與只通告 0.0.0.0/0 的 ExpressRoute 設定搭配使用。 

或者，您也可以下載 Azure 目前所使用的完整 CIDR 範圍清單。 您可以從 [Microsoft 下載中心][DownloadCenterAddressRanges]取得所有 Azure IP 位址範圍的 XML 檔案。  

> [!NOTE]
>
> Azure IP 位址範圍會隨著時間變更。 使用者定義路由必須定期進行手動更新，才能保持同步。
>
> 單一 UDR 的預設上限為 100 個路由。 您必須「摘要」Azure IP 位址範圍，以不超出 100 個路由的限制。 UDR 定義的路由必須比 ExpressRoute 連線所通告的路由更明確。
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>步驟3：將資料表與子網建立關聯

將路由表與您要部署「App Service 環境」的子網路建立關聯。 此命令會將 **DirectInternetRouteTable** 路由表與將包含「App Service 環境」的 **ASESubnet** 子網路建立關聯。

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>步驟4：測試並確認路由

將路由表繫結至子網路之後，請測試並確認路由。

將虛擬機器部署至子網路，並確認符合下列情況：

* 本文中所述連至 Azure 及非 Azure 端點的連出流量**不會**透過 ExpressRoute 線路往下流動。 如果來自子網路的連出流量在內部部署環境中使用強制通道，則「App Service 環境」的建立將一律會失敗。
* 本文中所述的端點 DNS 查閱全部都正確解析。 

在您完成設定步驟並確認路由之後，請刪除虛擬機器。 「App Service 環境」建立後，子網路必須是「空的」。

現在，您已做好部署「App Service 環境」的準備！

## <a name="next-steps"></a>後續步驟

若要開始使用「適用於 PowerApps 的 App Service 環境」，請參閱 [App Service 環境簡介][IntroToAppServiceEnvironment]。

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
