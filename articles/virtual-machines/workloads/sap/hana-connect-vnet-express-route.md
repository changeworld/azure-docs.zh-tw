---
title: 從虛擬網路到 Azure （大型實例）上 SAP Hana 的連線設定 |Microsoft Docs
description: 從虛擬網路設定為使用 Azure 上 SAP Hana （大型實例）的連線能力。
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
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617191"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>將虛擬網路連線至 HANA 大型執行個體

建立 Azure 虛擬網路之後，您可以將該網路連線到 Azure 大型執行個體上的 SAP HANA。 在虛擬網路上建立 Azure ExpressRoute 閘道。 此閘道可讓您將虛擬網路連結到 ExpressRoute 線路，而此線路會連線到 HANA 大型實例戳記上的客戶租使用者。

> [!NOTE] 
> 此步驟可能需要 30 分鐘的時間才能完成。 會在指定的 Azure 訂用帳戶中建立新閘道，然後連線到指定的 Azure 虛擬網路。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

如果閘道已經存在，請檢查其是否為 ExpressRoute 閘道。 如果它不是 ExpressRoute 閘道，請刪除閘道，然後將其重新建立為 ExpressRoute 閘道。 如果已建立 ExpressRoute 閘道，請參閱本文的下一節＜連結虛擬網路＞。 

- 使用 [Azure 入口網站](https://portal.azure.com/) 或 PowerShell 來建立連線到您虛擬網路的 ExpressRoute VPN 閘道。
  - 如果您使用 Azure 入口網站，請新增新的 [虛擬網路閘道]，然後選取 [ExpressRoute] 作為閘道類型。
  - 如果您是使用 PowerShell，請先下載並使用最新版的 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 
 
下列命令會建立 ExpressRoute 閘道。 前面加上 _$_ 的文字是使用者定義的變數，應以您的特定資訊進行更新。

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

Azure 虛擬網路現在具有 ExpressRoute 閘道。 使用 Microsoft 提供的授權資訊，將 ExpressRoute 閘道連線至 SAP Hana 大型實例 ExpressRoute 線路。 您可以使用 Azure 入口網站或 PowerShell 來連線。 PowerShell 指示如下所示。 

針對每個連接使用不同的 Authguid 來說都，為每個 ExpressRoute 閘道執行下列命令。 下列指令碼顯示的前兩個項目來自 Microsoft 所提供的資訊。 此外，AuthGUID 對每個虛擬網路及其閘道來說都是特定的。 如果您想要新增另一個 Azure 虛擬網路，您需要取得 ExpressRoute 線路的另一個 AuthID，以便將 HANA 大型實例連線到 Azure 中的 Microsoft。 

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
> 命令 Get-azvirtualnetworkgatewayconnection， **ExpressRouteGatewayBypass**中的最後一個參數是啟用 ExpressRoute 快速路徑的新參數。 可減少您的 HANA 大型實例單位與 Azure Vm 之間的網路延遲的功能。 在5月2019中新增的功能。 如需詳細資訊，請參閱[SAP Hana （大型實例）網路架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)一文。 請確定您執行的是最新版本的 PowerShell Cmdlet，然後再執行命令。

若要將閘道連線到與訂用帳戶關聯的多個 ExpressRoute 線路，可能需要執行此步驟多次。 例如，您可能要將相同的虛擬網路閘道連線到 ExpressRoute 線路，而此線路會將虛擬網路連線到內部部署網路。

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>將 ExpressRoute 快速路徑套用至現有的 HANA 大型實例 ExpressRoute 線路
目前為止的檔說明如何將使用 HANA 大型實例部署所建立的新 ExpressRoute 線路，連接至其中一個 Azure 虛擬網路的 Azure ExpressRoute 閘道。 但許多客戶已設定好 ExpressRoute 電路，並已將其虛擬網路連線到 HANA 大型實例。 由於新的 ExpressRoute 快速路徑會減少網路延遲，因此建議您套用變更以使用此功能。 用來連接新 ExpreesRoute 線路和變更現有 ExpressRoute 線路的命令是相同的。 因此，您需要執行這一系列的 PowerShell 命令，以變更要使用的現有線路 

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

請務必新增如上所示的最後一個參數，以啟用 ExpressRoute 快速路徑功能


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
因為您想要針對這兩個案例的其中一種或兩種情況啟用全球觸達：

 - HANA 系統複寫，不需要任何額外的 proxy 或防火牆
 - 在兩個不同區域中的 HANA 大型實例單位之間複本備份，以執行系統複製或系統重新整理

您需要考慮：

- 您必須提供/29 位址空間的位址空間範圍。 該位址範圍可能與您目前用來將 HANA 大型實例連線到 Azure 的任何其他位址空間範圍不重迭，而且可能不會與您在 Azure 或內部部署中其他地方使用的任何 IP 位址範圍重迭。
- Asn （自發系統編號）有一項限制，可用來將您的內部部署路由公告至 HANA 大型實例。 您的內部部署不得以65000–65020或65515的範圍來公告具有私用 Asn 的任何路由。 
- 針對將內部部署直接存取連接到 HANA 大型實例的案例，您需要計算連接到 Azure 的線路費用。 如需價格，請查看[全球接觸附加](https://azure.microsoft.com/pricing/details/expressroute/)元件的價格。

若要取得套用至部署的一或兩個案例，請使用 Azure 開啟支援訊息，如[開啟 HANA 大型實例的支援要求](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)中所述

需要使用的資料和關鍵字，讓 Microsoft 能夠在您的要求上路由及執行，如下所示：

- 服務： SAP Hana 大型實例
- 問題類型：設定和安裝
- 問題子類型：我的問題未列于上方
- 主旨「修改我的網路-加入全球範圍」
- 詳細資料：「新增至 hana 大型實例到 hana 大型實例租使用者的全域延伸，或將內部部署的全域延伸新增至 HANA 大型實例租使用者。
- HANA 大型實例到 HANA 大型實例租使用者案例的其他詳細資料：您必須定義**兩個 Azure 區域**，其中兩個租使用者會位於其中，**而**您需要提交 **/29 個 IP 位址範圍**
- 內部部署至 HANA 大型實例租使用者案例的其他詳細資料：您必須定義**Azure 區域**，其中部署了您想要直接連接的 HANA 大型實例租使用者。 此外，您還需要提供在內部部署與 Azure 之間建立 ExpressRoute 線路時所收到的**驗證 GUID**和**線路對等識別碼**。 此外，您必須為**ASN**命名。 最後一個交付項是 ExpressRoute 全球範圍的 **/29 IP 位址範圍**。

> [!NOTE]
> 如果您想要處理這兩個案例，您需要提供兩個不同的/29 個 IP 位址範圍，而不會與目前為止使用的任何其他 IP 位址範圍重迭。 




## <a name="next-steps"></a>後續步驟

- [HLI 的其他網路需求](hana-additional-network-requirements.md)
