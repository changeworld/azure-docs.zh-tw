---
title: 在 Azure（大型實例）上從虛擬網路連接到 SAP HANA 的連接 |微軟文檔
description: 從虛擬網路設置的連接，在 Azure 上使用 SAP HANA（大型實例）。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617191"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>將虛擬網路連線至 HANA 大型執行個體

建立 Azure 虛擬網路之後，您可以將該網路連線到 Azure 大型執行個體上的 SAP HANA。 在虛擬網路上建立 Azure ExpressRoute 閘道。 此閘道使您能夠將虛擬網路連結到連接到 HANA 大型實例戳上的客戶租戶的 ExpressRoute 電路。

> [!NOTE] 
> 此步驟可能需要 30 分鐘的時間才能完成。 會在指定的 Azure 訂用帳戶中建立新閘道，然後連線到指定的 Azure 虛擬網路。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

如果閘道已經存在，請檢查其是否為 ExpressRoute 閘道。 如果它不是 ExpressRoute 閘道，請刪除閘道，並將其重新創建為 ExpressRoute 閘道。 如果已建立 ExpressRoute 閘道，請參閱本文的下一節＜連結虛擬網路＞。 

- 使用 [Azure 入口網站](https://portal.azure.com/) 或 PowerShell 來建立連線到您虛擬網路的 ExpressRoute VPN 閘道。
  - 如果使用 Azure 門戶，請添加新**的虛擬網路閘道**，然後選擇**ExpressRoute**作為閘道類型。
  - 如果您是使用 PowerShell，請先下載並使用最新版的 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 
 
下列命令會建立 ExpressRoute 閘道。 前面帶有的文本_$_ 是使用者定義的變數，應使用您的特定資訊進行更新。

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

在此範例中，使用了 HighPerformance 閘道 SKU。 您的選項為 HighPerformance 或 UltraPerformance，因為 SAP HANA on Azure (大型執行個體) 僅支援這些閘道 SKU。

> [!IMPORTANT]
> 對於類型 II 類別 SKU 的 HANA 大型執行個體，您必須使用 UltraPerformance 閘道 SKU。

## <a name="link-virtual-networks"></a>連結虛擬網路

Azure 虛擬網路現在具有 ExpressRoute 閘道。 使用 Microsoft 提供的授權資訊將 ExpressRoute 閘道連接到 SAP HANA 大型實例 ExpressRoute 電路。 您可以使用 Azure 入口網站或 PowerShell 來連線。 PowerShell 說明如下。 

為每個快速路由閘道使用不同的 AuthGUID 運行以下命令。 下列指令碼顯示的前兩個項目來自 Microsoft 所提供的資訊。 此外，AuthGUID 對每個虛擬網路及其閘道來說都是特定的。 如果要添加另一個 Azure 虛擬網路，則需要為 ExpressRoute 電路獲取另一個 AuthID，該電路從 Microsoft 將 HANA 大型實例連接到 Azure。 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> 命令"新建-Az虛擬網路閘道連接"中的最後一個參數**是**啟用 ExpressRoute 快速路徑的新參數。 一種功能，可減少 HANA 大型實例單元和 Azure VM 之間的網路延遲。 該功能于 2019 年 5 月添加。 有關詳細資訊，請查看文章 SAP [HANA（大型實例）網路架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)。 在運行命令之前，請確保正在運行最新版本的 PowerShell Cmdlet。

若要將閘道連線到與訂用帳戶關聯的多個 ExpressRoute 線路，可能需要執行此步驟多次。 例如，您可能要將相同的虛擬網路閘道連線到 ExpressRoute 線路，而此線路會將虛擬網路連線到內部部署網路。

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>將快速路由快速路徑應用於現有的 HANA 大型實例快速路由電路
到目前為止，文檔說明了如何將使用 HANA 大型實例部署創建的新 ExpressRoute 電路連接到其中一個 Azure 虛擬網路的 Azure ExpressRoute 閘道。 但許多客戶已經設置了其 ExpressRoute 電路，並且已經將其虛擬網路連接到 HANA 大型實例。 由於新的 ExpressRoute 快速路徑正在減少網路延遲，因此建議您應用更改來使用此功能。 連接新的 ExpreesRoute 電路和更改現有 ExpressRoute 電路的命令是相同的。 因此，您需要運行此系列 PowerShell 命令，以更改要使用的現有電路 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

請務必添加上面顯示的最後一個參數，以啟用 ExpressRoute 快速路徑功能


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
您希望為兩種方案中的一種或兩種方案啟用全域覆蓋：

 - HANA 系統複製，無需任何其他代理或防火牆
 - 在兩個不同區域的 HANA 大型實例單元之間複本備份以執行系統副本或系統刷新

您需要考慮：

- 您需要提供 /29 位址空間的位址空間範圍。 該位址範圍可能與到目前為止用於將 HANA 大型實例連接到 Azure 的任何其他位址空間範圍重疊，並且不得與您在 Azure 或本地其他地方使用的任何 IP 位址範圍重疊。
- ASN（自治系統編號）有一個限制，可用於將本地路由通告給 HANA 大型實例。 您的本地不得通告任何具有 65000 + 65020 或 65515 範圍內的專用 ASN 路由。 
- 對於連接本地直接存取 HANA 大型實例的情況，您需要計算將您連接到 Azure 的電路的費用。 有關價格，請查看[全域覆蓋附加元件](https://azure.microsoft.com/pricing/details/expressroute/)的價格。

要將其中一個或兩個方案應用於部署，請打開 Azure 的支援消息，如打開[HANA 大型實例的支援請求](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)中所述

需要的資料以及 Microsoft 需要使用的關鍵字才能根據您的請求進行路由和執行，如下所示：

- 服務：SAP HANA 大型實例
- 問題類型：配置和設置
- 問題子類型：上面未列出我的問題
- 主題"修改我的網路 - 添加全球覆蓋"
- 詳細資訊："將全域覆蓋到 HANA 大型實例添加到 HANA 大型實例租戶"或"將全域覆蓋添加到本地到 HANA 大型實例租戶"。
- HANA 大型實例到 HANA 大型實例租戶案例的其他詳細資訊：您需要定義兩個要連接的租戶所在的**Azure 區域****，** 並且需要提交 **/29 IP 位址範圍**
- 本地到 HANA 大型實例租戶案例的其他詳細資訊：您需要定義要直接連接到的 HANA 大型實例租戶部署的**Azure 區域**。 此外，您需要提供在本地和 Azure 之間建立 ExpressRoute 電路時收到的**Auth GUID**和**電路對等體 ID。** 此外，您需要命名**ASN**。 最後一個交付結果是 ExpressRoute 全球覆蓋的 **/29 IP 位址範圍**。

> [!NOTE]
> 如果要處理這兩種情況，則需要提供兩個不同的 /29 IP 位址範圍，這些範圍與迄今為止使用的任何其他 IP 位址範圍不重疊。 




## <a name="next-steps"></a>後續步驟

- [HLI 的其他網路需求](hana-additional-network-requirements.md)
