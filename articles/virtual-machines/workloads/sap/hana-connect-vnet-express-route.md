---
title: 從虛擬網路到 Azure (大型實例上的 SAP Hana 設定的連線) |Microsoft Docs
description: 從虛擬網路設定的連線，以在 Azure (大型實例) 上使用 SAP Hana。
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
ms.openlocfilehash: 8c7e8d4875a8bf3f53ac536ae95ac7499a74d45c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082149"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>將虛擬網路連線至 HANA 大型執行個體

建立 Azure 虛擬網路之後，您可以將該網路連線到 Azure 大型執行個體上的 SAP HANA。 在虛擬網路上建立 Azure ExpressRoute 閘道。 此閘道可讓您將虛擬網路連結至 ExpressRoute 線路，以連接到 HANA 大型實例戳記上的客戶租使用者。

> [!NOTE] 
> 此步驟可能需要 30 分鐘的時間才能完成。 會在指定的 Azure 訂用帳戶中建立新閘道，然後連線到指定的 Azure 虛擬網路。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

如果閘道已經存在，請檢查其是否為 ExpressRoute 閘道。 如果它不是 ExpressRoute 閘道，請刪除閘道，然後重新建立為 ExpressRoute 閘道。 如果已建立 ExpressRoute 閘道，請參閱本文的下一節＜連結虛擬網路＞。 

- 使用 [Azure 入口網站](https://portal.azure.com/) 或 PowerShell 來建立連線到您虛擬網路的 ExpressRoute VPN 閘道。
  - 如果您使用 Azure 入口網站，請新增 **虛擬網路閘道**，然後選取 [ **ExpressRoute** ] 作為閘道類型。
  - 如果您是使用 PowerShell，請先下載並使用最新版的 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 
 
下列命令會建立 ExpressRoute 閘道。 前面加上的文字 _$_ 是使用者定義的變數，應以您的特定資訊進行更新。

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

Azure 虛擬網路現在具有 ExpressRoute 閘道。 使用 Microsoft 提供的授權資訊，將 ExpressRoute 閘道連接到 SAP Hana 大型實例 ExpressRoute 線路。 您可以使用 Azure 入口網站或 PowerShell 來連線。 PowerShell 指示如下所示。 

針對每個連線使用不同的 Authguid 來說都，針對每個 ExpressRoute 閘道執行下列命令。 下列指令碼顯示的前兩個項目來自 Microsoft 所提供的資訊。 此外，AuthGUID 對每個虛擬網路及其閘道來說都是特定的。 如果您想要新增另一個 Azure 虛擬網路，您需要從 Microsoft 取得 ExpressRoute 線路的另一個 AuthID，以將 HANA 大型實例連線到 Azure。 

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
> 命令 New-Get-azvirtualnetworkgatewayconnection， **ExpressRouteGatewayBypass** 中的最後一個參數是可啟用 ExpressRoute Fast Path 的新參數。 可減少您的 HANA 大型實例單位與 Azure Vm 之間的網路延遲的功能。 在5月2019中新增的功能。 如需詳細資訊，請參閱 [SAP Hana (大型實例) 網路架構](./hana-network-architecture.md)的文章。 執行命令之前，請確定您是執行最新版的 PowerShell Cmdlet。

若要將閘道連線到與訂用帳戶關聯的多個 ExpressRoute 線路，可能需要執行此步驟多次。 例如，您可能要將相同的虛擬網路閘道連線到 ExpressRoute 線路，而此線路會將虛擬網路連線到內部部署網路。

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>將 ExpressRoute 快速路徑套用至現有的 HANA 大型實例 ExpressRoute 線路
目前為止的檔說明如何將使用 HANA 大型實例部署所建立的新 ExpressRoute 線路，連接至其中一個 Azure 虛擬網路的 Azure ExpressRoute 閘道。 但許多客戶已經有 ExpressRoute 線路安裝程式，並已將其虛擬網路連線到 HANA 大型實例。 由於新的 ExpressRoute 快速路徑會減少網路延遲，建議您套用變更以使用這項功能。 連接新的 ExpreesRoute 線路和變更現有 ExpressRoute 線路的命令相同。 因此，您需要執行這一系列的 PowerShell 命令，以變更現有的線路以使用 

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

請務必新增上面所顯示的最後一個參數，以啟用 ExpressRoute 快速路徑功能。


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
當您想要為兩個案例中的其中一個或兩個案例啟用全球接觸：

 - 不需要任何其他 proxy 或防火牆的 HANA 系統複寫
 - 在兩個不同區域中的 HANA 大型實例單位之間複本備份，以執行系統複本或系統重新整理

您需要考慮：

- 您必須提供/29 位址空間的位址空間範圍。 該位址範圍可能不會與您到目前為止使用到目前為止的任何其他位址空間範圍重迭，而不會與您在 Azure 或內部部署中其他地方使用的任何 IP 位址範圍重迭。
- Asn (自發系統編號) 有一項限制，可用來將您的內部部署路由公告至 HANA 大型實例。 您的內部部署不得在65000–65020或65515的範圍內通告具有私用 Asn 的任何路由。 
- 針對將內部部署直接存取連接到「HANA 大型實例」的案例，您必須計算將您連接到 Azure 的線路費用。 如需價格，請查看 [全球接觸附加](https://azure.microsoft.com/pricing/details/expressroute/)元件的價格。

若要取得一或兩個套用至您部署的案例，請使用 Azure 開啟支援訊息，如「[針對 HANA 大型實例提出支援要求](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)」中所述。

需要使用的資料，以及您需要用來在您的要求上路由並執行的關鍵字，如下所示：

- 服務： SAP Hana 大型實例
- 問題類型：設定和設定
- 問題子類型：我的問題未列于上方
- 主旨「修改我的網路-加入全球接觸範圍」
- 詳細資料：「將全域範圍延伸至 HANA 大型實例至 HANA 大型實例租使用者」或「在內部部署環境中新增內部部署到 HANA 大型實例租使用者的全域範圍。
- HANA 大型實例至 HANA 大型實例租使用者案例的其他詳細資料：您必須定義兩個要連線的租使用者所在的 **Azure 區域** **，而** 您需要提交 **/29 個 IP 位址範圍**
- 內部部署至 HANA 大型實例租使用者案例的其他詳細資料：您必須定義您想要直接連線到 HANA 大型實例租使用者的 **Azure 區域** 。 此外，您必須提供在內部部署與 Azure 之間建立 ExpressRoute 線路時所收到的 **驗證 GUID** 和 **線路對等識別碼** 。 此外，您還需要為您的 **ASN**命名。 最後一個交付項是 ExpressRoute Global 觸及的 **/29 個 IP 位址範圍** 。

> [!NOTE]
> 如果您想要處理這兩個案例，您需要提供兩個不同的/29 IP 位址範圍，而這些範圍不會與目前為止使用的任何其他 IP 位址範圍重迭。 




## <a name="next-steps"></a>接下來的步驟

- [HLI 的其他網路需求](hana-additional-network-requirements.md)
