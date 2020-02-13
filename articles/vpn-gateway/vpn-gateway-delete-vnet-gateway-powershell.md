---
title: Azure VPN 閘道：刪除閘道： PowerShell
description: 在 Resource Manager 部署模型中使用 PowerShell 刪除虛擬網路閘道。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: f351f14796ec736bd5525f139a518c9a0dd3d19f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162101"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>使用 PowerShell 刪除虛擬網路閘道
> [!div class="op_single_selector"]
> * [Azure 入口網站](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (傳統)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

如果您想要刪除 VPN 閘道組態的虛擬網路閘道，您可以採取幾種不同的方法。

- 如果您想要刪除所有內容並重頭開始 (例如使用測試環境的情況)，您可以刪除資源群組。 刪除資源群組時，會一併刪除群組內的所有資源。 只有在您不想保留資源群組中的任何資源時，才建議使用此方法。 您無法使用這種方法，選擇性地只刪除一些資源。

- 如果您想要保留資源群組中的部分資源，刪除虛擬網路閘道會變得稍微複雜一點。 您必須先刪除任何相依於閘道的資源，才可以刪除虛擬網路閘道。 您遵循的步驟取決於您所建立的連線類型以及每個連線的相依資源。

## <a name="before-beginning"></a>開始之前



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. 下載最新的 Azure Resource Manager PowerShell Cmdlet。

下載並安裝最新版的 Azure Resource Manager PowerShell Cmdlet。 如需下載和安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

### <a name="2-connect-to-your-azure-account"></a>2. 連接到您的 Azure 帳戶。

開啟 PowerShell 主控台並連接到您的帳戶。 使用下列範例來協助您連接：

```powershell
Connect-AzAccount
```

檢查帳戶的訂用帳戶。

```powershell
Get-AzSubscription
```

如果您有多個訂用帳戶，請指定您要使用的訂用帳戶。

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>刪除站對站 VPN 閘道

若要刪除 S2S 組態的虛擬網路閘道，您必須先刪除虛擬網路閘道的每個相關資源。 由於相依性，您必須依照特定順序刪除資源。 使用下面的範例時，某些值必須特別指定，而其他值則為輸出結果。 我們在範例中使用下列特定值，做為示範之用︰

VNet 名稱︰VNet1<br>
資源群組名稱：GW1<br>
虛擬網路閘道名稱：GW1<br>

下列步驟適用於 Resource Manager 部署模型。

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. 取得您想要刪除的虛擬網路閘道。

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. 查看虛擬網路閘道是否有任何連線。

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. 刪除所有連接。

系統可能會提示您確認刪除每個連線。

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. 刪除虛擬網路閘道。

系統可能會提示您確認刪除閘道。 如果除了 S2S 組態，您還擁有此 VNet 適用的 P2S 組態，刪除虛擬網路閘道將自動中斷連接所有的 P2S 用戶端，而不會發出任何警告。


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

此時，虛擬網路閘道已經刪除。 您可以使用後續步驟來刪除不再使用的任何資源。

### <a name="5-delete-the-local-network-gateways"></a>5 刪除區域網路閘道。

取得對應區域網路閘道的清單。

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

刪除區域網路閘道。 系統可能會提示您確認刪除每個區域網路閘道。

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. 刪除公用 IP 位址資源。

取得虛擬網路閘道的 IP 組態。

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

取得用於此虛擬網路閘道的公用 IP 位址資源清單。 如果虛擬網路閘道為主動-主動，您會看到兩個公用 IP 位址。

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

刪除公用 IP 資源。

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. 刪除閘道子網並設定設定。

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>刪除 VNet 對 VNet VPN 閘道

若要刪除 V2V 組態的虛擬網路閘道，您必須先刪除虛擬網路閘道的每個相關資源。 由於相依性，您必須依照特定順序刪除資源。 使用下面的範例時，某些值必須特別指定，而其他值則為輸出結果。 我們在範例中使用下列特定值，做為示範之用︰

VNet 名稱︰VNet1<br>
資源群組名稱：GW1<br>
虛擬網路閘道名稱：GW1<br>

下列步驟適用於 Resource Manager 部署模型。

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. 取得您想要刪除的虛擬網路閘道。

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. 查看虛擬網路閘道是否有任何連線。

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
可能屬於不同的資源群組的其他連線至虛擬網路閘道。 檢查其他每個資源群組中的其他連線。 在此範例中，我們會檢查來自 RG2 的連線。 針對可能連線至虛擬網路閘道的每個資源群組，執行此作業。

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. 取得雙向連接的清單。

因為這是 VNet 對 VNet 組態，所以您需要雙向連線清單。

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
在此範例中，我們會檢查來自 RG2 的連線。 針對可能連線至虛擬網路閘道的每個資源群組，執行此作業。

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. 刪除所有連接。

系統可能會提示您確認刪除每個連線。

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. 刪除虛擬網路閘道。

系統可能會提示您確認刪除虛擬網路閘道。 如果除了 V2V 組態，您還擁有您 VNet 適用的 P2S 組態，刪除虛擬網路閘道將自動中斷連接所有的 P2S 用戶端，而不會發出任何警告。

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

此時，虛擬網路閘道已經刪除。 您可以使用後續步驟來刪除不再使用的任何資源。

### <a name="6-delete-the-public-ip-address-resources"></a>6. 刪除公用 IP 位址資源

取得虛擬網路閘道的 IP 組態。

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

取得用於此虛擬網路閘道的公用 IP 位址資源清單。 如果虛擬網路閘道為主動-主動，您會看到兩個公用 IP 位址。

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

刪除公用 IP 資源。 系統可能會提示您確認刪除公用 IP。

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. 刪除閘道子網並設定設定。

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>刪除點對站 VPN 閘道

若要刪除 P2S 組態的虛擬網路閘道，您必須先刪除每個與虛擬網路閘道相關的資源。 由於相依性，您必須依照特定順序刪除資源。 使用下面的範例時，某些值必須特別指定，而其他值則為輸出結果。 我們在範例中使用下列特定值，做為示範之用︰

VNet 名稱︰VNet1<br>
資源群組名稱：GW1<br>
虛擬網路閘道名稱：GW1<br>

下列步驟適用於 Resource Manager 部署模型。


>[!NOTE]
> 當您刪除 VPN 閘道時，將中斷所有已連接用戶端與 VNet 的連接，而不會發出任何警告。
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. 取得您想要刪除的虛擬網路閘道。

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. 刪除虛擬網路閘道。

系統可能會提示您確認刪除虛擬網路閘道。

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

此時，虛擬網路閘道已經刪除。 您可以使用後續步驟來刪除不再使用的任何資源。

### <a name="3-delete-the-public-ip-address-resources"></a>3. 刪除公用 IP 位址資源

取得虛擬網路閘道的 IP 組態。

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

取得用於此虛擬網路閘道的公用 IP 位址清單。 如果虛擬網路閘道為主動-主動，您會看到兩個公用 IP 位址。

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

刪除公用 IP。 系統可能會提示您確認刪除公用 IP。

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. 刪除閘道子網並設定設定。

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>藉由刪除資源群組來刪除 VPN 閘道

如果您不在乎保留資源群組中任何資源，而只想要從頭開始，您可以刪除整個資源群組。 這是移除所有項目的快速方法。 下列步驟僅適用於 Resource Manager 部署模型。

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. 取得訂用帳戶中所有資源群組的清單。

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. 找出您想要刪除的資源群組。

找出您想要刪除的資源群組，並檢視該資源群組中的資源清單。 在此範例中，資源群組的名稱為 RG1。 修改範例，以擷取所有資源的清單。

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. 確認清單中的資源。

清單傳回後，請加以檢閱，確認您想要刪除資源群組中的所有資源，以及資源群組本身。 如果您想要保留資源群組中的某些資源，請使用本文先前小節中的步驟來刪除您的閘道。

### <a name="4-delete-the-resource-group-and-resources"></a>4. 刪除資源群組和資源。

若要刪除資源群組和資源群組內含的所有資源，請修改範例並執行。

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. 檢查狀態。

Azure 需花一些時間刪除所有資源。 您可以使用 Cmdlet 檢查資源群組的狀態。

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

傳回的結果顯示「成功」。

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
